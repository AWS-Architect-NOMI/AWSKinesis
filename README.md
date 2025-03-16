Introduction

The rise of IoT devices has resulted in a massive influx of real-time data that needs efficient ingestion, transformation, storage, and visualization. This project demonstrates how to build a scalable, real-time IoT data pipeline using AWS services to collect, process, analyze, and visualize IoT-generated data for business insights.

Architecture Overview

AWS Kinesis Data Streams – Collects real-time IoT data from sensors and devices.

Amazon S3 – Stores raw IoT data.

AWS Glue – Transforms and processes the raw data.

Amazon Redshift – Stores structured data optimized for analytics.

Amazon QuickSight – Visualizes the processed data for decision-making.

STAR Methodology Implementation

Situation:

A smart manufacturing company needs a robust, real-time pipeline to ingest sensor data from industrial machines, analyze performance metrics, and detect anomalies to optimize maintenance schedules and improve efficiency.

Task:

Implement a scalable IoT data pipeline that ensures efficient ingestion, transformation, storage, and visualization, enabling stakeholders to monitor and analyze operational metrics dynamically.

Action:

Create a Kinesis Data Stream for Real-Time Data Ingestion:

aws kinesis create-stream --stream-name IoTDataStream --shard-count 1

Configure IoT devices to send data to the Kinesis stream using the AWS SDK.

Write a Python Script to Stream IoT Data:

import boto3
import json
kinesis = boto3.client('kinesis')
data = {'temperature': 25.6, 'humidity': 70, 'device_id': 'sensor-1'}
response = kinesis.put_record(
    StreamName='IoTDataStream',
    Data=json.dumps(data),
    PartitionKey='sensor-1'
)

Configure Amazon Kinesis Firehose to Store Data in S3:

Use AWS Firehose to automatically write incoming data into an S3 bucket for further processing.

aws firehose create-delivery-stream --delivery-stream-name IoTStreamToS3 --s3-destination-configuration BucketARN=arn:aws:s3:::iot-data-bucket

Transform Raw Data Using AWS Glue:

Create an AWS Glue Job to extract and transform IoT data.

import sys
from awsglue.transforms import *
from awsglue.utils import getResolvedOptions
from pyspark.context import SparkContext
from awsglue.context import GlueContext
from awsglue.job import Job

glueContext = GlueContext(SparkContext())
datasource0 = glueContext.create_dynamic_frame.from_catalog(database = "iot_db", table_name = "iot_data")
transformed_data = ApplyMapping.apply(frame = datasource0, mappings = [("device_id", "string", "device_id", "string"), ("temperature", "double", "temperature", "double"), ("humidity", "double", "humidity", "double")])
transformed_data.show()

Store structured data into Amazon Redshift.

Load Processed Data into Redshift:

aws redshift-data execute-statement --cluster-identifier my-cluster --database iotdb --sql "COPY iot_data FROM 's3://iot-data-bucket/transformed/' IAM_ROLE 'arn:aws:iam::account-id:role/RedshiftS3AccessRole' FORMAT AS PARQUET;"

Visualize Data in QuickSight:

Connect QuickSight to Amazon Redshift and create real-time dashboards showing temperature trends, device performance, and anomaly detection.

Result:

Achievement: Successfully built a real-time, end-to-end IoT data pipeline for predictive maintenance and performance monitoring.

Use Case: Helps businesses monitor critical metrics, reducing downtime and operational costs.

