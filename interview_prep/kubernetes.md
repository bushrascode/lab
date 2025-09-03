Explain Kubernetes Architecture 
Control Plane components:
    - api server [kubectl requests are sent to the api, the api sends req to schedular which decides on which node the pod should be scheduled, once this is done api server forwards the request to the kubelet of that particular node]
    - etcd which is not a database but for understanding purposes it is a database
    - schedular 
    - controller manager
Worker Node
    - kubelet [recieves request from api server, and kubelet invokes container runtime ]
    - container runtime [takes the responsibility of running the container in the pod]
    - kubeproxy 

when the pod is executed using kubernetes components, api server persists that information onto etcd 
kubernetes pods are maintained by replica set controller, these such controllers are managed by controller manager

How various components of kubernetes interact when you run kubectl apply (pod)?