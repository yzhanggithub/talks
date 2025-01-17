# Live Demo Commands

#### Table of Contents

<!-- TOC -->

- [LocalStack Demo](#localstack-demo)
- [Docker-Compose Walk-thru](#docker-compose-walk-thru)
- [Workflow #1: Interacting with S3](#workflow-1-interacting-with-s3)
- [Workflow #2: Working with DynamoDB](#workflow-2-working-with-dynamodb)
- [Workflow #3: Queueing with SQS](#workflow-3-queueing-with-sqs)

<!-- /TOC -->

## LocalStack Demo

```bash
# Go into directory
cd demo

# See running containers
docker ps

# Start LocalStack
docker run --rm -p 4566:4566 localstack/localstack

# Let’s use the AWS CLI to interact with localstack
# Let’s see what s3 buckets we have
aws --endpoint-url http://0.0.0.0:4566 s3 ls

# No buckets, so let’s create one
aws --endpoint-url http://0.0.0.0:4566 s3 mb s3://demo-bucket
aws --endpoint-url http://0.0.0.0:4566 s3 ls

# Introduce awslocal
# utility created by localstack to save us some time
(need commands)

# Now we’ll create a file
echo "Hello Python Web Conference" >> greeting.txt
cat greeting

# Upload to S3
awslocal s3 cp greeting.txt s3://demo-bucket/greeting.txt

# Show it’s there
awslocal s3 ls s3://demo-bucket

# Download file and show it’s there with an `ls` and `cat`
awslocal s3 cp s3://demo-bucket/greeting.txt ./new_greeting.txt
ls
cat new_greeting.txt

# And that’s s3, we’re just scratching the surface of what you can do with localstack.

# Close down localstack by stopping the container
```

## Docker-Compose Walk-thru

```bash
# show docker compose file and walk through how it's set up

# show resources

# Makefile
make up
```

## Workflow #1: Interacting with S3

```bash
# lets see what we have in s3
awslocal s3 ls
awslocal s3 ls example-bucket

# we'll start the python REPL
ipython
%run main.py

# create a file in an editor and save it

# lets use the function we looked at to upload this file
upload_file_to_s3("file_to_upload.txt", bucket="example-bucket", use_localstack=True)

# exit terminal
# lets see what we have in s3
awslocal s3 ls example-bucket
```

## Workflow #2: Working with DynamoDB

```bash
# create object
ipython
%run models.py
user = UserModel(email="alysivji@gmail.com", name_given="Aly", name_family="Sivji")
user.save()

# show item in DynamoDB
# open new terminal
npm  # since I load npm lazily
DYNAMO_ENDPOINT=http://0.0.0.0:4566 dynamodb-admin

user = UserModel(email="alysivji@gmail.com", name_given="Updated", name_family="Info")
user.save()
```

## Workflow #3: Queueing with SQS

```bash
# get queue URL
awslocal sqs list-queues

# create object
ipython
%run sender.py
message = {"event_type": "ping", "event_data": "test"}
send_message_to_queue(message, use_localstack=True)

# view message in console
awslocal sqs receive-message --queue-url http://localhost:4566/000000000000/inbound-queue
```
