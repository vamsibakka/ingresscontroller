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
