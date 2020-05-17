# Networking (WIP)

## Networking Prerequisites

- Switching and Routing
  + Switching
  + Routing
  + Default Gateway
- DNS
  + DNS Configuration on Linux
  + CoreDNS Introduction
- Network Namespaces
- Docker Networking


### Switching and Routing

what is a network?
Connect A and B to a switch to create a network
`iplink` command is used.

192.168.1.0
`ip addr add 192.168.1.101`

How do you connect a network on a different switch
> Use a router.


Router is just a device on the network.

Network is a room. Router is a door.
`ip route add 192.168.1.0/24 via $GATEWAY`

0.0.0.0 entry in the gateway means *you don't need a gateway*


> In Linux, ip_forwarding is not configured by default

`cat /proc/sys/net/ipv4/ip_forward` 0

Permanently apply:
```shell script
/etc/sysctl.conf
net.ipv4.ip_forward = 1
```

### Key commands

> To make these persist, you must set it int the `etc/networkinterfaces file`
- `ip link` : list and modify interfaces on the host
- `ip  addr` : see the ip addresses set on those interfaces
- `ip addr add 0.0.0.0/24 dev eth0` : set ip on the interfaces
- `ip route` : view the routing table
- `ip route add 192.168.1.0/24 via 192.168.2.1` : add entries to the routing table
- `cat /proc/sys/net/ipv4/ip_forward` : check ip forwarding is enabled


# DNS (Domain Name Service)
```shell script
cat >> /etc/hosts
192.168.1.11    db

# If you have a big system, you cannot set up everything in the /etc/hosts. --> DNS Server
/etc/resolv.conf
nameserver 192.168.1.100

search mycompany.com
# /etc/hosts comes first!
```


`nslookup www.google.com` > /etc/hosts를 배제됨
`dig` check hostname



```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: my-nginx
    image: nginx
    ports:
    - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
  type: LoadBalancer
```


# network namepspaces
`ip netns add red`
`ip netns`

`ip netns exec red ip link`
`ip -n red link`

`arp` host shows info, not inside the container

`ip link add veth-red type veth peer name veth-blue`
`ip link set veth-red netns red`
`ip link set veth-blue netns blue`

`ip -n red addr add 192.168.15.1 dev veth-red`
`ip -n blue addr add 192.168.15.2 dev veth-blue`

`ip -n red link set veth-red up`
`ip -n blue link set veth-blue up`


virtual switch

## linux bridge

`ip link add v-net-0 type bridge`
`ip link add dev v-net-0 up`

`ip -n red linke del veth-red`
`ip link add veth-red type veth peer name veth-red-br`
`ip link add veth-blue type veth peer name veth-blue-br`

`ip link set veth-red netns red`
`ip link set veth-red-br master v-net-0`
