##

- Identify the **Certificate file** used to authenticate `kube-apiserver`
- Identify the **Certificate file** used to authenticate **`kube-apiserver` as a client to `ETCD Server`**
- Identify the **key** used to authenticate **`kubeapi-server` to the `kubelet`** server
- Identify the ETCD **Server Certificate** used to host `ETCD server`
- Identify the ETCD Server **CA Root Certificate** used to serve `ETCD Server`



  

### `apiserver` yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    component: kube-apiserver
    tier: control-plane
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-apiserver
    - --advertise-address=172.17.0.34
    - --allow-privileged=true
    - --authorization-mode=Node,RBAC
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --enable-admission-plugins=NodeRestriction
    - --enable-bootstrap-token-auth=true
    - --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
    - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
    - --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key # 2. key used to for etcd comm.
    - --etcd-servers=https://127.0.0.1:2379
    - --insecure-port=0
    - --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
    - --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key # 3. kubelet key
    - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
    - --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
    - --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
    - --requestheader-allowed-names=front-proxy-client
    - --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
    - --requestheader-extra-headers-prefix=X-Remote-Extra-
    - --requestheader-group-headers=X-Remote-Group
    - --requestheader-username-headers=X-Remote-User
    - --secure-port=6443
    - --service-account-key-file=/etc/kubernetes/pki/sa.pub
    - --service-cluster-ip-range=10.96.0.0/12
    - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt #1.  tls cert used by the api
    - --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
```

### `etcd` yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    component: etcd
    tier: control-plane
  name: etcd
  namespace: kube-system
spec:
  containers:
  - command:
    - etcd
    - --advertise-client-urls=https://172.17.0.34:2379
    - --cert-file=/etc/kubernetes/pki/etcd/server.crt   #4. ETCD server certificate
    - --client-cert-auth=true
    - --data-dir=/var/lib/etcd
    - --initial-advertise-peer-urls=https://172.17.0.34:2380
    - --initial-cluster=master=https://172.17.0.34:2380
    - --key-file=/etc/kubernetes/pki/etcd/server.key
    - --listen-client-urls=https://127.0.0.1:2379,https://172.17.0.34:2379
    - --listen-metrics-urls=http://127.0.0.1:2381
    - --listen-peer-urls=https://172.17.0.34:2380
    - --name=master
    - --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
    - --peer-client-cert-auth=true
    - --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
    - --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    - --snapshot-count=10000
    - --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt # trusted CA.

```

- What is the Common Name (CN) configured on the Kube API Server Certificate?
- What is the name of the CA who issued the Kube API Server Certificate?
- Which of the below alternate names *is not configured* on the Kube API Server Certificate?
- How long, from the issued date, is the Kube-API Server Certificate valid for?


```shell script
openssl x509 -in file-path.crt -text -noout

openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 5844610356203484000 (0x511c3a272413f360)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN=kubernetes  # The CA the issued this Certificate
        Validity
            Not Before: May 13 15:20:40 2020 GMT
            Not After : May 13 15:20:41 2021 GMT ## expire date: 1 year
        Subject: CN=kube-apiserver   # Name of the CN
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:dd:32:31:37:db:cb:e3:93:1b:55:a3:60:28:aa:
                    e0:1e:3e:94:04:46:fc:44:d0:8a:94:97:0e:f2:3d:
                    01:50:a9:ff:b1:4a:93:c4:05:57:64:61:93:96:7f:
                    b9:6c:14:95:f4:a8:82:15:a4:e5:e6:1f:07:b7:d5:
                    10:f4:71:b7:e1:41:83:c8:37:9b:57:7d:13:74:3f:
                    72:d8:b1:83:bc:1b:b9:22:39:1a:71:43:b2:25:c5:
                    ed:8d:37:74:2b:3e:f8:96:5b:c6:4f:b2:2d:e5:c8:
                    b6:88:5d:9c:6c:d4:88:87:58:8e:7c:93:f7:c0:a8:
                    74:99:77:5d:92:92:79:77:8b:3e:56:ea:2e:b2:85:
                    cc:4b:9f:d1:93:78:c3:20:33:e4:a3:37:d5:8b:11:
                    d6:6f:70:c3:d1:ed:6f:64:45:18:c7:fb:2f:01:4b:
                    7d:f8:7f:05:07:c8:17:da:88:c7:ab:55:d0:46:bd:
                    a1:81:96:52:92:16:69:c8:e7:c8:0f:48:8f:05:6d:
                    58:47:c7:b2:f5:24:90:63:c1:50:9f:49:9b:c0:e9:
                    73:74:4e:f6:81:ca:e3:e2:1b:38:90:73:88:95:8e:
                    56:b6:ef:ba:50:63:7d:fd:01:16:e6:c3:e4:bb:5e:
                    3e:78:65:16:02:50:94:96:28:92:b6:aa:e5:ad:ba:
                    6e:cb
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage:
                TLS Web Server Authentication
            X509v3 Subject Alternative Name:
                DNS:master, # list of DNS
                DNS:kubernetes, 
                DNS:kubernetes.default, 
                DNS:kubernetes.default.svc, 
                DNS:kubernetes.default.svc.cluster.local, 
                IP Address:10.96.0.1, IP Address:172.17.0.34

    Signature Algorithm: sha256WithRSAEncryption
         0a:a4:37:17:4f:a9:bf:6c:ba:54:67:0f:c7:2a:22:df:99:3f:
         69:33:c7:7e:72:cb:8f:bd:9e:1c:7b:34:17:74:01:b2:dc:e3:
         d0:d7:a6:0b:9f:5f:39:56:1d:15:7e:fd:0c:fc:d4:d5:d7:2e:
         9f:44:a2:df:28:07:03:1c:e5:6f:76:22:c7:ce:bd:77:06:57:
         a6:b6:96:60:84:e6:2d:56:cc:bb:b0:b1:8b:24:0d:49:80:88:
         57:6c:98:72:46:3f:85:ad:66:f9:7a:65:c9:04:d3:25:c3:58:
         c2:1c:40:47:9d:86:fb:e7:5b:4d:d0:60:3a:0a:ac:64:d0:c3:
         8c:39:6f:26:72:85:8c:9f:60:a3:a1:28:e6:25:30:0e:b3:ba:
         8d:be:ab:19:0f:4c:11:ed:5a:69:f0:39:02:9a:54:ca:d2:56:
         f0:08:32:a8:e2:49:61:c1:21:7f:17:5b:e1:e4:09:87:25:83:
         a9:62:d3:45:7b:6c:15:33:32:c6:ec:97:36:8a:df:fb:70:75:
         99:fe:e0:94:9b:0f:90:20:15:49:51:fc:07:85:58:b5:64:e4:
         23:93:9f:19:10:b2:fa:7e:b8:47:83:4e:bc:9a:bf:52:37:46:
         a7:c9:3b:a2:5b:a1:5d:ab:c7:af:f6:e5:12:16:4e:c1:97:11:
         f4:ad:c6:2f

```

- What is the Common Name (CN) configured on the ETCD Server certificate?
  
```shell script
master $ openssl x509 -in /etc/kubernetes/pki/etcd/server.crt -text -noout

Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 3615702698107310483 (0x322d8e72761b9193)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN=etcd-ca
        Validity
            Not Before: May 13 15:20:42 2020 GMT
            Not After : May 13 15:20:43 2021 GMT
        Subject: CN=master # The CN value
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:cb:66:47:42:73:9e:b4:0c:4b:80:e2:2f:ed:80:
                    31:eb:7c:63:12:04:c3:b9:5d:b9:0c:d6:62:a1:80:
                    fa:f7:53:5c:a0:ae:0a:35:9e:73:e2:d6:a1:9c:fd:
                    0d:0b:b4:e0:36:4d:1d:dc:18:17:93:32:40:6b:ab:
                    90:72:db:f9:43:5f:e8:9c:ad:73:0b:67:2d:29:ea:
                    fb:04:e9:14:08:a5:dc:79:1c:be:93:85:eb:fd:c6:
                    9e:94:3e:1f:0b:50:8a:df:2e:0f:ec:e6:a8:57:e9:
                    f0:a2:90:42:a2:63:e5:72:ab:ff:bd:a3:6e:d9:1d:
                    77:49:34:b9:1c:e7:65:80:97:26:c6:67:39:51:3a:
                    73:a1:92:99:f2:2d:c4:00:8c:ec:8c:2f:fd:29:cc:
                    92:e3:70:1a:bc:74:3d:5d:e1:5b:b8:da:df:c1:50:
                    51:3d:3e:13:08:91:71:4a:8d:2a:60:90:9c:00:b8:
                    f9:94:3d:f0:9e:39:08:45:0e:2a:1f:a3:2b:f4:e3:
                    17:f7:6a:b1:c1:d4:1c:f6:0f:33:c4:35:6a:03:f1:
                    e9:56:40:69:8f:bc:68:0f:ac:26:dc:70:52:2a:59:
                    3c:d0:04:4c:a2:fc:59:c4:1b:70:8c:94:7e:47:eb:
                    5e:58:75:dd:ce:56:9f:61:cd:c4:42:8a:76:92:c1:
                    45:9d
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage:
                TLS Web Server Authentication, TLS Web Client Authentication
            X509v3 Subject Alternative Name:
                DNS:master, DNS:localhost, IP Address:172.17.0.34, IP Address:127.0.0.1, IP Address:0:0:0:0:0:0:0:1
    Signature Algorithm: sha256WithRSAEncryption
         ad:ff:76:eb:27:f1:36:66:aa:f5:82:cd:42:ed:54:cf:43:3a:
         c3:94:ec:c9:4a:1d:97:bc:a5:b0:e2:eb:e5:de:d4:25:5d:d5:
         d8:5d:28:4a:39:21:d2:11:84:a5:f8:0b:be:55:fe:70:6d:d9:
         90:55:b5:05:ce:ee:e8:fc:f1:bd:67:85:17:7d:81:ad:93:da:
         00:3e:6b:37:d2:ac:7a:61:2c:0f:3e:ee:cc:e1:f9:a6:2a:90:
         18:12:2e:e8:70:0e:10:84:bf:de:89:16:ce:fe:34:63:db:02:
         9a:c6:87:50:dd:8e:f3:04:d9:ef:65:85:2a:9d:d3:fd:63:e1:
         a8:69:ab:25:cc:b1:68:b1:e0:11:87:6b:6a:f9:74:2e:d2:de:
         89:e6:bd:0c:74:75:6d:5a:72:0a:c3:69:ff:9d:d8:42:51:94:
         68:3a:ee:59:98:08:58:0f:a4:f9:38:16:ed:ec:34:b8:96:c1:
         ea:b8:6a:97:23:84:c1:80:b9:ac:85:41:23:e1:ec:ed:9c:a5:
         3b:83:64:5e:8f:44:9e:57:6b:9a:af:4d:14:90:8e:98:42:44:
         bd:13:ae:79:19:e8:8c:4d:0e:fb:cb:00:9d:84:92:e6:96:75:
         3d:b0:81:50:94:cc:02:57:b7:0e:f3:36:f8:c6:a3:c9:96:4c:
         79:eb:e4:57


```  

- How long, from the issued date, is the Root CA Certificate valid for?
```shell script

master $ openssl x509 -in /etc/kubernetes/pki/ca.crt -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 0 (0x0)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN=kubernetes
        Validity
            Not Before: May 13 15:44:17 2020 GMT
            Not After : May 11 15:44:17 2030 GMT # 10 years period
        Subject: CN=kubernetes
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:fc:82:0d:8e:e7:fa:1a:cd:48:00:91:a3:aa:c3:
                    2d:47:6c:16:66:1a:24:a7:c9:80:d9:a2:a7:66:f8:
                    f8:57:ea:14:70:5c:9e:7f:75:ab:0b:04:82:4b:87:
                    0f:7d:0f:2e:a6:b0:fb:7b:b1:8b:6a:9f:61:b2:1e:
                    be:a0:4f:ba:2a:c2:74:90:4e:bf:72:ae:bc:3f:97:
                    37:d0:50:d1:7d:89:5f:61:7c:fc:65:a5:02:49:71:
                    27:2a:23:4e:d5:9e:2c:3f:e0:d1:f1:a6:36:09:06:
                    48:ae:dd:be:f7:89:66:8b:42:03:5a:08:b7:98:c4:
                    53:fe:c9:3b:c4:f2:d6:d2:e8:74:be:b7:30:49:5d:
                    b2:38:73:be:2e:54:e5:90:d8:41:2a:af:6b:8c:39:
                    ea:d1:45:9f:d3:fe:e7:bb:57:ad:a3:b3:fb:47:9a:
                    37:5b:47:62:aa:73:46:6f:8f:7e:73:44:41:c6:a1:
                    a5:43:fe:28:b1:69:da:e3:a4:80:1e:0a:ce:e2:40:
                    29:d9:0a:72:7e:09:0b:93:8b:9b:0a:a0:ec:a9:a0:
                    77:88:e2:a5:25:68:6b:ee:2d:1a:e9:fd:e7:35:5f:
                    00:70:6f:d0:e7:3c:e1:6d:2b:9d:1e:7f:50:e7:ed:
                    7c:eb:22:39:18:19:fb:d0:85:78:06:48:3f:9c:56:
                    f9:cf
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment, Certificate Sign
            X509v3 Basic Constraints: critical
                CA:TRUE
    Signature Algorithm: sha256WithRSAEncryption
         a6:1b:d9:c5:a5:e1:e7:65:68:9e:ba:e3:bf:90:7f:47:29:5a:
         31:7a:fd:f2:7b:ef:81:2d:e4:1d:a5:a1:d9:0f:a9:20:1c:33:
         10:ec:27:5e:6c:c1:b3:c1:c0:ce:8e:6e:3d:80:22:73:01:e5:
         3f:06:e1:d4:d6:b2:ce:94:b8:08:ea:d3:1e:69:e9:96:e7:fa:
         dc:87:65:7d:d9:38:e5:fd:7c:40:02:c7:40:a1:38:b0:43:37:
         c1:46:08:da:e4:e7:63:e0:dc:90:98:5c:ea:fd:aa:8d:10:bc:
         22:3e:65:cb:a2:d9:02:b5:86:04:24:35:27:21:a0:28:2a:19:
         7b:8d:2b:29:f2:dd:c1:77:bb:1f:37:46:c9:78:d3:57:85:4e:
         4e:84:19:f1:41:7f:36:80:3c:16:e7:b6:69:39:e3:ec:9e:3d:
         94:68:3a:cf:ea:25:f5:f3:2a:7d:61:e2:df:c8:42:e6:1f:47:
         14:7b:08:41:10:ae:91:0a:f8:de:85:92:e3:a3:b9:7b:6b:12:
         2f:37:96:e0:40:f8:71:34:59:11:96:e7:d8:77:52:4e:bb:98:
         e4:9a:06:30:fe:6a:36:45:f9:fe:55:65:5b:25:48:e8:e6:e8:
         ff:42:6d:6e:6e:c4:02:32:11:34:d8:51:60:84:12:67:c1:03:
         2d:7a:4b:45
```


Kubectl suddenly stops responding to your commands. Check it out! Someone recently modified the `/etc/kubernetes/manifests/etcd.yaml` file

You are asked to investigate and fix the issue. 
Once you fix the issue wait for sometime for kubectl to respond. Check the logs of the ETCD container.
```shell script
docker logs -f etcd-container

2020-05-13 15:51:26.961084 I | raft: a3fd6a805fbda170 became candidate at term 6
2020-05-13 15:51:26.961153 I | raft: a3fd6a805fbda170 received MsgVoteResp from a3fd6a805fbda170 at term 6
2020-05-13 15:51:26.961190 I | raft: a3fd6a805fbda170 became leader at term 6
2020-05-13 15:51:26.961200 I | raft: raft.node: a3fd6a805fbda170 elected leader a3fd6a805fbda170 at term 6
2020-05-13 15:51:26.961434 I | etcdserver: published {Name:master ClientURLs:[https://172.17.0.61:2379]} to cluster cb853b36b5066bc6
2020-05-13 15:51:26.961624 I | embed: ready to serve client requests
2020-05-13 15:51:26.961682 I | embed: ready to serve client requests
2020-05-13 15:51:26.961706 C | etcdmain: open /etc/kubernetes/pki/etcd/server-certificate.crt: no such file or directory

# fixes the manifest definition to the correct crt.

```

The kube-api server stopped again! Check it out. 
Inspect the kube-api server logs and identify the root cause and fix the issue.
```shell script
# Run docker ps -a command to identify the kube-api server container. 
# Run docker logs container-id command to view the logs.

1 clientconn.go:1120] grpc: addrConn.createTransport failed to connect to {https://127.0.0.1:2379 0  <nil>}. 
Err :connection error: desc = "transport: authentication handshake failed: x509: certificate signed by unknown authority". Reconnecting...

# it is having trouble communicating with etcd because its using the wrong ca.

```

The kube-api server stops responding one day when it tries to connect to ETCD server.


Inspect and fix the issue. If the certificate is expired, 
sign a new certificate by the CA and configure it to be used by the kube-api server

```shell script

# crs is the certificate signing request
openssl x509 -req -in /etc/kubernetes/pki/apiserver-etcd-client.csr -CA /etc/kubernetes/pki/etcd/ca.crt -CAkey /etc/kubernetes/pki/etcd/ca.key -CAcreateserial -out /etc/kubernetes/pki/apiserver-etcd-client.crt
```

