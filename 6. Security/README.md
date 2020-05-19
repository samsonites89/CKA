# Certificate API

what is the CA server... and where is it.
> the CA is just a key and certificate

The server that holds this.. is the CA server.
`kubeadm` stores key in the master,so master is the `CA server`

kube has a built in api for signing request.

1. Create CertificateSigningRequest Object
2. Review Request
3. Approve Request
4. Share the certificate


`kubectl get csr`

Remember, the CSR must be encoded 

`kubectl certificate approve jane`


## Image Security 

# image security

```bash
# secret type of docker-registry with the name regcred!
kubectl create secret docker-registry regcred \
--docker-server= \
--docker-username= \
--docker-password= \
--docker-email=
```



on the pod you need to set the following:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - name: priv-nginx
    image: private.io/nginx
  imagePullSecrets:
  - name: regcred

```


## Network Policies

`Ingress` : Inward Traffic
`Egress`  : Outward Traffic

```yaml
apiVerision: networking.k8s.io/v1
type: NetworkPolicy
metadata:
  name: db-policy
spec:
  podSelector:
    matchLabels:
      role: db
    policyTypes:
    - Ingress
    ingress:
    - from:
      - podSelector:
          matchLabels:
            name: api-pod
      ports:
      -  protocol: tcp
         ports: 3306
```

Flannel does not support network policies

