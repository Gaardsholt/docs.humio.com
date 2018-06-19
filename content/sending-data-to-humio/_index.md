---
title: "Sending Data to Humio"
weight: 200
category_title: "Overview"
---

There are steps to getting your data into Humio:

1. Sending data - Which is what this section is about
2. Parsing and ingesting data - Described in the [Parsers sections]({{< ref "parsing.md" >}})

Sending data to Humio (also called _data shipping_) can be done in a couple of ways:

- Using a [**Data Shipper**]({{< ref "#data-shippers" >}}) ([Filebeat]({{< ref "filebeat.md" >}}), [Logstash]({{< ref "logstash.md" >}}), [Rsyslog]({{< ref "rsyslog.md" >}}), etc.)
- Using a [**Platform Integration**]({{< ref "#platform-integrations" >}}) ([Filebeat]({{< ref "kubernetes.md" >}}), [Filebeat]({{< ref "docker.md" >}}), [Filebeat]({{< ref "mesos.md" >}}), etc)
- Using the [**Ingest API**]({{< ref "#ingest-api" >}}) directly

In most cases you will want to use a data shipper or one of our platform integrations.

Below is an overview of how the respective ways of sending data to Humio work:

## Data Shippers {#data-shippers}

A Data Shipper is a small system tool that looks at files and system properties
on a server and send it to Humio. Data shippers take care of buffering, retransmitting
lost messages, log file rolling, network disconnects, and a slew of other things
so your data or logs are send to Humio in a reliable.

**Example Flow**  
In this example "Your Application" is writing logs to a "Log File".
The "Data Shipper" reads the data and pre-processes it (e.g. this could be
converting a multiline stack-trace into a single event).
It then _ships_ the data to Humio on one of our [Ingest APIs]({{< ref "ingest-api.md" >}}).

{{<mermaid align="left">}}
graph LR;
    subgraph Application Server
    A[Your Application] -->|Logging| B(Log File)
    B --> C[Data Shipper]
    C -->|Pre-Processing| C
    end
    C -->|Ingest API| D{Humio}
    D -->|Parser 1| E(Repository 1)
    D -->|Parser 2| F(Repository 2)

    classDef green fill:#5de995;
    class C, green
{{< /mermaid >}}

[List of supported Data Shippers]({{< ref "integrations/data-shippers/_index.md" >}})


## Platform Integrations {#platform-integrations}

If you want to get logs and metrics from your deployment platform, e.g. a Kubernetes cluster or your company PaaS,
you can use one of our [Platform Integrations]({{sending-data-to-humio}}).

**Example Flow**  
Depending on your platform the data flow will look slightly different. Some systems
use a built-in logging subsystem, others you start a container running with a data shipper.
Usually you will _assign_ containers/instances in some way to indicate which repository and parser
should be used at ingestion.

{{<mermaid align="left">}}
graph LR;
    subgraph Platform
    C1["Nginx<br/>(assigned: Repo 2/Parser 2)"]   -->|StdOut| B(Platform Logging Sub-System)
    C2["MySQL<br/>(assigned: Repo 2/Parser 3)"]   -->|StdOut| B
    C3["Web App<br/>(assigned: Repo 1/Parser 1)"] -->|StdOut| B
    end
    B -->|Ingest API| D{Humio}
    D -->|Parser 1| E(Repo 1)
    D -->|Parser 2| F(Repo 2)
    D -->|Parser 3| F(Repo 2)


    classDef green fill:#5de995;
    class C, green
{{< /mermaid >}}

Take a look at the individual integrations pages for more details.

## Using the Ingest API {#ingest-api}

[List of API Clients and Framework Integrations]({{< relref "language-integrations/_index.md" >}})

If your needs are simple and you don't care too much about potential data loss due
to e.g. network problem, you can also use Humio's [Ingest API]({{< ref "ingest-api.md" >}}) directly
or through one of Humio's client libraries.

{{<mermaid align="left">}}
graph LR;
    A[Your Application] -->|Ingest API| B(Log File)
{{< /mermaid >}}

As you can see, this is by far the simplest flow, and is completely appropriate
for some scenarios e.g. analytics.