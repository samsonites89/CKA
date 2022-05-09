
How many ReplicaSets exist on the system?

```bash
# rs is short for ReplicaSet
kubectl get rs

# remember ReplicaSet's api is  apps/v1
kubectl describe rs *

```



```bash

# Scale using kubectl scale.
master $ kubectl scale replicaset --replicas=5 new-replica-set
replicaset.apps/new-replica-set scaled

# If you want to do it with the yaml, set the replicas key to a different value. 
# Under the spec section

```
