---
title: "Fixing not working AWS ElasticBeanstalk cron job leader instance select"
slug: "fixing-aws-eb-test_cron"
date: 2022-08-07T23:10:31+02:00
tags: [ "AWS", "ElasticBeanstalk", "bash" ]
---

### TLDR:

{{< gist amaro0 70e6ac6c583ed4651db87b44601f358a >}}

After doing all steps from [docs](https://aws.amazon.com/premiumsupport/knowledge-center/cron-job-elastic-beanstalk/),
trying multiple solutions from stackoverflow and wasting countless hours, I found nothing was working for this basic EB
env.
Once again, AWS documentation failed me!
I have decided to stick with solution from docs and debug actual reason why `test_cron.sh` was not detecting leader
instance.
The issue I found on `test_cron.sh` script was both `INSTANCE_ID` and `REGION` were not populated with
metadata.
It turned out that both requests to `http://169.254.169.254/...` were failing with 401 errors.
Quickly found out [here](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-retrieval.html) how to
add authorization to that requests, and it worked. Look into **TLDR** for minimal `test_cron.sh` with proper
authentication.

This bug would be left unseen for way longer without awesome [healthchecks.io](https://healthchecks.io/) monitoring.

Happy coding!
