# /search

Show search results for all notes or a specific tag.

## Parameters

- `term` (optional): Search string.
- `tag` (optional): Tag to search within.
- `show_window` (optional, macOS only): If `no`, do not force the main window to open.
- `token` (optional): Application token. If not provided, nothing is returned. See [references/token-generation.md](references/token-generation.md).

## x-success

- `notes`: JSON array of notes: `[{ title, identifier, [tag, ...], modificationDate, creationDate, pin }, ...]`.

## Notes

- Encrypted notes are excluded.

## Example

`bear://x-callback-url/search?term=nemo&tag=movies`

URL builder: https://bear.app/xurl/search/
