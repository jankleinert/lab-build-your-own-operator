---
Title: Create Deployment
PrevPage: step02
NextPage: step04
---

Create the Deployment containing the Etcd Operator container image:

```execute-1
cat > etcd-operator-deployment.yaml<<EOF
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    name: etcdoperator
  name: etcd-operator
spec:
  replicas: 1
  selector:
    matchLabels:
      name: etcd-operator
  template:
    metadata:
      labels:
        name: etcd-operator
    spec:
      containers:
      - command:
        - etcd-operator
        - --create-crd=false
        env:
        - name: MY_POD_NAMESPACE
          valueFrom:
            fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
        - name: MY_POD_NAME
          valueFrom:
            fieldRef:
              apiVersion: v1
              fieldPath: metadata.name
        image: quay.io/coreos/etcd-operator@sha256:c0301e4686c3ed4206e370b42de5a3bd2229b9fb4906cf85f3f30650424abec2
        imagePullPolicy: IfNotPresent
        name: etcd-operator
      serviceAccountName: etcd-operator-sa
EOF
```
<br>
```execute-1
oc create -f etcd-operator-deployment.yaml 
```
<br>
Verify the Etcd Operator Deployment was successfully created:

```execute-1
oc get deploy
```
<br>
Verify the Etcd Operator Deployment pods are running:

```execute-1
oc get pods
```
<br>
Open a new terminal window to follow Etcd Operator logs in real-time:

```execute-1
export ETCD_OPERATOR_POD=$(oc get pods -l name=etcd-operator -o jsonpath='{.items[0].metadata.name}')
oc logs $ETCD_OPERATOR_POD -f
```
<br>
Observe the leader-election lease on the Etcd Operator Endpoint:

```execute-1
oc get endpoints etcd-operator -o yaml
```
<br>
