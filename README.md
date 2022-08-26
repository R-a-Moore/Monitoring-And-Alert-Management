# Monitoring & Alert Management

In devOps projects, those operating it should be able to identify when service-level objectives are not being met, and thereby (likely) having a negative effect on user experience.

It ensures that deployment architecture is highly available.

We want to find out about issues before the user does.

![Monitoring   Alert Management(1)](https://user-images.githubusercontent.com/47668244/186459688-7d6ff291-9136-4012-bbc5-9f23808f131f.png)

### Defintion

Cloud monitoring is a method of reviewing, observing and managing the operational workflow 

Cloud monitoring is constant visibility into the performance, availability, and health of your applications and infrastructure.

part of software deployment cycle

starts after applications are deployed

we need to find out and deal with the situation if issues occur.

[Intro to alerts](https://cloud.google.com/monitoring/alerts)

[Types of cloud monitoring](https://www.netapp.com/cloud-services/what-is-cloud-monitoring/)
### Benefits

- aids scaling, making it more seemless, able to work in any size of organisation
- has dedicated tools, which can be used cross-platform, device etc, making for ease of use & access
- subscription based solutions can keep costs low
- identify trends in service-level objectives, allowing measure user experience
- can be part of issue prevention strategies

## Golden Signals

Along with the golden signals are two supporting methods:
```
The USE Method2: utilization, saturation, and errors. This method can analyze the performance of most any system.

The RED Method3: rate, errors, and duration. This method focuses on service monitoring.
```

### Latency
the time that it takes to service a request

### Traffic
the level of activity in the application, such as rate of requests to API, number of connections to an application server, or the bandwidth consumed to stream an application.

### Errors
the rate of requests that are failing

### Saturation
the measure of how "full" your service is. relies on utilization metrics; i.e., CPU & memory usages, Disk I/O rates (for db), 99th percentile for latency

Remember, application services usually start to degrade before a metric reaches 100% utilization.



## CloudWatch

![cloud watch coverage](https://user-images.githubusercontent.com/47668244/186727009-59f3fb08-b4cc-4620-bd0b-263f9f1bd686.png)


A cloud application monitoring service

can be used to monitor a number of resources, in our case EC2, or specified data.

can monitor resources: ec2, auto-scaling, load balancer, sns, sqs, rds, s3, dynamicDB


puts into understandable metrics for business and other layman (answer for why to use it, since it costs money)

free - 10 metrics
$0.30 - first 10,000 metrics

## SNS

[AWS SNS](https://aws.amazon.com/sns/?whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc)

Amazon simple notification service (SNS)

alert service, provides notifications for when conditional events have occured, particullarly issues detected by AWS cloudwatch.

## SQS 

[AWS SQS](https://aws.amazon.com/sqs/)

Amazon simple Queue service (SQS)

provides hierarchical queuing of alert notifications, so that higher concern alerts gain prioritization.

types of queues:

first come first serve (FIFO)

## Steps to Implementing Cloudwatch & SNS

### alarms
go to your instance --> actions --> monitor & troubleshoot --> manage cloudwatch alarms

or

go to cloudwatch site --> alarms --> all alarmas --> create alarm



### dashboards

go to your instance --> actions --> monitor & troubleshoot --> manage detailed monitoring

select instance -> monitoring --> add dashboard

### How to export logs to S3

These are the steps of how to export logs from your log group to be stored in your s3 bucket, on console.

[Exporting logs to s3](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/S3ExportTasksConsole.html)

#### Create a Bucket
First you need to create a bucket to store your logs.

create a bucket (with ACL permissions), adding the appropriate json policy.

json policy for buckets owned by you

```
{
    "Version": "2012-10-17",
    "Statement": [
      {
          "Action": "s3:GetBucketAcl",
          "Effect": "Allow",
          "Resource": "arn:aws:s3:::my-exported-logs",
          "Principal": { "Service": "logs.us-west-2.amazonaws.com" }
      },
      {
          "Action": "s3:PutObject" ,
          "Effect": "Allow",
          "Resource": "arn:aws:s3:::my-exported-logs/random-string/*",
          "Condition": { "StringEquals": { "s3:x-amz-acl": "bucket-owner-full-control" } },
          "Principal": { "Service": "logs.us-west-2.amazonaws.com" }
      }
    ]
}
```
json policy for buckets not owned by you
```
{
    "Version": "2012-10-17",
    "Statement": [
      {
          "Action": "s3:GetBucketAcl",
          "Effect": "Allow",
          "Resource": "arn:aws:s3:::my-exported-logs",
          "Principal": { "Service": "logs.us-west-2.amazonaws.com" }
      },
      {
          "Action": "s3:PutObject" ,
          "Effect": "Allow",
          "Resource": "arn:aws:s3:::my-exported-logs/random-string/*",
          "Condition": { "StringEquals": { "s3:x-amz-acl": "bucket-owner-full-control" } },
          "Principal": { "Service": "logs.us-west-2.amazonaws.com" }
      },
      {
          "Action": "s3:PutObject" ,
          "Effect": "Allow",
          "Resource": "arn:aws:s3:::my-exported-logs/random-string/*",
          "Condition": { "StringEquals": { "s3:x-amz-acl": "bucket-owner-full-control" } },
          "Principal": { "AWS": "arn:aws:iam::SendingAccountID:user/CWLExportUser" }
      }
    ]
}
```

remember to implement your specific bucket and region where the script says `my-exported-logs` and `us-west-2`

Also in the part where it says `random-strin` this is where you will be saving your logs, so make sure to call it something pertenant.

#### Creating a Log Group

in Cloudwatch, select Log Groups and create a new Log Group.

Choose Actions, Export data to Amazon S3.

On the Export data to Amazon S3 screen, under Define data export, set the time range for the data to export using From and To.

If your log group has multiple log streams, you can provide a log stream prefix to limit the log group data to a specific stream. Choose Advanced, and then for Stream prefix, enter the log stream prefix.

Under Choose S3 bucket, choose the account associated with the Amazon S3 bucket.

For S3 bucket name, choose an Amazon S3 bucket.

For S3 Bucket prefix, enter the randomly generated string that you specified in the bucket policy.

Choose Export to export your log data to Amazon S3.

#### Connect Log Group to EC2 Instance

[configure logs to existing instance](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/QuickStartEC2Instance.html)


# Autoscaling & Load Balancing

![Autoscaling   Load Balancing](https://user-images.githubusercontent.com/47668244/186459740-d516eb59-1fea-41ca-af19-5c140df2a941.png)

how many instances do we want to spin up? when? under what conditions? Autoscaling and load balancing dictates that.

a load balancer works when it needs to balance load between multiple instances (db, app, etc), balances traffic

job of load balancer is to see what instances are healthy or not, knowing to halt inhealthy ones, and redirect to healthy ones

they are both managed services (meaning it's managed by AWS, not the client).

together load balancers and autoscaling groups help provide scalability and high availability.

## Autoscaling group

[What is Auto Scaling?](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)

if we have a load balancer, why do we need auto scaling groups exist?

we create a policy for how many minimum instances there should be, how many are desired, and scaling to reach a maximum

all of these instances in the autoscaling group are replications of our app instance for example, the load balancer spins up, monitors and shuts down all of them according to the traffic and metrics of user requests.

auto scaling groups automatically adjusts the amount of computational resource based on the server load

#### Who's Using It?
- NETFLIX
- Amazon Prime
- Disney

## Load Balancer

balancer distributes traffic between ec2 instances so that no one instance gets overwhelmed

the load balancer will balance the traffic load between them all, all the whilst.

### Types of Load Balancer

- application load balancer (ALB)

- network load balancer (NLB)

- Elastic Load Balancer (ELB)

### Policies

they are all meant to meat out measuring of cost effectiveness

- simple policy; default policy, just carries out a simple action.
- step policy; has multiple steps/requirements, for example spin up 2 more instances if an instance reaches 40% cpu cap, and then another 2 if they reach 60%.
- target tracking policy; always tracks cpu utilization for example, and acts upon that single target.
- etc...

# Autoscaling & Load Balancer Task

![App ALB   Auto Scaling Group](https://user-images.githubusercontent.com/47668244/186885646-2f09d218-34b1-4953-951a-1a530fc5e604.png)

task steps:
- create autoscaling group; policy = min 2 - desired 2 - max 3
- tracking policy; tracks the cpu utilisation - will scale in/out
- know difference between; scale in/out and scale up/down - scaling up/down you are increasing or decreasing the quality/capacity or computational scale of an instance or instance group, scaling in/out increases or decreases the amount of them. Deciding between the two is a matter of measuring the cost effectiveness of either. You base it on your business needs.
- load balancer
- target group
- port(s) range
- launch template
- add user data that we would like in implemented in all our EC2-servers
- launch template
- security group(s); use existing
- virtual private cloud; existing (created by Shahrukh - called devopsstudentsdefault)
- 3 subnets; existing (created by Shahrukh) in multi-AZs eu-west-1a, b, c

## Setting up a Autoscaling group

### Create a Template Instance For the Autoscaling Group to Use
ec2 --> launch template --> create template --> configure:
- tags
- configure

### Autoscaling group

EC2 dashboard --> autoscaling groups (scroll down)

give name
![create an asg](https://user-images.githubusercontent.com/47668244/186873771-603d06ea-fb47-4700-ad34-11d160893eff.png)

select launch template
![select a template](https://user-images.githubusercontent.com/47668244/186873790-cd5b9bba-e93c-419e-8d6b-9ee43be1cd50.png)

select vpc (cloud region)

select subnet (availability zone)

![vpc   subnet](https://user-images.githubusercontent.com/47668244/186874874-e7dc8ff9-6db3-4c87-96d2-53f2e9912cbc.png)

select whether you need a load balancer and all of the specifications thereof.

Then set out your target group.

![load balancer   target group](https://user-images.githubusercontent.com/47668244/186874988-4133f98e-3c68-4800-ac8d-0137c2088a2c.png)

### Systemd Services

[Services in Linux](https://unixcop.com/how-to-create-a-systemd-service-in-linux/)

[Stackoverflow - Services solution](https://stackoverflow.com/questions/64353905/running-npm-start-from-execstart-in-systemctl-service-file)

Sometimes when you set up your auto scaling group, scripts will not work. Especially if you are setting up scripts in User Data, and you are calling to script files in your instance(s) that you're spinning up.

This is because your user data operates as a script for starting up the machine instance. So it may not have all of the pre-requesits (such as the scripts that are loaded on the machine that you're calling to).

If this is the case and it's not working, services may be the solution.

Services operate upon the start up, and can be used to enforce specific commands, such as script calls.

We'll use this to make sure that our provisioning script is running upon spinning up all of our new instances in our autoscaling group. So that we can just copy-paste the ip/dns and it will have ideally already start up an npm start.

Services operate with the `.service` extension.

Navigate to the systemd directory, where the services are held.
 `/etc/systemd/system`


create a service `sudo nano "SERVICE NAME".service` or `sudo touch "SERVICE NAME".service`

```
[Unit]
Description="GIVE IT A NAME"

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu/"SUBJECT FOLDER"
ExecStart=/usr/bin/"SUBJECT COMMAND"
Restart=always

[Install]
WantedBy=multi-user.target
```

Make sure that the service is reloaded and running:
- `sudo systemctl daemon-reload`

- `sudo systemctl start npm.service`

- `sudo systemctl enable "SERVICE NAME".service`


