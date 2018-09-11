---
title: "Choose Which Network Interface to Use"
author: "Stephane Busso"
cover: "/img/cover.jpg"
tags: ["golang", "net"]
date: 2018-08-23T09:18:23+02:00

---

Cut out summary from your post content here.

<!--more-->

The remaining content of your post.

```golang
func InterfaceByIP(ip string) (*net.Interface, error) {
	if ip == "" {
    return nil, &net.OpError{Op: "route", Net: "ip+net",
                Source: nil, Addr: nil, Err: ErrInvalidInterfaceIP}
	}

	ift, err := net.Interfaces()
	if err != nil {
		return nil, &net.OpError{Op: "route", Net: "ip+net", Source: nil, Addr: nil, Err: err}
	}
	for _, ifi := range ift {
		addrs, _ := ifi.Addrs()
		for _, addr := range addrs {
			if ip == addr.String() {
				return &ifi, nil
			}
		}
	}
  return nil, &net.OpError{Op: "route", Net: "ip+net",
              Source: nil, Addr: nil, Err: ErrNoSuchInterface}
}
```

Another example

{{< tweet 1032341997200318464 >}}
