# Helm minimal example showing overrides not working

## Description

Helm overrides from parent to child charts are not working properly.

### Parent Chart

#### default values file

```
child:
  enabled: false
```

#### Chart.yaml

```
apiVersion: v2
name: parent-child-test
description: A Helm chart for Kubernetes

type: application

version: 0.1.0

appVersion: "1.16.0"

dependencies:
  - name: child
    condition: child.enabled
    repository: file://charts/child
```

### Child Chart

#### default values file

Truncated for brevity
```
spec:
  test:
    value: set
```

### Example values file for testing

```
child:
  enabled: true
  spec:
    test: null
```

## To reproduce

Neither setting via a values file, or via `--set` options work:

```shell
❯ helm template --release-name demo . -f examples/child/values.yaml | yq '.spec.test'
value: set

❯ helm template --release-name demo . --set child.enabled=true --set child.spec.test=null | yq '.spec.test'
value: set
```

## Strangely working

If the parent's values file is updated to this:
```
child:
  enabled: true
  spec:
    test: null
```

This seems to work with 3.13.0

```shell
helm template --release-name demo .
```
