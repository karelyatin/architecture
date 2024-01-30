# Configuring networking and deploy the OpenStack control plane

## Assumptions

- A storage class called `local-storage` should already exist.

## Initialize

Switch to the "openstack" namespace
```
oc project openstack
```
Change to the hci directory
```
cd architecture/examples/va/hci
```
Edit the [values.yaml](nncp/values.yaml) file to suit your environment.
```
vi nncp/values.yaml
```
Alternatively use your own copy of `nncp/values.yaml` and edit
[kustomization.yaml](kustomization.yaml) to use that copy.
```
resources:
  - nncp/values-ci-framework.yaml
```

Generate the control-plane and networking CRs.
```
kustomize build nncp > nncp.yaml
```

## Create CRs
```
oc apply -f nncp.yaml
```

Wait for NNCPs to be available
```
oc wait nncp -l osp/nncm-config-type=standard --for jsonpath='{.status.conditions[0].reason}'=SuccessfullyConfigured --timeout=300s
```

Generate the control-plane and networking CRs.
```
kustomize build > control-plane.yaml
```

## Create CRs
```
oc apply -f control-plane.yaml
```

Wait for control plane to be available
```
oc wait osctlplane controlplane --for condition=Ready --timeout=600s
```
