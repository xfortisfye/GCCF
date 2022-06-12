#### Google Cloud Computing Foundations | C04 Data, ML, and AI in Google Cloud
#### M09-M10

# Perform Foundational Data, ML, and AI Tasks in Google Cloud: Challenge Lab

## Challenge scenario
As a junior data engineer in Jooli Inc. and recently trained with Google Cloud and a number of data services you have been asked to demonstrate your newly learned skills. The team has asked you to complete the following tasks.

You are expected to have the skills and knowledge for these tasks so don’t expect step-by-step guides.

### Task 1: Run a simple Dataflow job
You have used Dataflow in the quest to load data into BigQuery from Pub/Sub, now use the Dataflow batch template Text Files on Cloud Storage to BigQuery under "Process Data in Bulk (batch)" to transfer data from a Cloud Storage bucket (gs://cloud-training/gsp323/lab.csv). The following table has the values you need to correctly configure the Dataflow job.

You will need to make sure you have:

- Create a BigQuery dataset called `BigQuery Dataset Name` .
- Create a Cloud Storage Bucket called `Cloud Storage Bucket Name` .

| Field | Value |
| ---- | ---- |
| JavaScript UDF path in Cloud Storage | gs://cloud-training/gsp323/lab.js |
| JSON path | gs://cloud-training/gsp323/lab.schema |
| JavaScript UDF name | transform |
| BigQuery output table | Output Table Name |
| Cloud Storage input path | gs://cloud-training/gsp323/lab.csv |
| Temporary BigQuery directory | Temporary BigQuery Directory |
| Temporary location | Temporary Location |

### Task 2: Run a simple Dataproc job
You have used Dataproc in the quest, now you must run another example Spark job using Dataproc.

Before you run the job, log into one of the cluster nodes and copy the /data.txt file into hdfs (use the command hdfs dfs -cp gs://cloud-training/gsp323/data.txt /data.txt).

Run a Dataproc job using the values below.

| Field | Value |
| ---- | ---- |
| Region | Region |
| Job type | Spark |
| Main class or jar	| org.apache.spark.examples.SparkPageRank |
| Jar files | file:///usr/lib/spark/examples/jars/spark-examples.jar |
| Arguments | /data.txt |
| Max restarts per hour | 1 |

### Task 3: Run a simple Dataprep job
You have used Dataprep to import data files and transformed them to gain views of the data. Use Dataprep to import one CSV file (described below) that holds data of lab executions.

`gs://cloud-training/gsp323/runs.csv` structure:

| runid | userid | labid | lab_title | start | end | time | score | state |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 5556 | 545 | 122 | Lab 122 | 2020-04-09 | 16:18:19 | 2020-04-09 17:10:11 | 3112 | 61.25 | SUCCESS |
| 5557 | 116 | 165 | Lab 165 | 2020-04-09 | 16:44:45 | 2020-04-09 18:13:58 | 5353 | 60.5 | SUCCESS |
| 5558 | 969 | 31 | Lab 31 | 2020-04-09 | 17:59:01 | 2020-04-09 18:02:09 | 188 | 0 | FAILURE |

Perform the following transforms to ensure the data is in the right state:
- Remove all rows with the state of "FAILURE"
- Remove all rows with 0 or 0.0 as a score (Use the regex pattern `/(^0$|^0\.0$)/`)
- Label columns with the names above

### Task 4: AI
Complete one of the following tasks below.

- Use Google Cloud Speech API to analyze the audio file gs://cloud-training/gsp323/task4.flac. Once you have analyzed the file you can upload the resulting analysis to Cloud Speech Location .

- Use the Cloud Natural Language API to analyze the sentence from text about Odin. The text you need to analyze is "Old Norse texts portray Odin as one-eyed and long-bearded, frequently wielding a spear named Gungnir and wearing a cloak and a broad hat." Once you have analyzed the text you can upload the resulting analysis to Cloud Natural Language Location .

- Use Google Video Intelligence and detect all text on the video gs://spls/gsp154/video/train.mp4. Once you have completed the processing of the video, pipe the output into a file and upload to Video Intelligence Location . Ensure the progress of the operation is complete and the service account you're uploading the output with has the Storage Object Admin role.

> Note: If you are facing issues in any of the above task you can refer the respective labs for troubleshooting:
> 1. Google Cloud Speech API: Qwik Start
> 2. Cloud Natural Language API: Qwik Start
> 3. Video Intelligence: Qwik Start

# Answer

## Task 1: Run a simple Dataflow job
1. Navigation Menu > BigQuery
2. Under project, click three dots, create dataset
3. dataset ID: lab_181 > Create Dataset
4. Navigation Menu > Cloud Storage > Create Bucket
5. Name: 0eda2037-7a21-49f3-9c83-afd7d18b4d67 > Create
6. Navigation Menu > Dataflow > Create Job from Template

| Field | Value |
| ---- | ---- |
| Job Name | job1 |
| Region | us-east1 |
| Dataflow Template |Text Files on Cloud Storage to BigQuery (under "Process Data in Bulk (batch)") |
| ---- | ---- |
| JavaScript UDF path in Cloud Storage | gs://cloud-training/gsp323/lab.js |
| JSON path | gs://cloud-training/gsp323/lab.schema |
| JavaScript UDF name | transform |
| BigQuery output table | qwiklabs-gcp-04-69cf69e46287:lab_181.customers_826 |
| Cloud Storage input path | gs://cloud-training/gsp323/lab.csv |
| Temporary BigQuery directory | gs://0eda2037-7a21-49f3-9c83-afd7d18b4d67/bigquery_temp |
| Temporary location | gs://0eda2037-7a21-49f3-9c83-afd7d18b4d67/temp |

## Task 2: Run a simple Dataproc 
1. Navigation Menu > Dataproc > Clusters > Create Cluster
2. Region: us-east1 > Create
3. Click on the Cluster > VM Instances > SSH.
4. Run `hdfs dfs -cp gs://cloud-training/gsp323/data.txt /data.txt` in the SSH terminal.
5. Close SSH terminal, and Submit Job.
6. Fill up the following details

| Field | Value |
| ---- | ---- |
| Job type | Spark |
| Main class or jar	| org.apache.spark.examples.SparkPageRank |
| Jar files | file:///usr/lib/spark/examples/jars/spark-examples.jar |
| Arguments | /data.txt |
| Max restarts per hour | 1 |

7. Create

## Task 3: Run a simple Dataprep job
1. Navigation Menu > Dataprep > Agree non-stop.
2. Import Data > Cloud Storage > Pencil > Edit path to `gs://cloud-training/gsp323/runs.csv` > Add to New Flow > Continue
3. Add > Add Recipe > Edit Recipe > Continue
4. Click column10 > Delete rows from suggestion > Add
5. Click downward arrow beside column9 > Filter rows > On column value > Contains
6. Pattern to Match: `/(^0$|^0\.0$)/` > Action: Delete matching rows > 
7. Click downward arrow beside each respective column and rename each of them.
8. Run > Run

## Task 4: AI
1. Navigation Menu > APIs & Services > Create Credentials > API key > Copy API Key
2. At command line:

```bash
export API_KEY=<YOUR_API_KEY>
touch request.json
nano request.json
```

```json
{
  "config": {
      "encoding":"FLAC",
      "languageCode": "en-US"
  },
  "audio": {
      "uri":"gs://cloud-training/gsp323/task4.flac"
  }
}
```

3. Cltr X > Y > Enter

```bash
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json \
"https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}" > task4-gcs-206.result
```

4.
```bash
gsutil cp task4-gcs.result gs://qwiklabs-gcp-04-69cf69e46287-marking/task4-gcs-206.result
```