![Horus logo on light small](https://i.ibb.co/2gWkrRh/SMALL-horus-logo-color.png#gh-light-mode-only)
![Horus logo on black small](https://i.ibb.co/02Fxkjm/SMALL-horus-logo-color-on-dark.png#gh-dark-mode-only)

# Open-source monitoring and observability

Horus is an **open-source APM for small microservices** communicating through HTTP.

It's **an application's heart rate monitor**: it observes how it's behaving and helps you spot anomalies.

Horus is a fullstack APM that's:
- **Easy to install** because it runs on Docker and uses custom implementation
- **Easy to use** because of its simple features and use case
- **Open source**, making it extensible and deployable in any infrastructure

[![Version](https://img.shields.io/badge/npm-1.1.0-green)](https://www.npmjs.com/package/horus-agent)

## Table of Contents
- [Who it's for](#who-its-for)
- [What Horus does](#what-horus-does)
- [Prerequisites](#prerequisites)
- [How to set-up Horus](#how-to-set-up-horus)
- [How Horus works](#how-horus-works)
- [Team](#team)

## Who it's for

Horus was made to monitor microservice applications that have:

- A root service that centralize service communication
- Services communicate mainly through HTTP
- Basic monitoring and analytic needs

## What Horus does

*Image with 3 screenshots side by side showing the flow?????*

- Its **metrics summarize** your system's behavior
- Helps you **identify potential problems** by navigating from unusual metrics to traces
- **Understand a trace** by visualizing it through its span parts and their information

## Prerequisites

- Running Docker daemon
- A JavaScript root service to monitor

If your root service isn't in JavaScript, you'll need to manually translate `horus-agent` to your app's language.

## How to set-up Horus

### Horus instrumentation

[Follow the instructions in the NPM documentation.](https://www.npmjs.com/package/horus-agent)

Note that Horus will not capture any errors in Express because **Express fails silently**. Express returns status codes that match an error but doesn't **throw an error** unless explicitly told to.

Thus, to capture errors in its metrics you'll need to throw them within a middleware or in a route handler. This means that you'll need to define and implement what's an error in your application.

### Horus infrastructure
In the server that'll host Horus you'll need to:

1. Run the Docker daemon
2. Clone this `horus-compose` directory
3. `cd` into `horus-compose` and execute `docker-compose up`

You can do this by running the Docker daemon and then executing

```bash
git clone https://github.com/try-horus/horus-compose.git && cd horus-compose && docker-compose up
```

Horus will now be ready to process, store, and visualize your telemetry data.

## How Horus works

Horus depends on 2 parts:
1. `horus-agent`: the instrumentation that generates metrics and traces, and sends it to Horus
2. Horus: the infrastructure that processes, stores, and visualizes telemetry data

*Overall infrastructure diagram	*

### `horus-agent` instrumentation

The `horus-agent` Node package instruments a service automatically to generate and send OpenTelemetry Protocol (OTLP) data.

`horus-agent` is based on 2 key files:
- `index.js`: instruments the app to generate and send OpenTelemetry metrics and traces
- `config.json`: configures the endpoint where the data will be sent to

`index.js`'s instrumentation generates traces, which represent the path of an HTTP request, and 3 types of metrics:
- Requests per second: when a request is received
- Request latency: when a request is received
- Errors per second: when an error is thrown

`horus-agent` queues data in the root service and sends it as a batch at the specified interval because it reduces load on the Horus infrastructure. 

### Horus infrastructure

**Deploying Horus**
`horus-compose` (this directory) handles the deployment of the Horus infrastructure.

`docker-compose.yaml` defines the components Horus uses and how it executes them when running `docker-compose up`. 

**Images**
- `connector`: receives, processes, and issues the commands to insert data in the database ([repo](https://github.com/try-horus/horus-db))
- `docker-db`: creates a TimescaleDB container with the Horus schema ([repo](https://github.com/try-horus/db-docker/tree/master))
- `server`: handles the client's requests to the database ([repo](https://github.com/try-horus/horus-ui/tree/main/server))
-  `client`: the UI that visualizes data and handles user interactions ([repo](https://github.com/try-horus/horus-ui/tree/main/client))

**Container communication**
The default Docker network created by `docker-compose` enables containers to communicate between themselves using their container names.

The exceptions are the API calls made by `client` to `server` from the browser to Horus' IP address, which is automatically set by the application code. This is because an API call made from the browser is outside of the Docker network — meaning it can't understand Docker container names and needs explicit IP addresses.

**Data persistance**
`docker-compose` defines a volume for `docker-db`.

This means that when the database container is restarted it can continue working with the data it had before as long as it accesses the same volume.

## Team

**Frederick, Maryland**
- Callie Buruchara (LinkedIn, GitHub)

**London**
- Rich Morris (LinkedIn, GitHub)
- José de la Puente (LinkedIn, GitHub)

**Madrid**
- Juan García (LinkedIn, GitHub)
