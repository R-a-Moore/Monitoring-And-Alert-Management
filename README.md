# Monitoring & Alert Management

In devOps projects, those operating it should be able to identify when service-level objectives are not being met, and thereby (likely) having a negative effect on user experience.

It ensures that deployment architecture is highly available.

We want to find out about issues before the user does.

### Defintion

Cloud monitoring is a method of reviewing, observing and managing the operational workflow 

Cloud monitoring is constant visibility into the performance, availability, and health of your applications and infrastructure.

part of software deployment cycle

starts after applications are deployed

we need to find out and deal with the situation if issues occur.



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

A cloud application monitoring service

can be used to monitor a number of resources, in our case EC2, or specified data.

can monitor resources: ec2, auto-scaling, load balancer, sns, sqs, rds, s3, dynamicDB


puts into understandable metrics for business and other layman (answer for why to use it, since it costs money)

free - 10 metrics
$0.30 - first 10,000 metrics

## SNS
Amazon simple notification service (SNS)

alert service, provides notifications for when conditional events have occured, particullarly issues detected by AWS cloudwatch.

## SQS 
Amazon simple Queue service (SQS)

provides hierarchical queuing of alert notifications, so that higher concern alerts gain prioritization.

types of queues:

first come first serve (FIFO)

## Steps to Implementing Cloudwatch & SNS



go to your instance --> actions --> monitor & troubleshoot --> manage detailed monitoring

select instance -> monitoring --> add dashboard