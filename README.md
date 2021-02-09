## Install services
helm upgrade --install api-gateway ./api-gateway --namespace=skills-services  
helm upgrade --install identity-service ./identity-service --namespace=skills-services  
helm upgrade --install recruitments-service ./recruitments-service --namespace=skills-services  
helm upgrade --install meetings-service ./meetings-service --namespace=skills-services  
helm upgrade --install conferences-service ./conferences-service --namespace=skills-services  
helm upgrade --install candidates-service ./candidates-service --namespace=skills-services  
helm upgrade --install operations-service ./operations-service --namespace=skills-services  
helm upgrade --install metrics-service ./metrics-service --namespace=skills-services  
helm upgrade --install scheduledjobs-service ./scheduledjobs-service --namespace=skills-services  

## Uninstall services

helm uninstall api-gateway --namespace=skills-services
helm uninstall identity-service --namespace=skills-services
helm uninstall recruitments-service --namespace=skills-services
helm uninstall meetings-service --namespace=skills-services
helm uninstall conferences-service --namespace=skills-services
helm uninstall candidates-service --namespace=skills-services
helm uninstall operations-service --namespace=skills-services
helm uninstall metrics-service --namespace=skills-services
helm uninstall scheduledjobs-service --namespace=skills-services


## Extras  
helm install mailhog codecentric/mailhog --namespace=skills-infrastructure
helm upgrade --install fabio . --namespace=skills-infrastructure
  

## Uninstall

helm uninstall mailhog --namespace=skills-infrastructure
helm uninstall fabio --namespace=skills-infrastructure

## Create index.yaml
helm repo index --url https://github.com/AndreasW73/helm-chart.git/ .

  

## Kubernetes Dashboard
kubectl proxy
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login

## Kubeapps
kubectl port-forward -n kubeapps svc/kubeapps 8080:80
http://127.0.0.1:8080/

## Port forwarding
kubectl port-forward --namespace skills-infrastructure svc/seq 5341:80
kubectl port-forward --namespace skills-infrastructure svc/fabio 9998:9998
kubectl port-forward --namespace skills-infrastructure svc/consul-ui 8500:80
kubectl port-forward --namespace skills-monitoring  svc/grafana 3000:3000

kubectl port-forward --namespace skills-infrastructure svc/mongodb 27017:27017
kubectl port-forward --namespace skills-infrastructure svc/mailhog 8025:8025
kubectl port-forward --namespace skills-infrastructure svc/influxdb 8086:8086 & influx -host 127.0.0.1 -port 8086
kubectl port-forward --namespace skills-infrastructure svc/prometheus-kube-prometheus-prometheus 9090:9090


export POD_NAME=$(kubectl get pods --namespace skills-infrastructure -l "app.kubernetes.io/instance=jaeger,app.kubernetes.io/component=query" -o jsonpath="{.items[0].metadata.name}")
echo http://127.0.0.1:8080/
kubectl port-forward --namespace skills-infrastructure $POD_NAME 16686:16686


influx config create --config-name skills-config \
--host-url http://influxdb.skills-infrastructure:8086 \
--org ntrada \
--token  rc3A2cw3lUta5hq6hl2C\
--active

kubectl run influxdb-client --rm --tty -i --restart='Never' --namespace skills-infrastructure  \
      --image docker.io/bitnami/influxdb:2.0.3-debian-10-r0 \
      --command -- influx -host http://influxdb.skills-infrastructure:8086 -port 8086


