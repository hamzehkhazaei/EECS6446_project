# Requirements

To move along this tutorial and set up the required tools and applications, you will
need access to a cloud computing infrastructure including a set of Virtual Machines (VMs).


We’ll be using the [EECSVM Service](https://wiki.eecs.yorku.ca/dept/tdb/services:eecsvm) to create three Ubuntu machines. You can reach this service through [remotelab](https://remotelab.eecs.yorku.ca/#/). When connecting, make sure to choose one of the following servers: scarlet, rose, or ruby. To check if you have access, run the following command in remotelab:

```sh
% eecsvm access
You have access to EECSVM service.
```

Next, we need to install the `eecs6446-hkh` bundle, which sets up three Ubuntu VMs for you.
```sh
% eecsvm installbundle eecs6446-hkh
```
This will create three virtual machines named ubuntu1, ubuntu2, and ubuntu3. You’ll see messages showing their creation and registration (with UUIDs and progress percentages).

To make sure the three VMs were installed correctly, run:
```sh
% eecsvm list installed
ubuntu1
ubuntu2
ubuntu3
```

After installation, you can start and interact with any VM using:
```sh
% eecsvm start <vmname>
```
For example:
```sh
% eecsvm start ubuntu1
Waiting for VM "ubuntu1" to power on...
VM "ubuntu1" has been successfully started.
```

Alternatively, you can manage the VMs directly through the Oracle VM VirtualBox application, which is already installed on the remotelab servers.

Make sure all three VMs are running before moving on to the next step.

To set up a Kubernetes cluster, we will need different VMs to take `master`, `worker`, and `cluster head` roles.
The `master` nodes will be responsible for keeping the cluster running and scheduling resources on available
nodes while `worker` nodes will be responsible for running those workloads. `Cluster head` manages the nodes in the Kubernetes cluster and joins the worker node to the master. If you start the VMs in order, each one will be assigned a predictable IP address within VirtualBox’s internal NAT network.
Throughout this tutorial, we assume a cluster of following nodes: </br>
1. Master node: ubuntu1 with IP address 10.0.2.4
2. Worker node: ubuntu2 with IP address 10.0.2.5
3. Cluster head: ubuntu3 with IP address 10.0.2.6


## Initial Setup

First, we need to update all packages installed on all VMs. At the end of the following command, the OS asks for restarting some of the services, press `cancel` and move on.

```sh
# update the package list and upgrade installed packages on all machines
(master, worker and cluster head) $ sudo apt-get update && sudo apt-get upgrade -qy
```

In case you will be using more than one VM for your cluster, make sure that there
is network connectivity between your VMs by checking their `ping` status.

```sh
# ssh to the master from cluster head and check connectivity
(cluster head) $ ping 10.0.2.4
```

Make sure to replace `10.0.2.4` with your master VM IP, if different.
You should see an output like this:

```console
ping 10.0.2.4
PING 10.0.2.4 (10.0.2.4) 56(84) bytes of data.
64 bytes from 10.0.2.4: icmp_seq=1 ttl=64 time=0.404 ms
64 bytes from 10.0.2.4: icmp_seq=2 ttl=64 time=0.316 ms
64 bytes from 10.0.2.4: icmp_seq=3 ttl=64 time=0.385 ms
64 bytes from 10.0.2.4: icmp_seq=4 ttl=64 time=0.288 ms
^C
--- 10.0.2.4 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3098ms
rtt min/avg/max/mdev = 0.288/0.348/0.404/0.047 ms
```

## SSH-key

SSH keys are used to establish secure connections between two devices, allowing for secure and authenticated access to remote systems. SSH keys provide a more secure way to log in to a remote server than using a password alone. An SSH key pair typically consists of a private key, which should be kept confidential and stored on the client machine, and a public key, which can be shared and stored on remote servers. When connecting to a remote server using SSH, the client first authenticates using its private key, and the server then verifies the authenticity of the client's public key. If the public key matches the private key, the server grants access to the client. To learn more about SSH and how you can use it, watch [this tutorial](https://youtu.be/YS5Zh7KExvE).

In this project, we generate ssh key on ubuntu3 and then copy the ssh public key to ubuntu1 and ubuntu2.



```console
# Generating ssh key on ubuntu3
$ ssh-keygen

Generating public/private rsa key pair.
Enter file in which to save the key (/home/common/.ssh/id_rsa): [enter]
Enter passphrase (empty for no passphrase): [enter]
Enter same passphrase again: [enter]
Your identification has been saved in /home/common/.ssh/id_rsa
Your public key has been saved in /home/common/.ssh/id_rsa.pub
...
```

```sh
# This copies the ubuntu3's public key to the ubuntu1 and ubuntu2 authorized keys file
$ ssh-copy-id common@10.0.2.4
$ ssh-copy-id common@10.0.2.5
```

## The 'common' user on Ubuntu VMs
For some of the commands, you need to use `sudo` to run the command with the root access. `sudo` asks for the password from the user. By adding `common ALL=(ALL) NOPASSWD:ALL` at the end of the sudoers file, the `common` user won't have to enter a password each time using `sudo`. (Do the following part on all of the servers)

```sh
# open sudoers file
$ sudo visudo

# add following command at the end of the file
common ALL=(ALL) NOPASSWD:ALL

# press ctrl+o (write out), ENTER (save), and then ctrl+x (exit)
```
**Note:** The `common` has already have root access so to go to root mode you just need to enter `sudo sh`. 

<!-- 
```console
PING 10.1.1.2 (10.1.1.2) 56(84) bytes of data.
64 bytes from 10.1.1.2: icmp_seq=1 ttl=64 time=1.00 ms
64 bytes from 10.1.1.2: icmp_seq=2 ttl=64 time=0.416 ms
64 bytes from 10.1.1.2: icmp_seq=3 ttl=64 time=0.480 ms
64 bytes from 10.1.1.2: icmp_seq=4 ttl=64 time=0.471 ms
64 bytes from 10.1.1.2: icmp_seq=5 ttl=64 time=0.349 ms
64 bytes from 10.1.1.2: icmp_seq=6 ttl=64 time=0.348 ms
64 bytes from 10.1.1.2: icmp_seq=7 ttl=64 time=0.346 ms
64 bytes from 10.1.1.2: icmp_seq=8 ttl=64 time=0.362 ms
```
 -->
 
<!-- ## Firewall Configurations

The firewall configuration has been already done on your VMs, but generally we need the following
ports to be open for this tutorial:

- `TCP` port `6443` for Kubernetes API
- `UDP` port `8472` for Flannel VXLAN (Kubernetes CNI)
- `TCP` port `10250` for kubelet
- `TCP` port `80` for the web application
- `TCP` port `9090` for prometheus
- `TCP` port `8091` for locust
- `TCP` port `3000` for grafana -->

## Anaconda Installation

In this project, we will be using Python for interacting with our cluster. Using other
programming languages is also possible, but might need additional setup from you. You
can install [Anaconda](https://docs.conda.io/en/latest/) to act as the environment manager on your cluster.
**Run the following on your `master` node:**

```sh
# download miniconda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh
# install miniconda using non-interactive mode
bash ~/miniconda.sh -b -p $HOME/miniconda
# add bash hook for conda
echo 'eval "$(~/miniconda/bin/conda shell.bash hook)"' >> ~/.bashrc && source ~/.bashrc
```

After the installation is complete, you can test your installation using the following (It's not important to have the same version):

```console
$ conda --version
conda 25.7.0
```

## Jupyter Notebook Installation

Jupyter Notebook is a web-based interactive computational environment for creating and sharing documents that contain live code, equations, visualizations, and narrative text. Jupyter Notebook allows you to combine code, markdown, and multimedia in a single document, making it a powerful tool for data exploration and presentation. It is open-source software, built on top of the IPython (Interactive Python) shell, and runs on a variety of platforms, including Windows, MacOS, and Linux. You can install Jupyter on **master** VM using the conda package manager as follows:

```sh
$ conda install jupyter
```

<!-- Next, connect to the master node using visual studio code.
Then, open an empty file with `.py` extension to activate the python
extension on VS Code and click on the
`Select Python Interpreter` button on the bottom left corner of the window and
select `Python ... ('base': conda)` to use as the default python
environment. -->

Now that we have done our initialization step, you are ready to install
Kubernetes and other required tools on your cluster. 

[Next Step](02-kubernetes.md) -->
