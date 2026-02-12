# /trash

Move a note to the Trash sidebar item. This action cannot run while Bear is locked. Encrypted notes cannot be used.

## Parameters

- `id` (optional): Note identifier.
- `search` (optional): Search string.
- `show_window` (optional, macOS only): If `no`, do not force the main window to open.

## Notes

- If `id` is provided, `search` is ignored.

## Example

`bear://x-callback-url/trash?id=7E4B681B`

URL builder: https://bear.app/xurl/trash/
