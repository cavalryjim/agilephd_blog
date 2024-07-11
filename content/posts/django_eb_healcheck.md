+++
title = 'Elastic Beanstalk Healthcheck with a Django App'
tags = ["python", "aws"]
date = 2024-07-10T20:32:38-05:00
+++

### The Problem

Elastic Beanstalk (EB) is an orchestration service offered by AWS for deploying web applications.  The good think about EB is that it handles much of the setup for you.  The bad thing is that EB also includes services, such as Healthchecks, that do not work as anticipated.

