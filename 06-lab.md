#### Google Cloud Computing Foundations | C02 Infrastructure in Google Cloud
#### M06: You Can’t Secure the Cloud, Right?

# Lab 06-01: User Authentication: Identity-Aware Proxy

## Overview 
In this lab, you build a minimal web application with Google App Engine, then explore various ways to use Identity-Aware Proxy (IAP) to restrict access to the application and provide user identity information to it. Your app will:

* Display a welcome page

* Access user identity information provided by IAP

* Use cryptographic verification to prevent spoofing of user identity information

## Introduction
Authenticating users of your web app is often necessary, and usually requires special programming in your app. For Google Cloud apps you can hand those responsibilities off to the Identity-Aware Proxy service. If you only need to restrict access to selected users there are no changes necessary to the application. Should the application need to know the user's identity (such as for keeping user preferences server-side) Identity-Aware Proxy can provide that with minimal application code.

### What is Identity-Aware Proxy?
Identity-Aware Proxy (IAP) is a Google Cloud service that intercepts web requests sent to your application, authenticates the user making the request using the Google Identity Service, and only lets the requests through if they come from a user you authorize. In addition, it can modify the request headers to include information about the authenticated user.


## Download the Code
Click the command line area in the Cloud Shell so you can type commands.

Download the code from a public storage bucket and then change to the code folder:

```bash
gsutil cp gs://spls/gsp499/user-authentication-with-iap.zip .
```

```bash
unzip user-authentication-with-iap.zip
```

```bash
cd user-authentication-with-iap
```

This folder contains one subfolder for each step of this lab. You will change to the correct folder to perform each step.

## Deploy Application and Protect it with IAP
This is an App Engine Standard application written in Python that simply displays a "Hello, World" welcome page. We will deploy and test it, then restrict access to it using IAP.

### Review the Application Code
Change from the main project folder to the 1-HelloWorld subfolder that contains code for this step.

```bash
cd 1-HelloWorld
```

The application code is in the main.py file. It uses the Flask web framework to respond to web requests with the contents of a template. That template file is in templates/index.html, and for this step contains only plain HTML. A second template file contains a skeletal example privacy policy in templates/privacy.html.

There are two other files: requirements.txt lists all the non-default Python libraries the application uses, and app.yaml tells Google Cloud that this is a Python App Engine application.

You can list each file in the shell using the cat command, as in:

```bash
cat main.py
```

Or you can launch the Cloud Shell code editor by clicking the Pencil icon at the top right-hand side of the Cloud Shell window, and examine the code that way.

You do not need to change any files for this step.

### Deploy to App Engine
Deploy the app to the App Engine Standard environment for Python.

```bash
gcloud app deploy
```
Select a region near to you that says it "supports standard".

When you are asked if you want to continue, enter Y for yes.

> If you get a Gaia propagation related error message, re-run the gcloud app deploy command.

In a few minutes the deployment completes. You will see a message that you can view your application with gcloud app browse.

Enter that command:

```bash
gcloud app browse
```

Click the displayed link to open it in a new tab, or copy it to a manually opened new tab if necessary. Since this is the first time this app is run, it will take a few seconds to appear while a cloud instance is started, and you should see the following window.

You can open that same URL from any computer connected to the Internet to see that web page. Access is not yet restricted.

### Restrict Access with IAP
1. In the cloud console window, click the Navigation menu > Security > Identity-Aware Proxy.

2. Click ENABLE API.

3. Click GO TO IDENTITY-AWARE PROXY.

4. Click CONFIGURE CONSENT SCREEN.

5. Select Internal under User Type and click Create.

6. Fill in the required blanks with appropriate values:

| Field  | Value |
| ----- | ----- |
| App name | IAP Example |
| User support email | Select your Qwiklabs student email address from the dropdown. | 
| Application home page | The URL you used to view your app. You can find this again by running the gcloud app browse command in cloud shell again. |
| Application privacy Policy link | The privacy page link in the app, same as the homepage link with /privacy added to the end |
| Authorized domains | Click + ADD DOMAINThe hostname portion of the application's URL, e.g. iap-example-999999.appspot.com. You can see this in the address bar of the Hello World web page you previously opened. Do not include the starting https:// or trailing / from that URL. |
| Developer Contact Information | Enter at least one email |

7. Click SAVE AND CONTINUE.

8. For Scopes, click SAVE AND CONTINUE.

9. For Summary, click BACK TO DASHBOARD.

You might be prompted to create credentials. You do not need to create credentials for this lab, so you can simply close this browser tab.

10. In Cloud Shell, run this command to disable the Flex API:

```bash
gcloud services disable appengineflex.googleapis.com
```

> App Engine has its standard and flexible environments which are optimized for different application architectures. Currently, when enabling IAP for App Engine, if the Flex API is enabled, Google Cloud will look for a Flex Service Account. Your Qwiklabs project comes with a multitude of APIs already enabled for the purpose of convenience. However, this creates a unique situation where the Flex API is enabled without a Service Account created.

11. Return to the Identity-Aware Proxy page and refresh it. You should now see a list of resources you can protect.

Click the toggle button in the IAP column in the App Engine app row to turn IAP on.

12. The domain will be protected by IAP. Click TURN ON.

### Test that IAP is turned on
1. Open a browser tab and navigate to the URL for your app. A Sign in with Google screen opens and requires you to log in to access the app.

2. Sign in with the account you used to log into the console. You will see a screen denying you access.

You have successfully protected your app with IAP, but you have not yet told IAP which accounts to allow through.

3. Return to the Identity-Aware Proxy page of the console, select the checkbox next to App Engine app, and see the sidebar at the right of the page.

Each email address (or Google Group address, or GSuite domain name) that should be allowed access needs to be added as a Member.

4. Click ADD PRINCIPAL.

5. Enter your Qwiklabs Student email address.

6. Then, pick the Cloud IAP/IAP-Secured Web App User role to assign to that address.

You may enter more addresses or GSuite domains in the same way.

7. Click SAVE.

The message "Policy Updated" will appear at the bottom of the window.

### Test access
Navigate back to your app and reload the page. You should now see your web app, since you already logged in with a user you authorized.

If you still see the "You don't have access" page, IAP did not recheck your authorization. In that case, do the following steps:

1. Open your web browser to the home page address with /_gcp_iap/clear_login_cookie added to the end of the URL, as in https://iap-example-999999.appspot.com/_gcp_iap/clear_login_cookie.


2. You will see a new Sign in with Google screen, with your account already showing. Do not click the account. Instead, click Use another account, and re-enter your credentials.

> It takes a minute for the role change to take effect. If the page still shows the "You don't have access" message after following the previous steps, wait a minute and try refreshing the page.

These steps cause IAP to recheck your access and you should now see your application's home screen.

If you have access to another browser or can use Incognito Mode in your browser, and have another valid GMail or GSuite account, you can use that browser to navigate to your app page and log in with the other account. Since that account has not been authorized, it will see the "You Don't Have Access" screen instead of your app.

## Access User Identity Information
Once an app is protected with IAP, it can use the identity information that IAP provides in the web request headers it passes through. In this step, the application will get the logged-in user's email address and a persistent unique user ID assigned by the Google Identity Service to that user. That data will be displayed to the user in the welcome page.

In Cloud Shell, change to the folder for this step:

```bash
cd ~/user-authentication-with-iap/2-HelloUser
```

### Deploy to App Engine
Since deployment takes a few minutes, start by deploying the app to the App Engine Standard environment for Python:

```bash
gcloud app deploy
```

When you are asked if you want to continue, enter Y for yes.

In a few minutes the deployment should complete. While you are waiting you can examine the application files as described below.

### Examine the Application Files
This folder contains the same set of files as seen in the previous app you deployed, 1-HelloWorld, but two of the files have been changed: main.py and templates/index.html. The program has been changed to retrieve the user information that IAP provides in request headers, and the template now displays that data.

There are two lines in main.py that get the IAP-provided identity data:

```python
user_email = request.headers.get('X-Goog-Authenticated-User-Email')
user_id = request.headers.get('X-Goog-Authenticated-User-ID')
```

The X-Goog-Authenticated-User- headers are provided by IAP, and the names are case-insensitive, so they could be given in all lower or all upper case if preferred. The render_template statement now includes those values so they can be displayed:

```python
page = render_template('index.html', email=user_email, id=user_id)
```

The index.html template can display those values by enclosing the names in double curly braces:

```python
Hello, {{ email }}! Your persistent ID is {{ id }}.
```

As you can see, the provided data is prefixed with accounts.google.com, showing where the information came from. Your application can remove everything up to and including the colon to get the raw values if desired.

### Test the updated IAP
Going back to the deployment, when it is ready, you will see a message that you can view your application with gcloud app browse.

1. Enter that command.

```bash
gcloud app browse
```

2. If a new tab does not open on your browser, copy the displayed link and open it in a new tab normally. You should see a page similar to the following:

You may need to wait a few minutes for the new version of your application to replace the prior version. Refresh the page if needed to see a page similar to the above.

### Turn off IAP
What happens to this app if IAP is disabled, or somehow bypassed (such as by other applications running in your same cloud project)? Turn off IAP to see.

In the cloud console window, click Navigation menu > Security > Identity-Aware Proxy. Click the IAP toggle switch next to App Engine app to turn IAP off. Click TURN OFF.

You will be warned that this will allow all users to access the app.

Refresh the application web page. You should see the same page, but without any user information:

Since the application is now unprotected, a user could send a web request that appeared to have passed through IAP. For example, you can run the following curl command from the Cloud Shell to do that (replace <your-url-here> with the correct URL for your app):

```bash
curl -X GET <your-url-here> -H "X-Goog-Authenticated-User-Email: totally fake email"
```

The web page will be displayed on the command line, and look like the following (do not copy):

```html
<!doctype html>
<html>
<head>
  <title>IAP Hello User</title>
</head>
<body>
  <h1>Hello World</h1>
  <p>
    Hello, totally fake email! Your persistent ID is None.
  </p>
  <p>
    This is step 2 of the <em>User Authentication with IAP</em>
    codelab.
 </p>
</body>
</html>
```

There is no way for the application to know that IAP has been disabled or bypassed. For cases where that is a potential risk, Cryptographic Verification shows a solution.

## Use Cryptographic Verification
If there is a risk of IAP being turned off or bypassed, your app can check to make sure the identity information it receives is valid. This uses a third web request header added by IAP, called X-Goog-IAP-JWT-Assertion. The value of the header is a cryptographically signed object that also contains the user identity data. Your application can verify the digital signature and use the data provided in this object to be certain that it was provided by IAP without alteration.

Digital signature verification requires several extra steps, such as retrieving the latest set of Google public keys. You can decide whether your application needs these extra steps based on the risk that someone might be able to turn off or bypass IAP, and the sensitivity of the application.

In Cloud Shell, change to the folder for this step:

```bash
cd ~/user-authentication-with-iap/3-HelloVerifiedUser
```
### Deploy to App Engine
Deploy the app to the App Engine Standard environment for Python:

```bash
gcloud app deploy
```

When you are asked if you want to continue, enter Y for yes. In a few minutes the deployment should complete. While you are waiting you can examine the application files as described below.

### Examine the Application Files
This folder contains the same set of files as seen in 2-HelloUser, with two files altered and one new file. The new file is auth.py, which provides a user() method to retrieve and verify the cryptographically signed identity information. The changed files are main.py and templates/index.html, which now use the results of that method. The unverified headers as found in the last deployment are also shown for comparison.

The new functionality is primarily in the user() function:

```python
def user():
    assertion = request.headers.get('X-Goog-IAP-JWT-Assertion')
    if assertion is None:
        return None, None
    info = jwt.decode(
        assertion,
        keys(),
        algorithms=['ES256'],
        audience=audience()
    )
    return info['email'], info['sub']
```

The assertion is the cryptographically signed data provided in the specified request header. The code uses a library to validate and decode that data. Validation uses the public keys that Google provides for checking data it signs, and knowing the audience that the data was prepared for (essentially, the Google Cloud project that is being protected). Helper functions keys() and audience() gather and return those values.

The signed object has two pieces of data we need: the verified email address, and the unique ID value (provided in the sub, for subscriber, standard field).

This completes Step 3.

### Test the Cryptographic Verification
When the deployment is ready you will see a message that you can view your application with gcloud app browse.

Enter that command:

```bash
gcloud app browse
```

If a new tab does not open on your browser, copy the displayed link and open it in a new tab normally.

Recall that you previously disabled IAP, so the application provides no IAP data. You should see a page similar to the following:

As before, you may need to wait a few minutes for the newest version to be live to see the new version of the page.

Since IAP is disabled, no user information is available. Now turn IAP back on.

1. In the cloud console window, click the Navigation menu > Security > Identity-Aware Proxy.

2. Click the IAP toggle switch next to App Engine app to turn IAP on again. Click TURN ON.

3. Refresh the page. The page should look like the following:

Notice that the email address provided by the verified method does not have the accounts.google.com: prefix.

If IAP is turned off or bypassed, the verified data would either be missing, or invalid, since it cannot have a valid signature unless it was created by the holder of Google's private keys.


----
# Lab 06-02: Cloud IAM: Qwik Start

## Overview 
Google Cloud's Identity and Access Management (IAM) service lets you create and manage permissions for Google Cloud resources. Cloud IAM unifies access control for Google Cloud services into a single system and provides a consistent set of operations.

In this hands-on lab you learn how to assign a role to a second user and remove assigned roles associated with Cloud IAM. More specifically, you sign in with 2 different sets of credentials to experience how granting and revoking permissions works from Google Cloud Project Owner and Viewer roles.

## The IAM console and project level roles
1. Return to the Username 1 Cloud Console page.

2. Select Navigation menu > IAM & Admin > IAM. You are now in the "IAM & Admin" console.

3. Click +ADD button at the top of the page and explore the project roles associated with Projects by clicking on the "Select a role" dropdown menu.

There are four roles:

* Browser
* Editor
* Owner
* Viewer

These are primitive roles in Google Cloud. Primitive roles set project-level permissions and unless otherwise specified, they control access and management to all Google Cloud services.

The following table pulls definitions from the Google Cloud IAM article, Basic roles, which gives a brief overview of browser, viewer, editor, and owner role permissions:

| Role Name | Permissions |
| ----- | ----- |
| roles/viewer | Permissions for read-only actions that do not affect state, such as viewing (but not modifying) existing resources or data. |
| roles/editor | All viewer permissions, plus permissions for actions that modify state, such as changing existing resources. |
| roles/owner | All editor permissions and permissions for the following actions: * Manage roles and permissions for a project and all resources within the project.* Set up billing for a project. |
| roles/browser (beta) | Read access to browse the hierarchy for a project, including the folder, organization, and Cloud IAM policy. This role doesn't include permission to view resources in the project. |

Since you are able to manage roles and permissions for this project, Username 1 has Project owner permissions.

4. Click CANCEL to exit out of the "Add principal" panel.

## Explore editor roles
Now switch to the Username 2 console.

1. Navigate to the IAM & Admin console, select Navigation menu > IAM & Admin > IAM.

2. Search through the table to find Username 1 and Username 2 and examine the roles they are granted. The Username 1 and Username 2 roles are listed inline and to the right of each user.

You should see:

* Username 2 has the "Viewer" role granted to it.
* The +ADD button at the top is grayed out—if you try to click on it you get the message, "You need permissions for this action. Required permission(s): resource manager.projects.setIamPolicy".
This is one example of how IAM roles affect what you can and cannot do in Google Cloud.

3. Switch back to the Username 1 console for the next step.

## Prepare a resource for access testing
Ensure that you are in the Username 1 Cloud Console.

### Create a bucket
1. Create a Cloud Storage bucket with a unique name. From the Cloud Console, select Navigation menu > Cloud Storage > Browser.

2. Click CREATE BUCKET.

> Note: If you get a permissions error for bucket creation, sign out and then sign in back in with the Username 1 credentials.

3. Update the following fields, leave all others at their default values:

| Property | Value | 
| ----- | ----- |
| Name: | globally unique name (create it yourself!) and click CONTINUE. |
| Location Type: | Multi-Region |

Note the bucket name. You will use it in a later step.

4. Click CREATE.
> Note: If you get a permissions error for bucket creation, sign out and then sign in back in with the Username 1 credentials.

### Upload a sample file
1. On the Bucket Details page click UPLOAD FILES.

2. Browse your computer to find a file to use. Any text or html file will do.

3. Click on the three dots at the end of the line containing the file and click Rename.

4. Rename the file ‘sample.txt'.

5. Click RENAME.

Click Check my progress to verify the objective.

### Verify project viewer access
1. Switch to the Username 2 console.

2. From the Console, select Navigation menu > Cloud Storage > Browser. Verify that this user can see the bucket.

Username 2 has the "Viewer" role prescribed which allows them read-only actions that do not affect state. This example illustrates this feature—they can view Cloud Storage buckets and files that are hosted in the Google Cloud project that they've been granted access to.

## Remove project access
Switch to the Username 1 console.

### Remove Project Viewer for Username 2
1. Select Navigation menu > IAM & Admin > IAM. Then click the pencil icon inline and to the left of Username 2.

> Note: You may have to widen the screen to see the pencil icon.

2. Remove Project Viewer access for Username 2 by clicking the trashcan icon next to the role name. Then click SAVE.

Notice that the user has disappeared from the Member list! The user has no access now.

> Note: It can take up to 80 seconds for such a change to take effect as it propagates. Read more about Google Cloud IAM in the Google Cloud IAMResource Documentation, Frequently asked questions.

### Verify that Username 2 has lost access
1. Switch to Username 2 Cloud Console. Ensure that you are still signed in with Username 2's credentials and that you haven't been signed out of the project after permissions were revoked. If signed out, sign in back with the proper credentials.

2. Navigate back to Cloud Storage by selecting Navigation menu > Cloud Storage > Browser.

You should see a permission error.

> Note: As mentioned before, it can take up to 80 seconds for permissions to be revoked. If you haven't received a permission error, wait a 2 minutes and then try refreshing the console.

## Add Storage permissions
1. Copy Username 2 name from the Lab Connection panel.

2. Switch to Username 1 console. Ensure that you are still signed in with Username 1's credentials. If you are signed out, sign in back with the proper credentials.

3. In the Console, select Navigation menu > IAM & Admin > IAM.

4. Click + ADD button and paste the Username 2 name into the New principals field.

5. In the Select a role field, select Cloud Storage > Storage Object Viewer from the drop-down menu.

6. Click SAVE.

## Verify access
1. Switch to the Username 2 console. You'll still be on the Storage page.

Username 2 doesn't have the Project Viewer role, so that user can't see the project or any of its resources in the Console. However, this user has specific access to Cloud Storage, the Storage Object Viewer role - check it out now.

2. Click Activate Cloud Shell the icon that activates cloud shell to open the Cloud Shell command line. If prompted click Continue.

3. Open up a Cloud Shell session and then enter in the following command, replace [YOUR_BUCKET_NAME] with the name of the bucket you created earlier:

```bash
gsutil ls gs://[YOUR_BUCKET_NAME]
```

You should receive a similar output:

```
gs://[YOUR_BUCKET_NAME]/sample.txt
```

> Note: If you see AccessDeniedException, wait a minute and run the previous command again.

4. As you can see, you gave Username 2 view access to the Cloud Storage bucket.

----
## Test your understanding
1. Google Cloud Pub/Sub service allows applications to exchange messages reliably, quickly, and asynchronously.
- [x] True
- [x] False

2. A _____ is a shared string that allows applications to connect with one another.
- [x] subscription
- [x] message
- [x] topic

----