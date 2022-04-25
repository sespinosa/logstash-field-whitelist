# Logstash Docker configuration guide

## What is this?

This repo targets to solve the old mistery:

> ### How in the world I can move unknown fields from my document to a object inside a property?

*~Some Senior Cybersecurity Consultant, some company*

You can find the code in the `~/pipelines/norm.cfg` file, it's just a simple filter that runs [ruby](https://en.wikipedia.org/wiki/Ruby) code, it checks for every field, and if it's not part of the *whitelist*, it moves the field to an object in the `extra` property (the whitelisted fields are `message` and `sequence`).

This:

```json
{
  "message":"Hello world!",
  "sequence":4,
  "host":"db366ee5bfe3",
  "@timestamp":"2022-04-25T20:57:05.246Z",
  "@version":"1"
}
```
Becomes this:

```json
{
  "message":"Hello world!",
  "sequence":4,
  "extra": {
    "host":"db366ee5bfe3",
    "@timestamp":"2022-04-25T20:57:05.246Z",
    "@version":"1"
  }
}
```

---

## Install & run

### Install docker

The first step is to have docker installed on the target machine, you can find the official documentation [here](https://docs.docker.com/get-docker/).

### Set the configuration file

For logstash to be able to pickup the pipeline configuration, the files need to be placed inside the container in:

```
/usr/share/logstash/pipeline/
```
This allows `logstash` to pickup multiple pipeline configurations at once, the files should be provided to the container
as a `volume` in `read-only` mode.

### Run with docker

To run the docker container with `logstash`, run this command:

```
$ docker run --rm -it -v ~/pipeline/:/usr/share/logstash/pipeline/ docker.elastic.co/logstash/logstash:7.16.3
```

The terminal needs to be located on **this** folder (`./docker/logstash`).

### Run with docker-compose

In this directory you will find a `docker-compose.yml` file that is already setup to run this example, just use

```
$ docker-compose up
```

And this will run the stack, mount the volume and run the pipeline (I added the example pipeline in the `./pipelines` folder).

---