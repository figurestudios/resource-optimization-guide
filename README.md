# Figuring out the resource requirements of your local application

## Introduction

When I create applications myself if I don't build a multi-threaded app, I always just guess my numbers and round them up a lot. I use an image that doesn't save any files, and yet I rent a GB or so of ephemeral storage. I probably use a few hundreds worth of MiB's memory, yet I rent a GB or so of it. This quick research was done to help others optimize their usage on Akash.

## Instructions

### Acquire your container ID from Docker after starting your image:

```
docker ps -q
```

###  Run the `docker status` command for your container:

```
docker status CONTAINER_ID
```

### The values from the command can be interpreted to these values: ([source](https://docs.docker.com/engine/reference/commandline/stats/))

| Column name | Description |
| --- | --- |
| `CONTAINER ID` and `Name` | the ID and name of the container |
| `CPU %` and `MEM %` | the percentage of the host's CPU and memory the container is using |
| `MEM USAGE / LIMIT` | the total memory the container is using, and the total amount of memory it is allowed to use |
| `NET I/O` | The amount of data the container has received and sent over its network interface |
| `BLOCK I/O` | The amount of data the container has written to and read from block devices on the host |
| `PIDs` | the number of processes or threads the container has created |

### The values can be simplified to:

| Column name | Description |
| --- | --- |
| `PIDs` | Number of threads used |
| `CPU %` | Percentage usage of the threads |
| `MEM USAGE` | Memory/RAM used |

### With this, plus your best judgement, you can figure out how much resources you need:

1) How many virtual threads you need: think number of `PIDs` and peak `CPU %`. Note that not all threads are as fast.

2) How much storage you need: the image size is not included, the rest depends on your application. You should be able to figure this out without monitoring it too much, and for some applications, it will continue to grow.

3) How much memory/RAM you need: you can get a good ballpark number from the peak `MEM USAGE` if your application doesn't need more memory the longer it runs.

### Theory meets practice: it is advisable to add some overhead to your applications. If you want to keep monitoring, you can include a tool in your deployment that helps keep track of the resource usage. It is better to have too much resources than too little, as for some applications you can't just re-deploy however you like without downtime.
