# Offline monitoring stack

An example set up to demonstrate taking our example monitoring stack offline with Zarf and Hauler.

Original set up from: <https://github.com/FluentDo/fluent-bit-examples/tree/main/monitoring-stack>.
This includes a few components:

* kube-prometheus-stack helm chart
  * Loki, Prometheus and Grafana images required
  * Custom Grafana dashboard
* fluent-bit helm chart
  * OSS Fluent Bit image
  * FluentDo Agent image
  * Forwarder (Daemonset) --> Aggregator (Deployment) model

## Zarf.dev

Some useful commands are `zarf dev find-images` which will give you all the images you need plus signatures, etc.

We follow the simple steps here: <https://docs.zarf.dev/best-practices/airgap-demos/>.

```shell
zarf package create .
```

We can specify an architecture as well, e.g. `--architecture arm64`.
We can also create differential packages with `--differential`: <https://docs.zarf.dev/tutorials/9-package-create-differential/>.

Set up the demo cluster, this would be done however you normally do it with `zarf tools download-init` giving you a package to install as well:

```shell
kind create cluster
zarf tools download-init
```

Disconnect from network optionally to demonstrate it - but may break the presentation!

```shell
zarf init --confirm --components=git-server
zarf package deploy ./zarf-package-*.zst --confirm
```

You can even deploy a k3s cluster as well: `zarf init --components k3s,git-server --confirm`

Connect to Grafana with `admin:prom-operator` credentials by default:

```shell
zarf connect grafana-dashboard
```

Get credentials and do port-forward directly:

```shell
kubectl --namespace monitoring get secrets kube-prom-stack-grafana -o jsonpath="{.data.admin-password}" | base64 -d ; echo
kubectl --namespace monitoring port-forward \
    "$(kubectl --namespace monitoring get pod -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=kube-prom-stack" -oname)" \
    3000
```

To remove Zarf components:

```shell
zarf destroy --confirm
```

This will remove the container registry, gitea server and all Zarf components but not the package you have deployed - although images will need mirroring somewhere.

Issues:

* `unable to find pod attached to given resource: unable to find the service: services "zarf-gitea-http" not found`
  * Missing git server during: `zarf init --confirm --components=git-server`
