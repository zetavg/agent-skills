---
name: forge-deployment-safety
description: Safeguard Atlassian Forge app changes that become irreversible, immutable, migration-sensitive, permission-sensitive, or disruptive after deployment. Classify changes, require prominent human review of the final diff, and forbid any deployment or equivalent remote mutation until the user explicitly confirms review and authorizes that exact deployment.
---

# Forge Deployment Safety — Human Review Required

**MANDATORY SAFETY RULE: Never deploy a change covered by this skill before the user has reviewed the completed change and explicitly authorized deployment to the specified environment. This includes `development`, custom development environments, `staging`, and `production`.**

## When to use this skill

Apply this skill when designing, adding, removing, renaming, or modifying any of the items in the tables below, **including their first deployment**. Also apply it to any change that may alter persisted data, installation identity, end-user permissions, public endpoints, scheduled execution, or deployment/version behavior. If the impact is uncertain, **classify it as protected** until disproven; consult current Atlassian documentation.

A change need not be strictly irreversible to trigger this skill. A difficult migration, consent requirement, permanent resource consumption, or customer-visible deployment side effect is sufficient.

**Interpretation:** “Cannot change” means unsupported in place **under the stated condition**. It does not necessarily mean no migration or replacement is possible. “Difficult to change” means technically possible but requires migration, special sequencing, re-consent, reindexing, coordination, or an operational rollback plan. These are intentionally different categories.

## Risk inventory — review this table before making a deployment decision

**Table ordering is deliberate: changes unsupported in place FIRST; changes that are difficult or risky SECOND.** Source references appear at the end of this skill.

| Class | Forge surface / operation | After deployment: what is locked or risky? | Safer handling / review focus | Source |
|---|---|---|---|---|
| **CANNOT CHANGE IN PLACE** | Custom Entity Store: delete an entity (`app.storage.entities[].name`) | An entity that has been deployed cannot be removed from that app's manifest in the existing schema history, even when no records exist. A rename effectively removes the old entity and adds another. | Keep the existing declaration, or create a new entity and migrate while retaining the old one. Use a disposable custom development environment for spikes. | [S1], [S2] |
| **CANNOT CHANGE IN PLACE** | Custom Entity Store: delete or rename an attribute (`attributes.<name>`) | Deleting an already-deployed attribute is rejected. Renaming is not a supported in-place operation because the original declaration disappears. | Add a new attribute, migrate reads/writes, retain the old declaration. | [S1], [S2] |
| **CANNOT CHANGE IN PLACE** | Custom Entity Store: change an existing attribute's `type` | Server validation rejects type changes, including widening `string` to `any` (`BREAKING_CHANGES_NOT_SUPPORTED`). | Add a new differently named attribute of the desired type, migrate data, and keep the old declaration. | [S1], [S3] |
| **CANNOT CHANGE IN PLACE (CONDITIONAL)** | Custom Entity Store: remove an index after it has appeared in `staging` or `production` | Such indexes cannot subsequently be removed in **any** environment. Indexes deployed only to development/custom development environments can still be deleted, subject to current platform rules. | Finalize index names and query patterns before staging; create a replacement index without assuming you can reclaim the old slot. | [S1] |
| **CANNOT CHANGE IN PLACE (CONDITIONAL)** | Jira `jira:customField` / `jira:customFieldType`: change the data type after a field has been used | Atlassian explicitly says this is unsupported and can result in undefined behavior or an unusable field. This is **not** a blanket prohibition on editing field names or descriptions. | Introduce a new field/type, plan data and JQL migration, preserve existing consumers until migrated. | [S4], [S5] |
| **CANNOT CHANGE WITH EXISTING INSTALLATIONS (CONDITIONAL)** | `app.compatibility`: switch the required Atlassian product, or revert a multi-product app to single-product compatibility | The documented migration requires **no existing installations** for these transitions; a deployed multi-product app cannot simply switch back while installed. | Review installed products, upgrade strategy, uninstall implications, and customer communication. | [S6] |
| **NOT RESETTABLE IN PLACE (OBSERVED)** | Built-in `development` environment (`default` environment key): delete and recreate to clear deployed schemas | The Forge service has rejected deletion with: `default cannot be deleted because the environment was not created by a contributor.` This is an observed platform response, not a general guarantee about every future CLI version. | Do **not** assume uninstall clears schema history. Prefer a fresh disposable *custom* development environment; a newly registered app uses a different App ID and storage identity. | [S7], [S8], [S9] |
| **IDENTITY CANNOT BE RENAMED IN PLACE** | Registered Forge App ID (`app.id`) | `forge register` creates a **different** app registration/App ID; editing to that ID is not an in-place rename or reset. Old installations, variables, secrets, and storage are not carried over. | Treat re-registration as a separate-app migration. Record the old ID and explicitly plan all installations and dependencies. | [S9] |
| **DIFFICULT / RISKY TO CHANGE** | Custom Entity Store: initially add entities or attributes | Declarations that look experimental become non-removable once deployed; limits are **20 entities per app**, **50 attributes per entity**, and **7 custom indexes per entity**. | Review naming, type, precision, retention, index requirements, and long-term slot consumption **before first deploy**. | [S1] |
| **DIFFICULT / RISKY TO CHANGE** | Custom Entity Store: add, rename, or change index definitions | Index creation is asynchronous; availability can lag deployment. Changing an index should be treated as adding a replacement and separately evaluating whether the old index can legally be removed. Staging/production index slots may become permanent. | Review filter/range/partition strategy, per-entity index cap, query migrations, and `ACTIVE` verification before installation/upgrade. | [S1] |
| **DIFFICULT / RISKY TO CHANGE** | Backport code to an older Forge major version when it includes custom entity schema changes | Forge ignores the manifest `storage` section for `forge deploy --major-version` backports; it is **not** a schema rollback or migration tool. | Plan compatible code/schema changes and a forward migration instead. | [S1], [S10] |
| **DIFFICULT / RISKY TO CHANGE** | Forge SQL DDL, migrations, or switching storage models | `ALTER` / `DROP` are supported, but migrations operate across per-installation databases and older app versions may coexist. Destructive DDL or changed IDs can break old installations or lose data. | Use idempotent, per-installation migration steps, compatibility checks, backfill, backups/verification, and a roll-forward/recovery strategy. | [S11] |
| **DIFFICULT / RISKY TO CHANGE** | KVS / entity values, key formats, serializations, or ownership/partition assumptions | Values and keys are mutable, **not** immutable schema, but replacing key formats or formats in code requires backfill and backward-compatible readers. Hosted storage is scoped by app/environment/installation. | Review dual-read/dual-write, migration coverage, partial failure, and retention. | [S12] |
| **DIFFICULT / RISKY TO CHANGE** | Jira `jira:entityProperty` / `jira:entityPropertySet`: `propertyKey`, indexed `path`, `type`, or `searchAlias` | Existing JQL queries, indexed values, and consumers depend on these declarations. A changed type/alias may change query operators or meaning. No generic immutable-schema rule is asserted here. | Plan reindexing/dual compatibility, consumer migration, and query tests. | [S13] |
| **DIFFICULT / RISKY TO CHANGE** | Forge module `key` for externally referenced/persisted modules (especially Jira custom fields, custom field types, web triggers, Confluence macros) | Keys can form part of stable extension identities or consumers' references; a replacement key may be a **different extension**, and removing old modules may hide existing fields/content. This is module-specific, not a blanket Forge restriction. | Inventory IDs/URLs/JQL/macro usages, review lifecycle behavior, and migrate consumers before removal. | [S4], [S5], [S14] |
| **DIFFICULT / RISKY TO CHANGE** | `permissions.scopes`, external fetch/CSP permissions, egress category, `inScopeEUD`, new remote permissions | Many changes trigger a Forge **major version**, potentially requiring site-admin review/re-consent. Permission or egress changes may also affect compliance/data residency. Even **removing** a scope is listed among major changes. | Review precise privileges, affected installations, upgrade path, data egress, and approval requirements. | [S10], [S15] |
| **DIFFICULT / RISKY TO CHANGE** | OAuth providers, provider client IDs, external auth configuration | Adding/removing providers or changing provider client ID is a major-version trigger. Existing authorizations may need re-consent or reconnection. | Review user impact, credential rotation, callback settings, and planned reauthorization. | [S10] |
| **DIFFICULT / RISKY TO CHANGE** | Forge Remote endpoints, region `baseUrl`, `inScopeEUD`, external paths, and realm pinning | Many endpoint or purpose expansions trigger a major version. Changing regional mappings can require explicit data-residency realm migration to avoid misrouting existing installations. | Review customer region assignments, migration hooks, existing users' data location, and phased rollout. | [S16], [S17] |
| **DIFFICULT / RISKY TO CHANGE** | `app.compatibility`: expand to additional Atlassian products | Can alter installation, storage-access, licensing, and required-product expectations; a later reversal may be blocked while installations exist. | Review app compatibility and future exit strategy **before** expanding. | [S6] |
| **DIFFICULT / RISKY TO CHANGE** | `jira:customField` / `jira:customFieldType`: remove module or change referenced `key` | Removing a module hides its field/type and stored field values; reappearance/recovery has lifecycle limits. Do not equate reinstallation with permanent migration safety. | Inventory fields, screens, automation/JQL dependencies, and data recovery plan. | [S4], [S5] |
| **DIFFICULT / RISKY TO CHANGE** | Scheduled trigger module and interval (`modules.scheduledTrigger`) | **Any scheduled-trigger module changes reset trigger schedules/start times**, even though the interval can be changed. Execution can create real work items, incur cost, or cause duplicates if not idempotent. | Review schedule phase, idempotency, backfill/lookback, timezone/DST behavior, and affected installations. | [S18] |
| **DIFFICULT / RISKY TO CHANGE** | Web triggers: create/change public endpoint, especially static-to-dynamic | New `dynamic` web triggers or static-to-dynamic changes may trigger a major version. Web-trigger URLs are publicly reachable and do not have built-in authentication by default. | Review authentication, authorization, abuse prevention, URL consumers, and data exposure before deploying. | [S10], [S18] |
| **DIFFICULT / RISKY TO CHANGE** | Licensing enablement / Marketplace release | Enabling licensing requires Marketplace listing approval and triggers a major version. Distribution/customer entitlement behavior needs operational planning. | Review billing/licensing behavior, listing review, existing customers, and rollout. | [S10] |
| **DIFFICULT / RISKY TO CHANGE** | Runtime upgrade, production deployment, and Forge major/minor versions | A runtime-only change can be a **minor** version; minor versions automatically reach installations on the same major version. Platform rollouts do not offer a guaranteed direct rollback; fixing often means rolling forward. | Review runtime compatibility and all affected installations; design an emergency forward-fix plan. | [S10], [S19] |
| **DIFFICULT / RISKY TO CHANGE** | Uninstall, re-register, app deletion, or move between environments | These are **not** safe schema-reset shortcuts. Storage, provider secrets, installations and app IDs are scoped; recovery/retention is conditional and time-limited. | Require separate explicit authorization, recovery checklist, and exact environment/site confirmation. | [S9], [S12], [S20] |
| **DIFFICULT / RISKY TO CHANGE** | Environment variables/secrets changing deployed behavior | Values are per app/environment, **not** automatically copied. Updates do not take effect until deployment; secret changes can break old code or remote connections. | Review secret rollout ordering, targets, back-compat, and redeploy implications. | [S21] |

## Mandatory workflow (do not skip steps)

### 1. Detect and classify **before editing**

1. Inspect the planned changes and the table. Read the currently deployed baseline, not only the current working tree, when historical deployment matters. Check each affected environment separately.
2. Identify **first deployments**, additions that become permanent, and apparently harmless renames/removals. A clean Git diff does not imply a clean Forge deployment history.
3. If the app uses Custom Entity Store, explicitly compare `app.storage.entities` / attributes / indexes against the already-deployed schema. If that history is unavailable, state the uncertainty and treat the change as protected.
4. Give an early risk warning when useful. You may implement requested changes locally without deployment authorization, but **do not deploy or mutate remote state** as a test.

### 2. Implement and validate locally

Allowed before review: edit source files; inspect docs; run local type checks, unit tests, lint, builds, and `forge lint` **provided they cannot deploy or mutate a remote system**. Review scripts carefully: a package script or CI command named `test` may still execute `forge deploy` or modify cloud state.

Not allowed before the gates in step 4: deploying, upgrading installations, running schema migrations against remote storage, registering a replacement app, deleting environments, changing remote variables/secrets, running remote mutating tests, or triggering a pipeline that does any of these.

### 3. STOP and prominently request human review **after making changes**

**You MUST do this even when the user originally said “implement and deploy,” “finish the task,” or “handle everything.”** Those statements are not confirmation that the **final diff** has been reviewed.

At the top of your completion message, show a highly visible warning; **do not bury it under an implementation summary**:

> **🚨 FORGE DEPLOYMENT BLOCKED — USER REVIEW REQUIRED**
>
> I implemented changes that are irreversible or difficult to reverse after deployment. **Nothing has been deployed.** Please review the exact changes below before authorizing deployment.

Then show:

- **Affected resources:** exact entity/attribute/index/module/manifest paths and what was added, changed, renamed, or removed.
- **Risk class:** `CANNOT CHANGE IN PLACE` or `DIFFICULT / RISKY TO CHANGE`, referencing the table row(s).
- **Permanence / impact:** what becomes locked after the first deployment; whether previous schema or customer data remains; major-version/re-consent effects if applicable.
- **Deployment target:** exact app ID, named environment(s), site(s), and whether installations will be upgraded. If any are unknown, say so.
- **Validation:** what was tested **locally**, what could not be validated without deployment, and any uncertainty.
- **Review materials:** exact changed files, diff/PR link if available, or a short concrete before/after summary.
- **Recovery plan:** whether rollback is supported, or whether a forward migration is required.

End with a direct request, prominently visible:

> **ACTION REQUIRED — PLEASE REVIEW:** Review the changed files and confirm (1) you have reviewed the final changes and (2) you explicitly authorize deployment to **[exact environment/site]**. I will not deploy until both are confirmed.

**Repeat the warning** when returning with a revised diff, after answering review questions, or when the user asks for a status while approval is outstanding. Do not spam multiple copies in the same reply; repeat at meaningful checkpoints.

### 4. Enforce two independent gates

**Gate A — review confirmation:** The user must explicitly indicate that they have **reviewed the final, current diff** (or explicitly approve its contents after being shown it). Silence, general trust, an old approval, permission to edit, or mere acknowledgment of risk does not count.

**Gate B — deployment authorization:** The user must separately and explicitly authorize **deploying those reviewed changes** to a specific environment/site. One message may satisfy both gates if it clearly does both (for example: “I reviewed the diff. Deploy this version to clean-dev on test.example.atlassian.net.”). An explicit “do not deploy” always wins.

Once the final diff is changed in any material way, **invalidate Gate A and Gate B** for the changed portion and request fresh review/authorization. If the target environment, app ID, installation, site, or command scope changes, request authorization again.

If a user says only “looks good,” “continue,” “run the tests,” or “please handle it,” do **not** infer permission to deploy. Ask for the missing confirmation(s). If the user explicitly declines or postpones review, stop at locally validated code.

### 5. Only after both gates: verify scope, then deploy

- Confirm the current artifact and diff still match the reviewed version; record the approved commit or content hash when possible.
- State the exact app ID, target environment and site; never rely silently on the CLI's default environment. Use `-e <approved-environment>` on commands that support it.
- Inspect the CLI's major-version warning and affected installations. Do **not** reinterpret the user's review as blanket consent to extra scopes, remotes, or unrelated changes discovered at deployment time.
- Never bypass approval checks with `forge deploy --no-verify`, `--approve MAJOR_VERSION_RULE`, or a workaround unless the user has separately approved the exact new warning/permission change. CLI approval flags are not substitutes for human review.
- If deployment changes unexpectedly, errors out, or requires a new operation, stop and report the new risk. Do not automatically try an alternate environment, reinstall, new App ID, destructive migration, or higher-permission command.
- After a permitted deploy, report actual command/results, index status (`ACTIVE` where relevant), app version, environment, and any remaining install/upgrade steps. Never claim a deploy occurred when it did not.

### 6. No indirect bypass

The deployment gate applies regardless of execution mechanism: `forge deploy`, deployment via GitHub Actions / Bitbucket / other CI, build-tag promotion, rolling release, `forge install --upgrade`, `forge version bulk-upgrade`, Developer Console, scripts that deploy, a sub-agent, a third-party connector, or other systems that change live app configuration or data. **Do not delegate to another actor to avoid this rule.**

Do not use an unreviewed deployment to `development` or a disposable environment as a “harmless test”: first deploying a Custom Entity there can still create an undeletable schema history for that app/environment. Likewise, **do not run a destructive migration or registry change before review** simply because it is not literally named `deploy`.

If asked for just code or documentation, finish the work and stop. Do not assume deployment is part of the request.

## Reviewer-facing concise checklist

- [ ] I have seen the actual final diff, including `manifest.yml` and all migration files.
- [ ] I understand which additions/changes may be permanent **after the next deployment**.
- [ ] I reviewed names, types, indexes, module keys, compatibility, and public integrations.
- [ ] I understand effects on old installations, stored data, permission scopes, and versioning.
- [ ] I understand the exact App ID, environment, site, and installation upgrade scope.
- [ ] I have reviewed the testing limitations and recovery / roll-forward plan.
- [ ] I explicitly confirmed review **and** authorized this specific deployment.

## Practical design notes

- Prefer isolated, disposable **custom development environments** for Custom Entity schema spikes. Do not deploy throwaway entities into a long-lived default environment. Custom development environments are still real Forge deployments with their own history.
- Do not assume `forge uninstall` removes Custom Entity schema definitions. **Stored data deletion is not schema deletion.**
- For Custom Entity Store, prefer stable, queryable typed attributes; reserve `any` for genuinely flexible data that does **not** need an index. If a mutable relational schema is essential, assess Forge SQL with explicit migration discipline.
- For any rename of a persisted or externally referenced identifier, assume a multi-step migration rather than an atomic rename until its specific module/API documents otherwise.
- Re-check Atlassian's docs before applying the risk table: Forge platform behavior can change. If a documented restriction is relaxed, update this skill with evidence; do not silently downgrade review requirements.

## References (checked 2026-10-11)

- **[S1]** [Forge Custom Entities — limitations, entity/attribute/index deletion, backporting](https://developer.atlassian.com/platform/forge/storage-reference/entities-manifest/)
- **[S2]** [Developer community — `DELETED_ENTITY` in development](https://community.developer.atlassian.com/t/forge-deleting-redundant-storage-entity-deployed-to-development-environment/79191)
- **[S3]** [Developer community — rejected `string` → `any` attribute type change](https://community.developer.atlassian.com/t/error-manifest-validation-failed-breaking-changes-not-supported/83711)
- **[S4]** [Forge `jira:customField` lifecycle](https://developer.atlassian.com/platform/forge/manifest-reference/modules/jira-custom-field/)
- **[S5]** [Forge `jira:customFieldType` lifecycle](https://developer.atlassian.com/platform/forge/manifest-reference/modules/jira-custom-field-type/)
- **[S6]** [Migrating to multi-product Forge app: restrictions](https://developer.atlassian.com/platform/forge/migrating-a-forge-app-to-support-multiple-atlassian-apps/)
- **[S7]** [Forge CLI: environments delete](https://developer.atlassian.com/platform/forge/cli-reference/environments-delete/)
- **[S8]** [Forge environments and custom development environments](https://developer.atlassian.com/platform/forge/environments-and-versions/). Built-in `development` deletion rejection is based on the user's direct CLI/server test, not on an explicitly published guarantee.
- **[S9]** [Forge CLI: `register` consequences](https://developer.atlassian.com/platform/forge/cli-reference/register/)
- **[S10]** [Forge app versions, major/minor upgrades and backports](https://developer.atlassian.com/platform/forge/versions/)
- **[S11]** [Forge SQL schema migrations and backward compatibility](https://developer.atlassian.com/platform/forge/storage-reference/sql-api-schema/)
- **[S12]** [Forge SQL and installation/environment partitioning](https://developer.atlassian.com/platform/forge/storage-reference/sql/)
- **[S13]** [Forge Jira entity-property indexes and JQL aliases](https://developer.atlassian.com/platform/forge/manifest-reference/modules/jira-entity-property/)
- **[S14]** [Preserving macro keys when migrating Connect → Forge](https://developer.atlassian.com/platform/forge/adopting-forge-from-connect-migrate-macro/)
- **[S15]** [In-scope end-user data and major-version effects](https://developer.atlassian.com/platform/forge/in-scope-end-user-data/)
- **[S16]** [Forge Remote declarations and versioning](https://developer.atlassian.com/platform/forge/manifest-reference/remotes/)
- **[S17]** [Realm pinning and migration for Forge Remote](https://developer.atlassian.com/platform/forge/remote/remote-realm-pinning/)
- **[S18]** [Scheduled trigger behavior; web-trigger authentication](https://developer.atlassian.com/platform/forge/add-scheduled-trigger/)
- **[S19]** [Rolling releases and roll-forward recovery](https://developer.atlassian.com/platform/forge/rolling-releases/)
- **[S20]** [Forge-hosted storage data lifecycle](https://developer.atlassian.com/platform/forge/storage-reference/hosted-storage-data-lifecycle/)
- **[S21]** [Forge variables require environment-specific redeployment](https://developer.atlassian.com/platform/forge/cli-reference/variables-set/)
