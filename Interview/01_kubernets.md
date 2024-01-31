```

Why kubernetes?
k8's architecture?
diff b/w k8 & docker?
-------------
pods?
  > uses?
  > pod.yaml
  > cmd: kubectl apply -f pod.yaml
  > Pod debugging:
      > kubectl describe/log #podname

  > HPA(Horizental Pod Autoscaler):
      : is a powerful tool in Kubernetes for automatically scaling up or down the number of Pods in a Deployment,
        ReplicaSet, or StatefulSet based on resource utilization.
-------------- 

namespace?
  Logical isolation of groups of resources within a single cluster.
  Namespaces create virtual clusters within a physical Kubernetes cluster to help users avoid resource naming conflicts and manage capacity, among others.
  Namespaces are useful for organizing resources by project, team, or environment.
  Benefits:
    Isolation: From Accedental changes
    Security: Restricting access to resources
    Organization: organize resources by project, team, or environment.
kubectl create ns suresh-namespace
kubectl get ns
kubectl describe ns suresh-namespace
kubectl run nginx --image=nginx --namespace=my-namespace

namespace limit?
  > NO limit for creating namespaces but for resources
  > create namespace >> attach name space with pod, service, ResoureQuota
--------------

Replica Set?
  Kubernetes object that ensures that a specified number of identical pods are running at any given time

kubectl create replicaset my-replica-set --replicas=3 --image=nginx  
kubectl get replicaset  
kubectl get replicaset my-replica-set  
kubectl scale replicaset my-replica-set --replicas=5
----------------------

ResoureceQuota?
  Kubernetes administrators can restrict cluster tenants' resource usage per namespace.
  namespaces create virtual cluster in physical cluster to avoaid naming conflits for user among others
  ResoureceQuota defines ""HARD LIMITS" on amount of resouces usage for pods in a namespace
   ResourceQuota Types:
      CPU
      Memory
      Pods
      Ephemeral storage
      Persistent volumes
      Persistent volume claims
      
  If pod is created which exceeds ResourceQuota limits will be rejected by k8.
  ResourceQuotas can also be used to limit the number of objects of a particular type that can be created in a namespace:
    > number of Pods,  
    > Deployments,  
    > Services.

kubectl get ns
kubectl create ns dev_ns
kubectl apply -f resource_quota.yml -n dev_ns

kubectl get quota -n dev_ns               
        #shows the used & unused % of quota in ns dev_ns
-------------------

Services?
  is a way to expose a group of pods to other pods and services in the cluster, or to external clients outside the cluster.

Service Types:

Cluster IP:
  Allows access only who has access to Kubenets Cluster. - Inside cluster
Node port:
  Allows access only who has the Node (server/Instance) access - Inside Organization
Load Balancer(With Cloud Provider Only possible):
  To the whole outer world can be accessed.

Why services used?
  > Load Balancing: Trafic will route to one pod to another pods
    ingress controller should be installed not default
      nginx ingress loadbalencer is free for personal  & paid business
      Benefits of Enterprise Load balancers:  
      TLS(SECURE)
      Ratio Based Load Balencing ( 3 request to 1 pod 7 requests another pod)
      Sticky session ( If 1 request goes to 1 pod all the request sholud go to same pod)
      Path based
      Host/domain based
      White Listing
      Black Listing
  > Services Discovery (Through Labels & Selectors):
    When pod goes down, Using Auto healing creates a new Pod(Rs) but ip changes not possible to route trafic. So Labels is used.
  > Expose to world: Can't be provided to user to access a new ip address after every auto-Healing. So will be provided with elastic ip of Load Balancer to access from
    anywhere.
-------------
Deployment?
  > Auto scalling: resourcequota
  > Auto Healing: Replica controllers
  service, resplicaset, Labels, Pods
----------

k8'sSecurity?

Secure API server :
  (Transport Layer Security) Goto api server pod >> edit >> add TLS file, private key, client key Restric the access: trhough RBAC RBAC (Role Base Access Control) : Cluster   role Role Binding
Network policies:
  Creating network policies & attaching to the namespaces for limiting the access.
Encrypted at rest(ETCD):
  Sensitive data in ETCD can be secured using encrypting only who has decrypt KEY ONLY can read the files.
Secure container images:
  Scan docker images for vulnerbilities using synk or trivy
Cluster monitering:
  using ""Sysdig"" tool we can moniter & get alert and define rules no body should root login execpt, no bobody should reach /etc/shadow..
frequent upgrades:
  make sure to use updated version so that patches are fixed in the previous versions if were any!

Types of RBAC SUPPORT:
  1.USER ACCESS & SERVICE ACCOUNTS:
      USER: (ex: what level access should have for dev & QA team)
      SERVICE ACCOUNTS: EX: If a pod is created & connected with a Service. That service should have restrictions to access or delete or create to any secret files.
  2.Roles/CLUSTER ROLES: EX: like AWS IAM ROLES: What level of access a role should have on an account level or cluster level
  3.ROLE BINDING/CLUSTER ROLE BINDING: Connects users / Services account with Roles / Cluster Roles. With out Binding users with Roles no use.  
---------------
config maps?
  Configs Maps helps to save non-sensitive info: DB port, DB Username
  stored as key-value pairs, and can be used to configure Pods, Deployments, and other Kubernetes objects.
  create configmap:
    > kubectl create configmap my-configmap --from-file=/path/to/config/files
     volumeMounts:
          - name: db-connection
            mountPath: /opt #####
        ports:
        - containerPort: 8000
      #### volumes creating ######
      volumes:
        - name: db-connection
          configMap:
            name: test-cm  config map name
------------
why secrets?
   that store sensitive data such as passwords, tokens, and keys.
  but they are encrypted at rest and in transit

create secret

kubectl create secret --from-file=/path/to/access/secrets

How it protects the sensitive info:
  By using Strong RBAC(with least Privilliages) => secrets saves the data in encryption format.
  RBAC- Role-Based Access Control

Secret Types:
  Opaque: uses base64 encryption
  Docker Registry Secret: for accessing images from docker registriy
  Service Account Token Secret: Encrypted at rest(etcd) and in transit
  TLS: tls.cert tls.key will be created and mouted on pods volume to access.
------------
Probes:
By regularly performing health checks on your containers, probes help ensure only healthy pods are serving traffic and unhealthy ones are quickly restarted or scaled down.
features:
  Detecting pod crashes or malfunctions
  Controlling pod lifecycle
  Scaling efficiently
  Improved service reliability
Probes types:
Liveness probes:
  Check if a container is actually running and can handle requests.
  Unresponsive pods based on liveness probe failures are automatically restarted.
Readiness probes:
  Verify if a container is fully initialized and ready to receive traffic.
  Pods failing readiness probes are marked as unhealthy and removed from the service endpoint, preventing         users from encountering unresponsive application instances.
Startup probes:
  For slow-starting applications, these probes delay pod readiness until the application is fully ready and functioning, preventing premature exposure to traffic.
ex:
spec:
      containers:
      - name: my-web-container
        image: my-web-image:latest
        ports:
        - containerPort: 8080
        livenessProbe:
          httpGet:
            path: /healthz
            port: 8080
          initialDelaySeconds: 15
          periodSeconds: 20
          failureThreshold: 3
        readinessProbe:
          httpGet:
            path: /readyz
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 10
          successThreshold: 1
          failureThreshold: 3
```
debugging:
```
Imagepullbackoff
Image pulled but pod is pending
CrashLoopBackOff
Out of Memory
```
Cordon & uncordon:
-----------
```
kubectl cordon my-node     - makes nodes unschedulable but existing pods will be running
kubectl drain my-node      - Deletes pods which are on node
kubectl uncordon my-node   - makes node schedulable now. 
```
