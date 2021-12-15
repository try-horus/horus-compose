![Horus logo on light small](https://i.ibb.co/2gWkrRh/SMALL-horus-logo-color.png#gh-light-mode-only)

# Open-source monitoring and observability

Horus is an open-source **observability** solution for microservices. Users can generate, store, and visualize correlated telemetry data, allowing them to see the real-time health of their application.

It's **an application's heart rate monitor**: it observes how it's behaving and helps you spot anomalies.

Horus is a fullstack APM that's:
- **Easy to install**: runs on Docker and uses custom implementation
- **Easy to use**: only includes the features you really need
- **Open source**: extensible and deployable in any infrastructure

[![Version](https://img.shields.io/badge/npm-1.2.0-green)](https://www.npmjs.com/package/horus-agent)

### Brief Video Demo
[![Link to Youtube Video Demo of Horus](https://img.youtube.com/vi/-IrSQNMYBjA/0.jpg)](https://www.youtube.com/watch?v=-IrSQNMYBjA)

## Table of Contents
- [Who it's for](#who-its-for)
- [What Horus does](#what-horus-does)
- [Prerequisites](#prerequisites)
- [How to set-up Horus](#how-to-set-up-horus)
- [How Horus works](#how-horus-works)
- [Team](#team)

## Who it's for

Horus was developed to monitor microservice applications that have:

- A root service that centralizes microservice communication
- Services communicate mainly through HTTP
- Basic monitoring and analytic needs

## What Horus does

- **Metrics summarize** your system's behavior
- Helps **identify potential problems** by navigating from metrics to traces
- **Understand a trace** by visualizing it through its span segments and their information

## Prerequisites

- Running Docker daemon
- A JavaScript root service to monitor

If your root service isn't in JavaScript, you'll need to manually translate `horus-agent` into your app's language.

## How to set-up Horus

### Horus instrumentation

[Follow the instructions in the NPM documentation.](https://www.npmjs.com/package/horus-agent)

### Horus infrastructure
In the server that'll host Horus you'll need to:

1. Run the Docker daemon
2. Clone this `horus-compose` directory
3. `cd` into `horus-compose` and execute `docker-compose up`

You can do this by running the Docker daemon and then executing

```bash
git clone https://github.com/try-horus/horus-compose.git && cd horus-compose && docker-compose up
```

Horus will now be ready to process, store, and visualize your telemetry data. You can access the UI at port 3000. 

## How Horus works

Horus depends on 2 parts:
1. `horus-agent`: the instrumentation that generates metrics and traces, and sends it to Horus
2. Horus: the infrastructure that processes, stores, and visualizes telemetry data

# * Overall infrastructure diagram	needed here *

### `horus-agent` instrumentation

The `horus-agent` Node package instruments a service automatically to generate and send OpenTelemetry Protocol (OTLP) data.

`horus-agent` is based on 2 key files:
- `index.js`: instruments the app to generate and send OpenTelemetry metrics and traces
- `config.json`: configures the endpoint where the data will be sent to

`index.js`'s instrumentation generates traces, which represent the path of an HTTP request, and 3 types of metrics:
- Requests per second: when a request is received
- Request latency: when a request/response cycle completes
- Errors per second: when an error is thrown

### Horus infrastructure

**Deploying Horus**
`horus-compose` (this directory) handles the deployment of the Horus infrastructure.

`docker-compose.yaml` defines the components Horus uses and how it executes them when running `docker-compose up`. 

**Images**
- `connector`: receives, processes, and issues the commands to insert data in the database 
- `docker-db`: creates a TimescaleDB container with the Horus schema 
- `server`: handles the client's requests to the database 
- `client`: the UI that visualizes data and handles user interactions 

**Container communication**
The default Docker network created by `docker-compose` enables containers to communicate between themselves using their container names.

The exceptions are the API calls made by `client` to `server` from the browser to Horus' IP address, which is automatically set by the application code.

**Data persistance**
`docker-compose` defines a volume for `docker-db`. When the database container is restarted, it can continue working with the data it had before as long as it accesses the same volume.

## Team

**Maryland, USA**
- Callie Buruchara ([GitHub](https://github.com/callieburuchara))

**London, UK**
- Rich Morris ([GitHub](https://github.com/richwynmorris))
- José de la Puente ([LinkedIn](https://www.linkedin.com/in/ja-puente/), [GitHub](https://github.com/14jdelap))

**Madrid, Spain**
- Juan García ([LinkedIn](https://www.linkedin.com/in/juan-garc%C3%ADa-moreno-21bbbb155/), [GitHub](https://github.com/juan-gm))
