# DMS Exchange Specification
Version 1.0.0 - released: 26.02.2015

###Table of content
* [1. Specification](#1-specification)
* [2. Terminology](#2-terminology)
* [3. Packaging](#3-packaging)
  * [3.1 Containers](#31-containers)
  * [3.2 Export-archive](#32-export-archive)
* [4. Licence](#4-licence)



# 1. Specification
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

1. Software using the dms-exchange-specification MUST provide a way to import AND export the desired content via the user-interface (website, desktop or mobile application). Additional the export/import SHOULD be provided by an API.
2. Export-archives or single containers SHOULD be transfered over a secure encrypted connection (eg. HTTPS, SSH, SFTP, etc.)
4. Metadata for a document MUST validate against the provided [JSON-Schema](http://json-schema.org/) in the section "Document-metadata".
5. A document-container MUST comply with the packaging specified in the section "Containers"
6. An export-archive MUST comply with the packaging specified in the section "Export-archive"



# 2. Terminology
* **File** - A single file in the filesystem.
* **Page** - A single page of a document (eg. A4, Letter, custom, ..).
* **Document-file** - A file that is processable by a dms and contains at least one page.

    Document-files that can contain only one page are images (eg. jpg, tiff, png, ..) as well as some other formats such as txt (no semantical boundaries). Other document-files can contain more then one page (eg. pdf, doc, ppt, ..).
* **Revision** - Each document-file consists of at least one revision, each representing a state of the document-file, where each has a datetime associated to determine the order.
* **Document** - A document consists of at least one document-file, subsequent of at least one page.

    *Example 1*: A physical document with four pages have been scanned into four jpg files. The document consists of four document-files with four pages.

    *Example 2*: A physical document with four pages that have been scanned into one pdf file. The document consists of one document-file with four pages.

    *Example 3*: A physical document with four double-sided pages have been scanned into four pdf files. Each document-files contains two pages. The document consists of four document-files with eight pages.
* **Container** - A container consists of the document and the associated metadata. It groups the document-files and the metadata in a simple directory structure, which is put into an archive.

    The container can be used to exchange a single document when compressed, or by adding multiple uncompressed containers to an export-archive for larger exports.
* **Export-archive** - A gzip compressed stream of uncompressed containers that can be used to exchange (transport/export/backup) multiple document-containers.



# 3. Packaging
A dms typically stores large amounts of documents, exporting these as individual files without metadata is obviously not the most efficent option. The following section describes how a single document-container is structured, and how to combine them to create a container-stream for larger exports.

To simplify the transfer, storage and access - tar and gzip are choosen as archive format. They provides open implementations and tools for practically all plaforms and languages.

Document-metadata is written to simple json-files. Json is well known and has wide tool-support for all languages and plattforms. To validate and ensure correctness, json-schema is used.


## 3.1 Containers

### 3.1.1 Structure
A container is a directory with the metadata-file and a subdirectory containing the revisions of the document-files. The container-directory itself MUST be archived using tar and gzip. If the container is part of an export-archive, it MUST NOT be compressed using gzip, only archived using tar. The name of archive has no relevance. Containers MUST NOT be nested - a directory of a container can not have subdirectories with other containers or other data.

Structure:
````
<container>
|-- metadata
|-- revisions-directory
    |-- document-files with timestamp
````


### 3.1.2 Document-files
Each revision of a document-files MUST be placed in the `revisions` directory, which is placed in the root of the container. The document-file inside the `revisions` directory is composed by the added-timestamp (`addedTime`-property in UTC) and the filename (`filename`-property), separated by an underscore: `yyyyMMdd'T'HHmmss'Z'_<filename>`

A dms CAN provide an option to export only the latest revisons of the document-files. This can help reducing the size of the container/export-archive file.

The order of the document-files is defined by the sequence of the elements in the `documentFiles`-array in the metadata.

Example with two document-files that have a single revisions:
````
<container>
|-- meta.json
|-- revisions
   |-- 20140516T173112Z_first.jpg
   |-- 20140510T123210Z_second.jpg
````

Example with three document-files, where `first.jpg` has a single revision, `second.jpg` has two revision and `third.jpg` has three revisions:
````
<container>
|-- meta.json
|-- revisions
   |-- 20130102T091001Z_first.jpg
   |-- 20130612T085209Z_second.jpg
   |-- 20140310T123045Z_third.jpg
   |-- 20140516T173112Z_second.jpg
   |-- 20140609T103010Z_third.jpg
   |-- 20141013T131050Z_third.jpg
````

### 3.1.3 Document-metadata
The document-metadata file MUST be named `meta.json` and MUST be placed in the root of the container along with the directories that contain the document-files. The structure of the document-metadata is specified by a [JSON-Schema](http://json-schema.org/), and the `meta.json` file MUST validate against it. If `meta.json` is invalid, the container is invalid as well. Invalid containers MAY be ignored during the import, in this case the invalid containers MUST be listed to the user at the end of the process, so he can take appropriate steps.

A dms typically do not provide all options, therefore only fields MUST be set that are available to the specific dms. Eg. if the source dms does support labels but the target does not, the information is exported, but lost during import. It is the responsibility of the user selecting the dms, this goes along with the selection of the available features - or their absence.

See `meta.schema.json` on [GitHub](https://github.com/galan/dms-exchange-specification/blob/master/spec/1.0.0/meta.schema.json) or [Raw](https://raw.githubusercontent.com/galan/dms-exchange-specification/master/spec/1.0.0/meta.schema.json).


## 3.2 Export-archive
The export-archive is used to bundle and exchange multiple documents. It is basically a tar.gz file including the containers.

### 3.2.1 Structure
The documents and their metadata are added as a stream of containers, which are written to a tar file. The resulting archive MUST be compressed using gzip, resulting in the extension `.tar.gz` or `.tgz` (prefered).

The added containers MUST be archived using tar, without being compressed using gzip (this is only the case when a single container should be exchanged).

The containers MUST be spread into subdirectories for easier structuring and lowering the amount of files inside a single directory. The subdirectory-names have no relevance for the import. The following proposed directory-structure CAN be used, it consists of two subdirectories and a container with zero-left padded directory- and file names: `0000/0000/0000.tar`.

Structure:
````
<export-archive>
|-- folders
    |-- containers
````

Example of one export archive named `export-archive.tgz` with multiple containers:
````
export-archive.tgz
|-- 0000
    |-- 0000
    |   |-- 0000.tar
    |   |-- 0001.tar
    |   |-- 0002.tar
    |    ...
    |-- 0001
        |-- 0000.tar
        |-- 0001.tar
        |-- 0002.tar
````

### 3.2.2 Splitting
Depending on the amount of the documents and the limits of the system a single export-archive can become to large to fit all containers to export. In this case the export CAN be distributed over multiple archives. A container SHALL NOT be splitted or be placed into multiple export-archives. The extension of the different export-archives keeps `.tgz`.

### 3.2.3 MIME Type
The export-archive is a gzipped compressed tar, therefore the MIME Type MUST be `application/x-gtar`.

# 4. Licence
The specification itself is released under the [Creative Commons - CC BY-SA 4.0](http://creativecommons.org/licenses/by-sa/4.0/)

