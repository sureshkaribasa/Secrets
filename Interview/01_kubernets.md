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

-------------
Deployment?
  > Auto scalling: resourcequota
  > Auto Healing: Replica controllers
  service, resplicaset, Labels, Pods
----------
```
