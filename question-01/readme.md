# Kubernetes Practice Question 01 – Deployment with ConfigMap

## Scenario

CloudNova runs a backend API on Kubernetes.

Create a Kubernetes `Deployment` and `ConfigMap` using the following requirements.

## Requirements

* Namespace: `payments`
* Deployment name: `payment-api`
* Image: `cloudnova/payment-api:1.8`
* Replicas: `4`

### Labels

* `app=payment-api`
* `tier=backend`

### Container

* Container port: `9000`

### Resources

* CPU request: `200m`
* CPU limit: `500m`
* Memory request: `256Mi`
* Memory limit: `512Mi`

### Rolling Update Strategy

* `maxSurge: 1`
* `maxUnavailable: 1`

### ConfigMap

ConfigMap name:

`payment-config`

Values:

```text
LOG_LEVEL=info
PAYMENT_MODE=production
```

Both values should be available inside the container as environment variables.

---

## Solution

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: payment-api
  namespace: payments
  labels:
    app: payment-api
    tier: backend

spec:
  replicas: 4

  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1

  selector:
    matchLabels:
      app: payment-api
      tier: backend

  template:
    metadata:
      labels:
        app: payment-api
        tier: backend

    spec:
      containers:
        - name: backend-img
          image: cloudnova/payment-api:1.8

          ports:
            - containerPort: 9000

          resources:
            requests:
              memory: "256Mi"
              cpu: "200m"
            limits:
              memory: "512Mi"
              cpu: "500m"

          envFrom:
            - configMapRef:
                name: payment-config

---

apiVersion: v1
kind: ConfigMap

metadata:
  name: payment-config
  namespace: payments

data:
  LOG_LEVEL: "info"
  PAYMENT_MODE: "production"
```

## What I Practiced

Through this question, I practiced:

* Creating a Kubernetes Deployment
* Setting replicas
* Using labels and selectors
* Configuring rolling updates
* Setting CPU and memory requests and limits
* Creating a ConfigMap
* Injecting ConfigMap values as environment variables
* Exposing a container port

## Key Learning

`envFrom` can be used when we want to load all key-value pairs from a ConfigMap as environment variables inside the container.

```yaml
envFrom:
  - configMapRef:
      name: payment-config
```
