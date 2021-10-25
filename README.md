# bd-prometheus

bd-prometheus is a bankdata installed namespace running prometheus as pod.

It scrapes the following endpoints : Openshift-endpoints, Openshift-Pods, Openshift-nodes-cadvisor, HA-Proxy from openshift-ingress router-stats-default pods to get haproxy_* metrics     



## Installing/customize the tool

values-file need to be configured with HA-proxy settings:

Reveal of router-stats-default secrets use for username,password in values file.
Also update values file with infra-node names where openshift-ingress are running.

Create the namespace manually (as it is right now) :   oc  create namespace bd-prometheus
  
On your own computer run helm v3 client pointing to helm chart/values/templates  ex:

helm.exe install bd-prometheus  "/c/helmcharts/bd-prometheus"   --namespace=bd-prometheus  --values="/c/helmcharts/bd-prometheus/paasi-values.yaml"   




##  Configuring fmt-prometheus on FMT Servers to scrape the bd-prometheus metrics  

In Bitbucket repo  fmt.prometheus we need to configure the scrape endpoint to bd-prometheus in order to get metrics federated.

We need an bearer token to be able to "connect" to bd-prometheus : 

 oc  get serviceaccounts  --namespace=bd-prometheus
 oc  describe serviceaccount bd-prometheus-sa  -n bd-prometheus
 
 Use the sa-token ex:  bd-prometheus-sa-token-84kgm 
 
 oc  describe secret bd-prometheus-sa-token-84kgm -n bd-prometheus
 .
 .
 token:  zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz          
 
 
Use ansible-vault on ex b0633s01 to encrypt the token:

echo -n "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz" | ansible-vault encrypt_string 


Use the encrypted value in vars: openshift_federated_config.paasu4PW_bd_prometheus


By the way, we do the same for the "built-in prometheus" coming with Openshift in namespace openshift-monitoring  using prometheus-k8s-token:

 oc describe secret  prometheus-k8s-token-dfwww -n openshift-monitoring
 
 encrypt it using ansible-vault and use it in varibale : paasu4PW_openshift_monitoring  in fmt.prometheus repo as above.
 
 
 ## accessing the prometheus GUI:
 
 ex on paasu : 
 https://bd-prometheus-bd-prometheus.apps.c01u.paas.bdunet.dk/graph



