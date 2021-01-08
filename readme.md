#### Install kubectl

[installation guide Kubectl](https://v1-16.docs.kubernetes.io/docs/tasks/tools/install-kubectl/)
<img src="https://www.linux-magazin.de/wp-content/uploads/2017/09/kubernetes-1.png" width="30px" height="30px"/>

#### Install helm 3

[installation guide Helm](https://helm.sh/docs/intro/install/)
<img src="https://helm.sh/img/helm.svg" width="30px" height="30px">


#### Helm Chart repository links:

- [Ingress Nginx Controller Chart](https://github.com/kubernetes/ingress-nginx/tree/master/charts/ingress-nginx)
- [Fluentd Chart](https://github.com/bitnami/charts/tree/master/bitnami/fluentd)
- [Elastic Search Chart](https://github.com/elastic/helm-charts/blob/master/elasticsearch)
- [Kibana Chart](https://github.com/elastic/helm-charts/blob/master/kibana)


#### Install Java and set JAVA_HOME to execute ./gradlew command

[Installation Guide Java - Linux and Windows](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)

#### Configure Java home path for gradle through IDE or directly on the command line with command

    ./gradlew -Dorg.gradle.java.home=/jdk_path_directory


#### Fluentd Regex UI 
https://fluentular.herokuapp.com/



## Set up elastic stack in kubernetes cluster

###### Deploy and access Dasboard
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
    kubectl proxy 
    http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/.

###### For alternatives/ways to access K8s dashboard externally, see
    https://www.edureka.co/community/31282/is-accessing-kubernetes-dashboard-remotely-possible

###### create docker-registry secret for dockerHub
    DOCKER_REGISTRY_SERVER=docker.io
    DOCKER_USER=your dockerID, same as for `docker login`
    DOCKER_EMAIL=your dockerhub email, same as for `docker login`
    DOCKER_PASSWORD=your dockerhub pwd, same as for `docker login`

    kubectl create secret docker-registry myregistrysecret \
    --docker-server=$DOCKER_REGISTRY_SERVER \
    --docker-username=$DOCKER_USER \
    --docker-password=$DOCKER_PASSWORD \
    --docker-email=$DOCKER_EMAIL


### Set cluster-config.yaml file for kubectl after you downloading config file from Linode

###### Restrict kubeconfig file permissions - to avoid security warning in helm v.3.3.2 upwards
https://github.com/microsoft/azure-pipelines-tasks/pull/13633#issue-495303841

    chmod 600 /path/to/config.yaml

###### Linux, MacOS
    export KUBECONFIG=/path/to/config.yaml

###### Windows
    create folder C:\Users\username\.kube
    rename cluster-config.yaml to config
    put config file in .kube folder


### Install Elastic Stack (EFK) Elastic, FluentD, Kibana

##### install elastic search chart 
    helm repo add elastic https://Helm.elastic.co
    helm install elasticsearch elastic/elasticsearch -f values-linode.yaml

##### install Kibana chart
    helm install kibana elastic/kibana

##### access Kibana locally
    kubectl port-forward deployment/kibana-kibana 5601
    access: localhost:5601

##### install nginx-ingress controller
    helm repo add stable https://charts.helm.sh/stable 
    helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
    helm install nginx-ingress ingress-nginx/ingress-nginx

###### NOTE
Repo has been deprecated - https://stackoverflow.com/a/57970816    
    
    helm repo add stable https://kubernetes-charts.storage.googleapis.com/ 

##### install Fluentd
    helm repo add bitnami https://charts.bitnami.com/bitnami
    helm install fluentd bitnami/fluentd


### Other useful commands

##### restart Fluentd deamonSet
    kubectl rollout restart daemonset/fluentd

##### restart elastic search statefulSet
    kubectl rollout restart statefulset/elasticsearch-master

##### install specific helm version
    helm install elasticsearch elastic/elasticsearch --version="7.9.0" -f values-linode.yaml
    helm install kibana elastic/kibana --version="7.9.0"
    helm install fluentd bitnami/fluentd --version="2.0.1"

    helm install nginx-ingress ingress-nginx/ingress-nginx --version="2.15.0"

##### install helm chart in a specific namespace (namespace must already exist)
    helm install elasticsearch elastic/elasticsearch -f values-linode.yaml -n elastic
