# Spring Pet Clinic for Kubernetes Helm Chart

* Installs the [Spring](https;//spring.io) pet clinic [demo app](https://github.com/spring-petclinic/spring-petclinic-cloud)

## Get Repo Info

```bash
helm repo add Platform9-Community https://platform9-community.github.io/helm-charts
helm repo update
```

_See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

## Installing the Chart

To install the chart with the release name `spring-petclinic-cloud` to namespace `spring-petclinic`:

(be patient while everything comes up)

```bash
helm install spring-petclinic-cloud Platform9-Community/spring-petclinic-cloud --namespace spring-petclinic --create-namespace
```

## Uninstalling the Chart

To uninstall/delete the `spring-petclinic-cloud` deployment from namespace `spring-petclinic`:

```bash
helm uninstall spring-petclinic-cloud --namespace spring-petclinic

#remove the namespace
kubectl delete namespace spring-petclinic
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## About the chart

The Spring pet clinic application is a well known example showing off the power of Spring. Over the years it's been refectored into microservices and then refactored for Kubernetes. That is what this chart deploys. To get deep with the application have a look at [our fork](https://github.com/platform9-community/spring-petclinc-cloud).

## Access services

To access any of the below services you'll need to get the cluster's pubic IP. Here are a few suggestions:

```bash
kubectl get svc -n spring-petclinic api-gateway
kubectl get node -o wide
```

## Pet clinic UI

The UI is served by the api gateway, which has a `NodePort` service attached. To access the application visit `http://<IP>:30808`.

[![Pet Clinic Home](<https://github.com/Platform9-Community/spring-petclinic-cloud/raw/master/docs/petclinic-home.png>]

### Management with spring admin

There is an instance of spring admin running where you can see each service's essentials and do things like turn logging levels up/down. Access admin server at `http://<IP>:30909`.

[![Pet Clinic Home](<https://github.com/Platform9-Community/spring-petclinic-cloud/raw/master/docs/spring-admin-wallboard.png>]

## Running locally

Please note if you are running this chart on a local cluster, it will need a higher amount of compute than normal. This was tested using minikube with the following start command. It took everything a few minutes for the deployments to completely finish and go "green". None of the object deployed make resource claims.

```bash
minikube start --cpus=6 --memory='10000MB'
```

## Running in public cloud

Keep in kind the ports used by each serivcee are not common numbers (like 80 or 443). You will need to open these ports to the nodes.

## Optional add-ons

### Observability

If you would like to include observability set the `include-observability: true` flag in the chart values.

The chart deploys Grafana with all it's default settings. It has an accompanying `NodePort` service, which can be accessed at `http://<IP>:30300`. Use the default creds of admin:admin. There is a single dashboard loaded that has Prometheus as a data source. Once logged in to Grafana go to `Dashboards > Manage > Spring Petclinic Metrics`.

[![Pet Clinic Home](<https://github.com/Platform9-Community/spring-petclinic-cloud/raw/master/docs/grafana-dash.png>]

### Log Streaming

If you would like to include log streaming set the `include-log-streaming: true` flag in the chart values.

The chart will deploy a FluentBit daemon which forwards logs to Elasticstore. To query the logs visit the Kibana instance at `http://<IP>:30056`.

#### Request tracing with Zipkin

If you would like to include log streaming set the `include-request-tracing: true` flag in the chart values.

All services are emitting traces to a Zipkin instance were you can visualize all the requests. This helm chart has no fixed NodePort number. To access the collector UI get the auto-assigned forwarded port by running the below command.

```bash
kubectl -n spring-petclinic get svc zipkin-collector

#Output from the command
NAME               TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
zipkin-collector   NodePort   10.X.X.X   <none>        9411:31928/TCP   165m
```

Then combine the cluster IP with the exposed port #. In the above case it would be `http://<IP>:31928`.
