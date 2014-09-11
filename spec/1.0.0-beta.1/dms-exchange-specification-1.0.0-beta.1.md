# DMS Exchange Specification
Version 1.0.0-beta.1 - released: in development

###Table of content
* [1. Specification](#1-specification)
* [2. Terminology](#2-terminology)
* [3. Packaging](#3-packaging)
  * [3.1 Containers](#31-containers)
  * [3.2 Export-archive](#32-export-archive)
* [4. Licence](#4-licence)



# 1. Specification

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

1. Software using dxs MUST provide a way to import AND export the desired content via the user-interface (website, desktop or mobile application). Additional the export/import SHOULD be provided by an API.
2. The import and export SHOULD be transfered over a secure encrypted connection (eg. HTTPS, SSH, SFTP, etc.)
3. The export MUST comply with the packaging specified in the section "Packaging"
4. It is REQUIRED that the metadata for the documents and export-archive complies to the respective [JSON-Schema](http://json-schema.org/).



# 2. Terminology
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



# 3. Packaging
A dms typically stores large amounts of documents, exporting these as individual files is obviously not the most efficent option. To simplify the transfer, storage and access - zip is choosen as archive format. It provides open implementations and tools for practically all plaforms and languages.

Metadata is written to simple json-files. Json is well known and has wide tool-support for all languages and plattforms. To validate and ensure correctness, json-schema is used.

The following section describes how the archive is structured and the containers are stored in the exported archive.

## 3.1 Containers

### 3.1.1 Structure
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

### 3.1.2 Document-metadata
The document-metadata file MUST be named `meta.json` and MUST be placed in the root of the container along with the document-files. The structure of the document-metadata is specified by a [JSON-Schema](http://json-schema.org/), and the `meta.json` file MUST validate against it. If `meta.json` is invalid, the container is invalid as well. Invalid containers MAY be ignored during the import, in this case the invalid containers MUST be listed to the user, so he can take appropriate steps.

See `meta.schema.json` on [GitHub](https://github.com/galan/dms-exchange-specification/blob/master/spec/0.0.2/meta.schema.json) or [Raw](https://raw.githubusercontent.com/galan/dms-exchange-specification/master/spec/0.0.2/meta.schema.json).


## 3.2 Export-archive

### 3.2.1 Structure
The export-archive contains the export-metadata and all containers. The containers MUST be put into subdirectories for easier structuring and lowering the amount of files/directories inside a single directory. Directories MAY be nested. The subdirectory-names have no relevance for the import.

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
    |-- 0002-0002
        |-- invoice-2014.zip
        |-- doc-05
            |-- other.pdf
            |-- meta.json
````

### 3.2.2 Splitting
Depending on the amount of the documents and the limits of the system an export-archive can become to large to fit all containers into a single zip-archive. In this case the containers MAY be distributed over multiple export-archives. Each export-archives MUST contain the export-archive metadata. A container SHALL NOT be splitted or be placed into multiple export-archives. The extension of the different export-archives keeps `.zip`.

### 3.2.3 Export-archive metadata
The following [JSON-Schema](http://json-schema.org/) represents the archive-metadata specification. The file MUST be named `export.json` and MUST be placed in the root of the export-archive. If no valid `export.json` exists, the export-archive is invalid.

A dms typically do not provide all options, therefore only fields MUST be set that are available to the specific dms. Eg. if the source dms does support labels but the target does not, the information is exported, but lost during import. It is the responsibility of the user selecting the dms, this goes along with the selection of the available features - or their absence.

See `export.schema.json` on [GitHub](https://github.com/galan/dms-exchange-specification/blob/master/spec/0.0.2/export.schema.json) or [Raw](https://raw.githubusercontent.com/galan/dms-exchange-specification/master/spec/0.0.2/export.schema.json).

### 3.2.4 MIME Type
The export-archive is a zip, therefore the MIME Type MUST be `application/zip`.



# 4. Licence
The specification itself is released under the [Creative Commons - CC BY-SA 4.0](http://creativecommons.org/licenses/by-sa/4.0/)
