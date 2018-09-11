+++
draft = true
date = 2018-08-31T15:53:36+02:00
title = ""
slug = ""
tags = []
categories = []
+++


## Docker Host Network

In a container running with host networking option you can use a host network interface and its IP from the container and contact external network from this interface and source IP. So if a host has several IPs configured, the container can choose which one it uses.

`docker run --rm -ti --network host sbusso/lookup_ips:latest`

## Kubernetes case

`hostNetwork=true` in pod specification exposes host network to the pod, and container can access network interfaces:

    apiVersion: v1
    kind: Pod
    metadata:
      name: lookup
    spec:
      hostNetwork: true
      containers:
        - name: lookup
          image: sbusso/lookup_ips:latest
          ports:
          - containerPort: 9000

To test it: `kubectl port-forward lookup 9000` and then go to [http://127.0.0.1:9000/][1] and get network interfaces details:

    lo
    - 127.0.0.1/8
    - ::1/128
    eth0
    - 10.0.2.15/24
    - fe80::a00:27ff:fea1:6e61/64
    eth1
    - 192.168.99.101/24
    - fe80::a00:27ff:fe77:d179/64

## Notes

This option is not recommended in Kubernetes good practices: [https://kubernetes.io/docs/concepts/configuration/overview/#services][2]

It will limit how many pods the controller can deloy to each node.

  [1]: http://127.0.0.1:9000/
  [2]: https://kubernetes.io/docs/concepts/configuration/overview/#services