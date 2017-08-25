# Couchbase Cloud Test Drive Manual

## Introduction
The objective of this Test Drive is to familiarize you with the basics of Couchbase Server and Couchbase Sync Gateway. You will go through four exercises using this test drive:

1. Couchbase basics
    * You will log into the Couchbase Console (the web-based UI that comes with Couchbase Server)
    * Next, you will load the "travel-sample" bucket, a predefined data set that's great for experimentation.
    * Finally, you will log into the Sync Gateway console briefly.
2. Key/value document storage
    * You will use the Couchbase Console to add documents to a bucket.
    * Next, you will locate documents using the key
    * After that, you will learn how to edit documents.
    * Finally, you will see how to delete a document.
3. Querying with N1QL
    * You will use the Couchbase Console to view indexes on the "travel-sample" bucket.
    * Next, you will execute SELECT queries in the Query Workbench.
    * Finally, you will see how to use data modification queries like INSERT, UPDATE, and DELETE.
4. Full text search (FTS)
    * You will use the Couchbase Console to create a full text search index
    * After creating the index, you will execute the FTS using search terms

## About Couchbase

Couchbase’s mission is to be the data platform that revolutionizes digital innovation. To make this possible, Couchbase created the world’s first Engagement Database. Built on the most powerful NoSQL technology, the open source Couchbase Data Platform includes Couchbase Server and Couchbase Mobile. The platform provides unmatched agility and manageability – as well as unparalleled performance at any scale – to deliver ever-richer and ever more personalized customer experiences.

Couchbase customers include industry leaders like AOL, Amadeus, AT&T, Cisco, Comcast, Concur, Disney, Dixons Carphone, eBay, General Electric, Marriott, Neiman Marcus, Ryanair, Rakuten/Viber, Tesco, Verizon, Wells Fargo, as well as hundreds of other household names.

## Prerequisites

The only prerequisite is a web browser and an Azure account.

In this test drive, a 3 node cluster of Couchbase Server and 1 instance of Couchbase Sync Gateway will be at your disposal.

Note that Sync Gateway is only briefly touched on in this test drive. For more information on using Sync Gateway and the entire Couchbase Mobile solution, please contact [partners@couchbase.com](mailto:partners@couchbase.com) or [sales@couchbase.com](mailto:sales@couchbase.com) to plan a proof-of-concept.

## Guide to using this test drive

The first lab needs to be done first. After that, you can elect to work on any of the remaining 3 labs in any order.

Code snippets and fields in JSON document will appear as a monospaced font, like this: <code>SELECT t.* FROM \`travel-sample\` t</code>

## What you will learn from this Test Drive

You will learn the basics of using Couchbase Server in this test drive.

After you complete these labs, we invite you to [visit the Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/couchbase.couchbase-enterprise) to spin up your own Couchbase Server cluster.

Couchbase Server offers a full enterprise document database solution. A performant key/value database engine with a memory-first architecture is at the core of Couchbase Server. The Couchbase Console provides a built-in UI that makes it easy to perform ops and dev tasks out of the box. Couchbase Server also has robust querying options to match your use cases, including [N1QL (which is SQL for JSON)](https://www.couchbase.com/products/n1ql) and a language aware full text search engine built on the [Bleve](http://www.blevesearch.com/) engine. After completing these labs, you will understand how to use all these components.

In addition, you will also be taking a very shallow look at Sync Gateway, which is part of the [Couchbase Mobile platform](https://www.couchbase.com/products/mobile).

## Test Drive Support

If you have any questions or problems with this test drive, please contact [partners@couchbase.com](mailto:partners@couchbase.com) or [sales@couchbase.com](mailto:sales@couchbase.com).

## Labs

Great.  Now, we're ready to get started!  Here are the labs we'll work through in order:

* [1 - Logging into Couchbase](1%20-%20Logging%20into%20Couchbase.md)
* [2 - Key Value Document Storage](2%20-%20Key%20Value%20Document%20Storage.md)
* [3 - Querying with N1QL](3%20-%20Querying%20with%20N1QL.md)
* [4 - Full Text Search](4%20-%20Full%20Text%20Search.md)

##	Key Takeaways/Summary

**Couchbase basics**: Getting a cluster up and running in Azure. The Couchbase Console makes it easy to view your cluster and perform both dev and ops tasks.

**Key/value document storage**: Documents are keys and JSON values. You can add them quickly, find them by key, make changes by key (as long as it's valid JSON), and delete them by key.

**Querying with N1QL**: Key/value operations will always be the fastest, but querying multiple documents is easy with N1QL (which is just like SQL).

**Full Text Search**: A language-aware search for text is another way to locate document(s). You can leverage the many full text search options to help your users find the information they want.

##	Conclusions and Next Steps

If you're ready, please [visit the Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/couchbase.couchbase-enterprise) to spin up your own Couchbase Server cluster. If you'd like to do more research, please check out the [Couchbase Developer portal](https://developer.couchbase.com). If you'd like to try running Couchbase on your own machine, you can [download Couchbase Server Enterprise or Couchbase Server Community](https://www.couchbase.com/downloads) for free. It can run on Windows, Mac, and a variety of popular Linux distros.

##	Contact Information

If you have any questions about Couchbase Server or Couchbase Mobile, please contact [partners@couchbase.com](mailto:partners@couchbase.com) or [sales@couchbase.com](mailto:sales@couchbase.com).
