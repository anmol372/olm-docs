---
title: "CatalogSource"
weight: 3
---

A CatalogSource represents a store of metadata that OLM can query to discover and install operators and their dependencies.

The `.spec` of a CatalogSource indicates to how to construct a pod or how to talk to a service that serves the [operator-registry grpc api](/docs/concepts/olm-architecture/operator-registry/using-catalog-locally). 

There are three primary types:

 - `grpc` with and `image` referece: OLM will pull the image and run the pod, which is expected to serve a compliant api.
 - `grpc` with an `address` field: OLM will attempt to contact the grpc api at the given address. This should not be used in most cases.
 - `internal` or `configmap`: OLM will parse the configmap's data and spin up a pod that can serve the grpc api over it.

## Example - OperatorHub.io

```yaml
apiVersion: operators.coreos.com/v1alpha1
kind: CatalogSource
metadata:
  name: operatorhubio-catalog
  namespace: olm
spec:
  sourceType: grpc
  image: quay.io/operator-framework/upstream-community-operators:latest
  displayName: Community Operators
  publisher: OperatorHub.io
```

This defines a CatalogSource for OperatorHub.io's content. The `name` of the CatalogSource is used as input to a `Subscription`, which tells OLM where to look to find a requested operator:

```yaml
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: my-operator
  namespace: olm
spec:
  channel: stable
  name: my-operator
  source: operatorhubio-catalog
```