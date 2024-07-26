---
title: Traefik
tags:
 - kubernetes
 - docker
 - certificate
---

[Traefik](https://doc.traefik.io/traefik/) is a modern HTTP reverse proxy and load balancer that makes deploying 
microservices easy.
<!--more-->
Traefik integrates with your existing infrastructure components (Docker, Swarm mode, Kubernetes, Marathon, Consul, Etcd, 
Rancher, Amazon ECS, ...) and configures itself automatically and dynamically.

## [Configuration](https://doc.traefik.io/traefik/v2.0/getting-started/configuration-overview/)

There are three different, mutually exclusive (e.g. you can use only one at a time), ways to define static configuration 
options in Traefik:

1. In a configuration file
2. In the command-line arguments
3. As environment variables
These ways are evaluated in the order listed above.

## Middleware

### Basic Auth

You can add **basic auth** to a Kubernetes Ingress using a `htpasswd` containing your user(s):

1. Create the `htpasswd` file:
    ```shell
    htpasswd -c users my-app-user
    ```
2. Create the Secret:
    ```powershell
    kubectl create secret generic app-ui-basic-auth `
    -n <NAMESPACE> --from-file=users
    ```
3. Create the Traefik Middleware:
    ```yaml
    apiVersion: traefik.io/v1alpha1
    kind: Middleware
    metadata:
      name: my-app-auth
      namespace: <NAMESPACE>
    spec:
      basicAuth:
        # References the Secret containing the htpasswd file
        secret: app-ui-basic-auth
    ```
4. Add a Traefik annotation to your Ingress:
    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      annotations:
        traefik.ingress.kubernetes.io/router.middlewares: <NAMESPACE>-my-app-auth@kubernetescrd
      name: app-ui-ingress
      namespace: <NAMESPACE>
    spec:
      ...
    ```

### Traefik forward auth

[Traefik forward auth](https://github.com/thomseddon/traefik-forward-auth?tab=readme-ov-file) is a minimal forward 
authentication service that provides OAuth/SSO login and authentication for the traefik reverse proxy/load balancer.

You can use _Traefik forward auth_ to add login functionality to your Kubernetes Ingress' or IngressRoutes.

Below are the manifests for the setup for integration with an OpenID Connect provider such as [keycloak](./keycloak):

```yaml
#
# Traefik Forward Auth Deployment
#
# Based on: https://github.com/thomseddon/traefik-forward-auth/blob/master/examples/traefik-v2/kubernetes/simple-separate-pod/k8s-traefik-forward-auth.yml
#
apiVersion: apps/v1
kind: Deployment
metadata:
  name: traefik-forward-auth
  labels:
    app: traefik-forward-auth
spec:
  replicas: 1
  selector:
    matchLabels:
      app: traefik-forward-auth
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: traefik-forward-auth
    spec:
      terminationGracePeriodSeconds: 60
      containers:
#        - image: thomseddon/traefik-forward-auth:2
        - image: ghcr.io/stefanschoof/traefik-forward-auth:2.3.0
          name: traefik-forward-auth
          # Security
          securityContext:
            allowPrivilegeEscalation: false
            # *sigh* image runs as root
#            runAsNonRoot: true
            seccompProfile:
              type: RuntimeDefault
            capabilities:
              drop:
                - ALL
          ports:
            - containerPort: 4181
              protocol: TCP
          env:
            # INSECURE_COOKIE is required unless using https entrypoint
            - name: INSECURE_COOKIE
              value: "true"
            - name: COOKIE_DOMAIN
              value: "<YOUR_BASE_DOMAIN>" # Does support multiple domains
            - name: DEFAULT_PROVIDER
              value: oidc
            - name: PROVIDERS_OIDC_ISSUER_URL
              value: "https://<your keycloak URL>/auth/realms/master"
            - name: PROVIDERS_OIDC_CLIENT_ID
              valueFrom:
                secretKeyRef:
                  name: traefik-forward-auth-secrets
                  key: PROVIDERS_OIDC_CLIENT_ID
            - name: PROVIDERS_OIDC_CLIENT_SECRET
              valueFrom:
                secretKeyRef:
                  name: traefik-forward-auth-secrets
                  key: PROVIDERS_OIDC_CLIENT_SECRET
            - name: SECRET # Secret used for signing (required)
              valueFrom:
                secretKeyRef:
                  name: traefik-forward-auth-secrets
                  key: SECRET
---
# Auth Service
---
apiVersion: v1
kind: Service
metadata:
  name: traefik-forward-auth
  labels:
    app: traefik-forward-auth
spec:
  type: ClusterIP
  selector:
    app: traefik-forward-auth
  ports:
    - name: auth-http
      port: 4181
      targetPort: 4181
---
# Auth Middleware
---
apiVersion: traefik.containo.us/v1alpha1
kind: Middleware
metadata:
  name: traefik-forward-auth
spec:
  forwardAuth:
    address: http://traefik-forward-auth.<SVC_NAMESPACE>:4181
    authResponseHeaders:
      - X-Forwarded-User
```

With this setup you can then easily add an annotation to an Ingress to add SSO/OIDC functionality to the endpoint:

```yaml
kind: Ingress
apiVersion: networking.k8s.io/v1
metadata:
  name: whoami
  annotations:
    # Add authorisation to ingress endpoint
    traefik.ingress.kubernetes.io/router.middlewares: <TRAEFIK-FORWARD-AUTH_NAMESPACE>-traefik-forward-auth@kubernetescrd
spec:
  ...
```