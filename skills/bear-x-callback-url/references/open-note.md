# /open-note

Open a note by title or id and return its content.

## Parameters

- `id` (optional): Note identifier.
- `title` (optional): Note title.
- `header` (optional): Header inside the note to focus.
- `exclude_trashed` (optional): If `yes`, exclude trashed notes.
- `new_window` (optional, macOS only): If `yes`, open in an external window.
- `float` (optional, macOS only): If `yes`, float the external window on top.
- `show_window` (optional, macOS only): If `no`, do not force the main window to open.
- `open_note` (optional): If `no`, do not display the note in main or external window.
- `selected` (optional): If `yes`, return the currently selected note (token required; see [token-generation.md](token-generation.md)).
- `pin` (optional): If `yes`, pin the note to the top of the list.
- `edit` (optional): If `yes`, place the cursor inside the editor.
- `search` (optional): Open the in-note find/replace panel with this text.

## x-success

- `note`: Note text.
- `identifier`: Note identifier.
- `title`: Note title.
- `tags`: Tags array.
- `is_trashed`: `yes` if the note is trashed.
- `modificationDate`: ISO 8601.
- `creationDate`: ISO 8601.

## Example

`bear://x-callback-url/open-note?id=7E4B681B`

URL builder: https://bear.app/xurl/open-note/
