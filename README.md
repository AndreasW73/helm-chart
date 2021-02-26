## Install services
cd charts
helm upgrade --install skills-web-frontend ./skills-web-frontend --namespace=skills-services --set image.tag=192
helm upgrade --install api-gateway ./api-gateway --namespace=skills-services  --set image.tag=173 --set service.type=LoadBalancer --set service.port=5000
helm upgrade --install identity-service ./identity-service --namespace=skills-services --set image.tag=149  --set replicaCount=1
helm upgrade --install recruitments-service ./recruitments-service --namespace=skills-services  --set image.tag=137
helm upgrade --install meetings-service ./meetings-service --namespace=skills-services  --set image.tag=136
helm upgrade --install conferences-service ./conferences-service --namespace=skills-services  --set image.tag=139
helm upgrade --install candidates-service ./candidates-service --namespace=skills-services  --set image.tag=138
helm upgrade --install operations-service ./operations-service --namespace=skills-services  --set image.tag=140
helm upgrade --install metrics-service ./metrics-service --namespace=skills-services  --set image.tag=135
helm upgrade --install scheduledjobs-service ./scheduledjobs-service --namespace=skills-services  --set image.tag=143
cd ..
## Uninstall services

## Install services (locally)
cd charts
helm upgrade --install skills-web-frontend ./skills-web-frontend --namespace=skills-services 
helm upgrade --install api-gateway ./api-gateway --namespace=skills-services  --set image.repository=compose_api-gateway --set image.tag=latest --set pullPolicy=Always --set service.type=LoadBalancer --set service.port=5000
helm upgrade --install identity-service ./identity-service --namespace=skills-services --set image.repository=compose_identity-service --set image.tag=latest --set pullPolicy=Always
helm upgrade --install recruitments-service ./recruitments-service --namespace=skills-services  --set image.repository=compose_recruitments-service --set image.tag=latest --set pullPolicy=Always
helm upgrade --install meetings-service ./meetings-service --namespace=skills-services  --set image.repository=compose_meetings-service --set image.tag=latest --set pullPolicy=Always
helm upgrade --install conferences-service ./conferences-service --namespace=skills-services  --set image.repository=compose_conferences-service --set image.tag=latest --set pullPolicy=Always
helm upgrade --install candidates-service ./candidates-service --namespace=skills-services  --set image.repository=compose_candidates-service --set image.tag=latest --set pullPolicy=Always
helm upgrade --install metrics-service ./metrics-service --namespace=skills-services  --set image.repository=compose_metrics-service --set image.tag=latest --set pullPolicy=Always
helm upgrade --install operations-service ./operations-service --namespace=skills-services  --set image.repository=compose_operations-service --set image.tag=latest --set pullPolicy=Always --set service.type=LoadBalancer --set service.port=5005
helm upgrade --install scheduledjobs-service ./scheduledjobs-service --namespace=skills-services  --set image.repository=compose_scheduledjobs-service --set image.tag=latest --set pullPolicy=Always --set service.type=LoadBalancer --set service.port=5011

cd ..
## Uninstall services

helm uninstall skills-web-frontend 
helm uninstall api-gateway --namespace=skills-services
helm uninstall identity-service --namespace=skills-services
helm uninstall recruitments-service --namespace=skills-services
helm uninstall meetings-service --namespace=skills-services
helm uninstall conferences-service --namespace=skills-services
helm uninstall candidates-service --namespace=skills-services
helm uninstall operations-service --namespace=skills-services
helm uninstall metrics-service --namespace=skills-services
helm uninstall scheduledjobs-service --namespace=skills-services

## Create packages
helm package charts/*   

## Add repo
helm repo add skills https://andreasw73.github.io/helm-chart/
helm repo -h

## Commit changes to repo

## Create index.yaml
helm repo index --url https://github.com/AndreasW73/helm-chart.git/ .

## Extras  
helm upgrade --install skills ./skills-ingress --namespace=skills-infrastructure  --set image.tag=150
helm upgrade --install mailhog codecentric/mailhog --namespace=infrastructure --set service.type=LoadBalancer
helm upgrade --install fabio ./fabio --namespace=infrastructure --set service.type=LoadBalancer --set service.port=9998


kubectl create -f https://raw.githubusercontent.com/jaegertracing/jaeger-kubernetes/master/all-in-one/jaeger-all-in-one-template.yml --namespace=skills-tracing
  

## Uninstall

helm uninstall mailhog --namespace=skills-infrastructure
helm uninstall fabio --namespace=infrastructure

  

## Kubernetes Dashboard
kubectl proxy
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login

## Kubeapps
kubectl port-forward -n kubeapps svc/kubeapps 8080:80
http://127.0.0.1:8080/

## Port forwarding
kubectl port-forward --namespace skills-logging svc/seq 5341:80
kubectl port-forward --namespace skills-infrastructure svc/fabio 9998:9998
kubectl port-forward --namespace skills-infrastructure svc/consul-ui 8500:80
kubectl port-forward --namespace skills-monitoring  svc/grafana 3000:3000

kubectl port-forward --namespace skills-monitoring svc/kibana 5601:5601

kubectl port-forward --namespace skills-infrastructure svc/mongodb 27017:27017
kubectl port-forward --namespace skills-infrastructure svc/mailhog 8025:8025
kubectl port-forward --namespace skills-infrastructure svc/influxdb 8086:8086 & influx -host 127.0.0.1 -port 8086
kubectl port-forward --namespace skills-infrastructure svc/prometheus-kube-prometheus-prometheus 9090:9090

kubectl port-forward --namespace skills-services svc/scheduledjobs-service 8090:80


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

      az aks show --resource-group ntradaResources --name ntradaCluster --query agentPoolProfiles
      az aks scale --resource-group ntradaResources --name ntradaCluster --node-count 1 --nodepool-name nodepool1


