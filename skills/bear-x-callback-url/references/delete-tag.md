# /delete-tag

Delete an existing tag. This action cannot run while Bear is locked. If the tag contains any locked note, the delete is not performed.

## Parameters

- `name` (required): Tag name.
- `show_window` (optional, macOS only): If `no`, do not force the main window to open.

## Example

`bear://x-callback-url/delete-tag?name=todo`

URL builder: https://bear.app/xurl/delete-tag/
