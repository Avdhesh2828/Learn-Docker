# Docker Custom Bridge Network – Hands-on Guide

## 🎯 Learning Objective
Create a custom Docker **bridge network**, launch a container inside it, and verify **internet connectivity** from the container.

---

## 📌 Prerequisites
- Docker installed on macOS / Linux
- Basic Docker CLI knowledge

---

## 🔍 List Existing Docker Networks
```
docker network ls

Output:
NETWORK ID     NAME               DRIVER    SCOPE
28c2a15588ea   bridge             bridge    local
3bd57be54a6f   frontend_default   bridge    local
0a350b9a69b3   host               host      local
65d27fd56d89   none               null      local
```
##🌐 Create a Custom Bridge Network

Create a new bridge network with a custom subnet and gateway.
```
fintech@fintech-MacBook-Air ~ % docker network create --driver bridge --ipam-driver  default --subnet 11.1.2.0/24 --gateway 11.1.2.1 fintech-network
40c386dff88f4d797358e55463928a0d66fb74c75508dd9224235a57a23281c3
fintech@fintech-MacBook-Air ~ % docker network ls
NETWORK ID     NAME               DRIVER    SCOPE
0a02a79e96f7   bridge             bridge    local
40c386dff88f   fintech-network    bridge    local
3bd57be54a6f   frontend_default   bridge    local
0a350b9a69b3   host               host      local
65d27fd56d89   none               null      local
fintech@fintech-MacBook-Air ~ %
```


##📋 Verify Network Creation
```
fintech@fintech-MacBook-Air docker-networking % docker network ls | grep -iE 'fintech|NETWORK'
NETWORK ID     NAME               DRIVER    SCOPE
40c386dff88f   fintech-network    bridge    local
fintech@fintech-MacBook-Air docker-networking %
```
##🔎 Inspect the Custom Network
```
fintech@fintech-MacBook-Air ~ % docker inspect fintech-network
[
    {
        "Name": "fintech-network",
        "Id": "40c386dff88f4d797358e55463928a0d66fb74c75508dd9224235a57a23281c3",
        "Created": "2026-01-11T01:24:05.341357333Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv4": true,
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "11.1.2.0/24",
                    "IPRange": "",
                    "Gateway": "11.1.2.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Options": {
            "com.docker.network.enable_ipv4": "true",
            "com.docker.network.enable_ipv6": "false"
        },
        "Labels": {},
        "Containers": {
            "a8aa815648cf462974b6e64fa59ac9931318a1957a9cec8d5a17269d6e8e8876": {
                "Name": "conatiner01",
                "EndpointID": "fbcaca41f1305015bf3558b6e48ae72cda84d8c44962f43628279928e7b6bc3e",
                "MacAddress": "c6:64:59:30:87:40",
                "IPv4Address": "11.1.2.2/24",
                "IPv6Address": ""
            }
        },
        "Status": {
            "IPAM": {
                "Subnets": {
                    "11.1.2.0/24": {
                        "IPsInUse": 4,
                        "DynamicIPsAvailable": 252
                    }
                }
            }
        }
    }
]
fintech@fintech-MacBook-Air ~ %
```

##🚀 Launch a Container in the Custom Network
```
akankshabansal@Akankshas-MacBook-Air ~ % docker run -dit --name conatiner01 --net fintech-network ubuntu:14.04
Unable to find image 'ubuntu:14.04' locally
14.04: Pulling from library/ubuntu
a72d031efbfb: Pull complete
d1a5a1e51f25: Pull complete
75f8eea31a63: Pull complete
Digest: sha256:64483f3496c1373bfd55348e88694d1c4d0c9b660dee6bfef5e12f43b9933b30
Status: Downloaded newer image for ubuntu:14.04
a8aa815648cf462974b6e64fa59ac9931318a1957a9cec8d5a17269d6e8e8876
akankshabansal@Akankshas-MacBook-Air ~ %
```

##🔁 Verify Container in newly created Network
```
akankshabansal@Akankshas-MacBook-Air ~ % docker inspect fintech-network | grep 'Containers' -A8
        "Containers": {
            "a8aa815648cf462974b6e64fa59ac9931318a1957a9cec8d5a17269d6e8e8876": {
                "Name": "conatiner01",
                "EndpointID": "fbcaca41f1305015bf3558b6e48ae72cda84d8c44962f43628279928e7b6bc3e",
                "MacAddress": "c6:64:59:30:87:40",
                "IPv4Address": "11.1.2.2/24",
                "IPv6Address": ""
            }
        },
akankshabansal@Akankshas-MacBook-Air ~ %
```

#📦 List Running Containers
```
fintech@fintech-MacBook-Air ~ % docker ps
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS         PORTS     NAMES
a8aa815648cf   ubuntu:14.04   "/bin/bash"   3 minutes ago   Up 3 minutes             conatiner01
```
#🔐 Access the Container
```
fintech@fintech-MacBook-Air ~ % docker attach a8aa815648cf
root@a8aa815648cf:/#
```
###🌍 Test Internet Connectivity from Container
```
root@a8aa815648cf:/# ping google.com
PING google.com (142.250.191.78) 56(84) bytes of data.
64 bytes from nuq04s43-in-f14.1e100.net (142.250.191.78): icmp_seq=1 ttl=63 time=19.2 ms
64 bytes from nuq04s43-in-f14.1e100.net (142.250.191.78): icmp_seq=2 ttl=63 time=22.1 ms
^C
--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 19.251/20.696/22.142/1.452 ms
root@a8aa815648cf:/#
```
