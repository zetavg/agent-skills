# /add-file

Append or prepend a file to a note by title or id. This action cannot run while Bear is locked. Encrypted notes cannot be accessed.

## Parameters

- `id` (optional): Note identifier.
- `title` (optional): Note title.
- `selected` (optional): If `yes`, use the currently selected note (token required; see [references/token-generation.md](references/token-generation.md)).
- `file` (required): Base64 representation of a file (must be URL-encoded).
- `header` (optional): Add the file under the matching header inside the note.
- `filename` (required): File name with extension.
- `mode` (optional): `prepend`, `append`, `replace_all`, or `replace` (keeps the note title untouched).
- `open_note` (optional): If `no`, do not display the note in main or external window.
- `new_window` (optional, macOS only): If `yes`, open in an external window.
- `show_window` (optional, macOS only): If `no`, do not force the main window to open.
- `edit` (optional): If `yes`, place the cursor inside the editor.

## x-success

- `note`: Note text.

## Example

`bear://x-callback-url/add-file?filename=test.gif&id=4EDAF0D1&mode=append&file=BASE64_URL_ENCODED`

URL builder: https://bear.app/xurl/add-file/
