# AWS ECS service

---

1. When a cluster is created on AWS (via console or REST API) and cluster is "EC2 based" (not Fargate) then
corresponding EC2 instances with pre-installed ECS agents are provisioned.

2. ECS agents run as deamons on EC2 machines providing meta data about tasks running on EC2 machines. This data
is available via HTTP on port 51678, for example:

    curl http://localhost:51678/v1/metadata/tasks

above returns a json object with data about running tasks and their ARN, status, containers etc
