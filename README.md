### Kube EFK Stack Logging


The following resources will help you setting up logging mechanisms in your k8s cluster.

It consists of Fluent-bit, Elasticsearch (5.6), Kibana.


##### [Fluent bit](https://github.com/fluent/fluent-bit)

Even more lightweight log processor then FluentD. The DaemonSet is making sure one instance will run on every node. It will watch all logs from every running container, then add metadata with the [kubernetes_metadata_filter](https://github.com/fabric8io/fluent-plugin-kubernetes_metadata_filter) and finally push to Elasticsearch.

##### [Elasticsearch](https://www.elastic.co/products/elasticsearch)

NoSQL data store with powerful full-text-search, built to scale, built in REST api. 
  
##### [Kibana](https://www.elastic.co/products/kibana)

Visualization of data in ES using the REST api.


#### Notes

* ES by default does not support authentication, the X-Pack plugin is easy to set up, but requires subscription, so the authentication can be solved by an Nginx proxy and is already included in the example.
* The role of the proxy very well could be an Nginx Ingress, which needs a setup from [this](https://github.com/hip911/kube-nginx-ingress-with-cm) repo. Once installed, create a new certificate for every domain you plan to use for exposing the logging infrastructure `in the namespace "logging"`. (Most of the time Kibana, sometimes also ES)
* Nginx Ingress supports [Basic Auth](https://kubernetes.github.io/ingress-nginx/examples/auth/basic/README/), however I suspect there is a bug with it currently as I was not able complete the fairly simple setup (though the annotations are commented out). I suggest you try anyways, you will need to create two auth files:

    `htpasswd -c kibana-basic-auth kibana-user`

    `kubectl create secret generic kibana-basic-auth --from-file=kibana-basic-auth`
    
    ,then reference it in the ingresses.
 
* ES will eat your ram rapidly. The example has a minimum and maximum of 1G memory usage and just a single replica, which should be enough for testing.
* If some of your nodes have more memory you can force the ES pod with ['nodeSelector'](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#nodeselector) to be scheduled there.  

#### Installation


`kubectl apply -f .`


#### TODO

ES and Kibana docker images has been deprecated, update them to 6.x 
