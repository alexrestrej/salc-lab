# Amazon S3 and AWS Lambda Challenge

Link to course: https://cloudacademy.com/lab-challenge/amazon-s3-and-aws-lambda-challenge/session/?context_id=9637&context_resource=lp

Amazon S3 and AWS Lambda Challenge
 
Your Mission
A company has a legacy system that accepts CSV (comma separated values) file uploads. The company is preparing to migrate the system to the cloud.

The new cloud-based system will use an event-based trigger on an Amazon S3 bucket to run an AWS Lambda function and perform file processing. When a CSV file is uploaded, each line of the file is examined. The line is copied to a new file placed under a folder named after the first field in the line.

In the legacy system, a random identifier was assigned to each newly created file after processing. This identifier is six characters long. The legacy system stored separate processed files in a directory called "split". The new cloud-based system is expected to handle a large number of CSV file uploads in the future.

It has been decided to make the following changes:

Change the named of the folder storing separated files from split to output
Increase the number of characters in the random identifier from six to twelve
You have been tasked with the following:

Creating an Amazon S3 bucket
Implementing a Python AWS Lambda function using a pre-existing implementation
Modifying the function code to generate random identifiers that are 12 characters long
Testing the cloud-based system by uploading a test CSV file to ensure that the AWS Lambda function is triggered
In this challenge lab, an AWS Lambda function named s3-lambda with the default AWS Lambda implementation already exists.

Below is the CSV processing Python code you will need to use to complete the challenge:

Copy code
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
import io
import boto3
import string
import random

s3 = boto3.client("s3")
INPUT_PREFIX = "input"
OUTPUT_PREFIX = "split"
ID_LENGTH = 6


def random_id():
    return "".join(random.choices(string.ascii_uppercase + string.digits, k=ID_LENGTH))


def separate_object(bucket, key):
    body = s3.get_object(Bucket=bucket, Key=key)["Body"].read().decode("utf-8")
    output = {}
    for line in io.StringIO(body):
        fields = line.split(",")
        output.setdefault(fields[0], []).append(line)
    return output


def write_objects(objects, bucket, key):
    file_name = key.split("/")[-1]
    for prefix in objects.keys():
        identifier = random_id()
        s3.put_object(
            Body=",".join(objects[prefix]),
            Key=f"{OUTPUT_PREFIX}/{prefix}/{identifier}-{file_name}",
            Bucket=bucket,
        )


def lambda_handler(event, context):
    record = event["Records"][0]["s3"]
    bucket = record["bucket"]["name"]
    key = record["object"]["key"]

    if key.startswith(INPUT_PREFIX):
        objects = separate_object(bucket, key)
        write_objects(objects, bucket, key)

    return "OK"
You have also been given a sample CSV to test the system with:

Copy code
high,data1,10
medium,data2,20
low,data3,30
You can download a copy of this sample file here.

The following instructions prepare you to work on this challenge.

Instructions
To start the lab challenge, open the AWS Management Console by clicking the Open Environment button:

alt

Note: The challenge environment will take about one to two minutes to become ready. You will not be able to complete the challenge before it is ready.

Enter the following credentials created just for this lab, and click Sign In:

Account ID or alias: Keep the pre-populated value
IAM user name: student
Password: Ca1_EeRZExLP
To start the challenge, click Go to Validation Steps

Hints:

All tasks should be completed using the AWS Management Console
