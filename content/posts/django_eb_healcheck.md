+++
title = 'Elastic Beanstalk Healthcheck with a Django App'
tags = ["python", "aws"]
date = 2024-07-10T20:32:38-05:00
+++

### The Problem

Elastic Beanstalk (EB) is an orchestration service offered by AWS for deploying web applications.  The good think about EB is that it handles much of the setup for you.  The bad thing is that EB also includes services, such as Healthchecks, that do not work without additional setup.

Although my app was deployed and appeared to be working correctly, the Elastic Beanstalk environment reported Severe/Degraded state. The health monitoring was reporting that 100% of the requests to the load balancer were 4xx requests:



For my environment, the issue was caused by no knowing the local IP address of the EC2 server hosting my app. In this post I will go over my experience, and how I managed to resolve this issue.



