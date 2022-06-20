# Kubernetes Deployment Files

Status: **Under development**

This GitHub project contains some examples of Kubernetes manifest files to help you deploying Falco.

These resource definitions are automatically generated from the Helm chart located at the [charts repo](https://github.com/falcosecurity/charts) and it is strongly recommended that they are considered as templates, not as final resources to deploy Falco.

For ease of use, they are referenced by a [kustomization.yaml](https://github.com/vjjmiras/deploy-kubernetes/blob/main/kubernetes/falco/kustomization.yaml) file, but they can also be used individually. There are more detailed steps under the `kubernetes/` directory.
