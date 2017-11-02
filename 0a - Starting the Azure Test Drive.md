# Lab 0a - Starting the Azure Test Drive

These are step by step instruction to start the Couchbase Test Drive on Azure.  If you have found this manual from the Test Drive itself you may have already completed these steps.  

First off, navigate to the [Couchbase Enterprise on Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/couchbase.couchbase-enterprise). Then click the 'Test Drive' button.

![Start test drive](/images/0a/0101-start-test-drive.png)

You will be prompted for additional information, and you will be asked to agree to the terms of service. Click "Done" to proceed.

![Terms of service](/images/0a/0102-tos.png)

Next, Azure will start deploying the test drive. This should only take a few minutes.

![Deploying](/images/0a/0103-deploying.gif)

What Azure is doing:
* Deploying 3 "nodes" of Couchbase Server on 3 Azure VMs
* Setting up each node
* Creating a cluster, and adding all 3 nodes to the cluster
* Creating administrator credentials
* Deploying 1 node of Sync Gateway on an Azure VM.

When you use the real Azure Marketplace, it will do all these operations for you as well. However, you will have more control over the deployment. You can configure details like how many nodes, what type of VMs to use, the credentials, and more.

When the test drive is done deploying, you'll see "Access information" displayed.

![Access information](/images/0a/0104-access-information.png)

This includes:

* Server Admin URL (where you'll go in your browser to login to the Couchbase Server Console)
* Admin Credentials (how you'll login to the Console)
* Sync Gateway Admin URL (where you'll go in your browser to view the Sync Gateway console)

You may want to bookmark these URLs and make a note of the access information, since you will be using it throughout the remaining labs.

Great!  Now you're all set to get started with [Lab 1](1%20-%20Logging%20into%20Couchbase.md).
