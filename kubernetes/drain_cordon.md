# 

## 驱逐
kubectl drain --ignore-daemonsets k8s-node-1  

Drain node in preparation for maintenance.

 The given node will be marked unschedulable to prevent new pods from arriving. 'drain' evicts the pods if the APIServer
supportshttp://kubernetes.io/docs/admin/disruptions/ . Otherwise, it will use normal DELETE to delete the pods. The
'drain' evicts or deletes all pods except mirror pods (which cannot be deleted through the API server).  If there are
DaemonSet-managed pods, drain will not proceed without --ignore-daemonsets, and regardless it will not delete any
DaemonSet-managed pods, because those pods would be immediately replaced by the DaemonSet controller, which ignores
unschedulable markings.  If there are any pods that are neither mirror pods nor managed by ReplicationController,
ReplicaSet, DaemonSet, StatefulSet or Job, then drain will not delete any pods unless you use --force.  --force will
also allow deletion to proceed if the managing resource of one or more pods is missing.

 'drain' waits for graceful termination. You should not operate on the machine until the command completes.

 When you are ready to put the node back into service, use kubectl uncordon, which will make the node schedulable again.

 http://kubernetes.io/images/docs/kubectl_drain.svg

```
Examples:
  # Drain node "foo", even if there are pods not managed by a ReplicationController, ReplicaSet, Job, DaemonSet or
StatefulSet on it.
  $ kubectl drain foo --force
  
  # As above, but abort if there are pods not managed by a ReplicationController, ReplicaSet, Job, DaemonSet or
StatefulSet, and use a grace period of 15 minutes.
  $ kubectl drain foo --grace-period=900
```

Options:
      --delete-local-data=false: Continue even if there are pods using emptyDir (local data that will be deleted when
the node is drained).
      --dry-run=false: If true, only print the object that would be sent, without sending it.
      --force=false: Continue even if there are pods not managed by a ReplicationController, ReplicaSet, Job, DaemonSet
or StatefulSet.
      --grace-period=-1: Period of time in seconds given to each pod to terminate gracefully. If negative, the default
value specified in the pod will be used.
      --ignore-daemonsets=false: Ignore DaemonSet-managed pods.
      --pod-selector='': Label selector to filter pods on the node
  -l, --selector='': Selector (label query) to filter on
      --timeout=0s: The length of time to wait before giving up, zero means infinite

Usage:
  kubectl drain NODE [options]

## 禁用
kubectl cordon k8s-node-1

Options:                                                                                                                                                      
      --dry-run=false: If true, only print the object that would be sent, without sending it.                                                                 
  -l, --selector='': Selector (label query) to filter on                                                                                                      
~                                                                                                                                                             
Usage:                                                                                                                                                        
  kubectl cordon NODE [options]

## 恢复
kubectl uncordon k8s-node-1

Options:
      --dry-run=false: If true, only print the object that would be sent, without sending it.
  -l, --selector='': Selector (label query) to filter on

Usage:
  kubectl uncordon NODE [options]
