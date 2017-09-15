# ElasticSearch Deployment on Kubernetes
======

Following is a step by step guide for the deployment of Elasticsearch on top of Kubernetes using the content provided in this repository.
The provisioned setup has been tested on Google Container Engine, using Google’s standard persistent disk as the persistence storage for the Elasticsearch nodes, with the custom Elasticsearch Docker image being hosted on the docker hub.
The docker image has been uploaded to https://hub.docker.com/r/anchormen/elasticsearch-kubernetes/ and is referenced from the kubernetes statefulset object definition as ```anchormen/elasticsearch-kubernetes:5.6.0```.

### Deployment Instructions
1. have a kubernetes cluster up and running
2. clone this repository 
3. cd kubernetes
4. create a development namespace ```kubectl create -f dev-namespace.yml```
5. permanently save the namespace for all subsequent kubectl commands in that context ```kubectl config set-context $(kubectl config current-context) --namespace=development```
6. create the dynamic allocation storage class ```kubectl create -f gce-standard-sc.yml```
...note: storage classes spans multiple namespaces (i.e. the same storage class can be used across all the namespaces within a single project)
7. create the statefulset headless service ```kubectl create -f es-discovery-svc.yml```
8. create the cluster-ip service ```kubectl create -f es-ia-svc.yml```
9. create the load-balancer service ```kubectl create -f es-lb-svc.yml```
10. create the stateful set ```kubectl create -f elasticsearch.yml```

### Verification
1. verify the statefulset has been created and all the pods are alive ```kubectl get statefulset -w```
...use ```-w``` to watch changes that occur on the statefulset; keep watching until the current number of pods is ```3```
...once the current number of pods is 3, we have 3 elasticsearch pods that are up and running with the names ```elasticsearch-$i``` where $i={0,1,2}
2. to check the logs for a given pod use ```kubectl logs elasticsearch-$i```
...use ```-f``` to keep tracking the logs for the given pod
3. given the statefulset is created, is absolute verification that the headless-service is created properly and is properfly referenced by the elasticsearch nodes
4. verify the cluster-ip and load-balancer services are created; ```kubectl get svc``` 
...the cluester-ip service is used for internal access 
...the load-balancer service is used for external access, through the provided EXTERNAL-IP; 