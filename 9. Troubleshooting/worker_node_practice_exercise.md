# Worker Node Troubleshooting

```bash
Conditions:
  Type                 Status    LastHeartbeatTime                 LastTransitionTime                Reason              Message
  ----                 ------    -----------------                 ------------------                ------              -------
  NetworkUnavailable   False     Sun, 31 May 2020 06:43:52 +0000   Sun, 31 May 2020 06:43:52 +0000   WeaveIsUp           Weave pod has set this
  MemoryPressure       Unknown   Sun, 31 May 2020 07:03:13 +0000   Sun, 31 May 2020 07:04:07 +0000   NodeStatusUnknown   Kubelet stopped posting node status.
  DiskPressure         Unknown   Sun, 31 May 2020 07:03:13 +0000   Sun, 31 May 2020 07:04:07 +0000   NodeStatusUnknown   Kubelet stopped posting node status.
  PIDPressure          Unknown   Sun, 31 May 2020 07:03:13 +0000   Sun, 31 May 2020 07:04:07 +0000   NodeStatusUnknown   Kubelet stopped posting node status.
  Ready                Unknown   Sun, 31 May 2020 07:03:13 +0000   Sun, 31 May 2020 07:04:07 +0000   NodeStatusUnknown   Kubelet stopped posting node status.

node01 $ service kubelet status
● kubelet.service - kubelet: The Kubernetes Node Agent
   Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
  Drop-In: /etc/systemd/system/kubelet.service.d
           └─10-kubeadm.conf
   Active: inactive (dead) since Sun 2020-05-31 07:03:28 UTC; 2min 17s ago
     Docs: https://kubernetes.io/docs/home/
  Process: 3422 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_ARGS (code=exited, status=0/SUCCESS)
 Main PID: 3422 (code=exited, status=0/SUCCESS)
 
 node01 $ sudo service kubelet restart
 
 ## 2. Again, something is wrong with the kubelet process in node01
 
 -- The start-up result is done.
 May 31 07:09:08 node01 kubelet[14095]: Flag --cgroup-driver has been deprecated, This parameter should be set via the config file specified by the Kubelet's --config flag. See https://k
 May 31 07:09:08 node01 kubelet[14095]: Flag --cgroup-driver has been deprecated, This parameter should be set via the config file specified by the Kubelet's --config flag. See https://k
 May 31 07:09:08 node01 kubelet[14095]: F0531 07:09:08.414932   14095 server.go:251] unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-C
 May 31 07:09:08 node01 systemd[1]: kubelet.service: Main process exited, code=exited, status=255/n/a
 May 31 07:09:08 node01 systemd[1]: kubelet.service: Unit entered failed state.
 May 31 07:09:08 node01 systemd[1]: kubelet.service: Failed with result 'exit-code'.
 
 vim /var/lib/kubernetes/config.yaml
 #수정!
 
 
```