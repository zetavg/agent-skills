# /add-text

Append or prepend text to a note by title or id. Encrypted notes cannot be accessed with this action.

## Parameters

- `id` (optional): Note identifier.
- `title` (optional): Note title.
- `selected` (optional): If `yes`, use the currently selected note (token required; see [references/token-generation.md](references/token-generation.md)).
- `text` (optional): Text to add.
- `clipboard` (optional): If `yes`, use clipboard text.
- `header` (optional): Add text to the matching header inside the note.
- `mode` (optional): `prepend`, `append`, `replace_all`, or `replace` (keeps the note title untouched).
- `new_line` (optional): If `yes` and `mode=append`, force the text onto a new line.
- `tags` (optional): Comma-separated list of tags.
- `exclude_trashed` (optional): If `yes`, exclude trashed notes.
- `open_note` (optional): If `no`, do not display the note in main or external window.
- `new_window` (optional, macOS only): If `yes`, open in an external window.
- `show_window` (optional, macOS only): If `no`, do not force the main window to open.
- `edit` (optional): If `yes`, place the cursor inside the editor.
- `timestamp` (optional): If `yes`, prepend current date/time to the text.

## x-success

- `note`: Note text.
- `title`: Note title.

## Example

`bear://x-callback-url/add-text?text=new%20line&id=4EDAF0D1&mode=append`

URL builder: https://bear.app/xurl/add-text/
