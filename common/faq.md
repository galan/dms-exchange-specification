# FAQ

## How should I provide the archive to the user?
That's up to the dms provider. Some suggestions/examples how it could work:
* A simple download-link
* An option to  issue an export, which creates a background-job that processes the documents (large amounts of documents might take a while), and notifies the user when the download is ready.

## How should the data be imported?
This is highly dependent on the dms. It contains of the following generics steps:
1. Read and validate the `export.json`
2. Iterate recursivly over all directories
   1. If a `meta.json` file exists read it and import the documents. Travese directory up and continue (don't recurse inside containers).
   1. If a zip-file exists, read it, extract the `meta.json` and import the documents. Continue to traverse.
