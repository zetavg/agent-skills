# /open-tag

Show notes that match a tag (or any of multiple tags).

## Parameters

- `name` (required): Tag name or comma-separated list of tags.
- `token` (optional): Application token. If not provided, nothing is returned. See [references/token-generation.md](references/token-generation.md).

## x-success

- `notes`: JSON array of notes: `[{ title, identifier, modificationDate, creationDate, pin }, ...]`.

## Notes

- Encrypted notes are excluded.
- If multiple tags are provided, results include notes matching any of the tags.

## Example

`bear://x-callback-url/open-tag?name=todo%2Fwork`

URL builder: https://bear.app/xurl/open-tag/
