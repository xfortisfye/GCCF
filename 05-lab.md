#### Google Cloud Computing Foundations | C02 Infrastructure in Google Cloud
#### M05: There's an API for That!

# Lab 05-01: Cloud Endpoints: Qwik Start

## Overview 
In this lab you will deploy a sample API with Google Cloud Endpoints, which are a set of tools for generating APIs from within an App Engine application. The sample code will include:

* A REST API that you can query to find the name of an airport from its three-letter IATA code (for example, SFO, JFK, AMS).
* A script that uploads the API configuration to Cloud Endpoints.
* A script that deploys a Google App Engine flexible backend to host the sample API.

After you send some requests to the sample API, you can view the Cloud Endpoints Activity Graphs and Logs. Theses are tools that allow you to monitor your APIs and gain insights into their usage.

## Getting the sample code
Enter the following command in Cloud Shell to get the sample API and scripts:

```bash
gsutil cp gs://spls/gsp164/endpoints-quickstart.zip .
unzip endpoints-quickstart.zip
```

Change to the directory that contains the sample code:

```bash
cd endpoints-quickstart
```

## Deploying the Endpoints configuration
To publish a REST API to Endpoints, an OpenAPI configuration file that describes the API is required. The lab's sample API comes with a pre-configured OpenAPI file called openapi.yaml.

Endpoints uses Google Service Management, an infrastructure service of Google Cloud, to create and manage APIs and services. To use Endpoints to manage an API, you deploy the API's OpenAPI configuration to Service Management.

To deploy the Endpoints configuration.

In the endpoints-qwikstart directory, enter the following:

```bash
cd scripts
```
Run the following script, which is included in the sample:

```bash
./deploy_api.sh
```

Cloud Endpoints uses the `host` field in the OpenAPI configuration file to identify the service. The `deploy_api.sh` script sets the ID of your Cloud project as part of the name configured in the `host` field. (When you prepare an OpenAPI configuration file for your own service, you will need to do this manually.)

The script then deploys the OpenAPI configuration to Service Management using the command: `gcloud endpoints services deploy openapi.yaml`

As it is creating and configuring the service, Service Management outputs some information to the console. You can safely ignore the warnings about the paths in `openapi.yaml` not requiring an API key. On successful completion, you see a line like the following that displays the service configuration ID and the service name:

```bash
Service Configuration [2017-02-13-r2] uploaded for service [airports-api.endpoints.example-project.cloud.goog]
```

## Deploying the API backend
So far you have deployed the OpenAPI configuration to Service Management, but you have not yet deployed the code that will serve the API backend. The `deploy_app.sh` script included in the lab sample creates an App Engine flexible environment to host the API backend, and then the script deploys the API to App Engine.

To deploy the API backend, make sure you are in the endpoints-quickstart/scripts directory. Then, run the following script:

```bash
./deploy_app.sh
```

The script runs the following command to create an App Engine flexible environment in the us-central region: `gcloud app create --region="$REGION"`

It takes a couple minutes to create the App Engine flexible backend.

> If you get an `ERROR: NOT_FOUND: Unable to retrieve P4SA: from GAIA` message, rerun the `deploy_app.sh` script.

You'll see the following displayed in Cloud Shell after the App Engine is created:

```bash
Success! The app is now created. Please use `gcloud app deploy` to deploy your first app.
```

The script goes on to run the gcloud app deploy command to deploy the sample API to App Engine.

You'll then see a line like the following in Cloud Shell:

```bash
Deploying ../app/app_template.yaml...You are about to deploy the following services:
```

It takes several minutes for the API to be deployed to App Engine. You'll see a line like the following when the API is successfully deployed to App Engine:

```bash
Deployed service [default] to [https://example-project.appspot.com]
```

## Sending requests to the API
After deploying the sample API, you can send requests to it by running the following script:

```bash
./query_api.sh
```

The script echoes the curl command that it uses to send a request to the API, and then displays the result. You'll see something like the following in Cloud Shell:

```bash
curl "https://example-project.appspot.com/airportName?iataCode=SFO"
San Francisco International Airport
```

The API expects one query parameter, iataCode, that is set to a valid IATA airport code such as SEA or JFK.

To test, run this example in Cloud Shell:

```bash
./query_api.sh JFK
```

## Tracking API activity
With APIs deployed with Cloud Endpoints, you can monitor critical operations metrics in the Cloud Console and gain insight into your users and usage with Cloud Logging.

Run this traffic generation script in Cloud Shell to populate the graphs and logs.

```bash
./generate_traffic.sh
```

> Note: This script generates requests in a loop and automatically times out in 5 minutes. To end the script sooner, enter Ctrl-c in Cloud Shell.

In the Console, click on Endpoints in the Tools section to look at the activity graphs for your API. It may take a few moments for the requests to be reflected in the graphs. You can do this while you wait for data to be displayed:

* If the Permissions side panel is not open, click +Permissions. The Permissions panel allows you to control who has access to your API and the level of access.

* Click the Deployment history tab. This tab displays a history of your API deployments, including the deployment time and who deployed the change.

* Click the Overview tab. Here you'll see the traffic coming in. After the traffic generation script has been running for a minute, scroll down to see the three lines on the Total latency graph (50th, 95th, and 98th percentiles). This data provides a quick estimate of response times.

At the bottom of the Endpoints graphs, under Method, click the View all logs link for GET/airportName. The Logs Viewer page displays the request logs for the API.

Enter `Ctrl-C` in Cloud Shell to stop the script.

## Add a quota to the API
> Note: This is a beta release of Quotas. This feature might be changed in backward-incompatible ways and is not subject to any SLA or deprecation policy.

Cloud Endpoints lets you set quotas so you can control the rate at which applications can call your API. Quotas can be used to protect your API from excessive usage by a single client.

1. Deploy the Endpoints configuration that has a quota:

```bash
./deploy_api.sh ../openapi_with_ratelimit.yaml
```

2. Redeploy your app to use the new Endpoints configuration (this may take a few minutes):

```bash
./deploy_app.sh
```

3. In the Console, navigate to Navigation menu > API & Services > Credentials.

4. Click Create credentials and choose API key. A new API key is displayed on the screen.

5. Click the double rectangle icon to copy it to your clipboard.

6. In Cloud Shell, type the following. Replace YOUR-API-KEY with the API key you just created:

```bash
export API_KEY=YOUR-API-KEY
```

7. Send your API a request using the API key variable you just created:
```bash
./query_api_with_key.sh $API_KEY
```

8. You'll see something like the following on the console:
```bash
curl -H 'x-api-key: AIzeSyDbdQdaSdhPMdiAuddd_FALbY7JevoMzAB' "https://example-project.appspot.com/airportName?iataCode=SFO"
San Francisco International Airport
```

9. The API now has a limit of 5 requests per second. Run the following command to send traffic to the API and trigger the quota limit:
```bash
./generate_traffic_with_key.sh $API_KEY
```

10. After running the script for 5-10 seconds, enter Ctrl-c in Cloud Shell to stop the script.

11. Send another authenticated request to the API:
```bash
./query_api_with_key.sh $API_KEY
```
You'll see something like the following on the console:

```bash
{
    "code": 8,
    "message": "Quota exceeded for quota metric 'airport_requests' and limit 'limit-on-airport-requests' of service 'example-project.appspot.com' for consumer 'api_key:AIzeSyDbdQdaSdhPMdiAuddd_FALbY7JevoMzAB'.",
    "details": [
    {
    "@type": "type.googleapis.com/google.rpc.DebugInfo",
    "stackEntries": [],
    "detail": "internal"
    }
    ]
}
```

If you get a different response, try running the `generate_traffic_with_key.sh` script again and retry. 

----
# Lab 05-02: Google Cloud Pub/Sub: Qwik Start - Python

## Overview
The Google Cloud Pub/Sub service allows applications to exchange messages reliably, quickly, and asynchronously. To accomplish this, a data producer publishes messages to a Cloud Pub/Sub topic. A subscriber client then creates a subscription to that topic and consumes messages from the subscription. Cloud Pub/Sub persists messages that could not be delivered reliably for up to seven days.

## Create a virtual environment
Python virtual environments are used to isolate package installation from the system.

```bash
apt-get install -y virtualenv
```

> If prompted [Y/n], press Y and then Enter. If you get Permission denied error, then run the command `sudo apt-get install -y virtualenv`

```bash
python3 -m venv venv
```

Activate the virtual environment.

```bash
source venv/bin/activate
```

## Install the client library
Run the following to install the client library:

```bash
pip install --upgrade google-cloud-pubsub
```

Get the sample code by cloning a GitHub repository:

```bash
git clone https://github.com/googleapis/python-pubsub.git
```

Navigate to the directory:

```bash
cd python-pubsub/samples/snippets
```

## Pub/Sub - the Basics
Google Cloud Pub/Sub is an asynchronous global messaging service. There are three terms in Pub/Sub that appear often: topics, publishing, and subscribing.

A topic is a shared string that allows applications to connect with one another through a common thread.

Publishers push (or publish) a message to a Cloud Pub/Sub topic. Subscribers will then make a subscription to that thread, where they will either pull messages from the topic or configure webhooks for push subscriptions. Every subscriber must acknowledge each message within a configurable window of time.

In sum, a publisher creates and sends messages to a topic and a subscriber creates a subscription to a topic to receive messages from it.

### Pub/Sub in Google CLoud
Pub/Sub comes preinstalled in the Cloud Shell, so there are no installations or configurations required to get started with this service. In this lab you use Python to create the topic, subscriber, and then view the message. You use a gcloud command to publish the message to the topic.

## Create a topic
To publish data to Cloud Pub/Sub you create a topic and then configure a publisher to the topic.

In Cloud Shell, your Project ID should automatically be stored in the environment variable GOOGLE_CLOUD_PROJECT:

```bash
echo $GOOGLE_CLOUD_PROJECT
```

Ensure the output is the same as the Project ID in your CONNECTION DETAILS.

publisher.py is a script that demonstrates how to perform basic operations on topics with the Cloud Pub/Sub API. View the content of publisher script:

```bash
cat publisher.py
```

> Note: Alternatively, you can use the shell editors that are installed on Cloud Shell, such as nano or vim or use the Cloud Shell code editor to view python-pubsub/samples/snippets/publisher.py

For information about the publisher script:

```bash
python publisher.py -h
```

Run the publisher script to create Pub/Sub Topic:

```bash
python publisher.py $GOOGLE_CLOUD_PROJECT create MyTopic
```

This command returns a list of all Pub/Sub topics in a given project:

```bash
python publisher.py $GOOGLE_CLOUD_PROJECT list
```

You can also view the topic you just made in the Cloud Console.

Navigate to Navigation menu > Pub/Sub > Topics.

You should see MyTopic.

## Create a subscription
Create a Pub/Sub subscription for topic with subscriber.py script:

```bash
python subscriber.py $GOOGLE_CLOUD_PROJECT create MyTopic MySub
```

This command returns a list of subscribers in given project:

```bash
python subscriber.py $GOOGLE_CLOUD_PROJECT list-in-project
```

You'll see only one subscription because you've made only one subscription.

Check out the subscription you just made in the console. In the left pane, click Subscriptions. You should see the subscription name and other details.

For information about the `subscriber` script:

```bash
python subscriber.py -h
```

## Publish messages
Now that you've set up MyTopic (the topic), a subscription to MyTopic (MySub), see if you can use gcloud commands to publish a message to MyTopic.

Publish the message "Hello" to MyTopic:

```bash
gcloud pubsub topics publish MyTopic --message "Hello"
```

Publish a few more messages to MyTopic—run the following commands (replacing <YOUR NAME> with your name and <FOOD> with a food you like to eat):

```bash
gcloud pubsub topics publish MyTopic --message "Publisher's name is <YOUR NAME>"
```

```bash
gcloud pubsub topics publish MyTopic --message "Publisher likes to eat <FOOD>"
```

```bash
gcloud pubsub topics publish MyTopic --message "Publisher thinks Pub/Sub is awesome"
```

## View messages
Now that you've published messages to MyTopic, pull and view the messages using MySub.

Use MySub to pull the message from MyTopic:

```bash
python subscriber.py $GOOGLE_CLOUD_PROJECT receive MySub
```

Click Ctrl+c to stop listening.

## Test your understanding
1. Google Cloud Pub/Sub service allows applications to exchange messages reliably, quickly, and asynchronously.
- [x] True
- [ ] False

2. A _____ is a shared string that allows applications to connect with one another.
- [ ] subscription
- [ ] message
- [x] topic