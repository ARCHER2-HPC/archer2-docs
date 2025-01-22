# Globus

Globus is a service that enables high-performance and convenient data transfer and sharing of large amounts of data.

- [Globus website](https://www.globus.org/)
- [Globus documentation and tutorials](https://docs.globus.org/guides/)

## Using Globus on ARCHER2

The ARCHER2 filesystems have a Globus Collection (formerly known as an endpoint) with the name "Archer2 file systems"
A full step-by-step guide on transferring data to/from ARCHER2 is provided below

## Step-by-step example

### Setting up ARCHER2 filesystems

Navigate to [https://app.globus.org](https://app.globus.org)

Log in with your Globus identity (this could be a globusid.org or other identity)

![Globus web app data transfer interface at login](../images/globus/1-login.jpg)


In File Manager, use the search tool to search for “Archer2 file systems”. Select it.

![Using the app to locate the ARCHER2 collection](../images/globus/2-archer2-search.jpg)

In the transfer pane, you are told that Authentication/Consent is required. Click Continue.

![Continuing to the authetication step](../images/globus/3-continue.jpg)

![Interface required SAFE login](../images/globus/4-link-archer2.jpg)

Click on the ARCHER2 Safe (safe.epcc.ed.ac.uk) link

![Login to ARCHER2 SAFE and link account](../images/globus/5-safe.jpg)

Select the correct User account (if you have more than one)

Click Accept

![Return to Globus web app](../images/globus/6-continue.jpg)

Now confirm your Globus credentials – click Continue

![Linking credentials at Globus web app](../images/globus/7-identity.jpg)

Click on the SAFE ID you selected previously

![Select the correct user account in SAFE](../images/globus/8-safe.jpg)

Make sure the correct User account is selected and Accept again

Your ARCHER2 /home directory will be shown 

![Web app interface showing ARCHER2 home directory](../images/globus/9-home.jpg)

You can switch to viewing e.g. your /work directory by editing the path, or using the "up one folder" and selecting folders to move down the tree, as required

![Web app interface showing ARCHER2 work directory](../images/globus/9-work.jpg)


### Setting up the other end of the transfer

![Illustration of two-panel view in web interface](../images/globus/10a-two-pane.jpg)

Make sure you select two-panel view mode

#### Laptop

![Illustrating search option in right panel](../images/globus/10-destination.jpg)

If you wish to transfer data to/from your personal laptop or other device, click on the Collection Search in the right-hand panel

![Illustrating connection to laptop step](../images/globus/11-globus-connect-personal.jpg)

Use the link to “Get Globus Connect Personal” to create a Collection for your local drive.

![Interface showing local laptop view](../images/globus/12-to-laptop.jpg)

#### Other server e.g. JASMIN 

If you wish to connect to another server, you will need to search for the Collection e.g. JASMIN Default Collection and authenticate

Please see the [JASMIN Globus page for more information](https://help.jasmin.ac.uk/docs/data-transfer/globus-transfers-with-jasmin/)

![Illustration of connecting to JASMIN Collection](../images/globus/13-jasmin.jpg)

### Setting up and initiating the transfer

Once you are connected to both the Source and Destination Collections, you can use the File Manager to select the files to be transferred, and then click the Start button to initiate the transfer

![Selecting data you want to transfer](../images/globus/14-transfer.jpg)

A pop-up will appear once the Transfer request has been submitted successfully

Clicking on the “View Details” will show the progress and final status of the transfer

![Progress indicator](../images/globus/15-result.jpg)

![Interface once data transfer is complete](../images/globus/16-transferred.jpg)

### Using a different ARCHER2 account

If you want to use Globus with a different account on ARCHER2, you will have to go to Settings

![Settings to change ARCHER2 account](../images/globus/17-settings.jpg)

Manage Identities

![Multiple identities view](../images/globus/18-unlink.jpg)

And Unlink the current ARCHER2 safe identity, then repeat the link process with the other ARCHER2 account

## Globus Command Line Interface (CLI)

The Globus CLI is also available on ARCHER2 for transferring data. You can make it available with

```
module load globus-cli
```

For more information on use, see the [Globus CLI documentation](https://docs.globus.org/cli/).