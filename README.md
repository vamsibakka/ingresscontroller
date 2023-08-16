## nginx annotations reference link : `https://github.com/kubernetes/ingress-nginx/blob/main/docs/user-guide/nginx-configuration/annotations.md`.

##kubernetes one page reference link : `https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#objectmeta-v1-meta`.

**nginx.ingress.kubernetes.io/use-regex: "true"** the "use-regex" When this annotation is set to true, the case insensitive regular expression location modifier will be enforced on ALL paths for a given host regardless of what Ingress they are defined on.
**nginx.ingress.kubernetes.io/rewrite-target: /$2** is used when In some scenarios the exposed URL in the backend service differs from the specified path in the Ingress rule. Without a rewrite any request will return 404. Set the annotation nginx.ingress.kubernetes.io/rewrite-target to the path expected by the service. `https://github.com/kubernetes/ingress-nginx/blob/main/docs/examples/rewrite/README.md` refer this link for the examples for the rewrite-target annotation.

example :
```
$ echo '
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
  name: rewrite
  namespace: default
spec:
  ingressClassName: nginx
  rules:
  - host: rewrite.bar.com
    http:
      paths:
      - path: /something(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: http-svc
            port: 
              number: 80

```
# here In this ingress definition, any characters captured by (.*) will be assigned to the placeholder $2, which is then used as a parameter in the rewrite-target annotation.

For example, the ingress definition above will result in the following rewrites:

    rewrite.bar.com/something rewrites to rewrite.bar.com/
    rewrite.bar.com/something/ rewrites to rewrite.bar.com/
    rewrite.bar.com/something/new rewrites to rewrite.bar.com/new
### INSTALLING HELM in k8s cluster to run the nginx ingress controller , to install helm follow the link `https://helm.sh/docs/intro/install/`.
## To install the nginx controller using helm use the following commands : 
```
helm repo add nginx-stable https://helm.nginx.com/stable
helm repo update
helm upgrade --install ingress-nginx ingress-nginx \
             --repo https://kubernetes.github.io/ingress-nginx \
             --namespace ingress-nginx --create-namespace
```
* you will get the following output :
```
  Release "ingress-nginx" does not exist. Installing it now.
NAME: ingress-nginx
LAST DEPLOYED: Wed Aug 16 10:11:31 2023
NAMESPACE: ingress-nginx
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The ingress-nginx controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
***You can watch the status by running 'kubectl --namespace ingress-nginx get services -o wide -w ingress-nginx-controller'***
An example Ingress that makes use of the controller:
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: example
    namespace: foo
  spec:
    ingressClassName: nginx
    rules:
      - host: www.example.com
        http:
          paths:
            - pathType: Prefix
              backend:
                service:
                  name: exampleService
                  port:
                    number: 80
              path: /
    # This section is only required if TLS is to be enabled for the Ingress
    tls:
      - hosts:
        - www.example.com
        secretName: example-tls

If TLS is enabled for the Ingress, a Secret containing the certificate and key must also be provided:

  apiVersion: v1
  kind: Secret
  metadata:
    name: example-tls
    namespace: foo
  data:
    tls.crt: <base64 encoded cert>
    tls.key: <base64 encoded key>
  type: kubernetes.io/tls
```
