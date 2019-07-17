
#### Deploy ES (Single instance) + Kibana in dedicated OpenShift project   


Role Name
=========

##### elasticsearch_kibana

Requirements
------------

```pip install openshift``` 
 
##### Ansible 2.6+

Role Variables
--------------

---
```gap_projects``` - open incoming network traffic for projects from this list 

```kibana_configmap_name``` - kibana configmap name 

``logs_masters`` - users from this list can update deployment and run oc rsh (exec) in project pods  

```use_basic_auth``` - if setup values ```yes``` - will be configuring nginx reverse proxy for basic auth to ES listener  

```elasticsearch_route_name``` - name of ES route 

```nginx_deployment_name```   - nginx deployment name 

```basic_auth_secret_name``` - secret name for save ES basic auth password (will create if not exists) 

```nginx_http_port_name``` - nginx http listener port name 
```nginx_http_port_number``` -  nginx http port number 

```nginx_https_port_name``` - nginx https port name 
```nginx_https_port_number``` - nginx https port number 

```nginx_configmap_name``` - name configmap with nginx configuration

```nginx_htpasswd_name```  - name configmap with htpaswd string

```basic_auth_username```  - basuc auth username 

```nginx_service_name``` - nginx (k8s) service name 
 
```kibana_dc_name``` - kibana deploymentconfig name 
```kibana_proxy_sa``` - kibana service account name 

```router_prefix``` - default cluster router wildcard 

```kube_domain``` - k8s SDN domain name (```svc.cluster.local``` - by default )

```es_port``` - ElasticSearch port number 

```es_dc_name``` - ElasticSearch deploymentconfig name 
```es_sa_name``` - ElasticSearch pod service account name 

Curator settings:
 
```
delete_older_than: 7

curator_run_hour: 00
curator_run_minutes: 30
```

Default request + limits:
```
es_ram_request: 4Gi
es_ram_limit:   8Gi
es_cpu_request: 2
es_cpu_limit: 2

kibana_ram_limit: 1Gi
kibana_ram_request: 128Mi
kibana_cpu_limit: 1
kibana_cpu_request: 256m

nginx_cpu_request: 256m
nginx_ram_request: 256Mi
nginx_cpu_limit: 1
nginx_ram_limit: 1Gi
```


```registry_prefix``` - docker registry prefix (if '' - by default, not using)


ES , Kibana, Curator and Nginx images:

```
es_image_name: elasticsearch/elasticsearch
es_image_tag: 6.5.4

kibana_image_name: kibana/kibana
kibana_image_tag: 6.5.4

oauth_proxy_image_name: openshift3/oauth-proxy
oauth_proxy_image_tag: latest

curator_image_name: curator
curator_image_tag: latest

nginx_image_name: nginx
nginx_image_tag: latest
```


```rh_fluentd_project_name``` - name cluster infra project for logs aggregation ( by default- ```openshift-logging```)

```storage_class_name``` -  name of storge class

Storage settings:

```
use_es_pv: yes
es_pvc_name: es
es_pvc_size: 50Gi

use_kibana_pv: yes
kibana_pvc_name: kibana
kibana_pvc_size: 4Gi
```


```use_networkpolicy``` - configure network policies .






Example Playbook
----------------

```
- hosts: "{{ project_list }}"
  gather_facts: no
  vars_files:
    - vars/clusters.yaml

  environment:
    K8S_AUTH_HOST: "{{ clusters[cluster_name]['k8s_auth_host'] }}"
    K8S_AUTH_API_KEY: "{{ clusters[cluster_name]['k8s_auth_api_key'] }}"

  tasks:

    - include_role:
                name: elasticsearch_kibana
      vars:
         project_name: "{{ inventory_hostname }}"

```




License
-------

BSD

Author Information
------------------

Igor Yurev, idyurev@gmail.com
