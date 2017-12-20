# Learning Path, Connecting applications and data with event driven processing :
products :
    - amazon kinesis streams
    - amazon SWF
    - amazon SQS
    - amazon SNS
    - AWS lambda


# amazon kinesis streams
- is one solution for realtime data
- example implementation :
    - like show current user hotel
    - have a promo to competition & show realtime winner
    - like top 10 hotel flash sale product & showed realtime
    - like ticket / promo flash
    - optimized bid engine

- if you have high load of data & realtime better use kinesis instead of dynamoDB streams
- load & process large streams of data
- overview best flow ?
    - (mobile clients, EC2 instances, server) ---> stream (kinesis (shard1, shard2)) ---> (consumers [ app ]) ---> ( s3, dynamoDB, amazon redshift, amazon EMR)
    - legend :
        ---> = data streams (data record)
- kinesis services :
    - firehose = is like many data per once
    - streams = is like just small data

- shards & data records :
    - many streams :
        - shards :
            - Data Record :
                - sequenceId / sequence Number
                - partitionKey
                - content of the data

- reshard is need when hit limit
- kinesis code :
    - developing producers :
        - amazon kinesis streams API
        - amazon kinesis producers library : (async arc)
            - submit amazon cloudWatch metrics
            - PutRecordAPI :
                - PutRecordResult & Response
    - developing consumers :
        - amazon kinesis customer library :
            - GetRecordsAPI :
                - GetRecordsResult & Response
            - balances shard-worker associations
                - worker instance count changes
                - shards are split or merged
    - startup & shutdown :
        - startup :
            - KCL reads from the tip of stream (LATEST) :
                - Start Customer first, then start producers
            - read from the beginning ot stream (TRIM_HORIZON) :
                - can start producers first

- monitoring :
    - monitoring service with CWatch
    - KPL with CWatch
    - KCL with CWatch
    - API call with CouldTrails

- provisioning Throughput :
    - first just follow the default and then monitoring and adjust it !
    - like Queue System
    - add spare shards for exception

- handling ProvisionedThroughputExceededException :
    - Retry
    - Log & Monitor
    - Reshard
    - Increase DynamoDB throughput

- handle Duplicate Records :
    - by producers retries :
        - make sure the data that will unique that will be unique for consumer
    - by consumer retries :
        - ya bikin logic buat validate data ununique

- recovers from failures :
    - EC2 instance failure :
        - challanges :
            - coba klo cuman 1 EC2 trus masukin autoscaling , harus cuman 1 EC2 instance aja

- general scenario :
    - troubleshooting ( producers ) :
        - check permissions at the kinesis
        - make sure to start to consumer first
    - troubleshooting ( consumer ) :
        - consumers reading at slower rate :
            - increase workers , if still slower you need to increasing shards

- NOTE :
    - with stream you place the data based on partitionID
    - to get LATEST data , at kinesis client specified LATEST



### Developing Event-Driven Solution with Amazon SWF, SQS, SNS
    - SWF :
        - this is like make workflow
              - this is for quite specific workflow from normal one.
              - involved decision making

              - 1 workflow :
                    - multiple task
                    - connector :
                        - decider
                        - worker
                    - application must be stateless :
                        - store in storage elasticcache / dynamoDB
              - NOTE :
                - RRS amazon s3 ( cheaper & not much reliable )

              - implementing workflow:
                - register a domain
                - models a workflow

    - SQS:
        - the entire design is pooling message, consumer must get it's packets
        - it's our reponsibility for tell the Message Queue kita udah ambil dan kelar, klo nggak kalau enabled :
            - we can set *visibility timeout* for automatically make it invisible (attribute for message queue)
            - don't set visibility timeout too short, because if we gak sempat send ACK code.

        - Queuing Chain Pattern :
            - message queue models :
                 - at least one model
        - method to get queue :
            - what is the best? it depends.
            - type :
                - long polling :
                    - di loop semua distributed queue system nya. lebih tinggi kemungkinan dapat message
                    - get all updated data
                - short polling :
                    - just 1 - x queue system , doesn't scan all the server
                    - may chance get not updated data
        - lifecycle :
             - 4 days is retention period (the longest)
             - 14 days is the max if then go to DLQ (Death Letter Queue)
        - if the queue overflow then user just experience the slow app , but the message not lost
        - amazon SQS Queue Types :
            - FIFO Type :
                -  not need ACK , but it's good to send the ACK
        - max : 256kb / message
        - http://docs.aws.amazon.com/cli/latest/reference/sqs/purge-queue.html
        - data that send to SQS must be encrypted !!
        - in the email side if you want to update the email , you must add extra layers for it

    - SNS :
        - push model
        - publisher --> topics --> subscribers
        - use case :
            - like newsletter promo, it's like topic
        - Fan - Out :
            - publisher --> SNS --> SQS --> subscribers
        - max : 256kb / message
        - HTTP retry for retry the SNS
        - NOTE : endpoint vs URL :
                - endpoint didn't through the internet
                - URL : normal URL go trough internet



### Lambda
    - limitation :
        - only running code 5 minute, normally you must spare some time like 3 minute
    - if you grap the small data, if you have heterogent data (like big & small data it's not suitable)
    - lambda has a blueprint :
        - like template
    - cost of lambda :
        - processing power , if you have low processing power is it suitable
        - first 1 million request is free , others will be pay
    - 3 model it can be use :
        - event source ( push event model) :
            - X services push to lambda
        - pull event model :
            - you can add scheduler from X bucket , if object available then grab and delete the data
        - direct invocation :
            - application model
    - permission :
         - execution permissions :
            - access policy :
                - dikasih akses langsung dari account tersebut
            - trust policy :
                - grant AWS lambda permissions to assume the role :
                    - like boss libur tapi dikasih previledge like a boss (temporaryly / trusted account)
                        - role / policy .
         - invocation permissions :
         - lambda function execution :
            - are limited to 5 minute but minimum 3 seconds
            - why timeout is need to be adjusted ?
                - for example if you want to limit time to 15 seconds / 60 seconds for authentication / others so the customer can has expected time
         - code / plain code place to s3
    - terraform :
        - build the platform you can use lambda
    - cloudwatch :
        - to see the log
    - aliases :
        - like you want this lambda want different name
    - lambda need API Gateway :
        - we don't expose lambda directly
    - allow policy for cloudwatch logs
    - AWS cloudtrail <- like for check performance.
    - TIPS :
        - blue green deployment in lambda is possible
        - preventing cold-start to lambda, to maintain the warm period at lambda
    - some commands :
        - (uplaod to s3) aws s3 cp --source-region=us-west-2 s3://us-west-2-aws-training/awsu-ilt/AWS-100-DEV/v2.1/binaries/input/lab-6-lambda/DrugAdverseEvents_September.txt s3://<your-input-bucket>/DrugAdverseEvents_September.txt
        - grunt lambda_invoke test (test the lambda)
        - grunt lambda_package lambda_deploy (deploy the lambda)
        - (to see the ouput bucket) aws s3 ls s3://<your-output-bucket>

