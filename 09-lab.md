#### Google Cloud Computing Foundations | C04 Data, ML, and AI in Google Cloud
#### M09: You Have the Data, but What Are You Doing with It?

# Lab 09-01: Dataproc: Qwik Start-Console

## Overview 
Cloud Dataproc is a fast, easy-to-use, fully-managed cloud service for running Apache Spark and Apache Hadoop clusters in a simpler, more cost-efficient way. Operations that used to take hours or days take seconds or minutes instead. Create Cloud Dataproc clusters quickly and resize them at any time, so you don't have to worry about your data pipelines outgrowing your clusters.

This lab shows you how to use the Google Cloud Console to create a Google Cloud Dataproc cluster, run a simple Apache Spark job in the cluster, then modify the number of workers in the cluster.

## Create a cluster
In the Cloud Platform Console, select Navigation menu > Dataproc > Clusters, then click Create cluster.

Click Create for Cluster on Compute Engine.

Set the following fields for your cluster. Accept the default values for all other fields.

| Field | Value |
| ---- | ---- |
| Name | example-cluster |
| Region | us-central1 |
| Zone | us-central1-a |

> Note: A Zone is a special multi-region namespace that is capable of deploying instances into all Google Compute zones globally. You can also specify distinct regions, such as `us-east1` or `europe-west1`, to isolate resources (including VM instances and Cloud Storage) and metadata storage locations utilized by Cloud Dataproc within the user-specified region.

Click Create to create the cluster.

Your new cluster will appear in the Clusters list. It may take a few minutes to create, the cluster Status shows as Provisioning until the cluster is ready to use, then changes to Running.

## Submit a job
To run a sample Spark job:

Click Jobs in the left pane to switch to Dataproc's jobs view, then click Submit job:

Set the following fields to update Job. Accept the default values for all other fields.

| Field | Value |
| ---- | ---- |
| Cluster | example-cluster |
| Job type | Spark |
| Main class or jar | org.apache.spark.examples.SparkPi |
| Jar files | file:///usr/lib/spark/examples/jars/spark-examples.jar |
| Arguments	| 1000 (This sets the number of tasks.) |

Click Submit.

> How the job calculates Pi: The Spark job estimates a value of Pi using the Monte Carlo method. It generates x,y points on a coordinate plane that models a circle enclosed by a unit square. The input argument (1000) determines the number of x,y pairs to generate; the more pairs generated, the greater the accuracy of the estimation. This estimation leverages Cloud Dataproc worker nodes to parallelize the computation. For more information, see Estimating Pi using the Monte Carlo Method and see JavaSparkPi.java on GitHub.

Your job should appear in the Jobs list, which shows your project's jobs with its cluster, type, and current status. Job status displays as Running, and then Succeeded after it completes.

## View the job output
To see your completed job's output:

Click the job ID in the Jobs list.

Check Line wrapping or scroll all the way to the right to see the calculated value of Pi. Your output, with Line wrapping checked, should look something like this:

Your job has successfully calculated a rough value for pi!

### Update a cluster
To change the number of worker instances in your cluster:

1. Select Clusters in the left navigation pane to return to the Dataproc Clusters view.

2. Click example-cluster in the Clusters list. By default, the page displays an overview of your cluster's CPU usage.

3. Click Configuration to display your cluster's current settings.
Configuration-details.png

4. Click Edit. The number of worker nodes is now editable.

5. Enter 4 in the Worker nodes field.

6. Click Save.

Your cluster is now updated. Check out the number of VM instances in the cluster:

To rerun the job with the updated cluster, you would click Jobs in the left pane, then click SUBMIT JOB.

Set the same fields you set in the Submit a job section:

| Field | Value |
| ---- | ---- |
| Cluster | example-cluster |
| Job type | Spark |
| Main class or jar | org.apache.spark.examples.SparkPi |
| Jar files | file:///usr/lib/spark/examples/jars/spark-examples.jar |
| Arguments | 1000 (This sets the number of tasks.) |

Click Submit.

## Test your understanding
1. Which type of Dataproc job is submitted in the lab?
- [ ] Hadoop
- [ ] SparkSql
- [ ] PySpark
- [ ] Pig
- [ ] Spark

2. Dataproc helps users process, transform and understand vast quantities of data.
- [ ] True
- [ ] False

----
# Lab 09-02: Dataproc: Qwik Start - Command Line

## Overview
Cloud Dataproc is a fast, easy-to-use, fully-managed cloud service for running Apache Spark and Apache Hadoop clusters in a simpler, more cost-efficient way. Operations that used to take hours or days take seconds or minutes instead. Create Cloud Dataproc clusters quickly and resize them at any time, so you don't have to worry about your data pipelines outgrowing your clusters.

This lab shows you how to use gcloud on the Google Cloud to create a Google Cloud Dataproc cluster, run a simple Apache Spark job in the cluster, then modify the number of workers in the cluster.

## Create a cluster
In Cloud Shell, run the following command to set the Region:

```bash
gcloud config set dataproc/region us-central1
```

Run the following command to create a cluster called example-cluster with default Cloud Dataproc settings:

```bash
gcloud dataproc clusters create example-cluster --worker-boot-disk-size 500
```

If asked to confirm a zone for you cluster. Enter Y.

Your cluster will build for a couple of minutes.

```bash
Waiting for cluster creation operation...done.
Created [... example-cluster]
```

When you see a "Created" message, you're ready to move on.

## Submit a job
Run this command to submit a sample Spark job that calculates a rough value for pi:

```bash
gcloud dataproc jobs submit spark --cluster example-cluster \
  --class org.apache.spark.examples.SparkPi \
  --jars file:///usr/lib/spark/examples/jars/spark-examples.jar -- 1000
```

The command specifies:

- That you want to run a spark job on the example-cluster cluster
- The class containing the main method for the job's pi-calculating application
- The location of the jar file containing your job's code
- The parameters you want to pass to the job—in this case, the number of tasks, which is 1000
> Parameters passed to the job must follow a double dash (--). See the gcloud documentation for more information.

The job's running and final output is displayed in the terminal window:

```bash
Waiting for job output...
...
Pi is roughly 3.14118528
...
state: FINISHED
```

## Update a cluster
To change the number of workers in the cluster to four, run the following command:
```bash
gcloud dataproc clusters update example-cluster --num-workers 4
```

Your cluster's updated details are displayed in the command's output:
```bash
Waiting on operation [projects/qwiklabs-gcp-7f7aa0829e65200f/regions/global/operations/b86892cc-e71d-4e7b-aa5e-6030c945ea67].
Waiting for cluster update operation...done.
```

You can use the same command to decrease the number of worker nodes:
```bash
gcloud dataproc clusters update example-cluster --num-workers 2
```

Now you can create a Dataproc cluster and adjust the number of workers from the gcloud command line on the Google Cloud.

## Test your Understanding
1. Clusters can be created and scaled quickly with a variety of virtual machine types, disk sizes, and number of nodes.
- [ ] True
- [ ] False

----
# Lab 09-03: Dataflow: Qwik Start - Templates

## Overview
In this lab, you will learn how to create a streaming pipeline using one of Google's Cloud Dataflow templates. More specifically, you will use the Cloud Pub/Sub to BigQuery template, which reads messages written in JSON from a Pub/Sub topic and pushes them to a BigQuery table. You can find the documentation for this template here.

You'll be given the option to use the Cloud Shell command line or the Cloud Console to create the BigQuery dataset and table. Pick one method to use, then continue with that method for the rest of the lab. If you want experience using both methods, run through this lab a second time.

## Create a Cloud BigQuery Dataset and Table Using Cloud Shell
Let's first create a BigQuery dataset and table.

> Note: This section uses the bq command-line tool. Skip down if you want to run through this lab using the console.
Run the following command to create a dataset called taxirides:

```bash
bq mk taxirides
```

Your output should look similar to:
```bash
Dataset '<myprojectid:taxirides>' successfully created
```

Now that you have your dataset created, you'll use it in the following step to instantiate a BigQuery table. Run the following command to do so:
```bash
bq mk \
--time_partitioning_field timestamp \
--schema ride_id:string,point_idx:integer,latitude:float,longitude:float,\
timestamp:timestamp,meter_reading:float,meter_increment:float,ride_status:string,\
passenger_count:integer -t taxirides.realtime
```

Your output should look similar to:
```bash
Table 'myprojectid:taxirides.realtime' successfully created
```

On it's face, the bq mk command looks a bit complicated. However, with some assistance from the BigQuery command-line documentation, we can break down what's going on here. For example, the documentation tells us a little bit more about schema:

- Either the path to a local JSON schema file or a comma-separated list of column definitions in the form [FIELD]:[DATA_TYPE], [FIELD]:[DATA_TYPE].

In this case, we are using the latter—a comma-separated list.

### Create a storage bucket
Now that we have our table instantiated, let's create a bucket. Run the following commands to do so:
```bash
export BUCKET_NAME=<your-unique-name>
```
```bash
gsutil mb gs://$BUCKET_NAME/
```

Once you've made your bucket, scroll down to the Run the Pipeline section.

## Create a Cloud BigQuery Dataset and Table Using the Cloud Console
> Note: Don't go through this section if you've done the command-line setup!

From the left-hand menu, in the Big Data section, click on BigQuery. Then click Done.

Click on the three dots next to your project name under Explorer section, then click Create dataset.

1. Input taxirides as your dataset ID:

2. Select us (multiple regions in United States) in Data location.

Leave all of the other default settings in place and click CREATE DATASET.

You should now see the taxirides dataset underneath your project ID in the left-hand console.

Click on the three dots next to taxirides dataset and select Open. Then select CREATE TABLE in the right-hand side of the console.

In the Destination > Table Name input, enter realtime.

Under Schema, toggle the Edit as text slider and enter the following:

```
ride_id:string,point_idx:integer,latitude:float,longitude:float,timestamp:timestamp,
meter_reading:float,meter_increment:float,ride_status:string,passenger_count:integer
```

Your console should look like the following:

Now, click Create table.

### Create a storage bucket
Go back to the Cloud Console and navigate to Cloud Storage > Browser > Create bucket:

Give your bucket a unique name. Leave all other default settings, then click Create.

## Run the Pipeline
From the Navigation menu, find the Analytics section and click on Dataflow.

Click on + Create job from template at the top of the screen.

Enter iotflow as Job name for your Cloud Dataflow job.

Under Dataflow Template, select the Pub/Sub Topic to BigQuery template.

Under Input Pub/Sub topic, enter:
```
projects/pubsub-public-data/topics/taxirides-realtime
```

Under BigQuery output table, enter the name of the table that was created:
```
<myprojectid>:taxirides.realtime
```

Add your bucket as Temporary Location:
```bash
gs://Your_Bucket_Name/temp
```

Click the Run job button.

You'll watch your resources build and become ready for use.

Now, let's go view the data written to BigQuery by clicking on BigQuery found in the Navigation menu.

When the BigQuery UI opens, you'll see the taxirides dataset added under your project name and realtime table underneath that:

## Submit a query
You can submit queries using standard SQL.

In the BigQuery Editor field add the following, replacing myprojectid with the Project ID from the Qwiklabs page:
```sql
SELECT * FROM `myprojectid.taxirides.realtime` LIMIT 1000
```

Now click RUN.

If you run into any issues or errors, run the query again (the pipeline takes a minute to start up.)

When the query runs successfully, you'll see the output in the Query Results panel as shown below:

Great work! You just pulled 1000 taxi rides from a Pub/Sub topic and pushed them to a BigQuery table. As you saw firsthand, templates are a practical, easy-to-use way to run Dataflow jobs. Be sure to check out some other Google Templates here.

## Test your Understanding
1. Google Cloud Dataflow supports batch processing.
- [ ] True
- [ ] False

2. Which Dataflow Template used in the lab to run the pipeline?
- [ ] Pub/Sub to BigQuery
- [ ] Bulk Compress Cloud Storage Files
- [ ] Cloud Storage Text to BigQuery

----
# Lab 09-04: Dataflow: Qwik Start - Python

## Overview 
In this lab you will set up your Python development environment, get the Cloud Dataflow SDK for Python, and run an example pipeline using the Cloud Console.

## Create a Cloud Storage bucket
1. In the Cloud Console, click on Navigation menu and then click on Cloud Storage.

2. Click Create bucket.

3. In the Create bucket dialog, specify the following attributes:

- Name: A unique bucket name. Do not include sensitive information in the bucket name, as the bucket namespace is global and publicly visible.
- Location type: Region
- Location: us-central1
- A location where bucket data will be stored.

4. Click Create.

### Install pip and the Cloud Dataflow SDK
1. The Cloud Dataflow SDK for Python requires Python version 3.7.
To ensure you are running the process with the correct version, run the Python3.7 Docker Image:
```bash
docker run -it -e DEVSHELL_PROJECT_ID=$DEVSHELL_PROJECT_ID python:3.7 /bin/bash
```
This command pulls a Docker container with the latest stable version of Python 3.7 and then opens up a command shell for you to run the following commands inside your container.

2. After the container is running, install the latest version of the Apache Beam for Python by running the following command from a virtual environment:
```bash
pip install apache-beam[gcp]
```

You will see some warnings returned that are related to dependencies. It is safe to ignore them for this lab.

3. Run the wordcount.py example locally by running the following command:
```bash
python -m apache_beam.examples.wordcount --output OUTPUT_FILE
```
> Note: You installed google-cloud-dataflow but are executing wordcount with Apache_beam. The reason for this is that Cloud Dataflow is a distribution of Apache Beam

You may see a message similar to the following:
```bash
INFO:root:Missing pipeline option (runner). Executing pipeline using the default runner: DirectRunner.
INFO:oauth2client.client:Attempting refresh to obtain initial access_token
This message can be ignored.
```

You can now list the files that are on your local cloud environment to get the name of the OUTPUT_FILE:
```bash
ls
```

Copy the name of the OUTPUT_FILE and cat into it:
```bash
cat <file name>
```

Your results show each word in the file and how many times it appears.

### Run an Example Pipeline Remotely
Set the BUCKET environment variable to the bucket you created earlier.
```bash
BUCKET=gs://<bucket name provided earlier>
```

Now you'll run the wordcount.py example remotely:
```bash
python -m apache_beam.examples.wordcount --project $DEVSHELL_PROJECT_ID \
  --runner DataflowRunner \
  --staging_location $BUCKET/staging \
  --temp_location $BUCKET/temp \
  --output $BUCKET/results/output \
  --region us-central1
```

In your output, wait until you see the message:
```bash
JOB_MESSAGE_DETAILED: Workers have started successfully.
```

Then continue with the lab.

## Check that your job succeeded
Open the navigation menu and select Dataflow from the list of services:

You should see your wordcount job with a status of Running at first.

Click on the name to watch the process. When all the boxes are checked off, you can continue watching the logs in Cloud Shell.

The process is complete when the status is Succeeded:

Click on Cloud Storage in the Console.

Click on the name of your bucket. In your bucket, you should see the results and staging directories:

Click on the results folder and you should see the output files that your job created:

Click on a file to see the word counts it contains.

## Test your Understanding
1. Dataflow temp_location must be a valid Cloud Storage URL.
- [ ] True
- [ ] False

----
# Lab 09-05: Dataprep: Qwik Start

## Overview 
Cloud Dataprep by Trifacta is an intelligent data service for visually exploring, cleaning, and preparing data for analysis. Cloud Dataprep is serverless and works at any scale. There is no infrastructure to deploy or manage. Easy data preparation with clicks and no code!

In this lab you use Dataprep to manipulate a dataset. You import datasets, correct mismatched data, transform data, and join data. If this is new to you, you'll know what it all is by the end of this lab.

## Create a Cloud Storage bucket in your project
1. In the Cloud Console, select Navigation menu > Cloud Storage > Browser.

2. Click Create bucket.

3. In the Create a bucket dialog, Name the bucket a unique name. Leave other settings at their default value.
my-bucket.png

> For more information about naming buckets, see bucketname requirements.

4. Click Create.
You created your bucket. Remember the bucket name for later steps.

## Initialize Cloud Dataprep
1. Select Navigation menu > Dataprep.

2. Check to accept the Google Dataprep Terms of Service, then click Accept.

3. Check to authorize sharing your account information with Trifacta, then click Agree and Continue.

4. Click Allow to allow Trifacta to access project data.

5. Click your student username to sign in to Cloud Dataprep by Trifacta. Your username is the Username in the left panel in your lab.

6. Click Allow to grant Cloud Dataprep access to your Google Cloud lab account.

7. Check to agree to Trifacta Terms of Service, and then click Accept.

8. Click Continue on the "First time set up" screen to create the default storage location.

Dataprep opens.

Click on the Dataprep icon on the top left corner to go to the home screen.

## Create a flow
Cloud Dataprep uses a flow workspace to access and manipulate datasets.

1. Click Flows icon, then the Create button, then select Blank Flow :

2. Click on Untitled Flow, then name and describe the flow. Since this lab uses 2016 data from the United States Federal Elections Commission 2016, name the flow "FEC-2016", and then describe the flow as "United States Federal Elections Commission 2016".

3. Click OK.

The FEC-2016 flow page opens.

## Import datasets
In this section you import and add data to the FEC-2016 flow.

1. Click Add Datasets, then select the Import Datasets link.

2. In the left menu pane, select Cloud Storage to import datasets from Cloud Storage, then click on the pencil to edit the file path.
dataprep_choose_file1.png

3. Type gs://spls/gsp105 in the Choose a file or folder text box, then click Go.

You may have to widen the browser window to see the Go and Cancel buttons.

4. Click us-fec/.

5. Click the + icon next to cn-2016.txt to create a dataset shown in the right pane. Click on the title in the dataset in the right pane and rename it "Candidate Master 2016".

6. In the same way add the itcont-2016-orig.txt dataset, and rename it "Campaign Contributions 2016".

7. Both datasets are listed in the right pane; click Import & Add to Flow.

You see both datasets listed as a flow.

## Prep the candidate file
1. By default, the Candidate Master 2016 dataset is selected. In the right pane, click Edit Recipe.

The Candidate Master 2016 Transformer page opens in the grid view.

The Transformer page is where you build your transformation recipe and see the results applied to the sample. When you are satisfied with what you see, execute the job against your dataset.

Each of the column heads have a Name and value that specify the data type. Data types are shown when you click the column icon:

Also, when you click the name of the column, a Details panel opens on the right:

Click X in the top right of the Details panel to close the Details panel.

In the following steps you will explore data in the grid view and apply transformation steps to your recipe.

2. Column5 provides data from 1990-2064. Widen column5 (like you would on a spreadsheet) to separate each year. Click to select the tallest bin, which represents year 2016.

This creates a step where these values are selected.

3. In the Suggestions panel on the right, in the Keep rows section, click Add to add this step to your recipe.

The Recipe panel on the right now has the following step:

Keep rows where(DATE(2016, 1, 1) <= column5) && (column5 < DATE(2018, 1, 1))

4. In Column6 (State), hover over and click on the mismatched (red) portion of the header to select the mismatched rows.

Scroll down to the bottom (highlighted in red) find the mismatched values and notice how most of these records have the value "P" in column7, and "US" in column6. The mismatch occurs because column6 is marked as a "State" column (indicated by the flag icon), but there are non-state (such as "US") values.

5. To correct the mismatch, click X in the top of the Suggestions panel to cancel the transformation, then click on the flag icon in Column6 and change it to a "String" column.

There is no longer a mismatch and the column marker is now green.

6. Filter on just the presidential candidates, which are those records that have the value "P" in column7. In the histogram for column7, hover over the two bins to see which is "H" and which is "P". Click the "P" bin.
328626b128b93f1.png

7. In the right Suggestions panel, click Add to accept the step to the recipe.

## Wrangle the Contributions file and join it in
On the Join page, you can add your current dataset to another dataset or recipe based on information that is common to both datasets.

Before you join the Contributions file to the Candidates file, clean up the Contributions file.

1. Click on FEC-2016 (the dataset selector) at the top of the grid view page.

2. Click to select the grayed out Campaign Contributions 2016.

3. In the right pane, click Add > Recipe, then click Edit Recipe.

4. Click the recipe icon at the top right of the page, then click Add New Step.

Remove extra delimiters in the dataset.

5. Insert the following Wrangle language command in the Search box:
```wrangle
replacepatterns col: * with: '' on: `{start}"|"{end}` global: true
```

The Transformation Builder parses the Wrangle command and populates the Find and Replace transformation fields.

6. Click Add to add the transform to the recipe.

7. Add another new step to the recipe. Click New Step, then type "Join" in the Search box.

8. Click Join datasets to open the Joins page.

9. Click on "Candidate Master 2016" to join with Campaign Contributions 2016, then Accept in the bottom right.

10. On the right side, hover in the Join keys section, then click on the pencil (Edit icon).
edit_join.png

Dataprep infers common keys. There are many common values that Dataprep suggests as Join Keys.

11. In the Add Key panel, in the Suggested join keys section, click column2 = column11.
join_conditions.png

12. Click Save and Continue.

Columns 2 and 11 open for your review.

13. Click Next, then check the checkbox to the left of the "Column" label to add all columns of both datasets to the joined dataset.

14. Click Review, and then Add to Recipe to return to the grid view.

## Summary of data
Generate a useful summary by aggregating, averaging, and counting the contributions in Column 16 and grouping the candidates by IDs, names, and party affiliation in Columns 2, 24, 8 respectively.

1. At the top of the Recipe panel on the right, click on New Step and enter the following formula in the Transformation search box to preview the aggregated data.

```wrangle
pivot value:sum(column16),average(column16),countif(column16 > 0) group: column2,column24,column8
```
An initial sample of the joined and aggregated data is displayed, representing a summary table of US presidential candidates and their 2016 campaign contribution metrics.

2. Click Add to open a summary table of major US presidential candidates and their 2016 campaign contribution metrics.

## Rename columns
You can make the data easier to interpret by renaming the columns. Add each of the renaming and rounding steps individually to the recipe by clicking New Step, then enter:

```wrangle
rename type: manual mapping: [column24,'Candidate_Name'], [column2,'Candidate_ID'],[column8,'Party_Affiliation'], [sum_column16,'Total_Contribution_Sum'], [average_column16,'Average_Contribution_Sum'], [countif,'Number_of_Contributions']
```
Then click Add.

Add in this last New Step to round the Average Contribution amount:

```wrangle
set col: Average_Contribution_Sum value: round(Average_Contribution_Sum)
```
Then click Add.

Your results look something like this:

