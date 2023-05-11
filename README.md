# GSP311


	****************Automate Interactions with Contact Center AI: Challenge Lab******************************
              

-------------------------------------step 1-----------------------------
Task 1: Create a Cloud Storage Bucket


gsutil mb -l us-central1 gs://qwiklabs-gcp-04-ef0f8ad86166/

-------------------------------------step 2——————————————


Task 2: Create a Cloud Function


Go to the “saf-longrun-job-func” directory

cd dataflow-contact-center-speech-analysis/saf-longrun-job-func
gcloud functions deploy safLongRunJobFunc --runtime nodejs12 --trigger-resource a4177cd0-a01b-4d2d-b099-fb64ed25a04b --region us-central1 --trigger-event google.storage.object.finalize




 -------------------------------------step 3——————————————


Task 3: Create a BigQuery Dataset and Table
# To create the dataset
bq mk saf_574



 -------------------------------------step4-----------------------------


Task 4: Create Cloud Pub/Sub Topic
gcloud pubsub topics create saf-topic-290


 -------------------------------------step 5——————————————


Task 5: Create a Cloud Storage Bucket for Staging Contents
gsutil mb -l us-central1 gs://d05fe2cd-b8ab-49e6-9a09-306cf8a08532/
Create a folder “DFaudio” in the bucket

Need to do it manually

 -------------------------------------step 6——————————————


Task 6: Deploy a Cloud Dataflow Pipeline
Go to the cd saf-longrun-job-dataflow directory


python -m virtualenv env -p python3
source env/bin/activate
pip install apache-beam[gcp]
pip install dateparser


Now, execute the command given below:
python saflongrunjobdataflow.py \
--project=qwiklabs-gcp-04-ef0f8ad86166\
--region=us-central1\
--input_topic=projects/qwiklabs-gcp-04-ef0f8ad86166/topics/saf-topic-290\
--runner=DataflowRunner \
--temp_location=gs://a4177cd0-a01b-4d2d-b099-fb64ed25a04b/DFaudio-614\
--output_bigquery=qwiklabs-gcp-04-ef0f8ad86166:saf_574.transcripts \
--requirements_file=requirements.txt



 -------------------------------------step 7——————————————


Task 7: Upload Sample Audio Files for Processing
gsutil -h x-goog-meta-callid:1234567 -h x-goog-meta-stereo:false -h x-goog-meta-pubsubtopicname:saf-topic-290 -h x-goog-meta-year:2019 -h x-goog-meta-month:11 -h x-goog-meta-day:06 -h x-goog-meta-starttime:1116 cp gs://qwiklabs-bucket-gsp311/speech_commercial_mono.flac gs://a4177cd0-a01b-4d2d-b099-fb64ed25a04b


gsutil -h x-goog-meta-callid:1234567 -h x-goog-meta-stereo:true -h x-goog-meta-pubsubtopicname:saf-topic-290-h x-goog-meta-year:2019 -h x-goog-meta-month:11 -h x-goog-meta-day:06 -h x-goog-meta-starttime:1116 cp gs://qwiklabs-bucket-gsp311/speech_commercial_stereo.wav gs://a4177cd0-a01b-4d2d-b099-fb64ed25a04b


After performing Task 7: Upload Sample Audio Files for Processing, we have to wait....until we see output in bigquery > dataset > table.





Task 8: Run a Data Loss Prevention Job



select * from (SELECT entities.name,entities.type, COUNT(entities.name) AS count FROM dataset_name.transcripts, UNNEST(entities) entities GROUP BY entities.name, entities.type ORDER BY count ASC ) Where count > 5





—-----------------------------—-----------------END—----------------------------—-----------------------------
