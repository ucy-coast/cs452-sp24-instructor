# Homework Assignment 5

Submission Deadline: April 22, 2024

This is an individual assignment to be conducted individually by each student.

## Data Storage Services

Extend the `rate` microservice to store data in MongoDB and cache frequently-accessed data in Memcached. 
You will need to fill in code in `docker-compose.yml`, `internal/rate/mongodb.go`, and also generate (and post-process) the corresponding proto buffer.

Evaluate the throughput and latency performance of the MongoDB-based implementation when deployed on single and multiple machines. 

Evaluate the throughput and latency performance of the Memcached-based implementation when deployed on single and multiple machines. 

Compare the performance of the MongoDB implementation and the Memcached-based implementations.

### Submission

Now you need to submit your assignment. Commit your change and push it to the remote repository by doing the following:

```
$ git commit -am "[you fill me in]"
$ git push -u origin main
```

You may push you code as many times you like, grading and submission time will be based on your last push.
