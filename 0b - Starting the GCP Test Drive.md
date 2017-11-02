# Lab 0b - Starting the GCP Test Drive

These are step by step instruction to start the Couchbase Test Drive on Google Cloud Platform.  If you have found this manual from the Test Drive itself you may have already completed these steps.  

First off, navigate to the test drive [here](https://couchbase.orbitera.com/c2m/trial/1062).

![Start test drive](/images/0b/0.png)

If you don't have an account, you can create a login by filling in the form.

![Start test drive](/images/0b/1.png)

When complete you should see this page.

![Start test drive](/images/0b/2.png)

Now check your email.  It will have an email from Orbitera with a link to confirm your Test Drive account.

![Start test drive](/images/0b/3.png)

Click that link and you will be redirected to a confirmation page.

![Start test drive](/images/0b/4.png)

You can now sign in with your new credentials.

![Start test drive](/images/0b/5.png)

Now you're logged in on the Test Drive screen.  Click Launch Test Drive.

![Start test drive](/images/0b/6.png)

The framework will show the launch in progress.  This takes a few minutes.

What Orbitera is doing:
* Deploying 3 nodes of Couchbase Server on 3 GCE VMs
* Setting up each node
* Creating a cluster, and adding all 3 nodes to the cluster
* Creating administrator credentials
* Deploying 1 node of Sync Gateway on a GCE VM.

When you use the full GCP Cloud Launcher, it will do all these operations for you as well. However, you will have more control over the deployment. You can configure details like how many nodes, what type of VMs to use, the credentials, and more.

![Start test drive](/images/0b/7.png)

When complete the screen will redirect and show your cluster URL, along with credentials.  You should also receive an email with this information stating that your test drive is ready.

![Start test drive](/images/0b/8.png)

This includes:

* Server Admin URL (where you'll go in your browser to login to the Couchbase Server Console)
* Admin Credentials (how you'll login to the Console)
* Sync Gateway Admin URL (where you'll go in your browser to view the Sync Gateway console)

You may want to bookmark these URLs and make a note of the access information, since you will be using it throughout the remaining labs.

Great!  Now you're all set to get started with [Lab 1](1%20-%20Logging%20into%20Couchbase.md).
