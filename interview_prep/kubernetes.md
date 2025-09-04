-- Explain Kubernetes Architecture 
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

-- How various components of kubernetes interact ? (when you run kubectl apply pod)
1) kubectl request is set to control plane api server which validates + authenticates the request 
2) api server forwards request to schedular, which identifies which node is right for the pod 
3) api server then sends the request to the kubelet of whatever node the schedular selected
4) kubelet invokes container runtime which is needed to run the container and this is how your pod is run on the kubernetes cluster
5) once pod is running api server updates that info to the etcd (it persists the object to etcd)
6) replicaset controller which takes care of the pod in case it goes down, this controller is managed by controller manager in control plane

-- What is the purpose of services in kubernetes?
services takes care of service discovery 
pods are ephemeral, aka their ip addresses are constantly changing (as pods get terminated), so how can podA and podB communicate internally?
actually, podA communicates with the service associated with podB
and service communicates with podB not using the ip address but using an algorithm called labels and selectors 
service acts as the middleman as it never gets terminated 

services can also help with default load balancing when there are multiple replicas of a pod, when requests are sent to service, 
service ensures requests are split between multiple replicas of the pod 

--Why is hardcoding Pod IP communication is a bad practice?
pods are ephemeral in nature, they can go down and when this happens the ip address can change
hardcoding it will lead to 502 bad gateway error if the pod goes down
to avoid this, kubernetes uses a component called services which performs service discovery
service depends on labels and selectors not ip address to communicate to pods
service ensures communication is sent to podB even when it crashes and gets replaced
so requests from podA is always reaching podB

-- what are the types of services in kubernetes?
there are many types of services and the type typically defines the scope of access of your kubernetes service 
service types:
cluster ip, the kubernetes service can only be accessed within the kubernetes cluster (so any pod inside a cluster irrespective of namespace)
What it does: Exposes the Service only inside the cluster.
Use case: Internal communication between microservices. For example, your frontend talks to your backend using a ClusterIP service.
IP scope: Only reachable from within the Kubernetes cluster.
🔹 2. NodePort

What it does: Exposes the Service on a static port on every node’s IP.

Use case: You want external traffic to access your cluster, but in a simple way (not production-grade usually). Good for dev/test or quick demos.

How it works: Kubernetes opens a port (default range: 30000–32767) on every Node, and traffic hitting <NodeIP>:<NodePort> gets forwarded to the pods.

🔹 3. LoadBalancer

What it does: Integrates with your cloud provider (AWS ELB, GCP Load Balancer, Azure LB, etc.) to provision a real external load balancer.

Use case: Production services that need to be accessed from the internet.

How it works: Traffic comes into the cloud load balancer → NodePort → ClusterIP → Pods. (So it kind of stacks on top of the previous types).

🔹 4. ExternalName

What it does: Maps a Service to an external DNS name instead of pods.

Use case: When you want services in your cluster to use an external resource (like mydb.example.com) but still reference it as if it were a native Kubernetes Service.

How it works: It returns a CNAME record pointing to the external hostname.

🔹 5. Headless Service (special case)

What it does: A Service without a ClusterIP (clusterIP: None).

Use case: When you want to do direct service discovery of individual pods (e.g., StatefulSets like databases, where each pod should be individually addressable).

How it works: Instead of giving you one virtual IP, DNS will return the IPs of the actual pods.