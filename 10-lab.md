#### Google Cloud Computing Foundations | C04 Data, ML, and AI in Google Cloud
#### M10: Let Machines Do the Work

# Lab 10-01: AI Platform: Qwik Start

## Overview
This lab will give you hands-on practice with TensorFlow 2.x model training, both locally and on AI Platform. After training, you will learn how to deploy your model to AI Platform for serving (prediction). You'll train your model to predict income category of a person using the United States Census Income Dataset.

This lab gives you an introductory, end-to-end experience of training and prediction on AI Platform. The lab will use a census dataset to:
- Create a TensorFlow 2.x training application and validate it locally.
- Run your training job on a single worker instance in the cloud.
- Deploy a model to support prediction.
- Request an online prediction and see the response.

### What you will build
The sample builds a classification model for predicting income category based on United States Census Income Dataset. The two income categories (also known as labels) are:

- >50K — Greater than 50,000 dollars
- <=50K — Less than or equal to 50,000 dollars
The sample defines the model using the Keras Sequential API. The sample defines the data transformations particular to the census dataset, then assigns these (potentially) transformed features to either the DNN or the linear portion of the model.

## Launch Vertex Workbench Notebook
To launch Notebooks with Vertex AI:

1. Click on the Navigation Menu and navigate to Vertex AI, then to Workbench.

2. On the Notebook instances page, click New Notebook.

3. In the Customize instance menu, select TensorFlow Enterprise and choose the latest version of TensorFlow Enterprise 2.x (with LTS) > Without GPUs.

4. In the New notebook instance dialog, click the pencil icon to Edit instance properties.

5. For Instance name, enter a name for your instance.

6. For Region, select us-central1 and for Zone, select a zone within the selected region.

7. Scroll down to Machine configuration and select n1-standard-2 for Machine type.

8. Leave the remaining fields with their default and click Create.

After a few minutes, the Vertex AI console will display your instance name, followed by Open JupyterLab.

9. Click Open JupyterLab. A JupyterLab window will open in a new tab.

## Clone the example repo within your Vertex Notebook instance

To clone the training-data-analyst notebook in your JupyterLab instance:

1. In JupyterLab, click the Terminal icon to open a new terminal.

2. At the command-line prompt, type in the following command and press Enter.

```bash
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
```

3. Confirm that you have cloned the repository by double clicking on the training-data-analyst directory and ensuring that you can see its contents. The files for all the Jupyter notebook-based labs throughout this course are available in this directory.

### Navigate to the example notebook
In your Notebook, navigate to training-data-analyst/self-paced-labs/ai-platform-qwikstart and open ai_platform_qwik_start.ipynb.

Clear all the cells in the notebook (on the notebook toolbar, navigate to Edit > Clear All Outputs) and then Run the cells one by one.

## Run your training job in the cloud
> Note: Just running cells in the Jupyter Notebook

Step 3.1
- Set up a Cloud Storage bucket.
- Upload the data files to your Cloud Storage bucket.

Step 3.2
- Run a single-instance trainer in the cloud.

Step 3.3
- Create an AI Platform model.
- Create a version v1 of your model.

## Test your Understanding
1. A model version is an instance of a machine learning solution stored in the AI Platform model service.
- [ ] True
- [ ] False

2. AI Platform offers training jobs and batch prediction jobs.
- [ ] True
- [ ] False


----
# Lab 10-02: Cloud Natural Lanaguge API: Qwik Start

## Overview
Cloud Natural Language API lets you extract information about people, places, events, (and more) mentioned in text documents, news articles, or blog posts. You can use it to understand sentiment about your product on social media, or parse intent from customer conversations happening in a call center or a messaging app. You can even upload text documents for analysis.

### Cloud Natural Language API features
- **Syntax Analysis**: Extract tokens and sentences, identify parts of speech (PoS) and create dependency parse trees for each sentence.
- **Entity Recognition**: Identify entities and label by types such as person, organization, location, events, products and media.
- **Sentiment Analysis**: Understand the overall sentiment expressed in a block of text.
- **Content Classification**: Classify documents in predefined 700+ categories.
- **Multi-Language**: Enables you to easily analyze text in multiple languages including English, Spanish, Japanese, Chinese (Simplified and Traditional), French, German, Italian, Korean and Portuguese.
- **Integrated REST API**: Access via REST API. Text can be uploaded in the request or integrated with Cloud Storage.

In this lab you'll use the analyze-entities method to ask the Cloud Natural Language API to extract "entities" (e.g. people, places, and events) from a snippet of text.

## Create an API Key
First, you will set an environment variable with your PROJECT_ID which you will use throughout this codelab:

```bash
export GOOGLE_CLOUD_PROJECT=$(gcloud config get-value core/project)
```

Next, create a new service account to access the Natural Language API:

```
gcloud iam service-accounts create my-natlang-sa --display-name "my natural language service account"
```

Then, create credentials to log in as your new service account. Create these credentials and save it as a JSON file "~/key.json" by using the following command:

```bash
gcloud iam service-accounts keys create ~/key.json --iam-account my-natlang-sa@${GOOGLE_CLOUD_PROJECT}.iam.gserviceaccount.com
```

Finally, set the GOOGLE_APPLICATION_CREDENTIALS environment variable. The environment variable should be set to the full path of the credentials JSON file you created, which you can see in the output from the previous command:

```bash
export GOOGLE_APPLICATION_CREDENTIALS="/home/USER/key.json"
```

## Make an Entity Analysis Request
In order to perform next steps please connect to the instance provisioned for you via ssh. Open the navigation menu and select Compute Engine. You should see the following provisioned linux instance:

Click on the SSH button. You will be brought to an interactive shell. Remain in this SSH session for the rest of the lab.

Now you'll try out the Natural Language API's entity analysis with the following sentence:

_Michelangelo Caravaggio, Italian painter, is known for 'The Calling of Saint Matthew'_

Run the following gcloud command:

```bash
gcloud ml language analyze-entities --content="Michelangelo Caravaggio, Italian painter, is known for 'The Calling of Saint Matthew'." > result.json
```

Run the below command to preview the output of result.json file.

```bash
cat result.json
```

You should see a response similar to the following in the result.json file:

```json
{
  "entities": [
    {
      "name": "Michelangelo Caravaggio",
      "type": "PERSON",
      "metadata": {
        "wikipedia_url": "http://en.wikipedia.org/wiki/Caravaggio",
        "mid": "/m/020bg"
      },
      "salience": 0.83047235,
      "mentions": [
        {
          "text": {
            "content": "Michelangelo Caravaggio",
            "beginOffset": 0
          },
          "type": "PROPER"
        },
        {
          "text": {
            "content": "painter",
            "beginOffset": 33
          },
          "type": "COMMON"
        }
      ]
    },
    {
      "name": "Italian",
      "type": "LOCATION",
      "metadata": {
        "mid": "/m/03rjj",
        "wikipedia_url": "http://en.wikipedia.org/wiki/Italy"
      },
      "salience": 0.13870546,
      "mentions": [
        {
          "text": {
            "content": "Italian",
            "beginOffset": 25
          },
          "type": "PROPER"
        }
      ]
    },
    {
      "name": "The Calling of Saint Matthew",
      "type": "EVENT",
      "metadata": {
        "mid": "/m/085_p7",
        "wikipedia_url": "http://en.wikipedia.org/wiki/The_Calling_of_St_Matthew_(Caravaggio)"
      },
      "salience": 0.030822212,
      "mentions": [
        {
          "text": {
            "content": "The Calling of Saint Matthew",
            "beginOffset": 69
          },
          "type": "PROPER"
        }
      ]
    }
  ],
  "language": "en"
}
```

Read through your results. For each "entity" in the response, you'll see:

- The entity name and type, a person, location, event, etc.
- `metadata`, an associated Wikipedia URL if there is one.
- `salience`, and the indices of where this entity appeared in the text. Salience is a number in the [0,1] range that refers to the centrality of the entity to the text as a whole.
- `mentions`, which is the same entity mentioned in different ways.

You've sent your first request to the Cloud Natural Language API.

----
# Lab 10-03: Google Cloud Speech API: Qwik Start

## Overview
The Google Cloud Speech API enables easy integration of Google speech recognition technologies into developer applications. The Speech API allows you to send audio and receive a text transcription from the service (see What is the Google Cloud Speech API? for more information).

### What you'll do
- Create an API key
- Create a Speech API request
- Call the Speech API request

## Create an API Key
Since you'll be using curl to send a request to the Speech API, you'll need to generate an API key to pass in our request URL.

To create an API key, click Navigation menu > APIs & services > Credentials:

Then click Create credentials:

In the drop down menu, select API key:

Copy the key you just generated and click Close.

Now that you have an API key, you will save it as an environment variable to avoid having to insert the value of your API key in each request.

In order to perform next steps please connect to the instance provisioned for you via ssh. Open the Navigation menu and select Compute Engine. You should see the following provisioned linux instance:

Click on the SSH button. You will be brought to an interactive shell. In the command line, enter in the following, replacing <YOUR_API_KEY> with the key you just copied:

```bash
export API_KEY=<YOUR_API_KEY>
```

**Remain in this SSH session for the rest of the lab.**

## Create your Speech API request
> Note: You will use a pre-recorded file that's available on Cloud Storage: gs://cloud-samples-tests/speech/brooklyn.flac. You can listen to this file before sending it to the Speech API here.

Create request.json in SSH command line. You'll use this to build your request to the speech API:

```bash
touch request.json
```

Now open the request.json:

```bash
nano request.json
```

> You can use your preferred command line editor (nano, vim, emacs) or gcloud. This lab will provide instructions for nano.

Add the following to your request.json file, using the uri value of the sample raw audio file:

```json
{
  "config": {
      "encoding":"FLAC",
      "languageCode": "en-US"
  },
  "audio": {
      "uri":"gs://cloud-samples-tests/speech/brooklyn.flac"
  }
}
```

Press control + x and then y to save and close the request.json file.

The request body has a config and audio object.

In config, you tell the Speech API how to process the request:
- The encoding parameter tells the API which type of audio encoding you're using while the file is being sent to the API. FLAC is the encoding type for .raw files (here is documentation for encoding types for more details).

There are other parameters you can add to your config object, but encoding is the only required one.

In the audio object, you pass the API the uri of the audio file in Cloud Storage.

Now you're ready to call the Speech API!

## Call the Speech API
Pass your request body, along with the API key environment variable, to the Speech API with the following curl command (all in one single command line):

```bash
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json \
"https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}"
```

Your response should look something like this:

```json
{
  "results": [
    {
      "alternatives": [
        {
          "transcript": "how old is the Brooklyn Bridge",
          "confidence": 0.98267895
        }
      ]
    }
  ]
}
```

The transcript value will return the Speech API's text transcription of your audio file, and the confidence value indicates how sure the API is that it has accurately transcribed your audio.

You'll notice that you called the syncrecognize method in the request above. The Speech API supports both synchronous and asynchronous speech to text transcription. In this example you sent it a complete audio file, but you can also use the syncrecognize method to perform streaming speech to text transcription while the user is still speaking.

You created an Speech API request then called the Speech API. Run the following command to save the response in a result.json file:

```bash
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json \
"https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}" > result.json
```

----
# Lab 10-04: Video Intelligence: Qwik Start

## Overview 
Google Cloud Video Intelligence makes videos searchable and discoverable by extracting metadata with an easy to use REST API. You can now search every moment of every video file in your catalog. It quickly annotates videos stored in Cloud Storage, and helps you identify key entities (nouns) within your video; and when they occur within the video. Separate signal from noise by retrieving relevant information within the entire video, shot-by-shot, -or per frame.

Enable the Video Intelligence API
For this lab, the Cloud Video Intelligence API is enabled for you.

## Set up authorization
This lab creates and uses a service account that is tied to your Qwiklabs Google Cloud project for authorization.

In Cloud Shell, run the following command to create a new service account named quickstart:

```bash
gcloud iam service-accounts create quickstart
```

Create a service account key file, replacing <your-project-123> with your Qwiklabs Project ID:

```bash
gcloud iam service-accounts keys create key.json --iam-account quickstart@<your-project-123>.iam.gserviceaccount.com
```

Now authenticate your service account, passing the location of your service account key file:

```bash
gcloud auth activate-service-account --key-file key.json
```

Obtain an authorization token using your service account:

```bash
gcloud auth print-access-token
```

The token will print in the output, and you'll be using it in a future step.

## Make an annotate video request
Run this command to create a JSON request file with the following text, and save it as request.json :

```bash
cat > request.json <<EOF
{
   "inputUri":"gs://spls/gsp154/video/train.mp4",
   "features": [
       "LABEL_DETECTION"
   ]
}
EOF
```

> To make the process simpler, a public video of a train available to your qwiklabs project is used as the value for your inputUri. If preferred or running in a personal project, any video can be used in place by uploading it to Cloud Storage and providing its Cloud Storage URI (format: gs://bucket/object) for the value of "inputUri".

Use curl to make a videos:annotate request passing the filename of the entity request:

```bash
curl -s -H 'Content-Type: application/json' \
    -H 'Authorization: Bearer '$(gcloud auth print-access-token)'' \
    'https://videointelligence.googleapis.com/v1/videos:annotate' \
    -d @request.json
```

The Video Intelligence API creates an operation to process your request. You should now see a response that includes your operation name, which should look similar to this one:

```json
{
  "name": "projects/474887704060/locations/asia-east1/operations/16366331060670521152"
}
```

You will use this operation name, locations and projects in the future step.

Use this script to request information on the operation by calling the v1.operations endpoint. Replace the PROJECTS, LOCATIONS and OPERATION_NAME with the value you just received in the previous command:

```bash
curl -s -H 'Content-Type: application/json' \
    -H 'Authorization: Bearer '$(gcloud auth print-access-token)'' \
    'https://videointelligence.googleapis.com/v1/projects/PROJECTS/locations/LOCATIONS/operations/OPERATION_NAME'
```

You'll now see information related to your operation. If the operation has completed, a done field is included and set to true:

```json
{
  "name": "projects/425437283751/locations/asia-east1/operations/17938636079131796601",
  "metadata": {
    "@type": "type.googleapis.com/google.cloud.videointelligence.v1.Annota
tionProgressMetadata",
    "progressMetadata": [
      {
        "inputUri": "gs://spls/gsp154/video/train.mp4",
        "startTime": "2016-09-22T21:41:56.766091Z",
        "lastUpdateTime": "2016-09-22T21:42:03.889743Z"
      }
    ]
  },
  ...
}
```

After giving the request some time (about a minute, typically), re-run the command and the same request returns annotated results:

```json
{
  "name": "projects/425437283751/locations/asia-east1/operations/17938636079131796601",
  "metadata": {
    "@type": "type.googleapis.com/google.cloud.videointelligence.v1.AnnotateVideoProgress",
    "annotationProgress": [
      {
        "inputUri": "/spls/gsp154/video/train.mp4",
        "progressPercent": 100,
        "startTime": "2017-02-17T22:39:00.333942Z",
        "updateTime": "2017-02-17T22:39:11.414399Z"
      }
    ]
  },
  "done": true,
  "response": {
    "@type": "type.googleapis.com/google.cloud.videointelligence.v1.AnnotateVideoResponse",
    "annotationResults": [
      {
        "inputUri": "/spls/gsp154/video/train.mp4",
        "segmentLabelAnnotations": [
          {
            "entity": {
              "entityId": "/m/01yrx",
              "languageCode": "en-US"
            },
            "segments": [
              {
                "segment": {
                  "startTimeOffset": "0s",
                  "endTimeOffset": "14.833664s"
                },
                "confidence": 0.98509187
              }
            ]
          },
         ...
```

You've sent your first request to Cloud Video Intelligence API.

----
# Lab 10-05: Reinforcement Learning: Qwik Start

## Overview 
Like many other areas of machine learning research, reinforcement learning (RL) is evolving at breakneck speed. Just as they have done in other research areas, researchers are leveraging deep learning to achieve state-of-the-art results.

In particular, reinforcement learning has significantly outperformed prior ML techniques in game playing, reaching human-level and even world-best performance on Atari, beating the human Go champion, and is showing promising results in more difficult games like Starcraft II.

In this lab, you will learn the basics of reinforcement learning by building a simple game, which has been modelled off of a sample provided by OpenAI Gym.

## Reinforcement learning 101
Reinforcement learning (RL) is a form of machine learning whereby an agent takes actions in an environment to maximize a given objective (a reward) over this sequence of steps. Unlike more traditional supervised learning techniques, every data point is not labelled and the agent only has access to "sparse" rewards.

While the history of RL can be dated back to the 1950s and there are a lot of RL algorithms out there, 2 easy to implement yet powerful deep RL algorithms have a lot of attractions recently: deep Q-network (DQN) and deep deterministic policy gradient (DDPG). We briefly introduce the algorithms and variants based on them in this section.

https://cdn.qwiklabs.com/cDBDy0wLYFlwkAnG0PrdbCg7UAEngRYH%2BORdWseL14A%3D

_A conceptual process diagram of the Reinforcement Learning problem_

The Deep Q-network (DQN) was introduced by Google Deepmind's group in this Nature paper in 2015. Encouraged by the success of deep learning in the field of image recognition, the authors incorporated deep neural networks into Q-Learning and tested their algorithm in the Atari Game Engine Simulator, in which the dimension of the observation space is very large.

The deep neural network acts as a function approximator that predicts the output Q-values, or the desirability of taking an action, given a certain input state. Accordingly, DQN is a value-based method: in the training algorithm DQN updates Q-values according to Bellman's equation, and to avoid the difficulty of fitting a moving target, it employs a second deep neural network that serves as an estimation of target values.

On a more practical level, the following model highlights the source files, the shell command, and the endpoint to get an RL job running on Google Cloud:
https://cdn.qwiklabs.com/FQvwxiTxO%2FJ5baJVEDsj0tKHG1hvn27YHmaa0FHFbS4%3D

## Create an AI Platform Notebook
All the required files that you need for this lab can be found in this [repository](https://github.com/GoogleCloudPlatform/training-data-analyst/pull/745). You will create an AI platform tensorflow notebook to run all these commands.

From the left-hand navigation menu, select AI Platform > Notebooks. Then from the top-hand menu, select + New Notebook > TensorFlow 2.x > Without GPUs:

Then click Create. It will take a couple of minutes to provision your AI Platform Notebook. Refresh the page occasionally. Once the notebook has been built, click the OPEN JUPYTERLAB button:

## Clone the sample code
Click on the Terminal icon. This will give you a temporary shell to enter commands. Enter in the following command to clone the sample repo from the training data analyst repository:

```bash
git clone https://github.com/GoogleCloudPlatform/training-data-analyst.git
```

Wait for this command to propagate. Then from the left-hand menu, select training-data-analyst> quests> rl > early_rl > early_rl.ipynb. This will open a new tab.

## Run through the notebook
Your new tab should look similar to the following:

https://cdn.qwiklabs.com/eIiugfg275%2BwduGfjCmVnCwb%2B6FnsIZDN4I16yT0wCY%3D

Read through the following notebook and run all code blocks with Shift + Enter.