K3s installation
================

Compute requirements
---------------------

We scaled workadventure on a single node to easily 30 simultaneous connections.
We managed to work on a two node environment, but we got trouble with states on the back service.
To make it simple, just use a good single node by instance. If you really need to scale, think about your map setup.

We ran on a m4.xlarges during hackweek without any performance problems.

RAM usage was around 14GB and included the whole control plane of kubernetes.

Network requirements
--------------------

Due to the external use of STUN/TURN server, network wasn't a bottleneck. Work adventure traffic itself is low volume.
We didn't record network utilisation, sadly.

Complete list of firewall inbound rules required:

* tcp/80,443 for workadventure front end
* udp/3478,5349 (if you want to run your own stun/turn server)
* tcp/6443 between your kubernetes nodes (Internal/External IPs)
* udp/10000+ for websockets connections
* tcp/22 (for management, optional)
* udp/8472  (for flannel VXLAN, between internal IPs. NOT PUBLIC.)
* tcp/10250  (for metrics)
* tcp/2379-2380 (for etcd HA, NOT TESTED)

Storage requirements
--------------------

We never exceeded 2GB of storage space, for workadventure images and runtime data.
10GB storage for containers is large enough.
20GB total is enough.

Master node
-----------

On Digital ocean, the k3s install looks like this:

```
curl -sfL https://get.k3s.io | sh - server --no-deploy traefik
```

On AWS EC2, you need to pass the external node IP as the floating IP is not discoverable on the host.
k3s has a flag, ``--node-external-ip`` that you can use for it. The k3s install looks like this:

```
curl -sfL https://get.k3s.io | sh -s - server --no-deploy traefik --node-external-ip <MASTERNODE_EXTERNAL_IP>
```

Then, find the token for other node joins:

```
cat /var/lib/rancher/k3s/server/node-token
```

Other nodes
-----------

Join the cluster by issuing:

```
curl -sfL https://get.k3s.io | K3S_URL=https://<MASTERNODE_EXTERNAL_IP>:6443 K3S_TOKEN=<JOIN_TOKEN> K3S_NODE_NAME=<NODENAME> sh -s - --node-external-ip <CURRENT_NODE_EXTERNAL_IP>
```