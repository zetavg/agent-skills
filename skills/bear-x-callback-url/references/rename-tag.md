# /rename-tag

Rename an existing tag. This action cannot run while Bear is locked. If the tag contains any locked note, the rename is not performed.

## Parameters

- `name` (required): Existing tag name.
- `new_name` (required): New tag name.
- `show_window` (optional, macOS only): If `no`, do not force the main window to open.

## Example

`bear://x-callback-url/rename-tag?name=todo&new_name=done`

URL builder: https://bear.app/xurl/rename-tag/
