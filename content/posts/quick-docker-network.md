+++
date = 2018-08-26T21:20:33+02:00
title = "Quick Docker Network Playground"
slug = ""
tags = []
categories = []
+++

Quick experiment to tst Docker network option and golang network interfaces.

<!--more-->

## Code

```golang
package main

import (
	"log"
	"net"
)

func main() {
	ifs, err := net.Interfaces()
	log.Println("Looking up ips...")
	if err != nil {
		log.Fatal(err)
	}

	for _, ief := range ifs {
		log.Println(ief.Name)
		addrs, err := ief.Addrs()

		if err != nil {
			log.Fatal(err)
		}

		for _, addr := range addrs {
			// if addr.isLookback
			log.Println("- " + addr.String())
		}
	}
	log.Println("Done.")
}
```

Compile from MacOS

```shell
  CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o lookup_ips_linux lookup_ips.go
```

## Simple Docker Image

```dockerfile
  FROM scratch
  MAINTAINER Stephane Busso <stephane.busso@gmail.com>
  ADD lookup_ips_linux /lookup_ips
  ENTRYPOINT ["/lookup_ips"]
```

```shell
  docker build . -t lookup_ips
```

 ## Run

```shell
  docker run -ti lookup_ip
```

> 2018/08/26 19:10:16 Looking up ips...
2018/08/26 19:10:16 127.0.0.1/8
2018/08/26 19:10:16 172.17.0.2/16
2018/08/26 19:10:16 Done.


```shell
docker run -ti --network host lookup_ips
```


> 2018/08/26 19:10:42 Looking up ips...
2018/08/26 19:10:42 127.0.0.1/8
2018/08/26 19:10:42 ::1/128
2018/08/26 19:10:42 10.0.2.15/24
2018/08/26 19:10:42 fe80::a00:27ff:fe0c:10d/64
2018/08/26 19:10:42 192.168.33.111/24
2018/08/26 19:10:42 fe80::a00:27ff:fe29:b7bf/64
2018/08/26 19:10:42 192.168.33.114/24
2018/08/26 19:10:42 fe80::a00:27ff:fe82:13b7/64
2018/08/26 19:10:42 192.168.33.112/24
2018/08/26 19:10:42 fe80::a00:27ff:fe05:e62e/64
2018/08/26 19:10:42 192.168.33.113/24
2018/08/26 19:10:42 fe80::a00:27ff:fe02:ee7b/64
2018/08/26 19:10:42 172.17.0.1/16
2018/08/26 19:10:42 fe80::42:ecff:fed1:2bc7/64
2018/08/26 19:10:42 Done.

 Voilà.

