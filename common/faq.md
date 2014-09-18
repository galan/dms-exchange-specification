# FAQ

## My dms does not provide all fields available in the JSON-Schema.
Most dms do not provide support for all fields, therefore nearly all fields are optional. If your dms does not provide support for eg. a "project" or a "directory" field - simply omit them in the ´meta.json`. During import you can ignore those fields not supported by your dms.

## How should I provide the archive to the user?
That's up to the dms provider. Some suggestions/examples how it could work:
* A simple download-link
* An option to  issue an export, which creates a background-job that processes the documents (large amounts of documents might take a while), and notifies the user when the download is ready.

## How should the data be imported?
This is highly dependent on the dms. It contains of the following simple generics steps:

1. Read and validate the `export.json`
2. Iterate recursivly over all directories
    1. If a `meta.json` file exists read it and import the documents. Travese directory up and continue (don't recurse inside containers).
    2. If a zip-file exists, read it, extract the `meta.json` and import the documents. Continue to traverse.

## Are there other exchange formats out there?
To my knowledge I found the following related formats (give me a hint if you know others):
* *[docTag](https://github.com/docTag)* - An initiative by mostly billing-related services that has been started and ceased in 2012. Provides some JSON-Schemas that are invalid (eg. 'date' is no valid format) and code-examples.
* *[ODMA (Open Document Management Alliance)](http://odma.info/)* - The ODMA defined APIs for desktop applications accessing dms server. As far I could see it is DLL based and regarding to Wikipedia superseded by other ways of accessing the data inside an dms, namly WebDAV. The website looks out-dated and seems not be maintained anymore.
