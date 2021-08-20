# devconf-2021
Demo Repository for devconf-2021

This repository contains the manifests to the various deployments shown in the Devconf 2021 demo for the talk "Modernizing Machine Learning Workflows with GitOps".

All manifests are configured for deployment on OpenShift Container Platform (OCP).

# ArgoCD

The ArgoCD deployment in this repository points to the configuration found [here][ArgoCD] configured by [Operate-First][op1st]. 

We opt not to use the [GitOps Operator][GO] for provisioning ArgoCD on OCP, because currently you cannot add custom tools 
via Volume Mounts, and we require this feature in order to support secrets management via [ksops][ksops]. This feature
is eventually planned to be added to the GitOps operator, when that happens we highly recommend using the GitOps operator 
via OLM instead.

Aside from that, the manifests are largely similar to the ones found on the ArgoCD upstream repo (directly referenced in fact), 
with some additional configurations to support a deployment on OCP like the following: 

- OCP Route for UI
- Metrics Services to be consumed by Prometheus Operator
- Configuration to support declarative deployment
- Plugin Configuration
- Custom image that adds [ksops][ksops] and [helm-secrets][hs], the image can be found [here][custom-image]


# Open Data Hub

Sure you could use [OLM][olm] to install [ODH][odh], but that in itself is a change not being tracked anywhere in Git.
As soon as you install an Operator via OLM, how can you ensure the state of your cluster can be replicated instantly
by ArgoCD if you had to manually click items via the OCP console to install an operator? To avoid this, we can replicate
the process OLM undergoes, by having ArgoCD auto deploy the `Subscription` and `OperatorGroup` for the operator.

Now the Operator subscription can be managed via ArgoCD, and the state of the cluster continues to match the state in 
your Git repo!



[ArgoCD]: https://github.com/operate-first/apps/tree/master/argocd
[op1st]: https://www.operate-first.cloud/
[GO]: https://github.com/redhat-developer/gitops-operator
[ksops]: https://github.com/viaduct-ai/kustomize-sops
[hs]: https://github.com/jkroepke/helm-secrets
[custom-image]: https://github.com/operate-first/continuous-deployment/blob/master/Dockerfile
[olm]: https://docs.openshift.com/container-platform/4.8/operators/understanding/olm/olm-understanding-olm.html
[odh]: https://opendatahub.io/