# /tags

Return all tags currently displayed in Bear's sidebar.

## Parameters

- `token` (required): Application token. See [token-generation.md](token-generation.md).

## x-success

- `tags`: JSON array of tags. Example shape: `[{ name }, ...]`.

## Example

`bear://x-callback-url/tags?token=123456-123456-123456`

URL builder: https://bear.app/xurl/tags/
