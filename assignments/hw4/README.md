# Homework Assignment 4

Submission Deadline: April 19, 2024

This is a group assignment to be conducted in teams of two students.

## Sharding Hotel App

In this assignment, we will apply sharding to scale the memory capacity of individual services. We've previously explored scaling out services through replication, which increases the compute capacity of microservices by adding more container instances per microservice to manage a higher request volume. 
However, each microservice instance can accommodate only a limited amount of data, restricted by the available memory (and/or storage) on its respective node. 
In this assignment, you will leverage sharding as a solution to overcome this memory capacity limitation.

Specifically, you will implement sharding logic in the `frontend` and `profile` microservices to scale out the memory capacity of the `profile` microservice.

#### Extend the Profile Microservice for Sharding

- Modify the `docker-compose.yml` file to deploy multiple `profile` services, each operating as a shard. This differs from the previous approach of replicating a single `profile` service, where replication produced identical instances with the same configuration and dataset. Instead, deploying each shard as a distinct service allows for individual configuration, enabling the use of different datasets for each service.
- Partition the `hotels.json` dataset into multiple files, with each file containing a distinct subset of hotel profiles.
- Configure each profile microservice instance to utilize a different file, ensuring efficient data distribution.

#### Extend the Frontend Microservice for Sharding

- Extend the functionality of the `frontend` microservice to dynamically query all available profile microservice instances for a given hotel profile.
- Implement a mechanism in the frontend to aggregate responses from multiple `profile` instances for a comprehensive dataset.

#### Evaluate the Sharding Mechanism
- Assess the throughput and latency performance of the newly implemented sharding mechanism when deployed on a single machine.
- Extend the deployment to multiple machines, considering the scalability aspects, and evaluate its impact on performance.

### Submission

Now you need to submit your assignment. Commit your change and push it to the remote repository by doing the following:

```
$ git commit -am "[you fill me in]"
$ git push -u origin main
```

You may push you code as many times you like, grading and submission time will be based on your last push.
