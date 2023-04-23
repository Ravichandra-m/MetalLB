# MetalLB


kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.9/config/manifests/metallb-native.yaml

root@node1:~/metalLB# ip a s
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:15:5d:0a:03:05 brd ff:ff:ff:ff:ff:ff
    inet 172.26.78.27/20 brd 172.26.79.255 scope global dynamic noprefixroute eth0
       valid_lft 82886sec preferred_lft 82886sec
    inet6 fe80::215:5dff:fe0a:305/64 scope link
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:15:5d:0a:03:0e brd ff:ff:ff:ff:ff:ff
    inet6 fe80::d1cf:4f67:8ac4:e6c7/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
4: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 02:42:f4:c0:78:e8 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
5: calia24c4dcd764@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default
    link/ether ee:ee:ee:ee:ee:ee brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::ecee:eeff:feee:eeee/64 scope link
       valid_lft forever preferred_lft forever
6: calif3e8b3c2045@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default
    link/ether ee:ee:ee:ee:ee:ee brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::ecee:eeff:feee:eeee/64 scope link
       valid_lft forever preferred_lft forever
9: vxlan.calico: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default
    link/ether 66:f9:37:c3:7e:94 brd ff:ff:ff:ff:ff:ff
    inet 192.168.166.128/32 scope global vxlan.calico
       valid_lft forever preferred_lft forever
    inet6 fe80::64f9:37ff:fec3:7e94/64 scope link
       valid_lft forever preferred_lft forever
root@node1:~/metalLB#
root@node1:~/metalLB#

root@node1:~/metalLB# sipcalc 172.26.78.27/24
-[ipv4 : 172.26.78.27/24] - 0

[CIDR]
Host address            - 172.26.78.27
Host address (decimal)  - 2887405083
Host address (hex)      - AC1A4E1B
Network address         - 172.26.78.0
Network mask            - 255.255.255.0
Network mask (bits)     - 24
Network mask (hex)      - FFFFFF00
Broadcast address       - 172.26.78.255
Cisco wildcard          - 0.0.0.255
Addresses in network    - 256
Network range           - 172.26.78.0 - 172.26.78.255
Usable range            - 172.26.78.1 - 172.26.78.254

-
root@node1:~/metalLB#

root@node1:~/metalLB# cat first-IP-pool.yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: first-pool
  namespace: metallb-system
spec:
  addresses:
  - 172.26.78.200-172.26.78.250
root@node1:~/metalLB#
root@node1:~/metalLB# k create -f first-IP-pool.yaml
ipaddresspool.metallb.io/first-pool created
root@node1:~/metalLB#


root@node1:~/metalLB#
root@node1:~/metalLB# kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   2d1h
root@node1:~/metalLB# k create deploy nginx --image nginx
deployment.apps/nginx created
root@node1:~/metalLB# k expose deploy nginx --port 80 --type LoadBalancer
service/nginx exposed
root@node1:~/metalLB#
root@node1:~/metalLB# kubectl get svc
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
kubernetes   ClusterIP      10.96.0.1      <none>          443/TCP        2d1h
nginx        LoadBalancer   10.98.241.78   172.26.78.200   80:31733/TCP   5s
root@node1:~/metalLB#
root@node1:~/metalLB#

root@node1:~/metalLB# curl 172.26.78.200
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>


https://metallb.universe.tf/installation/   0.13.9 version installed

[ Kube 33.1 ] How to deploy & use MetalLB in bare metal Kubernetes![image](https://user-images.githubusercontent.com/52405974/233828785-53b1d1b2-57c7-4ad8-9e73-f6a3b00ea3f9.png)
