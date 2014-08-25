# DMS Exchange Specification
Version 0.0.1 - released: not yet released

# Specification

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

1. Software using dxs MUST provide a way to import AND export the desired content via the user-interface (website, desktop or mobile application). Additional the export/import SHOULD be provided by an API.
2. The import or export SHOULD be transfered over a secure encrypted connection, eg. HTTPS, SSH, SFTP, etc.
3. The export MUST comply with the packaging specified in the section "Packaging"
4. It is REQUIRED that the metadata is given in the JSON format, and validate against the JSON-Schema specified in the section "Reference"



# Terminology

## File
A single file in the filesystem.

## Page
A single page of a document (eg. A4, Letter, custom, ..).

## Document-file
A file that is processable by a dms and contains at least one page.

Document-files that can contain only one page are images (eg. jpg, tiff, png, ..) as well as some other formats such as txt (no sematical boundaries). Other document-files can contain more then one page (eg. pdf, doc, ppt, ..)

## Document
A document consists of at least one document-file, subsequent of at least one page.

Example 1: A physical document with four pages have been scanned into four jpg files. The document consists of four document-files with four pages.

Example 2: A physical document with four pages that have been scanned into one pdf file. The document consists of one document-file with four pages.

Example 3: A physical document with four double-sided pages have been scanned into four pdf files. Each document-files contains two pages. The document consists of four document-files with eight pages.

## Container
A container consists of the document and the associated metadata. It groups the document-files and the metadata either into a directory or a zip-archive.

The container itself will be added to the export-archive.

## Export-archive
The final zip file that contains all containers and is used by the end-user to transport/export/backup his documents.



# Packaging
A dms typically stores large amounts of documents, exporting these as individual files is obviously not the most efficent option. To simplify the transfer, storage and access - zip is choosen as archive format. It provides open implementations and tools for practically all plaforms and languages.

Metadata is written to simple json-files. Json is well known and has wide tool-support for all languages and plattforms. To validate and ensure correctness, json-schema is used.


The following section describes how the archive is structured and the containers are stored in the exported archive.

## Containers
A container is just a directory with the document-files and the metadata. This directory can be optionally compressed into a single zip-archive. The name of the directory or zip-archive has no relevance.

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

## Document-metadata
The following JSON-Schema represents the metadata specification, any `meta.json`  has to validate against it.

The following JSON-Schema represents the document-metadata specification. The file has to be named `meta.json` and must be placed in the root of the container along with the document-files. If the `meta.json` is invalid, the container is invalid as well.

```javascript
{
	"$schema": "http://json-schema.org/draft-04/schema#",
	"title": "dms-exchange-specification (dxs) metadata description",
	"description": "This schema defines the metadata for a logical document as specified by the dms-exchange-specification.",
	"type": "object",
	"properties": {
		"version": {
			"description": "Valid version for this schema.",
			"enum": ["0.1"]
		},
		"documents": {
			"description": "Groups the files that form a logical document.",
			"type": "array",
			"minItems": 1,
			"items": {
				"type": "object",
				"properties": {
					"filename": {
						"description": "Original name of the indexed file.",
						"type": "string"
					},
					"scannedBy": {
						"description": "User who has scanned the file.",
						"type": "string",
						"format": "email"
					},
					"importedBy": {
						"description": "User who has indexed the file",
						"type": "string",
						"format": "email"
					},
					"tsUpload": {
						"description": "Timestamp when the file has been added to the dms",
						"type": "string",
						"format": "date-time"
					},
					"tsUpdate": {
						"description": "Timestamp when the document has been changed the last time",
						"type": "string",
						"format": "date-time"
					},
					"rotation": {
						"description": "Degree the document has to be rotated clockwise to have a correct orientation.",
						"enum": [0, 90, 180, 270],
						"default": 0
					}
				},
				"required": ["filename"]
			}
		},
		"tsDocument": {
			"type": "string",
			"format": "date-time"
		},
		"note": {
			"description": "Free text field for document-notes, made by the users",
			"type": "string"
		},
		"location": {
			"description": "Physical location of the scanned documents, if available",
			"type": "string"
		},
		"userId": {
			"description": "An id the user can define uniquely for the document",
			"type": "string"
		},
		"systemId": {
			"description": "An id the system has provided for the document",
			"type": "string"
		},
		"directory": {
			"description": "If the dms works hierarchically, this field contains the directory. It is written in *nix-style, starting from the root downwards, using slashes.",
			"type": "string"
		},
		"labels": {
			"description": "Tagging keywords",
			"type": "array",
			"items": {
				"type": "string"
			},
			"uniqueItems": true
		},
		"optionIndexed": {
			"description": "Should the document be shown in the index (if using a search), or only when the user navigates to the document explicitly. Defaults to true.",
			"type": "boolean"
		},
		"optionOcr": {
			"description": "Should the document be analysed or not. Defaults to true.",
			"type": "boolean"
		}
	},
	"required": ["version", "documents"]
}
```


## Export-archive
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

## Export-archive metadata
The following JSON-Schema represents the archive-metadata specification. The file has to be named `export.json` and must be placed in the root of the export-archive. If no valid `export.json` exists, the export-archive is invalid.

```javascript
{
	"$schema": "http://json-schema.org/draft-04/schema#",
	"title": "dms-exchange-specification (dxs) archive metadata description",
	"description": "This schema defines metadata for the export",
	"type": "object",
	"properties": {
		"version": {
			"description": "Valid version for this export and containers. All metadata files from the containers (meta.json) have to match this version, otherwise the container is invalid.",
			"enum": ["0.1"]
		},
		"description": {
		    "description": "Free-text field for additional information",
		    "type": "string"
		}
		"exportBy": {
			"description": "User who has triggered the export.",
			"type": "string",
			"format": "email"
		}
		"tsExport": {
			"description": "Timestamp when the export was performed.",
			"type": "string",
			"format": "date-time"
		},
		"numberOfDocuments": {
		    "description": "The number of documents in this archive. Optional, since depending on the export-process this might not be known in advance.",
		    "type": "integer",
		    "minimum": 0
		}
    },
    "required": ["version"]
}
```
