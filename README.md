# Summary
The goal of the dms-exchange-specification (dxs for short) is to define a standard, language- and vendor-agnostic format to exchange data between document-management-systems, or create decentral offline-backups. Creating and reading of the format is designed to be simple and straight-forward.

To provide feedback, please [open an issue on github](https://github.com/galan/dms-exchange-specification/issues) or create a pull request.

# Specification

* [Current version (1.0.0)](https://github.com/galan/dms-exchange-specification/blob/master/spec/1.0.0/dms-exchange-specification-1.0.0.md)
* [Change History](https://github.com/galan/dms-exchange-specification/blob/master/common/changehistory.md)

# Introduction
The world of document-management-systems (dms for short) - for decades experts have preached the paperless office. Once scanned and indexed, tagged and categorized, linked and rotated, sorted and archived,.. finally a document is just a few clicks away. So basically in order to make the paperless office a reality, a lot of hard and often dull work has to be invested. It doesn't matter if you're doing this as an individual or as a company, you choose your dms and start the process. The benefits of a maintained dms are obvious, but there is one point that prevents the industry imho from a lift-off: Interoperability.

Without the ability to switch the underlying dms, you're stuck - a classical vendor lock-in.
Switching a dms isn't always a voluntary choice, from time to time cloud-based dms-provider stop working (last seen on doctape.com, doo.com and organize.me), stop developing their products, or might become the inappropriate tool for your process.
There is no standard to export the indexed documents. Nearly all dms vendors provide the ability to download single documents (via the user-interface), some provide an API, just a few provide more. But in common there is something missing that makes the dms valuable - the metadata. Only exporting your documents will left you to have to start the indexing process all over again, beside the huge time effort you won't be able to get it 100% equal again.

To create a full export of all your documents along with the metadata (the main reason to use a dms and not the plain filesystem) a standard is required, vendors and users can rely on. This is the purpose of this specification. In the first revision the core-features of a dms will be covered, these are documents and metadata. Vendor-specific parts are intentionally left out, such as workflows, usermanagement and constraints, etc.. Everyone is welcome to contribute input, ideas, proofreading, etc. in order to create a (hopefully) industry-wide standard that revives the tool-landscape.

For feedback, please [open an issue on github](https://github.com/galan/dms-exchange-specification/issues) or create a pull request.

# Further reading
* [FAQ](https://github.com/galan/dms-exchange-specification/blob/master/common/faq.md)
* [Reference](https://github.com/galan/dms-exchange-specification/blob/master/common/reference.md)
* [About](https://github.com/galan/dms-exchange-specification/blob/master/common/about.md)

