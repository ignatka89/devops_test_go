## Solution
In the following example, you can deploy application and monitoring in two ways, on the minikube cluster (the starter script provides for setting up the environment) and also via the docker compose. Configurations for building monitoring were taken from open sources and are given below. Information about monitoring go application with prometheus you can take [here](https://prometheus.io/docs/guides/go-application/) .

**prerequisites:**

1. **Distributor ID: Ubuntu Release: 20.04**

2. **Docker version 24.0.5, Built: Fri Jul 21 20:35:23 2023
3.  **Run:**
4. **Was isntalled library for prometeus go get github.com/prometheus/client_golang/prometheus/promhttp@v1.12.1
```bash
sudo chmod +x script.sh

./script.sh
```

### Build docker image [build_go_multistage](https://docs.docker.com/language/golang/build-images/#multi-stage-builds)

```bash 
sudo docker build -t ignatka89/docker-go-test-app:v1 .
```
### Run Docker container 
```bash
sudo docker run -d -p 7788:8080 -p 7777:9100 ignatka89/docker-go-test-app:v1
```
### Run Docker compose
```bash
cd compose/
docker-compose up -d
```

### Deploy cluster to minikube ( switch to CentOS Linux 7 as minikube required 2 Cores) 
```bash
cd k8s
helm install go-app-release go-app --values go-app/values.yaml
```

### create monitoring namespace
```bash
kubectl create namespace monitoring
```

### Deploy monitoring to minikube
```bash
kubectl apply -f ./prometheus
```

### Monitoring info 
[monitoring k8s cluster](https://devopscube.com/setup-grafana-kubernetes/)

### grafana example 
[json_for_import](https://github.com/jimmidyson/prometheus-grafana-dashboards/blob/master/prometheus-overview.json)


### public templates
[templates](https://grafana.com/grafana/dashboards/?search=kubernetes)

### for minicube testing from external host use
```bash
minikube tunnel
```

### reach the app 
```bash
curl --resolve "go-app-svc.info:80:$( minikube ip )" -i http://go-app-svc.info
```

**notes:**
Grafana login/pass: admin/admin

## additional Task

### Starting Minikube
```bash
minikube start --kubernetes-version v1.26.6 -p agones
```

###Installing Agones
```bash
kubectl create namespace agones-system
kubectl apply --server-side -f https://raw.githubusercontent.com/googleforgames/agones/release-1.34.0/install/yaml/install.yaml
```
###Create a Game Server
```bash
kubectl create -f solution/k8s/server_xonotic/xonotic/gameserver.yaml
kubectl get gameservers
watch kubectl describe gameserver
wait till the status ready
```

### Get ip addrees and port 
```bash
kubectl get gs xonotic -o jsonpath='{.status.address}:{.status.ports[0].port}'
```

**notes:**
You can find solution screenshots in server_xonotic folder.
Next step is connect from the client , to do that next time need choose another linux machine with additional resources (for client) connect from outside is problematic if you run minikube on virtual machine on Virtualbox, port forwarding in this case doesn't work properly.

Also we have possibility to add fleet and fleet autoscaller just use the command kubectl create files located in the solution/k8s/server_xonotic/xonotic/ folder.



