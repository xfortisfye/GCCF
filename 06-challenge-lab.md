#### Google Cloud Computing Foundations | C02 Infrastructure in Google Cloud
#### M04-M05-M06

# Perform Foundational Infrastructure Tasks in Google Cloud: Challenge Lab

## Challenge scenario
You are just starting your junior cloud engineer role with Jooli inc. So far you have been helping teams create and manage Google Cloud resources.

You are expected to have the skills and knowledge for these tasks so don’t expect step-by-step guides.

## Your challenge
You are now asked to help a newly formed development team with some of their initial work on a new project around storing and organizing photographs, called memories. You have been asked to assist the memories team with initial configuration for their application development environment; you receive the following request to complete the following tasks:

- Create a bucket for storing the photographs.
- Create a Pub/Sub topic that will be used by a Cloud Function you create.
- Create a Cloud Function.
- Remove the previous cloud engineer’s access from the memories project.

Some Jooli Inc. standards you should follow:

- Create all resources in the us-east1 region and us-east1-b zone, unless otherwise directed.
- Use the project VPCs.
- Naming is normally team-resource, e.g. an instance could be named kraken-webserver1
- Allocate cost effective resource sizes. Projects are monitored and excessive resource use will result in the containing project's termination (and possibly yours), so beware. This is the guidance the monitoring team is willing to share; unless directed, use f1-micro for small Linux VMs and n1-standard-1 for Windows or other applications such as Kubernetes nodes.

Each task is described in detail below, good luck!

### Task 1. Create a bucket
You need to create a bucket called `Bucket Name` for the storage of the photographs.

### Task 2. Create a Pub/Sub topic
Create a Pub/Sub topic called `Topic Name` for the Cloud Function to send messages.

### Task 3. Create the thumbnail Cloud Function
Create a Cloud Function called Cloud Function Name that executes every time an object is created in the bucket Bucket Name you created in task 1. The function is written in Node.js 14. Make sure you set the Entry point (Function to execute) to thumbnail and Trigger to Cloud Storage.

In line 15 of index.js replace the text REPLACE_WITH_YOUR_TOPIC ID with the Topic Name you created in task 2.

index.js:
```javascript
/* globals exports, require */
//jshint strict: false
//jshint esversion: 6
"use strict";
const crc32 = require("fast-crc32c");
const { Storage } = require('@google-cloud/storage');
const gcs = new Storage();
const { PubSub } = require('@google-cloud/pubsub');
const imagemagick = require("imagemagick-stream");
exports.thumbnail = (event, context) => {
  const fileName = event.name;
  const bucketName = event.bucket;
  const size = "64x64"
  const bucket = gcs.bucket(bucketName);
  const topicName = "REPLACE_WITH_YOUR_TOPIC ID";
  const pubsub = new PubSub();
  if ( fileName.search("64x64_thumbnail") == -1 ){
    // doesn't have a thumbnail, get the filename extension
    var filename_split = fileName.split('.');
    var filename_ext = filename_split[filename_split.length - 1];
    var filename_without_ext = fileName.substring(0, fileName.length - filename_ext.length );
    if (filename_ext.toLowerCase() == 'png' || filename_ext.toLowerCase() == 'jpg'){
      // only support png and jpg at this point
      console.log(`Processing Original: gs://${bucketName}/${fileName}`);
      const gcsObject = bucket.file(fileName);
      let newFilename = filename_without_ext + size + '_thumbnail.' + filename_ext;
      let gcsNewObject = bucket.file(newFilename);
      let srcStream = gcsObject.createReadStream();
      let dstStream = gcsNewObject.createWriteStream();
      let resize = imagemagick().resize(size).quality(90);
      srcStream.pipe(resize).pipe(dstStream);
      return new Promise((resolve, reject) => {
        dstStream
          .on("error", (err) => {
            console.log(`Error: ${err}`);
            reject(err);
          })
          .on("finish", () => {
            console.log(`Success: ${fileName} → ${newFilename}`);
              // set the content-type
              gcsNewObject.setMetadata(
              {
                contentType: 'image/'+ filename_ext.toLowerCase()
              }, function(err, apiResponse) {});
              pubsub
                .topic(topicName)
                .publisher()
                .publish(Buffer.from(newFilename))
                .then(messageId => {
                  console.log(`Message ${messageId} published.`);
                })
                .catch(err => {
                  console.error('ERROR:', err);
                });
          });
      });
    }
    else {
      console.log(`gs://${bucketName}/${fileName} is not an image I can handle`);
    }
  }
  else {
    console.log(`gs://${bucketName}/${fileName} already has a thumbnail`);
  }
};
```

package.json:
```json
{
  "name": "thumbnails",
  "version": "1.0.0",
  "description": "Create Thumbnail of uploaded image",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "@google-cloud/pubsub": "^2.0.0",
    "@google-cloud/storage": "^5.0.0",
    "fast-crc32c": "1.0.4",
    "imagemagick-stream": "4.1.1"
  },
  "devDependencies": {},
  "engines": {
    "node": ">=4.3.2"
  }
}
```
> You must upload one JPG or PNG image into the bucket, we will verify the thumbnail was created (after creating the function successfully). Use any JPG or PNG image, or use this image https://storage.googleapis.com/cloud-training/gsp315/map.jpg; download the image to your machine and then upload that file to your bucket. You will see a thumbnail image appear shortly afterwards (use REFRESH in the bucket details).

### Task 4: Remove the previous cloud engineer
You will see that there are two users, one is your account (with the role of Owner) and the other is the previous cloud engineer ( Username 2 with the role of Viewer). We like to keep our security tight, so please remove the previous cloud engineer’s access to the project.

# Answer

## Task 1. Create a bucket
```
1. Navigation Menu > Cloud Storage > Browser > Create Bucket

2. Name your bucket > Continue

3. Location type > Multi-region > us (multiple regions in United States)

4. Rest of the options remain default > Continue

5. Click CREATE
```

## Task 2. Create a Pub/Sub topic
1. Install script for Pub/Sub
```bash
apt-get install -y virtualenv
python3 -m venv venv
source venv/bin/activate
pip install --upgrade google-cloud-pubsub
git clone https://github.com/googleapis/python-pubsub.git
cd python-pubsub/samples/snippets
```

2. Create Pub/Sub topic
```bash
echo $GOOGLE_CLOUD_PROJECT
export GOOGLE_CLOUD_PROJECT=qwiklabs-gcp-01-eb65ee5dfae4
python publisher.py $GOOGLE_CLOUD_PROJECT create memories-topic-655
```

## Task 3. Create the thumbnail Cloud Function
1. Navigation Bar > Cloud Function
2. Create Function
3. Fill up the following information
- Function Name: memories-thumbnail-creator
- Region: us-east1
- Trigger Type: Cloud Storage
- Event Type: Finalize/Create
- Bucket: memories-bucket-77946
4. Save > Next
5. Fill up the following information
- Runtime: Node.js 14
- Entry Point: thumbnail
- Replace index.js with sample and replace topic ID in line 15
- Replace package.json with sample
6. Deploy
7. Download sample image and transfer to bucket
```bash
curl https://storage.googleapis.com/cloud-training/gsp315/map.jpg --output sample.jpg
gsutil cp sample.jpg gs://memories-bucket-77946
```

### Task 4: Remove the previous cloud engineer
1. Navigation Menu > IAM & Admin > IAM
2. Go to respective student > Edit Principal > Delete Role > Save

# References
https://github.com/GDSC-IIIT-Kalyani/qwiklabs_challenges/blob/master/Perform%20Foundational%20Infrastructure%20Tasks%20in%20Google%20Cloud%20Challenge%20Lab.md