# /todo

Select the Todo sidebar item.

## Parameters

- `search` (optional): Search string.
- `show_window` (optional, macOS only): If `no`, do not force the main window to open.
- `token` (optional): Application token. If not provided, nothing is returned. See [token-generation.md](token-generation.md).

## x-success

- `notes`: JSON array of notes: `[{ title, identifier, [tag, ...], modificationDate, creationDate, pin }, ...]`.

## Notes

- Encrypted notes are excluded.

## Example

`bear://x-callback-url/todo?search=home`

URL builder: https://bear.app/xurl/todo/
