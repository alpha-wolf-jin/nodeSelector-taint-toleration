# taint-toleration

```
echo "# taint-toleration" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/alpha-wolf-jin/taint-toleration.git
git config --global credential.helper 'cache --timeout 72000'
git push -u origin main

git add . ; git commit -a -m "update README" ; git push -u origin main
```

**Infrastructure Nodes in OpenShift 4**

Refer to: https://access.redhat.com/solutions/5034771


**Creating default cluster-wide node selectors**

Refer to: https://docs.openshift.com/container-platform/4.10/nodes/scheduling/nodes-scheduler-node-selectors.html#nodes-scheduler-node-selectors-cluster_nodes-scheduler-node-selectors


**Creating project-wide node selectors**

Refer to: https://docs.openshift.com/container-platform/4.10/nodes/scheduling/nodes-scheduler-node-selectors.html#nodes-scheduler-node-selectors-project_nodes-scheduler-node-selectors


**Creating a project with a node selector and toleration**

Refer to: https://docs.openshift.com/container-platform/4.10/nodes/scheduling/nodes-scheduler-taints-tolerations.html#nodes-scheduler-taints-tolerations-projects_nodes-scheduler-taints-tolerations


**Critical DaemonSets Missing Universal Toleration**

Refer to: https://access.redhat.com/solutions/5061861

machine-config-daemon
```
oc patch ds machine-config-daemon -n openshift-machine-config-operator  --type=merge -p '{"spec": {"template": { "spec": {"tolerations":[{"operator":"Exists"}]}}}}'
```

node-ca
```
oc patch ds node-ca -n openshift-image-registry --type=merge -p '{"spec": {"template": { "spec": {"tolerations":[{"operator":"Exists"}]}}}}'
```

csi-cephfsplugin
```
oc patch ds csi-cephfsplugin -n openshift-storage  --type=merge -p '{"spec": {"template": { "spec": {"tolerations":[{"operator":"Exists"}]}}}}'
```

csi-rbdplugin
```
oc patch ds csi-rbdplugin -n openshift-storage  --type=merge -p '{"spec": {"template": { "spec": {"tolerations":[{"operator":"Exists"}]}}}}'
```

dns-default
```

```

# Current Label on worker node
```
# oc get node
NAME                        STATUS   ROLES          AGE     VERSION
aro-k4j2l-master-0          Ready    master         6d13h   v1.23.5+9ce5071
aro-k4j2l-master-1          Ready    master         6d13h   v1.23.5+9ce5071
aro-k4j2l-master-2          Ready    master         6d13h   v1.23.5+9ce5071
aro-k4j2l-worker-eastus-1   Ready    infra,worker   6d13h   v1.23.5+9ce5071
aro-k4j2l-worker-eastus-2   Ready    infra,worker   6d13h   v1.23.5+9ce5071
aro-k4j2l-worker-eastus-3   Ready    infra,worker   6d13h   v1.23.5+9ce5071

# oc get node aro-k4j2l-worker-eastus-1 --show-labels

  labels:
    cluster.ocs.openshift.io/openshift-storage: ""
    kubernetes.io/hostname: aro-k4j2l-worker-eastus-1
    kubernetes.io/os: linux
    node-role.kubernetes.io/infra: ""
    node-role.kubernetes.io/worker: ""

spec:
  taints:
  - effect: NoSchedule
    key: node.ocs.openshift.io/storage
    value: "true"

```

# Error on console UI 'Overview'

- 1 Pods of DaemonSet openshift-ingress-canary/ingress-canary are running where they are not supposed to run.

That means the pod runs on some nodes, but without proper toleration.
```
# oc project openshift-ingress-canary

# oc get pod -o wide | grep ingress-canary
ingress-canary-qww5s   1/1     Running   1          2d15h   10.129.2.20   aro-k4j2l-worker-eastus-2   <none>           <none>

# oc get sub
No resources found in openshift-ingress-canary namespace.

# oc get ds
NAME             DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
ingress-canary   0         0         0       0            0           kubernetes.io/os=linux   6d14h

$ oc get ds ingress-canary -o yaml
apiVersion: apps/v1
kind: DaemonSet

spec:
  template:

    spec:
      containers:

      nodeSelector:
        kubernetes.io/os: linux

      tolerations:
      - effect: NoSchedule
        key: node-role.kubernetes.io/infra
        operator: Exists

# oc edit ds ingress-canary 

      tolerations:
      - effect: NoSchedule
        key: node-role.kubernetes.io/infra
        operator: Exists
      - effect: NoSchedule
        key: node.ocs.openshift.io/storage
        operator: Equal
        value: "true"


```
