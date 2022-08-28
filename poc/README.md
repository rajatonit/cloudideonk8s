# Simple POC to spawn ide pod and generate a unique URL

## Preq's
- Kubernetes cluster - Using gke cluster
- Install Traefik and JuypterHub pebble setup/integration with eachother for routing and temp SSL
- Ability to create pods/deployments on k8s cluster and access to creds

## Setup Traefik and Pebble

#### Install Traefik
```console
$ helm repo add traefik https://helm.traefik.io/
$ helm repo update
$ helm repo list
# change Values.yaml to your env specifications
$ helm show values traefik/traefik > traefik/values.yaml
$ helm install traefik traefik/traefik --values ./traefik/values.yaml -n traefik --create-namespace
```

#### Verify Traefik
```console
$ helm list -n traefik
$ kubectl -n traefik get all
```

#### Verify Dashboard
```console
$ kubectl -n traefik port-forward traefik-597f8c76c7-jqhk2 9000:9000
$ curl http://localhost:9000/dashboard/#/
```
You can also access dashboard in broswer:
http://localhost:9000/dashboard/#/


#### Install pebble
```console
$ helm repo add juypterhub https://jupyterhub.github.io/helm-chart/
$ helm repo update
# Change to your env settings 
$ helm show values juypterhub/pebble > ./pebble/values.yaml
# We need to update traefik again with pebble settings. See YAML file in repo on how its done
$ helm install pebble juypterhub/pebble --values ./pebble/values.yaml -n traefik
$  helm upgrade traefik traefik/traefik --values ./traefik/values.yaml -n traefik
```

## Expose Traffik to Web
```
#Note change LB IP/URL in YAML FILE
kubectl apply -f ./traefik/ingress.yaml
```