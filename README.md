# Cloud Foundry Firehose Exporter

A [Prometheus][prometheus] exporter for [Cloud Foundry Firehose][firehose] metrics. It exports `ContainerMetric`, `CounterEvent` and `ValueMetric` events.

## Building and running

```bash
make
./firehose_exporter <flags>
```

### Flags

| Flag | Required | Default | Description
| ---- | -------- | ------- | -----------
| metrics.namespace           | No  | firehose_exporter | Metrics Namespace
| metrics.garbage             | No  | 1 minute          | How long to run the metrics garbage
| web.listen-address          | No  | :9186             | Address to listen on for web interface and telemetry
| web.telemetry-path          | No  | /metrics          | Path under which to expose Prometheus metrics
| uaa.url                     | Yes |                   | Cloud Foundry UAA URL
| uaa.client-id               | Yes |                   | Cloud Foundry UAA Client ID
| uaa.client-secret           | Yes |                   | Cloud Foundry UAA Client Secret
| doppler.url                 | Yes |                   | Cloud Foundry Doppler URL
| doppler.subscription-id     | No  | prometheus        | Cloud Foundry Doppler Subscription ID
| doppler.idle-timeout-seconds| No  | 5                 | Cloud Foundry Doppler Idle Timeout (in seconds)
| doppler.metric-expiry       | No  | 5 minute          | How long a Cloud Foundry Doppler metric is valid
| skip-ssl-verify             | No  | false             | Disable SSL Verify |

### UAA Client

In order to connect to the [Cloud Foundry Firehose][firehose] a `client-id` and `client-secret` must be provided. The `client-id` must have the `doppler.firehose` authority.

For example, to create a new `client-id` and `client-secret` with the right permissions:

```bash
uaac target https://<YOUR UAA URL> --skip-ssl-validation
uaac token client get <YOUR ADMIN CLIENT ID> -s <YOUR ADMIN CLIENT SECRET>
uaac client add prometheus-firehose \
  --name prometheus-firehose \
  --secret prometheus-client-secret \
  --authorized_grant_types client_credentials,refresh_token \
  --authorities doppler.firehose
```

## Running tests

```bash
make test
```

## Using Docker

You can deploy this exporter using the [frodenas/firehose-exporter][hub] Docker image. For example:

```bash
docker pull frodenas/firehose-exporter

docker run -d -p 9186:9186 frodenas/firehose-exporter <flags>
```

[firehose]: https://docs.cloudfoundry.org/loggregator/architecture.html#firehose
[hub]: https://hub.docker.com/r/frodenas/firehose-exporter/
[prometheus]: https://prometheus.io/
