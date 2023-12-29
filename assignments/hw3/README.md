# Homework Assignment 3

Submission Deadline: March 25, 2024

This assignment comprises three parts. This is an individual assignment to be conducted individually by each student.

## Part 1: Implement Hotel Map Microservices

In this part, you will implement the microservices for the Hotel Map application. We provide you with a partial implementation that you can use as a starting point. You can find the code in the directory `hotelapp`. The parts you need to fill in are marked with `TODO` comments. 

You should establish that your web app is running correctly. You can test your application by sending to it search queries through the web interface, for example, using your web browser or the `curl` utility, as described in the [lab notes](https://github.com/ucy-coast/cs452-sp24/blob/main/labs/06-hotelapp/README.md#testing).

## Part 2: Evaluate Hotel Map Microservices

In this part, you will conduct a characterization and analysis of the Hotel Map microservices. You can use either your microservices container images (from part 1) or the ones available from DockerHub. You should evaluate how throughput and latency performance scales with increasing number of nodes (machines).

You will use the [wrk2](https://github.com/giltene/wrk2) HTTP benchmarking tool described in the [lab notes](https://github.com/ucy-coast/cs452-sp24/blob/main/labs/06-hotelapp/README.md#benchmarking). You can find the code for `wrk2` in the directory `hotelapp/wrk2`.

### Single node

Use [Docker Compose](https://docs.docker.com/compose/) to deploy the [monolithic implementation](https://github.com/ucy-coast/cs452-sp24/tree/main/labs/06-hotelapp#deploying-web-applications-with-docker) and the microservices-based implementation on a single machine.

Evaluate the throughput and latency performance of the monolithic and microservices-based implementations when deployed on a single machine.

Using jaeger, [trace the RPC call chains](https://github.com/ucy-coast/cs452-sp24/tree/main/labs/07-microservices#tracing-requests) and identify any bottlenecks. 

### Multiple nodes

Use [Docker Swarm](https://docs.docker.com/engine/swarm/swarm-tutorial/) to deploy the microservices-based implementation on multiple machines. Deploy each microservice on a separate machine.

Evaluate the throughput and latency performance of the microservices-based implementation when deployed on multiple machines. Compare its performance to that of the monolithic implementation and the microservices-based implementations when deployed on a single machine.

Using jaeger, [trace the RPC call chains](https://github.com/ucy-coast/cs452-sp24/tree/main/labs/07-microservices#tracing-requests) and identify any bottlenecks. 

## Part 3: Scale Hotel Map Microservices

In this part, you will scale out the Hotel Map application by running multiple microservice instances on a cluster using Docker Swarm. You can use either your microservices container images (from part 1) or the ones available from DockerHub. You should evaluate how throughput and latency performance scales with increasing number of microservice instances. You can focus on a single microservice of your choice (e.g. search). 

A microservice deployment can have many identical back-end instances serving many client requests. Each backend server has a certain capacity. Load balancing is key for distributing the load from clients across available instances. Load balancing had many benefits and some of them are: (i) Tolerance of failures: if one of your replicas fails, then other servers can serve the request, (ii) Increased Scalability: you can distribute user traffic across many servers increasing the scalability, (iii) Improved throughput: you can improve the throughput of the application by distributing traffic across various backend servers, and (iv) No downside deployment: you can achieve no downtime deployment using rolling deployment techniques.

Hotel Map microservices use gRPC for communication with other microservices. gRPC is one of the most popular modern RPC frameworks for inter-process communication. It's a great choice for microservice architecture. Docker and Kubernetes services provide load-balanced IP Addresses. But, this default load balancing doesn't work out of the box with gRPC. gRPC works on HTTP/2. The TCP connection on the HTTP/2 is long-lived. A single connection can multiplex many requests. This reduces the overhead associated with connection management. But it also means that connection-level load balancing is not very useful. The default load balancing in Docker Swarm is based on connection-level load balancing. For that reason, default load balancing does not work with gRPC.

Below we provide hints for how to use the apply load balancing through proxy load balancing.

### Proxy load balancing

In Proxy load balancing, the client issues RPCs to a Load Balancer (LB) proxy. The LB distributes the RPC call to one of the available backend servers that implement the actual logic for serving the call. The LB keeps track of load on each backend and implements algorithms for distributing load fairly. The clients themselves do not know about the backend servers. Clients can be untrusted. This architecture is typically used for user-facing services where clients from open internet can connect to the servers.

The pros and cons of proxy load balancing include:
- Pros: Clients are not aware of backend instances
- Pros: Helps you work with clients where incoming load cannot be trusted
- Cons: Since the load balancer is in the data path, higher latency is incurred
- Cons: Load balancer throughput may limit scalability

You can use NGINX as a gRPC proxy and load-balancer. For example, consider a gRPC microservice `profile` that listens to port `8081` deployed using Docker. You can create the NGINX service through `docker-compose.yml`:

```
services:

  profile:
    build: .
    image: ${REGISTRY-127.0.0.1:5000}/hotel_app_profile_single_node_memdb
    entrypoint: profile
    ports:
      - "8081"
    restart: always

  nginx:
    image: nginx:1.20.0
    container_name: nginx
    ports:
      - "8581:8581"
    volumes:
      - ./conf/nginx.conf:/etc/nginx/nginx.conf:ro

  ...
```

The NGINX proxy config looks like:

```
upstream profile_server {
  server profile:8081;
}

server {
  listen 8581 http2;
  location / {
    grpc_pass grpc://profile_server;
  }
}
```

The main things that are happening here are that we are defining NGINX to listen on port `8581` and proxy this HTTP2 traffic to our gRPC server defined as `profile_server`. NGINX figures out that this `serviceName:port` combo resolves to more than one instance through Docker DNS. By default, NGINX will round robin over these servers as the requests come in. There is a way to set the load-balancing behavior to do other things, which you can learn about more [here](https://www.nginx.com/faq/what-are-the-load-balancing-algorithms-supported/).

There are a few notable things that need to happen in your `docker-compose.yml` file.

#### Let your containers grow

Make sure you remove any `container_name` from a service you want to scale, otherwise you will get a warning.

This is important because docker will need to name your containers individually when you want to have more than one of them running.

#### Don’t port clash

We need to make sure that if you are mapping ports, you use the correct format. The standard host port mapping in short syntax is `HOST:CONTAINER` which will lead to port clashes when you attempt to spin up more than one container. We will use ephemeral host ports instead.

Instead of:

```
   ports:
     - "8081:8081"
```

Do this:

```
   ports:
     - "8581"
```     

Doing it this way, Docker will auto-”magic”-ly grab unused ports from the host to map to the container and you won’t know what these are ahead of time. You can see what they ended up being after you bring your service up.

#### Get the proxy hooked up

Using the `nginx` service in `docker-compose.yml` plus the `nginx.conf` should be all you need here. Just make sure that you replace the `profile:8081` with your service’s name and port if it is different from the example.

#### Bring it up

After working through the things outlined above, you start your proxy and service up with a certain number of instances.

To scale the service with [Docker Swarm](https://docs.docker.com/engine/swarm/swarm-tutorial/scale-service/), you run the following command:

```
docker service scale profile=3
```

To scale the service with Docker Compose, you need to pass an additional argument `--scale <serviceName>:<number of instances>`.

```
docker-compose up --scale profile=3
```

#### Inspecting containers

You can use the handy command `docker stats` to get a view in your terminal of your containers. This is a nice and quick way to see the running containers’ CPU, memory, and network utilization, but it shows you these live with no history view.

## Point Distribution

| Problem    | Points |
|------------|--------|
| Q1         | 40     |
| Q2         | 30     |
| Q3         | 30     |

### Submission

Now you need to submit your assignment. Commit your change and push it to the remote repository by doing the following:

```
$ git commit -am "[you fill me in]"
$ git push -u origin main
```

You may push you code as many times you like, grading and submission time will be based on your last push.
