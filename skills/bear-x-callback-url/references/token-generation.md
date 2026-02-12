# Token Generation

Some actions require an API token or return data only when a token is provided. Tokens are platform-specific (iOS tokens do not work on macOS and vice versa).

## macOS

- Open Bear.
- Select Help -> Advanced -> API Token -> Copy Token.
- The token is placed on the clipboard.

## iOS

- Open Bear.
- Go to Preferences -> Advanced.
- In the API Token section, tap the cell below to generate or copy the token.

## Where tokens are required or recommended

- `selected=yes` in `open-note`, `add-text`, and `add-file` requires a token.
- `tags` requires a token.
- `open-tag`, `untagged`, `todo`, `today`, and `search` return data only if a token is provided.
