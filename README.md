## Solace → Elastic Agent → Elasticsearch

Solace broker logs are forwarded via syslog (TCP) to a standalone Elastic Agent,
parsed by the `syslog` processor, and indexed into Elasticsearch.

### Run

```bash
docker compose up -d
```

### Components

| Service | Role |
|---|---|
| `solace-pubsub` | Sends broker syslog to `elastic-agent:9514` (TCP) |
| `elastic-agent` | Standalone mode, TCP input on `0.0.0.0:9514` |
| `elasticsearch` | Receives `logs-syslog.tcp-default` data stream |

All services share the `lab` Docker network, the agent is reachable by its service name.

### Solace setup

Configure a syslog target on the broker pointing to `elastic-agent:9514` over TCP,
with the `event`, `command` and `system` facilities enabled.

access container: `docker exec -it solace-pubsub /bin/bash`

```
enable
configure
create syslog elastic
host elastic-agent:9514 transport tcp
facility event
y # confirm
facility command
y # confirm
facility system
y # confirm
```

### Verify

```bash
docker exec elastic-agent elastic-agent status
```

* log into the broker
* create a queue

Then browse in Kibana at `http://localhost:5601/app/discover`.