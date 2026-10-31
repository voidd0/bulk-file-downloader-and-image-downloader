# Bulk File Downloader and Image Downloader: export to CSV or JSON with Python, Node.js or curl

Download files in bulk from direct links, or every image, video, audio file, PDF, document or archive on a list of web pages. Each file is stored with a link you can open, plus its name, type, size, SHA-256 checksum, image width, height and alt text. Duplicates are found and not charged.

This repository has ready scripts for the [Bulk File Downloader and Image Downloader: Web Pages, PDFs](https://apify.com/v0iddo/bulk-file-downloader) Actor on Apify. The Actor does the
fetching, paging and cleaning; the scripts here run it with your input and save the results as a CSV or JSON file.

## Quick start

1. Create an Apify account and copy your API token from
   [Console > Settings > Integrations](https://console.apify.com/settings/integrations).
2. Install the client and run the script with the example input:

```bash
pip install "apify-client>=2"
export APIFY_TOKEN=your_token
python run.py input.json -o results.csv --max-items 50
```

Edit `input.json` for your own run. You can also run the Actor in the browser on its
[Apify page](https://apify.com/v0iddo/bulk-file-downloader), schedule it there, and connect its output to Google Sheets, Slack, Zapier or Make.

## Input

The example input (`input.json`):

```json
{
  "pageUrls": [
    "https://en.wikipedia.org/wiki/Hummingbird"
  ],
  "fileTypes": [
    "image"
  ],
  "maxFilesPerPage": 20,
  "maxItems": 20
}
```

| Field | Type | What it does |
| --- | --- | --- |
| `fileUrls` | array | Direct links to files of any type (images, video, audio, PDF, documents, archives). |
| `pageUrls` | array | Web pages to download files from: the images it shows (the largest size it offers), its video and audio, and the files it links to, filtered by File types. |
| `fileTypes` | array | Which files to take from Pages. |
| `sameSiteOnly` | boolean | Take only files on the host of the page or its subdomains, leaving out files on other hosts (many sites keep their images on a separate CDN host, so this can... |
| `maxFilesPerPage` | integer | Caps the files taken from one page, in the order they appear on it. |
| `minImageKB` | integer | Images found on pages that are smaller than this are left out (icons, spacers, tracking pixels) and not charged; 0 keeps every image. |
| `maxFileMB` | integer | Larger files come back as a row with the reason, at no charge. |
| `maxItems` | integer | The run stops after this many result rows. |

## Output

One row per result. A real row from a run of the example input (more in
[`examples/sample-output.json`](examples/sample-output.json)):

```json
{
  "kind": "file",
  "url": "https://thumb.wikimedia.org/wikipedia/commons/thumb/7/7c/Trinidad_and_Tobago_hummingbirds_composite.jpg/1280px-Trinidad_and_Tobago_hummingbirds_composite.jpg?utm_source=en.wikipedia.org&utm_campaign=index&utm_content=thumbnail",
  "pageUrl": "https://en.wikipedia.org/wiki/Hummingbird",
  "finalUrl": "https://thumb.wikimedia.org/wikipedia/commons/thumb/7/7c/Trinidad_and_Tobago_hummingbirds_composite.jpg/1280px-Trinidad_and_Tobago_hummingbirds_composite.jpg?utm_source=en.wikipedia.org&utm_campaign=index&utm_content=thumbnail",
  "foundIn": "og:image",
  "fileName": "Trinidad_and_Tobago_hummingbirds_composite.jpg",
  "fileType": "image",
  "contentType": "image/jpeg",
  "fileBytes": 292246,
  "sha256": "82cafb5947635e2a5d5244de379932a7697c3aeb0e66bec7ea9e5fde9f78d303",
  "imageWidth": 1280,
  "imageHeight": 1280,
  "altText": null,
  "fileUrl": "https://api.apify.com/v2/key-value-stores/W43OdOgQOAX8HzM9c/records/000001-Trinidad_and_Tobago_hummingbirds_composite.jpg?signature=1SnByMDZ3Sy1PkM75xgMY",
  "duplicateOf": null,
  "chargeUnits": 1,
  "error": null,
  "downloadedAt": "2026-10-31T02:40:46Z"
}
```

Fields: `kind`, `url`, `pageUrl`, `finalUrl`, `foundIn`, `fileName`, `fileType`, `contentType`, `fileBytes`, `sha256`, `imageWidth`, `imageHeight`, `altText`, `fileUrl`, `duplicateOf`, `chargeUnits`, `error`, `downloadedAt`.

## Node.js

```javascript
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: process.env.APIFY_TOKEN });
const run = await client.actor('v0iddo/bulk-file-downloader').call({"pageUrls": ["https://en.wikipedia.org/wiki/Hummingbird"], "fileTypes": ["image"], "maxFilesPerPage": 20, "maxItems": 20});
const { items } = await client.dataset(run.defaultDatasetId).listItems();
```

Full example: [`examples/node/run.mjs`](examples/node/run.mjs).

## Any language: one HTTP request

```bash
curl -X POST "https://api.apify.com/v2/acts/v0iddo~bulk-file-downloader/run-sync-get-dataset-items" \
  -H "Authorization: Bearer $APIFY_TOKEN" -H 'Content-Type: application/json' \
  -d '{"pageUrls": ["https://en.wikipedia.org/wiki/Hummingbird"], "fileTypes": ["image"], "maxFilesPerPage": 20, "maxItems": 20}'
```

The response is the JSON array of results.

## Cost

The Actor is paid per result: **$2.00 per 1,000 results**, nothing per run. `maxItems` caps the rows, and so the cost, of one run. A new Apify account gets $5 of platform credit every month.

## License

MIT for the code in this repository. Made by [vøiddo](https://voiddo.com).
