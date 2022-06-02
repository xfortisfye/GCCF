#### Google Cloud Computing Foundations | C02 Infrastructure in Google Cloud
#### M04: Where Do I Store This Stuff?

# Lab 04-01: Cloud Storage: Qwik Start - CLI/SDK

## Overview 
Cloud Storage allows world-wide storage and retrieval of any amount of data at any time. You can use Cloud Storage for a range of scenarios including serving website content, storing data for archival and disaster recovery, or distributing large data objects to users via direct download.

- - - -
## Create a bucket
1. In the Cloud Console, go to Navigation menu > Cloud Storage > Browser. Click CREATE BUCKET

2. Name your bucket: Enter a unique name for your bucket.

3. Bucket naming rules:
* Do not include sensitive information in the bucket name, because the bucket namespace is global and publicly visible.
* Bucket names must contain only lowercase letters, numbers, dashes (-), underscores (_), and dots (.). Names containing dots require verification.
* Bucket names must start and end with a number or letter.
* Bucket names must contain 3 to 63 characters. Names containing dots can contain up to 222 characters, but each dot-separated component can be no longer than 63 characters.
* Bucket names cannot be represented as an IP address in dotted-decimal notation (for example, 192.168.5.4).
* Bucket names cannot begin with the "goog" prefix.
* Bucket names cannot contain "google" or close misspellings of "google".
* Also, for DNS compliance and future compatibility, you should not use underscores (_) or have a period adjacent to another period or dash. For example, ".." or "-." or ".-" are not valid in DNS names.

4. Click __CONTINUE__.

5. Location type: Multi-region

6. Location: us (multiple regions in United States)

7. Click __CONTINUE__.

8. Default Storage class: Standard

9. Click __CONTINUE__.

10. Uncheck _Enforce public access prevention on this bucket_ checkbox under Prevent public access.

11. Choose Fine-grained under Access Control.

12. Click __CONTINUE__.

13. Once you've gotten your bucket configured, click CREATE:

14. That's it — you've just created a Cloud Storage bucket!

> Note: If the bucket name is already taken, either by you or someone else, the command returns:
> 
> `Creating gs://YOUR-BUCKET-NAME/... ServiceException: 409 Bucket YOUR-BUCKET-NAME already exists.`
>
> Try again with a different bucket name.

----
## Test your understanding
1. Each bucket has a default storage class, which you can specify when you create your bucket.
- [x] True
- [ ] False
----

## Upload an object into your bucket
Now upload an object into a bucket.

First, download this image to a temporary instance (ada.jpg) in Cloud Shell:

```bash
curl https://upload.wikimedia.org/wikipedia/commons/thumb/a/a4/Ada_Lovelace_portrait.jpg/800px-Ada_Lovelace_portrait.jpg --output ada.jpg
```

Use the `gsutil cp` command to upload the image from the location where you saved it to the bucket you created:

```bash
gsutil cp ada.jpg gs://YOUR-BUCKET-NAME
```

> Tip: When typing your bucket name, you can use the tab key to autocomplete it.

You can see the image load into your bucket from the command line. You've just > stored an object in your bucket!

Now remove the downloaded image:

```bash
rm ada.jpg
```

## Download an object from your bucket
Use the gsutil cp command to download the image you stored in your bucket to Cloud Shell:

```bash
gsutil cp -r gs://YOUR-BUCKET-NAME/ada.jpg .
```

If successful, the command returns:

```bash
Copying gs://YOUR-BUCKET-NAME/ada.jpg...
/ [1 files][299.6 KiB/299.6 KiB]
Operation completed over 1 objects/299.6 KiB.
```

You've just downloaded the image from your bucket.

## Copy an object to a folder in the bucket
Use the `gsutil cp` command to create a folder called image-folder and copy the image (ada.jpg) into it:

```bash
gsutil cp gs://YOUR-BUCKET-NAME/ada.jpg gs://YOUR-BUCKET-NAME/image-folder/
```

> Note: Folders in Cloud Storage have limitations compared to local file systems, but many of the same operations are supported.

If successful, the command returns:

```bash
Copying gs://YOUR-BUCKET-NAME/ada.jpg [Content-Type=image/png]...
- [1 files] [ 299.6 KiB/ 299.6 KiB]
Operation completed over 1 objects/299.6 KiB
Now the image file has been copied into a new folder in your bucket.
```

## List contents of a bucket or folder
Use the `gsutil ls` command to list the contents of the bucket:

```bash
gsutil ls gs://YOUR-BUCKET-NAME
```

If successful, the command returns a message similar to:

```bash
gs://YOUR-BUCKET-NAME/ada.jpg
gs://YOUR-BUCKET-NAME/image-folder/
```

That's everything currently in your bucket.

## List details for an object
Use the `gsutil ls` command, with the `-l` flag to get some details about the image file you uploaded to your bucket:

```bash
gsutil ls -l gs://YOUR-BUCKET-NAME/ada.jpg
```

If successful, the command returns a message similar to:

```bash
306768  2017-12-26T16:07:570Z  gs://YOUR-BUCKET-NAME/ada.jpg
TOTAL: 1 objects, 30678 bytes (299.58 KiB)
```

Now you know the image's size and date of creation.

## Make your object publicly accessible
Use the `gsutil acl ch` command to grant all users read permission for the object stored in your bucket:

```bash
gsutil acl ch -u AllUsers:R gs://YOUR-BUCKET-NAME/ada.jpg
```
If successful, the command returns:

```bash
Updated ACL on gs://YOUR-BUCKET-NAME/ada.jpg
```

Your image is now public, and can be made available to anyone.

Validate that your image is publicly available. Go to Navigation menu > Cloud Storage, then click on the name of your bucket. You should see your image with the Public link box. Click the Copy URL and open the URL in a new browser tab.

> Who are you looking at? This is Ada Lovelace, credited with being the first computer programmer. She worked with mathematician and computer pioneer Charles Babbage, who proposed the Analytical Engine. Her interest in the Analytical Engine lead to translating a paper on the machine by Italian mathematician Luigi Menabrea, adding her own extensive annotations. These notes are considered the first computer program - an algorithm designed to be carried out by the machine. She developed a vision of the capability of computers, going beyond number crunching, and examined how individuals and society relate to technology as a collaborative tool. Citation: Ada Lovelace, https://commons.wikimedia.org/w/index.php?title=Ada_Lovelace&oldid=176490980 (last visited December 6, 2017).

----
## Test your understanding
1. An access control list (ACL) is a mechanism you can use to define who has access to your buckets and objects.
- [x] True
- [ ] False
----

## Remove public access
To remove this permission, use the command:

```bash
gsutil acl ch -d AllUsers gs://YOUR-BUCKET-NAME/ada.jpg
```
If successful, the command returns:

```bash
Updated ACL on gs://YOUR-BUCKET-NAME/ada.jpg
```

You have removed public access to this object. You can verify this by clicking the Refresh button in the Console. The checkmark will be removed.

----
## Test your understanding
1. You can stop publicly sharing an object by removing permission entry that have:
- [ ] By updating storage class
- [x] allUsers
- [ ] By removing project owner role
----

### Delete objects
Use the gsutil rm command to delete an object - the image file in your bucket:

```bash
gsutil rm gs://YOUR-BUCKET-NAME/ada.jpg
```
If successful, the command returns:

```bash
Removing gs://YOUR-BUCKET-NAME/ada.jpg...
```

Refresh the Console. The copy of the image file is no longer stored on Cloud Storage (though the copy you made in the image-folder/ folder still exists).

# Lab 04-02: Cloud SQL for MySQL: Qwik Start

## Overview 
In this lab you will learn how to create and connect to a Google Cloud SQL MySQL instance and perform basic SQL operations using the Cloud Console and the mysql client.

## Create a Cloud SQL instance
1. From the Navigation menu, click on SQL.

2. Click Create Instance.

3. Create your instance with the following settings:
* Click choose MySQL
* Type "myinstance" for Instance ID
* In the password field click on the Generate link and the eye icon to see the password. Save the password, you'll need it in the next section.
* Use the default values for the other fields.

4. Click Create Instance.
After a few minutes the instance is created and you can continue to the next section. If it seems to be taking a long time, refresh your browser.

----
## Test your understanding
1. Instance ID is used to uniquely identify your instance within the project.
- [x] True
- [ ] False
----

## Connect to your instance using the mysql client in the Cloud Shell
1. In the Cloud Console, click the Cloud Shell icon in the upper right corner.

2. Then click Continue.

3. At the Cloud Shell prompt, connect to your Cloud SQL instance by running the following:

```bash
gcloud sql connect myinstance --user=root
```

4. Click Authorize.

5. Enter your root password when prompted. Note: The cursor will not move.

6. Press the Enter key when you're done typing.

7. You should now see the `mysql` prompt.

## Create a database and upload data
1. Create a SQL database called guestbook on your Cloud SQL instance:

```sql
CREATE DATABASE guestbook;
```

2. Insert the following sample data into the guestbook database:

```sql
USE guestbook;
CREATE TABLE entries (guestName VARCHAR(255), content VARCHAR(255),
    entryID INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(entryID));
INSERT INTO entries (guestName, content) values ("first guest", "I got here!");
INSERT INTO entries (guestName, content) values ("second guest", "Me too!");
```

3. Now retrieve the data:

```sql
SELECT * FROM entries;
```

4. You should see:
```sql
+--------------+-------------------+---------+
| guestName    | content           | entryID |
+--------------+-------------------+---------+
| first guest  | I got here!       |       1 |
| second guest | Me too!           |       2 |
+--------------+-------------------+---------+
2 rows in set (0.00 sec)
mysql>
```