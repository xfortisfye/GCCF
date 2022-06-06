#### Google Cloud Computing Foundations | C03 Networking and Security in Google Cloud
#### M07: It Helps to Network

# Lab 07-01: Multiple VPC Networks

## Overview 
In this lab you create several VPC networks and VM instances and test connectivity across networks. Specifically, you create two custom mode networks (managementnet and privatenet) with firewall rules and VM instances as shown in this network diagram

https://cdn.qwiklabs.com/OBtRY37ZCmWiHi%2FHsG8XCSGDBfsuKk3IMJVgQscsg2E%3D

The mynetwork network with its firewall rules and two VM instances (mynet-eu-vm and mynet-us-vm) have already been created for you in this Qwiklabs project.

## Create custom mode VPC networks with firewall rules
Create two custom networks managementnet and privatenet, along with firewall rules to allow SSH, ICMP, and RDP ingress traffic.

### Create the managementnet network
Create the managementnet network using the Cloud Console.

1. In the Cloud Console, navigate to Navigation menu > VPC network > VPC networks.
2. Notice the default and mynetwork networks with their subnets. Each Google Cloud project starts with the default network. In addition, the mynetwork network has been premade as part of your network diagram.
3. Click Create VPC Network.
4. Set the Name to managementnet.
5. For Subnet creation mode, click Custom.
6. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | managementsubnet-us |
| Region | us-central1 |
| IP address range | 10.130.0.0/20 |

7. Click Done.
8. Click EQUIVALENT COMMAND LINE.

These commands illustrate that networks and subnets can be created using the Cloud Shell command line. You will create the privatenet network using these commands with similar parameters.

```bash
gcloud compute networks create managementnet --project=qwiklabs-gcp-00-c83ea62b7aca --subnet-mode=custom --mtu=1460 --bgp-routing-mode=regional
```

```bash
gcloud compute networks subnets create managementsubnet-us --project=qwiklabs-gcp-00-c83ea62b7aca --range=10.130.0.0/20 --stack-type=IPV4_ONLY --network=managementnet --region=us-central1
```

9. Click Close.

10. Click Create.

## Create the privatenet network
Create the privatenet network using the Cloud Shell command line.

1. Run the following command to create the privatenet network:
```bash
gcloud compute networks create privatenet --subnet-mode=custom
```

2. Run the following command to create the privatesubnet-us subnet:
```bash
gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24
```

3. Run the following command to create the privatesubnet-eu subnet:
```bash
gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west4 --range=172.20.0.0/20
```

4. Run the following command to list the available VPC networks:
```bash
gcloud compute networks list
```

The output should look like this (do not copy; this is example output):

```bash
NAME           SUBNET_MODE  BGP_ROUTING_MODE  IPV4_RANGE  GATEWAY_IPV4
default        AUTO         REGIONAL
managementnet  CUSTOM       REGIONAL
mynetwork      AUTO         REGIONAL
privatenet     CUSTOM       REGIONAL
```

> default and mynetwork are auto mode networks, whereas, managementnet and privatenet are custom mode networks. Auto mode networks create subnets in each region automatically, while custom mode networks start with no subnets, giving you full control over subnet creation

5. Run the following command to list the available VPC subnets (sorted by VPC network):

```bash
gcloud compute networks subnets list --sort-by=NETWORK
```

The output should look like this (do not copy; this is example output):

```bash
NAME                REGION                   NETWORK        RANGE
default             asia-northeast1          default        10.146.0.0/20
default             us-west1                 default        10.138.0.0/20
default             southamerica-east1       default        10.158.0.0/20
default             europe-west4             default        10.164.0.0/20
default             asia-east1               default        10.140.0.0/20
default             europe-north1            default        10.166.0.0/20
default             asia-southeast1          default        10.148.0.0/20
default             us-east4                 default        10.150.0.0/20
default             europe-west1             default        10.132.0.0/20
default             europe-west2             default        10.154.0.0/20
default             europe-west3             default        10.156.0.0/20
default             australia-southeast1     default        10.152.0.0/20
default             asia-south1              default        10.160.0.0/20
default             us-east1                 default        10.142.0.0/20
default             us-central1              default        10.128.0.0/20
default             northamerica-northeast1  default        10.162.0.0/20
managementsubnet-us us-central1              managementnet  10.130.0.0/20
mynetwork           asia-northeast1          mynetwork      10.146.0.0/20
mynetwork           us-west1                 mynetwork      10.138.0.0/20
mynetwork           southamerica-east1       mynetwork      10.158.0.0/20
mynetwork           europe-west4             mynetwork      10.164.0.0/20
mynetwork           asia-east1               mynetwork      10.140.0.0/20
mynetwork           europe-north1            mynetwork      10.166.0.0/20
mynetwork           asia-southeast1          mynetwork      10.148.0.0/20
mynetwork           us-east4                 mynetwork      10.150.0.0/20
mynetwork           europe-west1             mynetwork      10.132.0.0/20
mynetwork           europe-west2             mynetwork      10.154.0.0/20
mynetwork           europe-west3             mynetwork      10.156.0.0/20
mynetwork           australia-southeast1     mynetwork      10.152.0.0/20
mynetwork           asia-south1              mynetwork      10.160.0.0/20
mynetwork           us-east1                 mynetwork      10.142.0.0/20
mynetwork           us-central1              mynetwork      10.128.0.0/20
mynetwork           northamerica-northeast1  mynetwork      10.162.0.0/20
privatesubnet-eu    europe-west1             privatenet     172.20.0.0/20
privatesubnet-us    us-central1              privatenet     172.16.0.0/24
```

> As expected, the default and mynetwork networks have subnets in each region as they are auto mode networks. The managementnet and privatenet networks only have the subnets that you created as they are custom mode networks.

6. In the Cloud Console, navigate to Navigation menu > VPC network > VPC networks.
7. You see that the same networks and subnets are listed in the Cloud Console.

### Create the firewall rules for managementnet
Create firewall rules to allow SSH, ICMP, and RDP ingress traffic to VM instances on the managementnet network.

1. In the Cloud Console, navigate to Navigation Menu > VPC network > Firewall.

2. Click + Create Firewall Rule.

3. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | managementnet-allow-icmp-ssh-rdp |
| Network | managementnet |
| Targets | All instances in the network |
| Source filter	| IPv4 Ranges |
| Source IPv4 ranges | 0.0.0.0/0 | 
| Protocols and ports | Specified protocols and ports, and then check tcp, type: 22, 3389; and check Other protocols, type: icmp. |
> Make sure to include the /0 in the Source IPv4 ranges to specify all networks.
4. Click EQUIVALENT COMMAND LINE.

These commands illustrate that firewall rules can also be created using the Cloud Shell command line. You will create the privatenet's firewall rules using these commands with similar parameters.

```bash
gcloud compute --project=qwiklabs-gcp-00-c83ea62b7aca firewall-rules create managementnet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=managementnet --action=ALLOW --rules=tcp:22,tcp:3389,icmp --source-ranges=0.0.0.0/0
```

5. Click Close.

6. Click Create.

### Create the firewall rules for privatenet
Create the firewall rules for privatenet network using the Cloud Shell command line.

1. In Cloud Shell, run the following command to create the privatenet-allow-icmp-ssh-rdp firewall rule:

```bash
gcloud compute firewall-rules create privatenet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=privatenet --action=ALLOW --rules=icmp,tcp:22,tcp:3389 --source-ranges=0.0.0.0/0
```

The output should look like this (do not copy; this is example output):

```bash
NAME                           NETWORK     DIRECTION  PRIORITY  ALLOW                 DENY
privatenet-allow-icmp-ssh-rdp  privatenet  INGRESS    1000      icmp,tcp:22,tcp:3389
```

2. Run the following command to list all the firewall rules (sorted by VPC network):

```bash
gcloud compute firewall-rules list --sort-by=NETWORK
```

The output should look like this (do not copy; this is example output):

```bash
NAME                              NETWORK        DIRECTION  PRIORITY  ALLOW                         DENY
default-allow-icmp                default        INGRESS    65534     icmp
default-allow-internal            default        INGRESS    65534     tcp:0-65535,udp:0-65535,icmp
default-allow-rdp                 default        INGRESS    65534     tcp:3389
default-allow-ssh                 default        INGRESS    65534     tcp:22
managementnet-allow-icmp-ssh-rdp  managementnet  INGRESS    1000      icmp,tcp:22,tcp:3389
mynetwork-allow-icmp              mynetwork      INGRESS    1000      icmp
mynetwork-allow-rdp               mynetwork      INGRESS    1000      tcp:3389
mynetwork-allow-ssh               mynetwork      INGRESS    1000      tcp:22
privatenet-allow-icmp-ssh-rdp     privatenet     INGRESS    1000      icmp,tcp:22,tcp:3389
```

The firewall rules for mynetwork network have been created for you. You can define multiple protocols and ports in one firewall rule (privatenet and managementnet), or spread them across multiple rules (default and mynetwork).

3. In the Cloud Console, navigate to Navigation menu > VPC network > Firewall.

4. You see that the same firewall rules are listed in the Cloud Console.

## Create VM instances
Create two VM instances:
- managementnet-us-vm in managementsubnet-us
- privatenet-us-vm in privatesubnet-us

### Create the managementnet-us-vm instance
Create the managementnet-us-vm instance using the Cloud Console.

1. In the Cloud Console, navigate to Navigation menu > Compute Engine > VM instances.

The mynet-eu-vm and mynet-us-vm has been created for you, as part of your network diagram.

2. Click Create instance.

3. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | managementnet-us-vm |
| Region | us-central1 |
| Zone | us-central1-f |
| Series | N1 |
| Machine type | 1 vCPU (f1-micro) |

4. Click NETWORKING, DISKS, SECURITY, MANAGEMENT, SOLE-TENANCY.
5. Click Networking.
6. For Network interfaces, click the dropdown to edit.
7. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Network | managementnet |
| Subnetwork | managementsubnet-us |

8. Click Done.
9. Click EQUIVALENT COMMAND LINE.

This illustrate that VM instances can also be created using the Cloud Shell command line. You will create the privatenet-us-vm instance using these commands with similar parameters.

```bash
gcloud compute instances create managementnet-us-vm --project=qwiklabs-gcp-00-c83ea62b7aca --zone=us-central1-f --machine-type=f1-micro --network-interface=network-tier=PREMIUM,subnet=managementsubnet-us --metadata=enable-oslogin=true --maintenance-policy=MIGRATE --provisioning-model=STANDARD --service-account=17523071719-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --create-disk=auto-delete=yes,boot=yes,device-name=managementnet-us-vm,image=projects/debian-cloud/global/images/debian-11-bullseye-v20220519,mode=rw,size=10,type=projects/qwiklabs-gcp-00-c83ea62b7aca/zones/us-central1-f/diskTypes/pd-balanced --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --reservation-affinity=any
```

10. Click Close.
11. Click Create.

### Create the privatenet-us-vm instance
Create the privatenet-us-vm instance using the Cloud Shell command line.

1. In Cloud Shell, run the following command to create the privatenet-us-vm instance:

```bash
gcloud compute instances create privatenet-us-vm --zone=us-central1-f --machine-type=n1-standard-1 --subnet=privatesubnet-us
```

The output should look like this (do not copy; this is example output):

```bash
NAME              ZONE           MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP    STATUS
privatenet-us-vm  us-central1-f  n1-standard-1               172.16.0.2   35.184.221.40  RUNNING
```

2. Run the following command to list all the VM instances (sorted by zone):

```bash
gcloud compute instances list --sort-by=ZONE
```

The output should look like this (do not copy; this is example output):

```bash
NAME                 ZONE            MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
mynet-eu-vm          europe-west4-c  n1-standard-1               10.132.0.2   35.205.124.164  RUNNING
managementnet-us-vm  us-central1-f   n1-standard-1               10.130.0.2   35.226.20.87    RUNNING
mynet-us-vm          us-central1-f   n1-standard-1               10.128.0.2   35.232.252.86   RUNNING
privatenet-us-vm     us-central1-f   n1-standard-1               172.16.0.2   35.184.221.40   RUNNING
```

3. In the Cloud Console, navigate to Navigation Menu > Compute Engine > VM instances.

4. You see that the VM instances are listed in the Cloud Console.

5. Click on Column display options, then select Network. Click Ok.

There are three instances in us-central1 and one instance in europe-west4. However, these instances are spread across three VPC networks (managementnet, mynetwork and privatenet), with no instance in the same zone and network as another. In the next section, you explore the effect this has on internal connectivity.

## Explore the connectivity between VM instances
Explore the connectivity between the VM instances. Specifically, determine the effect of having VM instances in the same zone versus having instances in the same VPC network.

### Ping the external IP addresses
Ping the external IP addresses of the VM instances to determine if you can reach the instances from the public internet.

1. In the Cloud Console, navigate to Navigation menu > Compute Engine > VM instances.

2. Note the external IP addresses for mynet-eu-vm, managementnet-us-vm, and privatenet-us-vm.

3. For mynet-us-vm, click SSH to launch a terminal and connect.

4. To test connectivity to mynet-eu-vm's external IP, run the following command, replacing mynet-eu-vm's external IP:

```bash
ping -c 3 <Enter mynet-eu-vm's external IP here>

# PING x.x.x.x (x.x.x.x) 56(84) bytes of data.
# 64 bytes from x.x.x.x: icmp_seq=1 ttl=51 time=103 ms
# 64 bytes from x.x.x.x: icmp_seq=2 ttl=51 time=103 ms
# 64 bytes from x.x.x.x: icmp_seq=3 ttl=51 time=103 ms
#
# --- x.x.x.x ping statistics ---
# 3 packets transmitted, 3 received, 0% packet loss, time 2003ms
# rtt min/avg/max/mdev = 1.404/1.506/1.646/0.107 ms
```

This should work!

5. To test connectivity to managementnet-us-vm's external IP, run the following command, replacing managementnet-us-vm's external IP:

```bash
ping -c 3 <Enter managementnet-us-vm's external IP here>

# PING x.x.x.x (x.x.x.x) 56(84) bytes of data.
# 64 bytes from x.x.x.x: icmp_seq=1 ttl=61 time=1.46 ms
# 64 bytes from x.x.x.x: icmp_seq=2 ttl=61 time=1.40 ms
# 64 bytes from x.x.x.x: icmp_seq=3 ttl=61 time=1.64 ms
# 
# --- x.x.x.x ping statistics ---
# 3 packets transmitted, 3 received, 0% packet loss, time 2003ms
# rtt min/avg/max/mdev = 1.322/1.386/1.480/0.080 ms
```
This should work!

6. To test connectivity to privatenet-us-vm's external IP, run the following command, replacing privatenet-us-vm's external IP:

```bash
ping -c 3 <Enter privatenet-us-vm's external IP here>

# PING  x.x.x.x (x.x.x.x) 56(84) bytes of data.
# 64 bytes from x.x.x.x: icmp_seq=1 ttl=64 time=1.35 ms
# 64 bytes from x.x.x.x: icmp_seq=2 ttl=64 time=1.32 ms
# 64 bytes from x.x.x.x: icmp_seq=3 ttl=64 time=1.48 ms

# --- x.x.x.x ping statistics ---
# 3 packets transmitted, 3 received, 0% packet loss, time 2003ms
# rtt min/avg/max/mdev = 1.322/1.386/1.480/0.080 ms
```

This should work!

> You are able to ping the external IP address of all VM instances, even though they are either in a different zone or VPC network. This confirms public access to those instances is only controlled by the ICMP firewall rules that you established earlier.

### Ping the internal IP addresses
Ping the internal IP addresses of the VM instances to determine if you can reach the instances from within a VPC network.

1. In the Cloud Console, navigate to Navigation menu > Compute Engine > VM instances.
2. Note the internal IP addresses for mynet-eu-vm, managementnet-us-vm, and privatenet-us-vm.
3. Return to the SSH terminal for mynet-us-vm.
4. To test connectivity to mynet-eu-vm's internal IP, run the following command, replacing mynet-eu-vm's internal IP:

```bash
ping -c 3 <Enter mynet-eu-vm's internal IP here>

PING x.x.x.x (x.x.x.x) 56(84) bytes of data.
64 bytes from x.x.x.x: icmp_seq=1 ttl=64 time=110 ms
64 bytes from x.x.x.x: icmp_seq=2 ttl=64 time=109 ms
64 bytes from x.x.x.x: icmp_seq=3 ttl=64 time=109 ms

# --- x.x.x.x ping statistics ---
# 3 packets transmitted, 3 received, 0% packet loss, time 2003ms
# rtt min/avg/max/mdev = 109.170/109.615/110.440/0.583 ms
```

> You are able to ping the internal IP address of mynet-eu-vm because it is on the same VPC network as the source of the ping (mynet-us-vm), even though both VM instances are in separate zones, regions and continents!

5. To test connectivity to managementnet-us-vm's internal IP, run the following command, replacing managementnet-us-vm's internal IP:

```bash
ping -c 3 <Enter managementnet-us-vm's internal IP here>

# PING x.x.x.x (x.x.x.x) 56(84) bytes of data.
#
# --- x.x.x.x ping statistics ---
# 3 packets transmitted, 0 received, 100% packet loss, time 2027ms
```

> This should not work as indicated by a 100% packet loss!

6. To test connectivity to privatenet-us-vm's internal IP, run the following command, replacing privatenet-us-vm's internal IP:

```bash
ping -c 3 <Enter privatenet-us-vm's internal IP here>

# PING x.x.x.x (x.x.x.x) 56(84) bytes of data.
#
# --- x.x.x.x ping statistics ---
# 3 packets transmitted, 0 received, 100% packet loss, time 2038ms
```

> This should not work either as indicated by a 100% packet loss! You are unable to ping the internal IP address of managementnet-us-vm and privatenet-us-vm because they are in separate VPC networks from the source of the ping (mynet-us-vm), even though they are all in the same zone us-central1.

VPC networks are by default isolated private networking domains. However, no internal IP address communication is allowed between networks, unless you set up mechanisms such as VPC peering or VPN.

1. Which instance(s) should you be able to ping from mynet-us-vm using internal IP addresses?
- [x] managementnet-us-vm
- [ ] mynet-eu-vm
- [ ] privatenet-us-vm

## Create a VM instance with multiple network interfaces
Every instance in a VPC network has a default network interface. You can create additional network interfaces attached to your VMs. Multiple network interfaces enable you to create configurations in which an instance connects directly to several VPC networks (up to 8 interfaces, depending on the instance's type).

### Create the VM instance with multiple network interfaces
Create the vm-appliance instance with network interfaces in privatesubnet-us, managementsubnet-us and mynetwork. The CIDR ranges of these subnets do not overlap, which is a requirement for creating a VM with multiple network interface controllers (NICs).

1. In the Cloud Console, navigate to Navigation menu > Compute Engine > VM instances.
2. Click Create instance.
3. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | vm-appliance |
| Region | us-central1 |
| Zone | us-central1-f |
| Series | N1 |
| Machine type | 4 vCPUs (n1-standard-4) |

> The number of interfaces allowed in an instance is dependent on the instance's machine type and the number of vCPUs. The n1-standard-4 allows up to 4 network interfaces. Refer here for more information.

4. Click NETWORKING, DISKS, SECURITY, MANAGEMENT, SOLE-TENANCY.
5. Click Networking.
6. For Network interfaces, click the dropdown to edit.
7. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Network | privatenet |
| Subnetwork | privatesubnet-us |

8. Click Done.
9. Click Add network interface.
10. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Network | managementnet |
| Subnetwork | managementsubnet-us |

11. Click Done.
12. Click Add network interface.
13. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Network | mynetwork |
| Subnetwork | mynetwork |
14. Click Done.
15. Click Create.

### Explore the network interface details
Explore the network interface details of vm-appliance within the Cloud Console and within the VM's terminal.

1. In the Cloud Console, navigate to Navigation Menu > Compute Engine > VM instances.
2. Click nic0 within the Internal IP address of vm-appliance to open the Network interface details page.
3. Verify that nic0 is attached to privatesubnet-us, is assigned an internal IP address within that subnet (172.16.0.0/24), and has applicable firewall rules.
4. Click nic0 and select nic1.
5. Verify that nic1 is attached to managementsubnet-us, is assigned an internal IP address within that subnet (10.130.0.0/20), and has applicable firewall rules.
6. Click nic1 and select nic2.
7. Verify that nic2 is attached to mynetwork, is assigned an internal IP address within that subnet (10.128.0.0/20), and has applicable firewall rules.

> Each network interface has its own internal IP address so that the VM instance can communicate with those networks.
8. In the Cloud Console, navigate to Navigation menu > Compute Engine > VM instances.
9. For vm-appliance, click SSH to launch a terminal and connect.
10. Run the following, to list the network interfaces within the VM instance:

```bash
sudo ifconfig
```

The output should look like this (do not copy; this is example output):

```bash
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1460
        inet 172.16.0.3  netmask 255.255.255.255  broadcast 172.16.0.3
        inet6 fe80::4001:acff:fe10:3  prefixlen 64  scopeid 0x20<link>
        ether 42:01:ac:10:00:03  txqueuelen 1000  (Ethernet)
        RX packets 626  bytes 171556 (167.5 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 568  bytes 62294 (60.8 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1460
        inet 10.130.0.3  netmask 255.255.255.255  broadcast 10.130.0.3
        inet6 fe80::4001:aff:fe82:3  prefixlen 64  scopeid 0x20<link>
        ether 42:01:0a:82:00:03  txqueuelen 1000  (Ethernet)
        RX packets 7  bytes 1222 (1.1 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 17  bytes 1842 (1.7 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
eth2: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1460
        inet 10.128.0.3  netmask 255.255.255.255  broadcast 10.128.0.3
        inet6 fe80::4001:aff:fe80:3  prefixlen 64  scopeid 0x20<link>
        ether 42:01:0a:80:00:03  txqueuelen 1000  (Ethernet)
        RX packets 17  bytes 2014 (1.9 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 17  bytes 1862 (1.8 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

> The sudo ifconfig command lists a Linux VM's network interfaces along with the internal IP addresses for each interface.

### Explore the network interface connectivity
Demonstrate that the vm-appliance instance is connected to privatesubnet-us, managementsubnet-us and mynetwork by pinging VM instances on those subnets.

1. In the Cloud Console, navigate to Navigation menu > Compute Engine > VM instances.

2. Note the internal IP addresses for privatenet-us-vm, managementnet-us-vm, mynet-us-vm, and mynet-eu-vm.

3. Return to the SSH terminal for vm-appliance.

4. To test connectivity to privatenet-us-vm's internal IP, run the following command, replacing privatenet-us-vm's internal IP:

```bash
ping -c 3 <Enter privatenet-us-vm's internal IP here>

# PING 172.16.0.2 (172.16.0.2) 56(84) bytes of data.
# 64 bytes from 172.16.0.2: icmp_seq=1 ttl=64 time=0.971 ms

# 64 bytes from 172.16.0.2: icmp_seq=2 ttl=64 time=0.194 ms
# 64 bytes from 172.16.0.2: icmp_seq=3 ttl=64 time=0.231 ms

# --- 172.16.0.2 ping statistics ---
# 3 packets transmitted, 3 received, 0% packet loss, time 2026ms
# rtt min/avg/max/mdev = 0.194/0.465/0.971/0.357 ms
```
This works!

Repeat the same test by running the following:

```bash
ping -c 3 privatenet-us-vm

# PING privatenet-us-vm.us-central1-f.c.qwiklabs-gcp-00-c83ea62b7aca.internal (172.16.0.2) 56(84) bytes of data.
# 64 bytes from privatenet-us-vm.us-central1-f.c.qwiklabs-gcp-00-c83ea62b7aca.internal (172.16.0.2): icmp_seq=1 ttl=64 time=1.14 ms
# 64 bytes from privatenet-us-vm.us-central1-f.c.qwiklabs-gcp-00-c83ea62b7aca.internal (172.16.0.2): icmp_seq=2 ttl=64 time=0.171 ms
# 64 bytes from privatenet-us-vm.us-central1-f.c.qwiklabs-gcp-00-c83ea62b7aca.internal (172.16.0.2): icmp_seq=3 ttl=64 time=0.201 ms

# --- privatenet-us-vm.us-central1-f.c.qwiklabs-gcp-00-c83ea62b7aca.internal ping statistics ---
# 3 packets transmitted, 3 received, 0% packet loss, time 2003ms
# rtt min/avg/max/mdev = 0.171/0.504/1.141/0.450 ms
```

> You are able to ping privatenet-us-vm by its name because VPC networks have an internal DNS service that allows you to address instances by their DNS names rather than their internal IP addresses. When an internal DNS query is made with the instance hostname, it resolves to the primary interface (nic0) of the instance. Therefore, this only works for privatenet-us-vm in this case.

6. To test connectivity to managementnet-us-vm's internal IP, run the following command, replacing managementnet-us-vm's internal IP:

```bash
ping -c 3 <Enter managementnet-us-vm's internal IP here>

# PING 10.130.0.2 (10.130.0.2) 56(84) bytes of data.
# 64 bytes from 10.130.0.2: icmp_seq=1 ttl=64 time=1.46 ms
# 64 bytes from 10.130.0.2: icmp_seq=2 ttl=64 time=0.259 ms
# 64 bytes from 10.130.0.2: icmp_seq=3 ttl=64 time=0.174 ms
# 
# --- 10.130.0.2 ping statistics ---
# 3 packets transmitted, 3 received, 0% packet loss, time 2005ms
# rtt min/avg/max/mdev = 0.174/0.631/1.462/0.588 ms
```
This works!

7. To test connectivity to mynet-us-vm's internal IP, run the following command, replacing mynet-us-vm's internal IP:

```bash
ping -c 3 <Enter mynet-us-vm's internal IP here>

# PING 10.128.0.2 (10.128.0.2) 56(84) bytes of data.
# 64 bytes from 10.128.0.2: icmp_seq=1 ttl=64 time=1.88 ms
# 64 bytes from 10.128.0.2: icmp_seq=2 ttl=64 time=0.227 ms
# 64 bytes from 10.128.0.2: icmp_seq=3 ttl=64 time=0.227 ms
#
# --- 10.128.0.2 ping statistics ---
# 3 packets transmitted, 3 received, 0% packet loss, time 2005ms
# rtt min/avg/max/mdev = 0.227/0.776/1.876/0.777 ms
```
This works!

8. To test connectivity to mynet-eu-vm's internal IP, run the following command, replacing mynet-eu-vm's internal IP:

```bash
ping -c 3 <Enter mynet-eu-vm's internal IP here>

# PING 10.164.0.2 (10.164.0.2) 56(84) bytes of data.
#
# --- 10.164.0.2 ping statistics ---
# 3 packets transmitted, 0 received, 100% packet loss, time 2025ms
```

> This does not work! In a multiple interface instance, every interface gets a route for the subnet that it is in. In addition, the instance gets a single default route that is associated with the primary interface eth0. Unless manually configured otherwise, any traffic leaving an instance for any destination other than a directly connected subnet will leave the instance via the default route on eth0.
9. To list the routes for vm-appliance instance, run the following command:

```bash
ip route
```

The output should look like this (do not copy; this is example output):

```bash
default via 172.16.0.1 dev eth0
10.128.0.0/20 via 10.128.0.1 dev eth2
10.128.0.1 dev eth2 scope link
10.130.0.0/20 via 10.130.0.1 dev eth1
10.130.0.1 dev eth1 scope link
172.16.0.0/24 via 172.16.0.1 dev eth0
172.16.0.1 dev eth0 scope link
```

> The primary interface eth0 gets the default route (default via 172.16.0.1 dev eth0), and all three interfaces eth0, eth1 and eth2 get routes for their respective subnets. Since, the subnet of mynet-eu-vm (10.132.0.0/20) is not included in this routing table, the ping to that instance leaves vm-appliance on eth0 (which is on a different VPC network). You could change this behavior by configuring policy routing as documented here.

----
# Lab 07-02: VPC Networks - Controlling Access

## Overview 
In this lab, you create two nginx web servers and control external HTTP access to the web servers using tagged firewall rules. Then, you explore IAM roles and service accounts.

## Create the web servers
Create two web servers (blue and green) in the default VPC network. Then, install nginx on the webservers and modify the welcome page to distinguish the servers.

### Create the blue server with a network tag.

1. In the Console, navigate to Navigation Menu > Compute Engine > VM instances.

2. Click Create Instance.

3. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | blue |
| Region | us-central1 (Iowa) |
| Zone | us-central1-a |

4. Click NETWORKING, DISKS, SECURITY, MANAGEMENT, SOLE-TENANCY.

5. Click Networking.

6. For Network tags, type web-server.

> Note: Networks use network tags to identify which VM instances are subject to certain firewall rules and network routes. Later in this lab, you create a firewall rule to allow HTTP access for VM instances with the web-server tag. Alternatively, you could check the Allow HTTP traffic checkbox, which would tag this instance as http-server and create the tagged firewall rule for tcp:80 for you.

7. Click Create.

### Create the green server
Create the green server without a network tag.

1. Still in the Console, in the VM instances dialog, click Create instance.

2. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | green |
| Region | us-central1 (Iowa) |
| Zone | us-central1-a |

3. Click Create.

### Install nginx and customize the welcome page
Install nginx on both VM instances and modify the welcome page to distinguish the servers.

1. Still in the VM instances dialog, for blue, click SSH to launch a terminal and connect.

2. In the SSH terminal to blue, run the following command to install nginx:

```bash
sudo apt-get install nginx-light -y
```

3. Open the welcome page in the nano editor:

```bash
sudo nano /var/www/html/index.nginx-debian.html
```

4. Replace the `<h1>Welcome to nginx!</h1>` line with `<h1>Welcome to the blue server!</h1>`.

5. Press <kbd>CTRL</kbd> <kbd>+</kbd> <kbd>O</kbd>, <kbd>ENTER</kbd>, <kbd>CTRL</kbd> <kbd>+</kbd> <kbd>X</kbd>.

6. Verify the change:

```bash
cat /var/www/html/index.nginx-debian.html
```

The output should contain the following (do not copy; this is example output):

```html
...
<h1>Welcome to the blue server!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>
...
```

7. Close the SSH terminal to blue:

```bash
exit
```

Repeat the same steps for the green server:

8. For green, click SSH to launch a terminal and connect.

9. Install nginx:

```bash
sudo apt-get install nginx-light -y
```

10. Open the welcome page in the nano editor:

```bash
sudo nano /var/www/html/index.nginx-debian.html
```

11. Replace the `<h1>Welcome to nginx!</h1>` line with `<h1>Welcome to the green server!</h1>`.

12. Press <kbd>CTRL</kbd> <kbd>+</kbd> <kbd>O</kbd>, <kbd>ENTER</kbd>, <kbd>CTRL</kbd> <kbd>+</kbd> <kbd>X</kbd>.

13. Verify the change:

```bash
cat /var/www/html/index.nginx-debian.html
```

The output should contain the following (do not copy; this is example output):

```html
...
<h1>Welcome to the green server!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>
...
```

14. Close the SSH terminal to green:

```bash
exit
```

## Create the firewall rule
Create the tagged firewall rule and test HTTP connectivity.

### Create the tagged firewall rule
Create a firewall rule that applies to VM instances with the web-server network tag.

1. In the Console, navigate to Navigation Menu > VPC network > Firewall.

2. Notice the default-allow-internal firewall rule.

> The default-allow-internal firewall rule allows traffic on all protocols/ports within the default network. You want to create a firewall rule to allow traffic from outside this network to only the blue server, by using the network tag web-server.

3. Click Create Firewall Rule.

4. Set the following values, leave all other values at their defaults and click Create:

| Property | Value (type value or select option as specified) | 
| ---- | ---- |
| Name | allow-http-web-server | 
| Network | default |
| Targets | Specified target tags |
| Target tags | web-server |
| Source filter | IP Ranges |
| Source IP ranges | 0.0.0.0/0 |
| Protocols and ports | Specified protocols and ports, and then _check_ tcp, _type_: 80; and _check_ Other protocols, type: _icmp_. |

> Make sure to include the /0 in the Source IP ranges to specify all networks.

5. Click Create.

### Create a test-vm
Create a test-vm instance using the Cloud Shell command line.

Create a test-vm instance, in the us-central1-a zone:

```bash
gcloud compute instances create test-vm --machine-type=f1-micro --subnet=default --zone=us-central1-a
```

The output should look like this (do not copy; this is example output):

```bash
NAME     ZONE           MACHINE_TYPE  PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP    STATUS
test-vm  us-central1-a  f1-micro                   10.142.0.4   35.237.134.68  RUNNING
```

You can easily create VM instances from the Console or the gcloud command line.

### Test HTTP connectivity
From test-vm curl the internal and external IP addresses of blue and green.

1. In the Console, navigate to Navigation Menu > Compute Engine > VM instances.

2. Note the internal and external IP addresses of blue and green.

3. For test-vm, click SSH to launch a terminal and connect.

4. To test HTTP connectivity to blue's internal IP, run the following command, replacing blue's internal IP:

```bash
curl <Enter blue's internal IP here>
```

You should see the Welcome to the blue server! header.

5. To test HTTP connectivity to green's internal IP, run the following command, replacing green's internal IP:

```bash
curl -c 3 <Enter green's internal IP here>
```

You should see the `Welcome to the green server!` header.

> You are able to HTTP access both servers using their internal IP addresses. The connection on tcp:80 is allowed by the default- allow-internal firewall rule, as test-vm is on the same VPC network as the web servers default network.

6. To test HTTP connectivity to blue's external IP, run the following command, replacing blue's external IP:

```bash
curl <Enter blue's external IP here>
```

You should see the Welcome to the blue server! header.

7. To test HTTP connectivity to green's external IP, run the following command, replacing green's external IP:

```bash
curl -c 3 <Enter green's external IP here>
```

> This should not work! The request hangs.

8. Press CTRL+c to stop the HTTP request.

> As expected, you are only able to HTTP access the external IP address of the blue server as the allow-http-web-server only applies to VM instances with the web-server tag.

You can verify the same behavior from your browser by opening a new tab and navigating to http://[External IP of server].

## Explore the Network and Security Admin roles
Cloud IAM lets you authorize who can take action on specific resources, giving you full control and visibility to manage cloud resources centrally. The following roles are used in conjunction with single-project networking to independently control administrative access to each VPC Network:

- Network Admin: Permissions to create, modify, and delete networking resources, except for firewall rules and SSL certificates.
- Security Admin: Permissions to create, modify, and delete firewall rules and SSL certificates.

Explore these roles by applying them to a service account, which is a special Google account that belongs to your VM instance, instead of to an individual end user. Rather than creating a new user, you will authorize test-vm to use the service account to demonstrate the permissions of the Network Admin and Security Admin roles.

### Verify current permissions
Currently, test-vm uses the Compute Engine default service account, which is enabled on all instances created by Cloud Shell command-line and the Cloud Console.

Try to list or delete the available firewall rules from test-vm.

1. Return to the SSH terminal of the test-vm instance.

2. Try to list the available firewall rules:

```bash
gcloud compute firewall-rules list
```

The output should look like this (do not copy; this is example output):

```bash
ERROR: (gcloud.compute.firewall-rules.list) Some requests did not succeed:
 - Insufficient Permission
```

> This should not work!

3. Try to delete the allow-http-web-server firewall rule:

```bash
gcloud compute firewall-rules delete allow-http-web-server
```

4. Enter Y, if asked to continue.

The output should look like this (do not copy; this is example output):

```bash
ERROR: (gcloud.compute.firewall-rules.delete) Could not fetch resource:
 - Insufficient Permission
```

> This should not work!

> The Compute Engine default service account does not have the right permissions to allow you to list or delete firewall rules. The same applies to other users who do not have the right roles.

### Create a service account
Create a service account and apply the Network Admin role.

1. In the Console, navigate to Navigation Menu > IAM & admin > Service Accounts.

2. Notice the Compute Engine default service account.

3. Click Create service account.

4. Set the Service account name to Network-admin and click CREATE AND CONTINUE.

5. For Select a role, select Compute Engine > Compute Network Admin and click CONTINUE then click DONE.

6. After creating service account 'Network-admin', click on three dots at the right corner and click Manage Key in the dropdown, then click on Add Key and select Create new key from the dropdown. Click Create to download your JSON output.

7. Click Close.

A JSON key file download to your local computer. Find this key file, you will upload it in to the VM in a later step.

8. Rename the JSON key file on your local machine to credentials.json

### Authorize test-vm and verify permissions
Authorize test-vm to use the Network-admin service account.

The Network Admin role provides permissions to:
- [ ] Create a firewall rules
- [x] List the available firewall rules
- [ ] Modify the available firewall rules
- [ ] Delete the available firewall rules
- [ ] Neither list, create, modify, or delete the available firewall rules

1. Return to the SSH terminal of the test-vm instance.

2. To upload credentials.json through the SSH VM terminal, click on the gear icon in the upper-right corner, and then click Upload file.

3. Select credentials.json and upload it.

4. Click Close in the File Transfer window.

5. Authorize the VM with the credentials you just uploaded:

```bash
gcloud auth activate-service-account --key-file credentials.json
```

> The image you are using has the Cloud SDK pre-installed; therefore, you don’t need to initialize the Cloud SDK. If you are attempting this lab in a different environment, make sure you have followed the procedures regarding installing the Cloud SDK.

6. Try to list the available firewall rules:

```bash
gcloud compute firewall-rules list
```

The output should look like this (do not copy; this is example output):

```bash
NAME                    NETWORK  DIRECTION  PRIORITY  ALLOW     DENY
allow-http-web-server   default  INGRESS    1000      tcp:80
default-allow-icmp      default  INGRESS    65534     icmp
default-allow-internal  default  INGRESS    65534     all
default-allow-rdp       default  INGRESS    65534     tcp:3389
default-allow-ssh       default  INGRESS    65534     tcp:22
```

This should work!

7. Try to delete the allow-http-web-server firewall rule:

```bash
gcloud compute firewall-rules delete allow-http-web-server
```

8. Enter Y, if asked to continue.
The output should look like this (do not copy; this is example output):

```bash
ERROR: (gcloud.compute.firewall-rules.delete) Could not fetch resource:
 - Required 'compute.firewalls.delete' permission for 'projects/[PROJECT_ID]/global/firewalls/allow-http-web-server'
```

> This should not work!

> As expected, the Network Admin role has permissions to list but not modify/delete firewall rules.

### Update service account and verify permissions
Update the Network-admin service account by providing it the Security Admin role.

The Security Admin role, provides permissions to:
- [x] Create a firewall rules
- [x] List the available firewall rules
- [x] Modify the available firewall rules
- [x] Delete the available firewall rules
- [ ] Neither list, create, modify, or delete the available firewall rules

1. In the Console, navigate to Navigation Menu > IAM & admin > IAM.

2. Find the Network-admin account. Focus on the Name column to identify this account.

3. Click on the pencil icon for the Network-admin account.

4. Change Role to Compute Engine > Compute Security Admin.

5. Click Save.

6. Return to the SSH terminal of the test-vm instance.

7. Try to list the available firewall rules:

```bash
gcloud compute firewall-rules list
```

The output should look like this (do not copy; this is example output):

```bash
NAME                    NETWORK  DIRECTION  PRIORITY  ALLOW     DENY
allow-http-web-server   default  INGRESS    1000      tcp:80
default-allow-icmp      default  INGRESS    65534     icmp
default-allow-internal  default  INGRESS    65534     all
default-allow-rdp       default  INGRESS    65534     tcp:3389
default-allow-ssh       default  INGRESS    65534     tcp:22
```
This should work!

8. Try to delete the allow-http-web-server firewall rule:

```bash
gcloud compute firewall-rules delete allow-http-web-server
```

9. Enter Y, if asked to continue.
The output should look like this (do not copy; this is example output):
```bash
Deleted [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-00e186e4b1cec086/global/firewalls/allow-http-web-server].
```
This should work!

> As expected, the Security Admin role has permissions to list and delete firewall rules.

### Verify the deletion of the firewall rule
Verify that you can no longer HTTP access the external IP of the blue server, because you deleted the allow-http-web-server firewall rule.

1. Return to the SSH terminal of the test-vm instance.

2. To test HTTP connectivity to blue's external IP, run the following command, replacing blue's external IP:

```bash
curl -c 3 <Enter blue's external IP here>
```
> This should not work!

3. Press CTRL+c to stop the HTTP request.

> Provide the Security Admin role to the right user or service account to avoid any unwanted changes to your firewall rules!

----
# Lab 07-03: HTTP Load Balancer with Cloud Armor

## Overview
Google Cloud HTTP(S) load balancing is implemented at the edge of Google's network in Google's points of presence (POP) around the world. User traffic directed to an HTTP(S) load balancer enters the POP closest to the user and is then load balanced over Google's global network to the closest backend that has sufficient capacity available.

Cloud Armor IP allowlist/denylist enable you to restrict or allow access to your HTTP(S) load balancer at the edge of the Google Cloud, as close as possible to the user and to malicious traffic. This prevents malicious users or traffic from consuming resources or entering your virtual private cloud (VPC) networks.

In this lab, you configure an HTTP Load Balancer with global backends, as shown in the diagram below. Then, you stress test the Load Balancer and denylist the stress test IP with Cloud Armor.

## Configure HTTP and health check firewall rules
Configure firewall rules to allow HTTP traffic to the backends and TCP traffic from the Google Cloud health checker.

### Create the HTTP firewall rule
Create a firewall rule to allow HTTP traffic to the backends.

1. In the Cloud Console, navigate to Navigation menu > VPC network > Firewall.

2. Notice the existing ICMP, internal, RDP, and SSH firewall rules.

Each Google Cloud project starts with the default network and these firewall rules.

3. Click Create Firewall Rule.

4. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | default-allow-http |
| Network | default |
| Targets | Specified target tags |
| Target tags | http-server |
| Source filter | IPv4 Ranges |
| Source IPv4 ranges | 0.0.0.0/0 |
| Protocols and ports | Specified protocols and ports, and then _check_ tcp, _type_: 80 |
> Make sure to include the /0 in the Source IP ranges to specify all networks.

5. Click Create.

### Create the health check firewall rules
Health checks determine which instances of a load balancer can receive new connections. For HTTP load balancing, the health check probes to your load balanced instances come from addresses in the ranges 130.211.0.0/22 and 35.191.0.0/16. Your firewall rules must allow these connections.

1. Still in the Firewall rules page, click Create Firewall Rule.

2. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | default-allow-health-check | 
| Network | default |
| Targets | Specified target tags |
| Target tags | http-server |
| Source filter | IPv4 Ranges |
| Source IPv4 ranges | 130.211.0.0/22, 35.191.0.0/16 |
| Protocols and ports | Specified protocols and ports, and then check tcp |
> Make sure to enter the two Source IP ranges one-by-one and pressing SPACE in between them.

3. Click Create.

## Configure instance templates and create instance groups
A managed instance group uses an instance template to create a group of identical instances. Use these to create the backends of the HTTP Load Balancer.

### Configure the instance templates
An instance template is an API resource that you use to create VM instances and managed instance groups. Instance templates define the machine type, boot disk image, subnet, labels, and other instance properties. Create one instance template for us-east1 and one for europe-west1.

1. In the Cloud Console, navigate to Navigation menu > Compute Engine > Instance templates, and then click Create instance template.

2. For Name, type us-east1-template.

3. For Series, select N1.

4. Click NETWORKING, DISKS, SECURITY, MANAGEMENT, SOLE-TENANCY.

5. Click the Management tab.

6. Under Metadata, click + ADD ITEM and specify the following:

| Key | Value |
| ---- | ---- |
| startup-script-url | gs://cloud-training/gcpnet/httplb/startup.sh |
> The startup-script-url specifies a script that executes when instances are started. This script installs Apache and changes the welcome page to include the client IP and the name, region, and zone of the VM instance. Feel free to explore this script here.

7. Click Networking.

8. Set the following values and leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Network tags | http-server |
| Network | default |
| Subnetwork | default (us-east1) |

Click Done.

> The network tag http-server ensures that the HTTP and Health Check firewall rules apply to these instances.

9. Click Create.

10. Wait for the instance template to be created.

Now create another instance template for subnet-b by copying us-east1-template:

1. Click on us-east1-template and then click on the Copy option from the top.

2. For Name, type europe-west1-template.

3. Click NETWORKING, DISKS, SECURITY, MANAGEMENT, SOLE-TENANCY.

4. Click Networking.

5. For Subnetwork, select default (europe-west1).

6. Click Done.

7. Click Create.

### Create the managed instance groups
Create a managed instance group in us-east1 and one in europe-west1.

1. Still in Compute Engine, click Instance groups in the left menu.

2. Click Create instance group.

3. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | us-east1-mig |
| Location | Multiple zones |
| Region | us-east1 |
| Instance template | us-east1-template |
| Autoscaling > Autoscaling metrics > Click dropdown > Metric type | CPU utilization |
| Target CPU utilization | 80, click Done. |
| Cool-down period | 45 |
| Minimum number of instances | 1 |
| Maximum number of instances | 5 |
> Managed instance groups offer autoscaling capabilities that allow you to automatically add or remove instances from a managed instance group based on increases or decreases in load. Autoscaling helps your applications gracefully handle increases in traffic and reduces cost when the need for resources is lower. You just define the autoscaling policy and the autoscaler performs automatic scaling based on the measured load.

4. Click Create.

Now repeat the same procedure for create a second instance group for europe-west1-mig in europe-west1:

1. Click Create Instance group.

2. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | europe-west1-mig |
| Location | Multiple zones |
| Region | europe-west1 | 
| Instance template | europe-west1-template |
| Autoscaling > Autoscaling metrics > Click dropdown > Metric type | CPU utilization |
| Target CPU utilization | 80, click Done. |
| Cool-down period | 45 |
| Minimum number of instances | 1 |
| Maximum number of instances | 5 |

3. Click Create.

### Verify the backends
Verify that VM instances are being created in both regions and access their HTTP sites.

1. Still in Compute Engine, click VM instances in the left menu.

2. Notice the instances that start with us-east1-mig and europe-west1-mig.

These instances are part of the managed instance groups.

3. Click on the External IP of an instance of us-east1-mig.

You should see the Client IP (your IP address), the Hostname (starts with us-east1-mig) and the Server Location (a zone in us-east1).

4. Click on the External IP of an instance of europe-west1-mig.

You should see the Client IP (your IP address), the Hostname (starts with europe-west1-mig) and the Server Location (a zone in europe-west1).

> The Hostname and Server Location identifies where the HTTP Load Balancer sends traffic.

Which of these fields identify the region of the backend?
- [x] Hostname
- [x] Server Location
- [ ] Client IP

## Configure the HTTP Load Balancer
Configure the HTTP Load Balancer to balance traffic between the two backends (us-east1-mig in us-east1 and europe-west1-mig in europe-west1), as illustrated in the network diagram:

### Start the configuration
1. In the Cloud Console, click Navigation menu > click Network Services > Load balancing, and then click Create load balancer.

2. Under HTTP(S) Load Balancing, click on Start configuration.

3. Select From Internet to my VMs or serverless services, and click Continue.

4. Set the Name to http-lb.

### Configure the backend
Backend services direct incoming traffic to one or more attached backends. Each backend is composed of an instance group and additional serving capacity metadata.

1. Click on Backend configuration.

2. For Backend services & backend buckets, click Create a backend service.

3. Set the following values, leave all other values at their defaults:

| Property | Value (select option as specified) |
| ---- | ---- |
| Name | http-backend |
| Instance group | us-east1-mig |
| Port numbers | 80 |
| Balancing mode | Rate |
| Maximum RPS | 50 |
| Capacity | 100 |
> This configuration means that the load balancer attempts to keep each instance of us-east1-mig at or below 50 requests per second (RPS).

4. Click Done.

5. Click Add backend.

6. Set the following values, leave all other values at their defaults:

| Property | Value (select option as specified) |
| ---- | ---- |
| Instance group | europe-west1-mig |
| Port numbers | 80 |
| Balancing mode | Utilization |
| Maximum backend utilization | 80 |
| Capacity | 100 |

> This configuration means that the load balancer attempts to keep each instance of europe-west1-mig at or below 80% CPU utilization.

7. Click Done.

8. For Health Check, select Create a health check.

9. Set the following values, leave all other values at their defaults:

| Property | Value (select option as specified) |
| ---- | ---- |
| Name | http-health-check |
| Protocol | TCP |
| Port | 80 |

> Health checks determine which instances receive new connections. This HTTP health check polls instances every 5 seconds, waits up to 5 seconds for a response and treats 2 successful or 2 failed attempts as healthy or unhealthy, respectively.

10. Click Save.

11. Check the Enable Logging box.

12. Set the Sample Rate to 1:

13. Click Create to create the backend service.

### Configure the frontend
The host and path rules determine how your traffic will be directed. For example, you could direct video traffic to one backend and static traffic do another backend. However, you are not configuring the Host and path rules in this lab.

1. Click on Frontend configuration.

2. Specify the following, leaving all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Protocol | HTTP |
| IP version | IPv4 |
| IP address | Ephemeral |
| Port | 80 | 

3. Click Done.

4. Click Add Frontend IP and port.

5. Specify the following, leaving all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Protocol | HTTP |
| IP version | IPv6 |
| IP address | Ephemeral |
| Port | 80 |

6. Click Done.

> HTTP(S) load balancing supports both IPv4 and IPv6 addresses for client traffic. Client IPv6 requests are terminated at the global load balancing layer, then proxied over IPv4 to your backends.

### Review and create the HTTP Load Balancer
1. Click on Review and finalize.

2. Review the Backend services and Frontend.

3. Click on Create.

4. Wait for the load balancer to be created.

5. Click on the name of the load balancer (http-lb).

6. Note the IPv4 and IPv6 addresses of the load balancer for the next task. They will be referred to as [LB_IP_v4] and [LB_IP_v6], respectively.

> The IPv6 address is the one in hexadecimal format.

## Test the HTTP Load Balancer
Now that you created the HTTP Load Balancer for your backends, verify that traffic is forwarded to the backend service.

The HTTP load balancer should forward traffic to the region that is closest to you.
- [x] True
- [ ] False

### Access the HTTP Load Balancer
To test IPv4 access to the HTTP Load Balancer, open a new tab in your browser and navigate to http://[LB_IP_v4]. Make sure to replace [LB_IP_v4] with the IPv4 address of the load balancer.

> It might take up to 5 minutes to access the HTTP Load Balancer. In the meantime, you might get a 404 or 502 error. Keep trying until you see the page of one of the backends.

> Depending on your proximity to us-east1 and europe-west1, you traffic is either forwarded to a us-east1-mig or europe-west1-mig instance.

If you have a local IPv6 address, try the IPv6 address of the HTTP Load Balancer by navigating to http://[LB_IP_v6]. Make sure to replace [LB_IP_v6] with the IPv6 address of the load balancer.

### Stress test the HTTP Load Balancer
Create a new VM to simulate a load on the HTTP Load Balancer using siege. Then, determine if traffic is balanced across both backends when the load is high.

1. In the Console, navigate to Navigation Menu > Compute Engine > VM instances.

2. Click Create instance.

3. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | siege-vm |
| Region | us-west1 |
| Zone | us-west1-c |
| Series | N1 |
> Given that us-west1 is closer to us-east1 than to europe-west1, traffic should be forwarded only to us-east1-mig (unless the load is too high).

4. Click Create.

5. Wait for the siege-vm instance to be created.

6. For siege-vm, click SSH to launch a terminal and connect.

7. Run the following command, to install siege:

```bash
sudo apt-get -y install siege
```

8. To store the IPv4 address of the HTTP Load Balancer in an environment variable, run the following command, replacing [LB_IP_v4] with the IPv4 address:

```bash
export LB_IP=[LB_IP_v4]
```

9. To simulate a load, run the following command:

```bash
siege -c 250 -t150s http://$LB_IP
```

The output should look like this (do not copy; this is example output):

```bash
New configuration template added to /home/cloudcurriculumdeveloper/.siege
Run siege -C to view the current settings in that file
[alert] Zip encoding disabled; siege requires zlib support to enable it: No such file or directory
** SIEGE 4.0.2
** Preparing 250 concurrent users for battle.
The server is now under siege...
```

10. In the Cloud Console, on the Navigation menu, click Network Services > Load balancing.

11. Click Backends.

12. Click http-backend.

13. Navigate to http-lb.

14. Click on the Monitoring tab.

15. Monitor the Frontend Location (Total inbound traffic) between North America and the two backends for 2 to 3 minutes.

At first, traffic should just be directed to us-east1-mig but as the RPS increases, traffic is also directed to europe-west1-mig.

This demonstrates that by default traffic is forwarded to the closest backend but if the load is very high, traffic can be distributed across the backends.

16. Return to the SSH terminal of siege-vm.

17. Press CTRL+C to stop siege if it's still running.

## Denylist the siege-vm
Use Cloud Armor to denylist the siege-vm from accessing the HTTP Load Balancer.

### Create the security policy
Create a Cloud Armor security policy with a denylist rule for the siege-vm.

1. In the Console, navigate to Navigation menu > Compute Engine > VM instances.

2. Note the External IP of the siege-vm. This will be referred to as [SIEGE_IP].

> There are ways to identify the external IP address of a client trying to access your HTTP Load Balancer. For example, you could examine traffic captured by VPC Flow Logs in BigQuery to determine a high volume of incoming requests.

3. In the Cloud Console, navigate to Navigation menu > Network Security > Cloud Armor.

4. Click Create policy.

5. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | denylist-siege |
| Default rule action | Allow |

6. Click Next step.

7. Click Add rule.

8. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Condition | Enter the SIEGE_IP |
| Action  | Deny |
| Deny status | 403 (Forbidden) |
| Priority | 1000 |

9. Click Done.

10. Click Next step.

11. Click Add Target.

12. For Type, select Load balancer backend service.

13. For Target, select http-backend.

14. Click Create policy.

> Alternatively, you could set the default rule to Deny and only allowlist/allow traffic from authorized users/IP addresses.

15. Wait for the policy to be created before moving to the next step.

### Verify the security policy
Verify that the siege-vm cannot access the HTTP Load Balancer.

1. Return to the SSH terminal of siege-vm.

2. To access the load balancer, run the following:

```bash
curl http://$LB_IP
```
The output should look like this (do not copy; this is example output):

```html
<!doctype html><meta charset="utf-8"><meta name=viewport content="width=device-width, initial-scale=1"><title>403</
title>403 Forbidden
```

> It might take a couple of minutes for the security policy to take affect. If you are able to access the backends, keep trying until you get the 403 Forbidden error.

3. Open a new tab in your browser and navigate to http://[LB_IP_v4]. Make sure to replace [LB_IP_v4] with the IPv4 address of the load balancer.

> You can access the HTTP Load Balancer from your browser because of the default rule to allow traffic; however, you cannot access it from the siege-vm because of the deny rule that you implemented.

4. Back in the SSH terminal of siege-vm, to simulate a load, run the following command:

```bash
siege -c 250 -t150s http://$LB_IP
```

The output should look like this (do not copy; this is example output):

```bash
[alert] Zip encoding disabled; siege requires zlib support to enable it
** SIEGE 4.0.2
** Preparing 250 concurrent users for battle.
The server is now under siege...
```
Explore the security policy logs to determine if this traffic is also blocked.

5. In the Console, navigate to Navigation menu > Network Security > Cloud Armor.

6. Click denylist-siege.

7. Click Logs.

8. Click View policy logs.

9. On the Logging page, make sure to clear all the text in the Query preview. Select resource to Cloud HTTP Load Balancer > http-lb-forwarding-rule > http-lb then click Add.

10. Now click Run Query.

11. Expand a log entry in Query results.

12. Expand httpRequest.

The request should be from the siege-vm IP address. If not, expand another log entry.

13. Expand jsonPayload.

14. Expand enforcedSecurityPolicy.

15. Notice that the configuredAction is to DENY with the name denylist-siege.

> Cloud Armor security policies create logs that can be explored to determine when traffic is denied and when it is allowed, along with the source of the traffic.

----
# Lab 07-04: Create an Internal Load Balancer

## Overview
Google Cloud offers Internal Load Balancing for your TCP/UDP-based traffic. Internal Load Balancing enables you to run and scale your services behind a private load balancing IP address that is accessible only to your internal virtual machine instances.

In this lab you create two managed instance groups in the same region. Then, you configure and test an Internal Load Balancer with the instances groups as the backends, as shown in this network diagram:

https://cdn.qwiklabs.com/k3u04mphJhk%2F2yM84NjgPiZHrbCuzbdwAQ98vnaoHQo%3D

## Configure HTTP and health check firewall rules
Configure firewall rules to allow HTTP traffic to the backends and TCP traffic from the Google Cloud health checker.

### Explore the my-internal-app network
The network my-internal-app with subnet-a and subnet-b along with firewall rules for RDP, SSH, and ICMP traffic have been configured for you.

1. In the Console, navigate to Navigation menu > VPC network > VPC networks.

2. Scroll down and notice the my-internal-app network with its subnets: subnet-a and subnet-b

Each Google Cloud project starts with the default network. In addition, the my-internal-app network has been created for you, as part of your network diagram.

You will create the managed instance groups in subnet-a and subnet-b. Both subnets are in the us-central1 region because an Internal Load Balancer is a regional service. The managed instance groups will be in different zones, making your service immune to zonal failures.

### Create the HTTP firewall rule
Create a firewall rule to allow HTTP traffic to the backends from the Load Balancer and the internet (to install Apache on the backends).

1. Still in VPC network, in the left pane click Firewall.

2. Notice the app-allow-icmp and app-allow-ssh-rdp firewall rules.

These firewall rules have been created for you.

3. Click Create Firewall Rule.

4. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | app-allow-http |
| Network | my-internal-app |
| Targets | Specified target tags |
| Target tags | lb-backend |
| Source filter | IPv4 Ranges |
| Source IPv4 ranges | 0.0.0.0/0 |
| Protocols and ports | Specified protocols and ports, and then _check_ tcp, _type_: 80 |
> Make sure to include the /0 in the Source IPv4 ranges to specify all networks.

5. Click Create.

### Create the health check firewall rules
Health checks determine which instances of a Load Balancer can receive new connections. For Internal load balancing, the health check probes to your load balanced instances come from addresses in the ranges 130.211.0.0/22 and 35.191.0.0/16. Your firewall rules must allow these connections.

1. Still in the Firewall rules page, click Create Firewall Rule.

2. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | app-allow-health-check |
| Targets | Specified target tags |
| Target tags | lb-backend | 
| Source filter | IPv4 Ranges |
| Source IPv4 ranges | 130.211.0.0/22 and 35.191.0.0/16 |
| Protocols and ports | Specified protocols and ports, and then check tcp |
> Make sure to enter the two Source IPv4 ranges one-by-one and pressing SPACE in between them.

3. Click Create.

## Configure instance templates and create instance groups
A managed instance group uses an instance template to create a group of identical instances. Use these to create the backends of the Internal Load Balancer.

### Configure the instance templates
An instance template is an API resource that you can use to create VM instances and managed instance groups. Instance templates define the machine type, boot disk image, subnet, labels, and other instance properties. Create an instance template for both subnets of the my-internal-app network.

1. In the Console, navigate to Navigation menu > Compute Engine > Instance templates.

2. Click Create instance template.

3. For Name, type instance-template-1.

4. For Series, select N1.

5. Click Networking, disks, security, management, sole tenancy.

6. Click Networking.

7. For Network tags, specify lb-backend.
> The network tag lb-backend ensures that the HTTP and Health Check firewall rules apply to these instances.

8. For Network interfaces, click the dropdown icon to edit.

9. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Network | my-internal-app |
| Subnetwork | subnet-a |

10. Click Done.

11. Click Management.

12. Under Metadata, click Add item and specify the following:

| Key | Value |
| ---- | ---- |
| startup-script-url | gs://cloud-training/gcpnet/ilb/startup.sh |

> The startup-script-url specifies a script that will be executed when instances are started. This script installs Apache and changes the welcome page to include the client IP and the name, region and zone of the VM instance. Feel free to explore this script here.

13. Click Create.

14. Wait for the instance template to be created.

### Configure the next instance template
Create another instance template for subnet-b by copying instance-template-1:

1. Still in Instance templates, check the box next to instance-template-1, then click Copy. You will see the instance is named instance-template-2.

2. Click Networking, disks, security, management, sole tenancy.

3. Click the Networking tab.

4. For Network interfaces, click the dropdown icon to edit.

5. Select subnet-b as the Subnetwork.

6. Click Done and then click Create.

### Create the managed instance groups
Create a managed instance group in subnet-a (us-central1-a) and one subnet-b (us-central1-b).

1. Still in Compute Engine, in the left pane click Instance groups, and then click Create Instance group.

2. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | instance-group-1 |
| Instance template | instance-template-1 |
| Location | Single-zone |
| Region | us-central1 |
| Zone | us-central1-a |
| Autoscaling > Minimum number of instances | 1 |
| Autoscaling > Maximum number of instances | 5 |
| Autoscaling > Autoscaling metrics (click the dropdown icon to edit) > Metric type | CPU utilization |
| Target CPU utilization | 80 |
| Cool-down period | 45 |
> Managed instance groups offer autoscaling capabilities that allow you to automatically add or remove instances from a managed instance group based on increases or decreases in load. Autoscaling helps your applications gracefully handle increases in traffic and reduces cost when the need for resources is lower. You just define the autoscaling policy and the autoscaler performs automatic scaling based on the measured load.

3. Click Create.

Repeat the same procedure for instance-group-2 in us-central1-b:

4. Click Create Instance group.

5. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | instance-group-2 |
| Instance template | instance-template-2 |
| Location | Single-zone |
| Region | us-central1 |
| Zone | us-central1-b |
| Autoscaling > Minimum number of instances | 1 |
| Autoscaling > Maximum number of instances | 5 |
| Autoscaling > Autoscaling metrics (click the dropdown icon to edit) > Metric type | CPU utilization |
| Target CPU utilization | 80 |
| Cool-down period | 45 |

6. Click Create.

### Verify the backends
Verify that VM instances are being created in both subnets and create a utility VM to access the backends' HTTP sites.

1. Still in Compute Engine, click VM instances.

2. Notice two instances that start with instance-group-1 and instance-group-2.

These instances are in separate zones and their internal IP addresses are part of the subnet-a and subnet-b CIDR blocks.

3. Click Create instance.

4. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | utility-vm |
| Region | us-central1 |
| Zone | us-central1-f |
| Series | N1 |
| Machine type | f1-micro (1 shared vCPU) |

5. Click Networking, disks, security, management, sole tenancy.

6. Click Networking.

7. For Network interfaces, click the dropdown icon to edit.

8. Set the following values, leave all other values at their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Network | my-internal-app |
| Subnetwork | subnet-a |
| Primary internal IP | Ephemeral (Custom) |
| Custom ephemeral IP address | 10.10.20.50 |

9. Click Done and then click Create.

10. Note that the internal IP addresses for the backends are 10.10.20.2 and 10.10.30.2.
> If these IP addresses are different, replace them in the two curl commands below.

11. For utility-vm, click SSH to launch a terminal and connect.

12. To verify the welcome page for instance-group-1-xxxx, run the following command:

```bash
curl 10.10.20.2
```

The output should look like this (do not copy; this is example output):

```html
<h1>Internal Load Balancing Lab</h1><h2>Client IP</h2>Your IP address : 10.10.20.50<h2>Hostname</h2>Server Hostname:
 instance-group-1-1zn8<h2>Server Location</h2>Region and Zone: us-central1-a
```

13. To verify the welcome page for instance-group-2-xxxx, run the following command:

```bash
curl 10.10.30.2
```

The output should look like this (example output):

```html
<h1>Internal Load Balancing Lab</h1><h2>Client IP</h2>Your IP address : 10.10.20.50<h2>Hostname</h2>Server Hostname:
 instance-group-2-q5wp<h2>Server Location</h2>Region and Zone: us-central1-b
```

Which of these fields identify the location of the backend?
- [ ] Client IP
- [x] Server Location
- [x] Server Hostname

> The curl commands demonstrate that each VM instance lists the Client IP and its own name and location. This will be useful when verifying that the Internal Load Balancer sends traffic to both backends.

14. Close the SSH terminal to utility-vm:

```bash
exit
```

## Configure the Internal Load Balancer
Configure the Internal Load Balancer to balance traffic between the two backends (instance-group-1 in us-central1-a and instance-group-2 in us-central1-b), as illustrated in this diagram:

### Start the configuration
1. In the Cloud Console, navigate to Navigation menu > Network Services > Load balancing, and then click Create load balancer.

2. Under TCP Load Balancing, click on Start configuration.

3. For Internet facing or internal only, select Only between my VMs.

> Choosing Only between my VMs makes this Load Balancer internal. This choice requires the backends to be in a single region (us-central1) and does not allow offloading TCP processing to the Load Balancer.

4. Click Continue.

5. For Name, type my-ilb.

6. For Region, select us-central1.

7. For Network, select my-internal-app.

### Configure the regional backend service
The backend service monitors instance groups and prevents them from exceeding configured usage.

1. Click on Backend configuration.

2. Set the following values, leave all other values at their defaults:

| Property | Value (select option as specified) |
| ---- | ---- |
| Instance group | instance-group-1 (us-central1-a) |

3. Click Add backend.

4. For Instance group, select instance-group-2 (us-central1-b).

5. For Health Check, select Create a health check.

6. Set the following values, leave all other values at their defaults:

| Property | Value (select option as specified) |
| ---- | ---- |
| Name | my-ilb-health-check |
| Protocol | TCP |
| Port | 80 |

> Health checks determine which instances can receive new connections. This HTTP health check polls instances every 5 seconds, waits up to 5 seconds for a response and treats 2 successful or 2 failed attempts as healthy or unhealthy, respectively.

7. Click Save.

8. Click Done.

9. Verify that there is a blue check mark next to Backend configuration in the Cloud Console. If not, double-check that you have completed all the steps above.

### Configure the frontend
The frontend forwards traffic to the backend.

1. Click on Frontend configuration.

2. Specify the following, leaving all other values with their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Subnetwork | subnet-b |
| Internal IP | Under IP address select Create IP address |

3. Specify the following, leaving all other values with their defaults:

| Property | Value (type value or select option as specified) |
| ---- | ---- |
| Name | my-ilb-ip |
| Static IP address | Let me choose |
| Custom IP address | 10.10.30.5 |

4. Click Reserve.

5. In Port number, type 80.

6. Click Done .

### Review and create the Internal Load Balancer
1. Click on Review and finalize.

2. Review the Backend and Frontend.

3. Click on Create. Wait for the Load Balancer to be created, before moving to the next task.

## Test the Internal Load Balancer
Verify that the my-ilb IP address forwards traffic to instance-group-1 in us-central1-a and instance-group-2 in us-central1-b.

### Access the Internal Load Balancer
1. In the Cloud Console, navigate to Navigation menu > Compute Engine > VM instances.

2. For utility-vm, click SSH to launch a terminal and connect.

3. To verify that the Internal Load Balancer forwards traffic, run the following command:

```bash
curl 10.10.30.5
```

The output should look like this (example output):

```html
<h1>Internal Load Balancing Lab</h1><h2>Client IP</h2>Your IP address : 10.10.20.50<h2>Hostname</h2>Server Hostname:
 instance-group-1-1zn8<h2>Server Location</h2>Region and Zone: us-central1-a
```

> As expected, traffic is forwarded from the Internal Load Balancer (10.10.30.5) to the backend.

4. Run the same command a couple more times.

You should be able to see responses from instance-group-1 in us-central1-a and instance-group-2 in us-central1-b.