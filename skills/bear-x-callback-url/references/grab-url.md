# /grab-url

Create a new note with the content of a web page.

## Parameters

- `url` (required): URL to grab.
- `tags` (optional): Comma-separated list of tags. If tags are specified in Bear's web content preferences, this parameter is ignored.
- `pin` (optional): If `yes`, pin the note to the top of the list.
- `wait` (optional): If `no`, `x-success` is called immediately without `identifier` and `title`.

## x-success

- `identifier`: Note identifier.
- `title`: Note title.

## Example

`bear://x-callback-url/grab-url?url=https://bear.app`

URL builder: https://bear.app/xurl/grab-url/
