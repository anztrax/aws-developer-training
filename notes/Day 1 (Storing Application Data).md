- platform service :
   - databases :
        - RDS
        - dynamoDB
        - redshift
        - elasticache
   - analytics :
        - kinesis
        - EMR
        - data pipeline
        - cloudsearch
   - app services :
        - SES
        -


- stream real-time data :
    - why we need this in the first place ? ... like we can use P2P webRTC for example is this solutio


- region :
    - AZ : availability zone, we need 2 at least to minimize down
        - edge location (like CDN service ( AWS like cloudfront)
- https://aws.amazon.com/about-aws/global-infrastructure/


# best practice for developing cloud :
- loosely coupled
- design for failure, architect for resilience
- log metrics and monitoring performance
- implementing a strong devops models (CI CD things = this cut the staging / scrum / manual things into automatically)
- beware of security and regulatory restrictions
- implementing security in every layer ( VPC layer ( private subnet, WEF , security group (like door) , NSEL (like gate) ( we use  xxxxx ) )


# shared responsibility for security :
- always make snapshot for EC2 things


- roles
    - policies
- ARN : amazon resource name
   - format :
           - arn:aws:dynamoDB:region:10112321312:test


- setup dev env :
    - signin to AWS and get AWS credential.

# AWS account
- how many ssh key & secret key that max we create :
 - 1 person , 1 account , 2 key activated


data stores :
- amazon s3 :
    - 1 account 100 buckets
    - unlimited data size , 5TB max size
    - upload time 5TB time ? <- you must do multipart to split into smaller chunk
    - multipart for log if there's big file !!
    - permission access :
            - IAM role
            - send URL to get the data ?
    - SLA 9,9x11 %
    - why using static web page into s3 :
            - percepat performance langsung
            - front facing for s3 for 500 page / 400page / etc
    - configurable lifecycle :
            - move s3 standard (frequent access) ---> s3 standard (infrequent access: long-lived but ) ---> amazon glazier (like after x year delete it automatically (and encrypted automatically ))
     - security :
            - encryption :
                - in your server encyrpt it and pass to s3

            - versioning stategy :
                 - just duplicate all the files into X type
    - global service for all the end users, but
    - s3 select <-for queryable files by it's attributes


- dynamoDB :
    - noSQL database service
    - SLA 9,9x11 %
    - the crateria of dynamoDB :
        - key & value
        - schemeless entity
        - flexible provisioning
        - not ACID compliance ( strongly consitency / partially concistency it depends, but the default is partially )
        - horizontal partioning
        - fully managed :
            - just think of use, not maintain / scale

- amazon elasticCache :
    - in memory database
    - you can select cache engine
    - elasticCache place infront of database / application server ( session data / other services need for datas )

- amazon RDS (relatinal database):
    - automatically update your software.
    - amazon aurora is highlighted !! (check further into this)
    - use the same code and tools that you are familiar with without the administrative burden !
    - replication , patching, data recovery
    - maintenance cost 0, just use the table and query !!
    - optimisting updating

- amazon redshift :
    - fast , fully managed, petabyte-scale data warehouse
    - cost effectively analyze all your data by using existing BI ( business intelligence ) tools
    - use columnar storage technology, parallel processing capabilities  and improved I/O performance
    - custom JDBC / ODBC drivers !!


####
# Developing Storage Solutions with Amazon s3 :
    - max length name : 1024 bytes
    - each object has a unique key
    - URLS for s3 :
        - path style
        - virtual host :
    - Pre-Signed URL : download the files based on datetime restriction ( only on programming code/ not console)
    - CORS things (on programming & console)
         - s3 support CORS !
            - <CORSConfiguration>

    - if use many files in DO NOT use same foldername
    - data integrity : (remember design to failure)
        - ensure the data has not been corrupted in transit, check MD5 of the object from the GET operation

    - troubleshooting :
        - check permission
        - check loading (enabled logging for better debuggin)
        - request ID pair :
            - amazon S3 return pair something !
    - if multipart process is failed
    - streaming data ---> lambda(logic multipart do here) ---> s3


#=====================
== practice session 2:
- it's possible to update the code :
  - pre-signedURL for upload
  - pre-signedURL for download limit like 1GB / download credential for some person



####
# Developing Storage Solutions with Amazon dynamoDB :
    - SQL databases :
        - transactions
    - noSQL databases :
        - supported varies
    - access :
        - granularity access level
    - use cases :
        - IOT <- should try this one
    - concept in dynamoDB :
        - table
             - items
                  - attributes
        - partition key <- primary key :
             - influencing the where this data will be store
             - how dynamoDB store <- using sharding like mongoDB for horizontal scalling
        - sort key <- composite key / non unique partition key
    - using JSON for storing data, it like JSON
    - not optimisting updating (
        evantually consistency:  didn't guarantee the most updated data
        slightly stale data : like milisecond, like the x part of partition is not updated yet
    )
    - scenario for evantually consistency :
        - data that u read is not critical (ex: game maps markers that not important)
    - read and write throught :
         - strong read : 4kb / sec
         - evantually read : ( half the size of strong)
         - strong / evantually ( 1kb )
         - example :
            - avg file size : 100kb
            - item read : 2sec = 100 (read capacity)
    - secondary indexes :
        - why is this IMPORTANT : for the sake of flexibility for index query, so every search term is unique
        - GSI (global secondary index) :
            - use case :
                 - partition key changes :
                    - partition key :
                         - albumID
                    - sort key :
                        -  Name
                    - use find by SongID <- search key index

        - LSI (local secondary index) :
            - use case :
                 - sort key changes :
                    - sort key :
                        - Name
                    - partition key :
                        - albumID
                    - use find by albumID + countryCode

    - changes of the data :
        - Streams :
            - http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html
            - stream lifetime is 24 hours
    - Query & Scan in dynamoDB  :
        - scan
            - if table size big use query
            - if table size is small use scan is good
            - http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/QueryAndScanGuidelines.html
    - Batch Operations :
        - if failed then we must start from the begining from revision
    - Access Management :
        - can use fine-grained access control
    - Hot & Cold Data :
        - use partition key to control the tables
        - separated table into like SQL type schema :
            - like 1 to many / other relationship things
    - note :
        - AWS cloudTrail <- setup send API to this cloudtrail
        - cloudwatch <- for metrics


# --------------------------------------- #
# ------- end of storage things --------- #
# --------------------------------------- #



