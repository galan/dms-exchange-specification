# DMS Exchange Specification
Version 0.0.2 - released: 2014-09-01

# Specification

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

1. Software using dxs MUST provide a way to import AND export the desired content via the user-interface (website, desktop or mobile application). Additional the export/import SHOULD be provided by an API.
2. The import and export SHOULD be transfered over a secure encrypted connection (eg. HTTPS, SSH, SFTP, etc.)
3. The export MUST comply with the packaging specified in the section "Packaging"
4. It is REQUIRED that the metadata for the documents and export-archive complies to the respective JSON-Schema.



# Terminology
* **File** - A single file in the filesystem.
* **Page** - A single page of a document (eg. A4, Letter, custom, ..).
* **Document-file** - A file that is processable by a dms and contains at least one page.

    Document-files that can contain only one page are images (eg. jpg, tiff, png, ..) as well as some other formats such as txt (no sematical boundaries). Other document-files can contain more then one page (eg. pdf, doc, ppt, ..)
* **Document** - A document consists of at least one document-file, subsequent of at least one page.

    *Example 1*: A physical document with four pages have been scanned into four jpg files. The document consists of four document-files with four pages.

    *Example 2*: A physical document with four pages that have been scanned into one pdf file. The document consists of one document-file with four pages.

    *Example 3*: A physical document with four double-sided pages have been scanned into four pdf files. Each document-files contains two pages. The document consists of four document-files with eight pages.
* **Container** - A container consists of the document and the associated metadata. It groups the document-files and the metadata either into a directory or a zip-archive.

    The container itself will be added to the export-archive.
* **Export-archive** - The final zip file that contains all containers and is used by the end-user to transport/export/backup his documents.


# Packaging
A dms typically stores large amounts of documents, exporting these as individual files is obviously not the most efficent option. To simplify the transfer, storage and access - zip is choosen as archive format. It provides open implementations and tools for practically all plaforms and languages.

Metadata is written to simple json-files. Json is well known and has wide tool-support for all languages and plattforms. To validate and ensure correctness, json-schema is used.

The following section describes how the archive is structured and the containers are stored in the exported archive.

## Containers

### Structure
A container is just a directory with the document-files and the related metadata. This directory can be optionally compressed into a single zip-archive. The name of the directory or zip-archive has no relevance. Containers can not be nested - a directory of a container can not have subdirectories with other containers or other data.

Structure:
````
container-directory
|-- document-files
|-- metadata
````

Example:
````
0001
|-- first.jpg
|-- second.jpg
|-- meta.json
````

### Document-metadata
The following JSON-Schema represents the metadata specification, any `meta.json`  has to validate against it.

The following JSON-Schema represents the document-metadata specification. The file has to be named `meta.json` and must be placed in the root of the container along with the document-files. If the `meta.json` is invalid, the container is invalid as well.

See `meta.schema.json` on [GitHub](https://github.com/galan/dms-exchange-specification/blob/master/spec/0.0.2/meta.schema.json) or [Raw](https://raw.githubusercontent.com/galan/dms-exchange-specification/master/spec/0.0.2/meta.schema.json).


## Export-archive

### Structure
The export-archive contains the export-metadata and all containers. The containers can be put into subdirectories for easier structuring and lowering the amount of files/directories inside a single directory. The subdirectory-names have no relevance for the import.

Structure:
````
export-archive
|-- export metadata
|-- folders
    |-- containers
````

Example:
````
export-archive.zip
|-- export.json
|-- 0001
|   |-- doc-01.zip
|   |-- doc-02.zip
|-- 0002
    |-- 0002-0001
        |-- doc-03.zip
        |-- doc-04
            |-- document.pdf
            |-- meta.json
````

### Export-archive metadata
The following JSON-Schema represents the archive-metadata specification. The file has to be named `export.json` and must be placed in the root of the export-archive. If no valid `export.json` exists, the export-archive is invalid.

See `export.schema.json` on [GitHub](https://github.com/galan/dms-exchange-specification/blob/master/spec/0.0.2/export.schema.json) or [Raw](https://raw.githubusercontent.com/galan/dms-exchange-specification/master/spec/0.0.2/export.schema.json).
