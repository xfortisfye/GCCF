#### Google Cloud Computing Foundations | C01 Cloud Computing Fundamentals
#### M02: Start with a Solid Platform 

# Lab 02-01: Getting Started with Cloud Shell and gcloud

## Overview 
Cloud Shell provides you with command-line access to computing resources hosted on Google Cloud. Cloud Shell is a Debian-based virtual machine with a persistent 5-GB home directory, which makes it easy for you to manage your Google Cloud projects and resources. The gcloud command-line tool and other utilities you need are pre-installed in Cloud Shell, which allows you to get up and running quickly.

## Setup

1. Active Cloud Shell
gcloud is the command-line tool for Google Cloud. It comes pre-installed on Cloud Shell and supports tab-completion.

    1. List active account name 
    ```bash
    gcloud auth list
    ```

    2. List project ID
    ```bash
    gcloud config list project
    ```
    
## Task 1: Configure your environment
In this section, you'll learn about aspects of the development environment that you can adjust.

### Understanding regions and zones
Certain Google Compute Engine resources live in regions or zones. A region is a specific geographical location where you can run your resources. Each region has one or more zones. For example, the us-central1 region denotes a region in the Central United States that has zones us-central1-a, us-central1-b, us-central1-c, and us-central1-f. The following table shows zones in their respective regions:

| Western US | Central US | Eastern US | Western Europe | Eastern Asia |
| ---- | ---- | ---- | ---- | ---- | 
| us-west1-a | us-central-a | us-east1-b | europe-west1-b | asia-east1-a |
| us-west1-b  | us-central-b | us-east1-c | europe-west1c | asia-east1-b |
| - | us-central1-c | us-east1-d | europe-west1-d | aisia-east1-c |
| - | us-central1-f | - | - | - |

Resources that live in a zone are referred to as zonal resources. Virtual machine instances and persistent disks live in a zone. If you want to attach a persistent disk to a virtual machine instance, both resources must be in the same zone. Similarly, if you want to assign a static IP address to an instance, the instance must be in the same region as the static IP address.

> Note: Learn more about regions and zones and see a complete list in Google Cloud Compute Engine's Regions and Zones documentation.

1. Set the region to us-central1
```bash
gcloud config set compute/region us-central1
```

2. To view the project region setting, run the following command:
```bash
gcloud config get-value compute/region
```

3. Set the zone to us-central1-b
```bash
gcloud config set compute/zone us-central1-b
```

4. To view the project zone setting, run the following command:
```bash
gcloud config get-value compute/zone
```

### Finding project information
1. Copy your project ID to your clipboard or text editor. The project ID is listed in 2 places:
* In the Google Cloud Console, on the Dashboard, under Project info. (Click Navigation menu (Navigation menu icon), and then click Home > Dashboard.)
* On the Qwiklabs tab near your username and password.

2. In Cloud Shell, run the following gcloud command, to view the project id for your project:
```bash
gcloud config get-value project
```

3. In Cloud Shell, run the following gcloud command to view details about the project:
```bash
gcloud compute project-info describe --project $(gcloud config get-value project) 
```
Find the zone and region metadata values in the output. You'll use the zone (google-compute-default-zone) from the output later in this lab.

> Note: When the google-compute-default-region and google-compute-default-zone keys and values are missing from the output, no default zone or region is set. The output includes other useful information regarding your project. Take some time to explore this in more detail.

### Setting environment variables
Environment variables define your environment and help save time when you write scripts that contain APIs or executables.

1. Create an environment variable to store Project ID, replacing `<your_project_ID>` with the value for `name` from the `gcloud compute project-info describe` command
```bash
export PROJECT_ID=$(gcloud config get-value project)
```

2. Create an environment variable to store your Zone, replacing `<your_zone>` with the value for `zone` from the `gcloud compute project-info describe` command
```bash
export ZONE=$(gcloud config get-value compute/zone)
```

3. To verify that variables were set properly
```bash
echo -e "PROJECT ID: $PROJECT_ID\nZONE: $ZONE"
```
If the variables were set correctly, the echo commands will output your Project ID and Zone.

### Create a virtual machine with gcloud tool
Use the gcloud tool to create a new virtual machine (VM) instance.

1. Create VM
```bash
gcloud compute instances create gcelab2 --machine-type n1-standard-2 --zone $ZONE
```
**Output:**
```bash
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-04-326fae68bc3d/zones/us-central1-b/instances/gcelab2].
NAME     ZONE           MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP   STATUS
gcelab2  us-central1-b  n1-standard-2               10.128.0.2   34.67.152.90  RUNNING
```
Command details
- gcloud compute allows you to manage your Compute Engine resources in a format that's simpler than the Compute Engine API. 
- instances create creates a new instance.
- gcelab2 is the name of the VM.
- The --machine-type flag specifies the machine type as n1-standard-2.
- The --zone flag specifies where the VM is created.
- If you omit the --zone flag, the gcloud tool can infer your desired zone based on your default properties. Other required instance settings, such as machine type and image, are set to default values if not specified in the create command.
- To open help for the `create` command:
```bash
gcloud compute instances create --help
```
> Note: Press ENTER or the spacebar to scroll through the help content. To exit the content, type Q.

### Explore gcloud commands
1. The gcloud tool offers simple usage guidelines that are available by adding the `-h` flag (for help) onto the end of any gcloud command.
```bash
gcloud -h
```

2. You can access more verbose help by appending the `--help` flag onto a command or running the gcloud help command.
```bash
gcloud config --help
```
> Note: Press ENTER or the spacebar to scroll through the help content. To exit the content, type Q.

3. Run the following command
```bash
gcloud help config 
```
> Note: Press ENTER or the spacebar to scroll through the help content. To exit the content, type Q.

`gcloud` Global Flags govern the behavior of commands on a per-invocation level. Flags override any values set in SDK properties

4. View the list of configurations in your environment:
```bash
gcloud config list
```

5. To see all properties and their settings
```bash
gcloud config list --all
```

6. List your components:
```bash
gcloud components list
```

## Task 2: Filtering command line output
The gcloud CLI is a powerful tool for working at the command line. You may want specific information to be displayed.

1. List the compute instance available in the project
```bash
gcloud compute instances list
```
> Note: Having multiple resources deployed in a project is very common. Fortunately gcloud has some clever formatting that can help identify specific resources.
**Output:**
```bash
NAME                   ZONE           MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
cloudlearningservices  us-central1-a  e2-standard-2               10.128.0.2   35.202.228.143  RUNNING
gcelab2                us-central1-b  n1-standard-2               10.128.0.2   34.68.7.186     RUNNING
```

2. List the gcelab2 virtual machine
```bash
gcloud compute instances list --filter="name=('gcelab2')"
```
**Output:**
```bash
NAME                   ZONE           MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
gcelab2                us-central1-b  n1-standard-2               10.128.0.2   34.68.7.186     RUNNING
```
In the above command we have asked gcloud to only show the information matching the criteria i.e. a virtual instance name matching the criteria.

3. List the Firewall rules in the project
```bash
gcloud compute firewall-rules list
```
**Output:**
```bash
NAME                         NETWORK      DIRECTION  PRIORITY  ALLOW                         DENY  DISABLED
default-allow-icmp           default      INGRESS    65534     icmp                                False
default-allow-internal       default      INGRESS    65534     tcp:0-65535,udp:0-65535,icmp        False
default-allow-rdp            default      INGRESS    65534     tcp:3389                            False
default-allow-ssh            default      INGRESS    65534     tcp:22                              False
dev-net-allow-ssh            dev-network  INGRESS    1000      tcp:22                              False
serverless-to-vpc-connector  dev-network  INGRESS    1000      icmp,udp:665-666,tcp:667            False
vpc-connector-egress         dev-network  INGRESS    1000      icmp,udp,tcp                        False
vpc-connector-health-check   dev-network  INGRESS    1000      tcp:667                             False
vpc-connector-to-serverless  dev-network  EGRESS     1000      icmp,udp:665-666,tcp:667            False
```

4. List the Firewall rules for the default network
```bash
gcloud compute firewall-rules list --filter="network='default'"
```
**Output:**
```bash
NAME                         NETWORK      DIRECTION  PRIORITY  ALLOW                         DENY  DISABLED
default-allow-icmp           default      INGRESS    65534     icmp                                False
default-allow-internal       default      INGRESS    65534     tcp:0-65535,udp:0-65535,icmp        False
default-allow-rdp            default      INGRESS    65534     tcp:3389                            False
default-allow-ssh            default      INGRESS    65534     tcp:22                              False
```

5. List the Firewall rules for the default network where the allow rule matches an ICMP rule
```bash
gcloud compute firewall-rules list --filter="NETWORK:'default' AND ALLOW:'icmp'"
```
**Output:**
```bash
NAME                         NETWORK      DIRECTION  PRIORITY  ALLOW                         DENY  DISABLED
default-allow-icmp           default      INGRESS    65534     icmp                                False
default-allow-internal       default      INGRESS    65534     tcp:0-65535,udp:0-65535,icmp        False
```

## Task 3: Connect to your VM instance
`gcloud compute` makes connecting to your instances easy. The `gcloud compute ssh` command provides a wrapper around SSH, which takes care of authentication and the mapping of instance names to IP addresses.

1. Connect to VM with SSH
```bash
gcloud compute ssh gcelab2 --zone $ZONE
```
**Output:**
```bash
WARNING: The public SSH key file for gcloud does not exist.
WARNING: The private SSH key file for gcloud does not exist.
WARNING: You do not have an SSH key for gcloud.
WARNING: [/usr/bin/ssh-keygen] will be executed to generate a key.
This tool needs to create the directory
[/home/gcpstaging306_student/.ssh] before being able to generate SSH Keys.
```
```bash
Do you want to continue? (Y/n)
```

2. To continue, type `Y`.
```bash
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase)
```

3. To leave the passphrase empty, press <kbd>ENTER</kbd> twice.
> Note: You have connected to the virtual machine created earlier in the lab. Did you notice how the command prompt changed?
> The prompt now says something similar to sa_107021519685252337470@gcelab2.
> - The reference before the @ indicates the account being used.
> - After the @ sign indicates the host machine being accessed.

4. Install nginx web server on to virtual machine
```bash
sudo apt install -y nginx
```

5. You don't need to do anything here, so to disconnect from SSH and exit the remote shell, run the following command:
```bash
exit
```
You should be back at your project's command prompt.

## Task 4: Updating the Firewall
When using compute resources such as virtual machines, it's important to understand the associated firewall rules.

1. List the firewall rules for the project

```bash
gcloud compute firewall-rules list
```
**Output:**
```bash
NAME                         NETWORK      DIRECTION  PRIORITY  ALLOW                         DENY  DISABLED
default-allow-icmp           default      INGRESS    65534     icmp                                False
default-allow-internal       default      INGRESS    65534     tcp:0-65535,udp:0-65535,icmp        False
default-allow-rdp            default      INGRESS    65534     tcp:3389                            False
default-allow-ssh            default      INGRESS    65534     tcp:22                              False
dev-net-allow-ssh            dev-network  INGRESS    1000      tcp:22                              False
serverless-to-vpc-connector  dev-network  INGRESS    1000      icmp,udp:665-666,tcp:667            False
vpc-connector-egress         dev-network  INGRESS    1000      icmp,udp,tcp                        False
vpc-connector-health-check   dev-network  INGRESS    1000      tcp:667                             False
vpc-connector-to-serverless  dev-network  EGRESS     1000      icmp,udp:665-666,tcp:667            False
```
From the above we can see we have two networks available. The default network is where our virtual machine gcelab2 is located.

2. Try to access the nginx service running on the gcelab2 virtual machine
> Note: Communication with the virtual machine will fail as it does not have an appropriate firewall rule. Our nginx web server is expecting to communicate on tcp:80. To get communication working we need to:
> - Add a tag to the gcelab2 virtual machine
> - Add a firewall rule for http traffic

3. Add a tag to the virtual machine
```bash
gcloud compute instances add-tags gcelab2 --tags http-server,https-server
```

4. Update the firewall rule to allow
```bash
gcloud compute firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server
```

5. List the firewall rules for the project
```bash
gcloud compute firewall-rules list --filter=ALLOW:'80'
```
**Output:**
```bash
NAME                NETWORK  DIRECTION  PRIORITY  ALLOW   DENY  DISABLED
default-allow-http  default  INGRESS    1000      tcp:80        False
```

6. Verify communication is possible for http to the virtual machine
```bash
curl http://$(gcloud compute instances list --filter=name:gcelab2 --format='value(EXTERNAL_IP)')
```
You will see the default nginx output.

## Task 5. Viewing the system logs
Viewing logs is essential to understanding the working of your project. Use gcloud to access the different logs available on Google Cloud.

1. View the available logs on the system:

```bash
gcloud logging logs list 
```
**Output:**
```bash
NAME
projects/qwiklabs-gcp-03-6bef54ddd5e7/logs/GCEGuestAgent
projects/qwiklabs-gcp-03-6bef54ddd5e7/logs/OSConfigAgent
projects/qwiklabs-gcp-03-6bef54ddd5e7/logs/autoscaler.googleapis.com%2Fstatus_change
projects/qwiklabs-gcp-03-6bef54ddd5e7/logs/cloudaudit.googleapis.com%2Factivity
projects/qwiklabs-gcp-03-6bef54ddd5e7/logs/cloudaudit.googleapis.com%2Fsystem_event
projects/qwiklabs-gcp-03-6bef54ddd5e7/logs/compute.googleapis.com%2Fautoscaler
projects/qwiklabs-gcp-03-6bef54ddd5e7/logs/compute.googleapis.com%2Finstance_group_manager_events
projects/qwiklabs-gcp-03-6bef54ddd5e7/logs/compute.googleapis.com%2Fshielded_vm_integrity
projects/qwiklabs-gcp-03-6bef54ddd5e7/logs/run.googleapis.com%2Frequests
projects/qwiklabs-gcp-03-6bef54ddd5e7/logs/run.googleapis.com%2Fstderr
projects/qwiklabs-gcp-03-6bef54ddd5e7/logs/run.googleapis.com%2Fstdout
```

2. View the logs that relate to compute resources
```bash
gcloud logging logs list --filter="compute" 
```
**Output:**
```bash
NAME
projects/qwiklabs-gcp-03-6bef54ddd5e7/logs/compute.googleapis.com%2Fautoscaler
projects/qwiklabs-gcp-03-6bef54ddd5e7/logs/compute.googleapis.com%2Finstance_group_manager_events
projects/qwiklabs-gcp-03-6bef54ddd5e7/logs/compute.googleapis.com%2Fshielded_vm_integrity
```

3. Read the logs related to the resource type of gce_instance
```bash
gcloud logging read "resource.type=gce_instance" --limit 5
```

4. Read the logs for a specific virtual machine
```bash
gcloud logging read "resource.type=gce_instance AND labels.instance_name='gcelab2'" --limit 5
```

## Test your understanding
1. Three basic ways to interact with Google Cloud services and resources are:
- [ ] GStreamer
- [x] Cloud Console
- [x] Client Libraries
- [x] Command-line interface
- [ ] GLib
