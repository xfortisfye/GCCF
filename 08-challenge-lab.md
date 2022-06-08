#### Google Cloud Computing Foundations | C03 Networking and Security in Google Cloud
#### M07-M08

# Build and Secure Networks in Google Cloud: Challenge Lab

## Challenge scenario
You are a security consultant brought in by Jeff, who owns a small local company, to help him with his very successful website (juiceshop). Jeff is new to Google Cloud and had his neighbour's son set up the initial site. The neighbour's son has since had to leave for college, but before leaving, he made sure the site was running.

You need to help out Jeff and perform appropriate configuration for security. Below is the current situation:

https://cdn.qwiklabs.com/qEwFTP7%2FkyF3cRwfT3FGObt7L7VLB60%2Bvp92hZVnogw%3D

## Your challenge
You need to configure this simple environment securely. Your first challenge is to set up appropriate firewall rules and virtual machine tags. You also need to ensure that SSH is only available to the bastion via IAP.

For the firewall rules, make sure:
- The bastion host does not have a public IP address.
- You can only SSH to the bastion and only via IAP.
- You can only SSH to juice-shop via the bastion.
- Only HTTP is open to the world for juice-shop.

Tips and tricks:
- Pay close attention to the network tags and the associated VPC firewall rules.
- Be specific and limit the size of the VPC firewall rule source ranges.
- Overly permissive permissions will not be marked correct.

https://cdn.qwiklabs.com/BgxgsuLyqMkhxmO3jDlkHE7yGLIR%2B3rrUabKimlgrbo%3D

### Task 1
Suggested order of actions:

1. Check the firewall rules. Remove the overly permissive rules.

2. Navigate to Compute Engine in the Cloud Console and identify the bastion host. The instance should be stopped. Start the instance.

3. The bastion host is the one machine authorized to receive external SSH traffic. Create a firewall rule that allows SSH (tcp/22) from the IAP service. The firewall rule must be enabled for the bastion host instance using a network tag of SSH IAP network tag .

4. The juice-shop server serves HTTP traffic. Create a firewall rule that allows traffic on HTTP (tcp/80) to any address. The firewall rule must be enabled for the juice-shop instance using a network tag of HTTP network tag .

5. You need to connect to juice-shop from the bastion using SSH. Create a firewall rule that allows traffic on SSH (tcp/22) from acme-mgmt-subnet network address. The firewall rule must be enabled for the juice-shop instance using a network tag of SSH internal network tag .

6. In the Compute Engine instances page, click the SSH button for the bastion host. Once connected, SSH to juice-shop.

> **Hint**: If you're having difficulties with the compute ssh connection or IAP tunnel, make use of the --troubleshoot flag.

# Answer

## Task 1: Remove Overly Permissive Firewall Rules
1. Navigation Menu > VPC Network > Firewall

2. Delete open-access rules

## Task 2: Start Bastion Host/Instance
1. Navigation Menu > Compute Engine > VM instances

2. Start/Resume bastion

## Task 3: Create SSH Firewall Rule for Bastion Host
Create firewall rule 
1. Navigation Menu > VPC Network > Firewall > Create a firewall rule

| Field | Detail |
| ---- | ---- |
| Name: | bastion-ssh |
| Network: | acme-vpc |
| Direction of traffic: | Ingress |
| Action on match: | Allow |
| Target tags: | allow-ssh-iap-ingress-ql-555 |
| Source Filter: | IPv4 Ranges |
| Source IPv4 ranges: | 35.235.240.0/20 |
| Protocol and ports: | Specfied protocol and ports \| tcp: 22 |

> Note: To allow IAP to connect to your VM instances, create a firewall rule that:
> - applies to all VM instances that you want to be accessible by using IAP.
> - **allows ingress traffic from the IP range 35.235.240.0/20. This range contains all IP addresses that IAP uses for TCP forwarding.**
> - allows connections to all ports that you want to be accessible by using IAP TCP forwarding, for example, port 22 for SSH and port 3389 for RDP.
>> Reference from: https://cloud.google.com/iap/docs/using-tcp-forwarding#create-firewall-rule


Add Network Tag to bastion instance
1. Navigation Menu > Compute Engine > VM instances
2. Click on Bastion
3. Click Edit
4. Add `allow-ssh-iap-ingress-ql-555` to Network Tag

## Task 4: Create firewall rule of any to port 80 for Juice-Shop server
Create firewall rule 
1. Navigation Menu > VPC Network > Firewall > Create a firewall rule

| Field | Detail |
| ---- | ---- |
| Name: | js-http |
| Network: | acme-vpc |
| Direction of traffic: | Ingress |
| Action on match: | Allow |
| Target tags: | allow-http-ingress-ql-334 |
| Source Filter: | IPv4 Ranges |
| Source IPv4 ranges: | 0.0.0.0/0 |
| Protocol and ports: | Specfied protocol and ports \| tcp: 80 |

Add Network Tag to juice-shop instance
1. Navigation Menu > Compute Engine > VM instances
2. Click on juice-shop
3. Click Edit
4. Add `allow-http-ingress-ql-334` to Network Tag

## Task 5: Create firewall rule for Bastion Host to Juice-Shop Server
Create firewall rule 
1. Navigation Menu > VPC Network > VPC networks
2. Notice acme-mgmt-subnet network range: Internal IP ranges: 192.168.10.0/24
3. Navigation Menu > VPC Network > Firewall > Create a firewall rule

| Field | Detail |
| ---- | ---- |
| Name: | bastion-ssh-js |
| Network: | acme-vpc |
| Direction of traffic: | Ingress |
| Action on match: | Allow |
| Target tags: | allow-ssh-internal-ingress-ql-964 |
| Source Filter: | IPv4 Ranges |
| Source IPv4 ranges: | 192.168.10.0/24 |
| Protocol and ports: | Specfied protocol and ports \| tcp: 22 |

Add Network Tag to juice-shop instance
1. Navigation Menu > Compute Engine > VM instances
2. Click on juice-shop
3. Click Edit
4. Add `allow-ssh-internal-ingress-ql-964` to Network Tag

## Task 6: SSH from Bastion Host to Juice-Shop server
1. Navigation Menu > Compute Engine > VM instances
2. Note internal ip of juice-shop.
3. Click ssh on bastion.
4. Key in the following command 
```bash
gcloud compute ssh juice-shop --internal-ip
```
> Note: I have no idea why ssh $INTERNAL_IP did not work.
5. Press "Enter" twice and "Y" once.
