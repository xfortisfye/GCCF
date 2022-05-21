# Getting Started with Cloud Shell and gcloud

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
----
## Task 1: Configure your environment

1. See default region and zone setting
```bash
gcloud config get-value compute/zone
gcloud config get-value compute/region
```
> Note: Unset response = no default zone or region set
2. Identify default region and zone
```bash
gcloud compute project-info describe --project <your_project_ID>
```
3. Set environment variables
    1. Create an environment variable to store Project ID, replacing `<your_project_ID>` with the value for `name` from the `gcloud compute project-info describe` command
    ```bash
    export PROJECT_ID=<your_project_ID>
    ```
    2. Create an environment variable to store your Zone, replacing `<your_zone>` with the value for `zone` from the `gcloud compute project-info describe` command
    ```bash
    export ZONE=<your_zone>
    ```
    3. To verify that variables were set properly
    ```bash
    echo $PROJECT_ID
    echo $ZONE
    ```
    4. If the variables were set correctly, the echo commands will output your Project ID and Zone.
4. Create a virtual machine with gcloud tool
    1. Create VM
    ```bash
    gcloud compute instances create gcelab2 --machine-type n1-standard-2 --zone $ZONE
    ```
    > - gcloud compute allows you to manage your Compute Engine resources in a format that's simpler than the Compute Engine API. 
    > - instances create creates a new instance.
    > - gcelab2 is the name of the VM.
    > - The --machine-type flag specifies the machine type as n1-standard-2.
    > - The --zone flag specifies where the VM is created.
    > - If you omit the --zone flag, the gcloud tool can infer your desired zone based on your default properties. Other required instance settings, such as machine type and image, are set to default values if not specified in the create command.
    2. To open help for the `create` command:
    ```bash
    gcloud compute instances create --help
    ```
5. Explore gcloud command
    1. The gcloud tool offers simple usage guidelines that are available by adding the `-h` flag (for help) onto the end of any gcloud command.
    ```bash
    gcloud -h
    ```
    2. You can access more verbose help by appending the `--help` flag onto a command or running the gcloud help command.
    ```bash
    gcloud config --help
    # or
    gcloud help config 
    ```
    > Note: gcloud Global Flags govern the behavior of commands on a per-invocation level. Flags override any values set in SDK properties
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
----
## Task 2: Install a new component

gcloud interactive has auto prompting for commands and flags and displays inline help snippets in the lower section of the pane as the command is typed

1. Install the beta components
```bash
sudo apt-get install google-cloud-sdk
```
2. Enable `gcloud interactive`
```bash
gcloud beta interactive
```
> Note
> - When using the interactive mode, press TAB to complete file path and resource arguments. If a dropdown menu appears, press TAB to move through the list, and press the spacebar to select your choice.
> - A list of commands is displayed below the Cloud Shell pane. Pressing F2 toggles the active help section to ON or OFF.
3. To exit from interactive mode
```bash
exit
```
----
## Task 3: Connect to your VM instancce with SSH

`gcloud compute` makes connecting to your instances easy. The `gcloud compute ssh` command provides a wrapper around SSH, which takes care of authentication and the mapping of instance names to IP addresses.

1. Connect to VM with SSH
```bash
gcloud compute ssh gcelab2 --zone $ZONE
```
2. To continue, type `Y`.
3. To leave the passphrase empty, press <kbd>ENTER</kbd> twice.
----
## Task 4: Use the Home directory
1. Change your current working directory
```bash
cd $HOME
```
2. Open .bashrc configuration file with vi text editor
```bash
vi ./.bashrc
```
> To exit editor, press <kbd>ESC</kbd>, then type `:wq`, and then press <kbd>ENTER</kbd>
----
## Test your understanding
1. Three basic ways to interact with Google Cloud services and resources are:
- [ ] GStreamer
- [x] Cloud Console
- [x] Client Libraries
- [x] Command-line interface
- [ ] GLib
