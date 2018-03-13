# raspi-k8s
Kubernetes cluster built on Raspberry PI

# Shopping List
- 3 x PI3
- 3 microSD Card  (cat10)
- 1 5 Port Switch
- 5 port use charger + cables
- 1 Raspi case
- 5 CAT6 ethernet cables

# Set up OS and networking
- Flash carts with Hypriot OS images https://github.com/hypriot/flash
- Configure Neworking inside the cluster
- Allocate statoc IP ni the master node
```
allow-hotplug eth0
iface eth0 inet static
    address 10.0.0.1
    netmask 255.255.255.0
    broadcast 10.0.0.255
    gateway 10.0.0.1

```
- Install DHCP Server n master node
```
option domain-name "cluster.home";
option domain-name-servers 8.8.8.8, 8.8.4.4;

default-lease-time 600;
max-lease-time 7200;
authoritative;

subnet 10.0.0.0 netmask 255.255.255.0 {
  range 10.0.0.1 10.0.0.10;
  option subnet-mask 255.255.255.0;
  option broadcast-address 10.0.0.255;
  option routers 10.0.0.1;
}
```
- Start DHCP Server
```
 /etc/init.d/isc-dhcp-server start
```
- Configure NAT & iptables
```
# enable IP forwarding
sudo vim /etc/sysctl.conf
net.ipv4.ip_forward=1

# configure IP tables
sudo vim /etc/rc.local
iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE
iptables -A FORWARD -i wlan0 -o eth0 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD -i eth0 -o wlan0 -j ACCEPT

```
# Set up Kubernetes https://kubernetes.io/

