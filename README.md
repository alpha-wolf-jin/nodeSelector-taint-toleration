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



