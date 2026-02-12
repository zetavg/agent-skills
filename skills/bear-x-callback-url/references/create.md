# /create

Create a new note and return its identifier. Empty notes are not allowed.

## Parameters

- `title` (optional): Note title.
- `text` (optional): Note body.
- `clipboard` (optional): If `yes`, use clipboard text.
- `tags` (optional): Comma-separated list of tags.
- `file` (optional): Base64 representation of a file (must be URL-encoded).
- `filename` (optional): File name with extension. Both `file` and `filename` are required to add a file.
- `open_note` (optional): If `no`, do not display the note in main or external window.
- `new_window` (optional, macOS only): If `yes`, open in an external window.
- `float` (optional, macOS only): If `yes`, float the external window on top.
- `show_window` (optional, macOS only): If `no`, do not force the main window to open.
- `pin` (optional): If `yes`, pin the note to the top of the list.
- `edit` (optional): If `yes`, place the cursor inside the editor.
- `timestamp` (optional): If `yes`, prepend current date/time to the text.
- `type` (optional): If `html`, convert `text` from HTML to Markdown.
- `url` (optional): If `type=html`, resolve relative image links using this URL.

## x-success

- `identifier`: Note identifier.
- `title`: Note title.

## Example

`bear://x-callback-url/create?title=My%20Note%20Title&text=First%20line&tags=home,home%2Fgroceries`

URL builder: https://bear.app/xurl/create/
