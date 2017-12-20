# Developing and deploying secure and scalable applications :
- EB / ECS can be use interchangeably


# securing your data :
    - cognito alternative u can use AP (ex : LDAP)
    - how cognito works :
        - to store
    - strictly using least priviledge type :
        - if you have 1 can update & 1 can't update <-- can't update the result
    - SDLC :
        - developer -------------> customer
                      process
        - process :
            - delivery pipeline :
                     (delivery pipeline)
                - build --> test --> release
                  -------------------------- (security)
                  plan <--- monitoring
                      (feedback loop)

        - devOps : Efficiencies that speed up this lifecycle

    - AWS Secure Access Points :
         - API endpoints allow HTTP & HTTPS access
         - monitoring inbound & outbound network traffic
         - NASL to protect inbound & outbound traffic :
            - Managed DDos Protection (AWS Shield)
- IAM :
    - group , user,  roles , policy
    - group : only 1 layer don't have nested group
    - policy : directly assign to person & group & role :
        - you can choose from default policy
        - you can generate policy (better with security generator)
        - there 2 type :
            - inline policy :
            - managed policy :
        - IAM policy rule: DEFAULT is DENIED, you must do give permissions explicitly
        - exp :
            - Default ==> Deny
            - Explicitly ---> Allow
            - Explicitly ---> Deny
            - result is deny
        - when making Deny First paradigm
        -  2types :
            - trusted policy
            - access policy
    - role : we don't say assign , we use assume.
         - can assume by user, services
    - permission types :
        - user based permission :
            - based on user.
        - resource based permission :
            - future proof than user based permission (berapa bkyk user yg mau diurus)
            - you can specified the resource if

    - STS (security Token Service):
        - Identity federation Using Identity Providers :
            - Identity federation :
                - seperti login ke google / facebook jadi kita federating indentity to trusted partners
                - web identity federation.
             - support SAML 2.0 (something like OAuth) & OpenID
             - best practice using cognito

    - Cognito (is suitable to mobile developer) :
        - Cognito Identity Broker.
        - Cognito Sync Store : is for like you have your own (identity Application App)


#-------------------#
- session 2 -
# ------------------#
- caching with cloudfront :
    - seed the cache :
        - disiapin dulu cache nya sebelum di akses ke frontend yg pakai
- elasticCache :
    - support Master - Slave replication,
    - if you use Master - Slave ,make sure place it in different AZ
    - using endpoint to access the Cached
    - 2 type caching :
        - lazy loading
            - klo diminta baru di bikin cache nya
        - write trhought :
            - cache curn : https://www.elastic.co/guide/en/elasticsearch/guide/current/filter-caching.html
- you can Read Replicas for RDS
- session data :
    - Managing Session Data Without Sticky Sessions :
        - it didn't scale well
        - we can use elasticcache / dynamoDB in session data

- CloudWatch :
    - default : 5 minutes , you can get detail monitoring
    - you can set period like 3 times itu terjadi di period tertentu baru fire alarm
    - you can push custom metrics to cloudWatch

- ELK + CloudWatch :
    -


# ---------------------------------#
- Hosting available Application :
    - IaaC <- instrastructure as a code (CloudFormation)
    - read replica (secondary mongo klo di kita T__T) , don't query from secondary, must from master (in the term of multiple AZ)

- launch configration :
    - abstraction of AMI :
- amazon AMI :


- ELB (Elastic Load Balancing) :
    - can do healthcheck
    - using Round Robin Only

- IaaC + Code app :
     - lazy one :(ElasticBeanStalk) :
        - the IaaC stuff in AWS side, and you just think about your application
        - you just deploy your application
        - EB is suitable for small application (ex : like a small startup)
        - EB require (linux AMI) :
            - service role :
                - like s3 / ec2 things
        - custom your env :
            - you can specified in config.yml
         - Advanced Customization :
            - customize AWS Elastic Beanstalk
            - use Docker container (you can ECS)
     - using CloudFront :
        - don't write from scratch template !!!
        - the concept is :
            - you create per layer of system , like :
                - making VPC first
                - making role IAM
                - making making ec2 instances
        - you can create your own template (by drag & drop too)
        - depends-on :
            - so we depends on
        - parameters :
            - valuenya gak boleh hardcode !
            - Strongly type, alphanumeric
            - jump host / bastion host / like NAT.
        - mapping :
            - aa::aa <- like mapping value ke object lain

        - Bulletproof AWS CloudFormation :
            - define parameters
            - use mapping
         - user data :
            - itu custom command to specific data
         - https://aws.amazon.com/blogs/aws/code-management-and-deployment/