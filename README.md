## Linux Virtualization
---
#### Part 1: Introduction to Virtualization Concepts

1. __Virtualization:__ Virtualization is the process of creating virtual instances of computer resources, such as operating systems, storage, and networks, allowing multiple environments to run on a single physical system.

2. __Hypervisor:__ A hypervisor is a software layer that enables virtualization by allowing multiple virtual machines (VMs) to share the same physical hardware.

3. __Virtual Machines (VMs):__ VMs are software-based emulations of a physical computer, running an OS and applications as if they were on a dedicated machine.

4. __Containers:__ Containers are lightweight, portable environments that package an application along with its dependencies, ensuring consistency across different environments.

5. __Key Differences Between VMs and Containers:__

| Virtual Machines  | Containers |
|---------------------|------------|
| Includes full OS, virtual hardware | Shares host OS kernel |
| Requires more resources | Lightweight |
| Strong isolation, each VM runs separately | Process-level isolation |
|  Slightly slower due to full OS overhead | Faster startup  |

#### Part 2: Working with Multipass:
Following the Source Guide
__Source Used:__ [Canonical Install Guide](https://canonical.com/multipass/docs/install-multipass)

__Steps:__

To install multipass:

```
snap install multipass
```
Checking if its installed correctly or not by running this command:
```

ls -l /var/snap/multipass/common/multipass_socket
```
Expected result:
```
srw-rw---- 1 root sudo 0 Dec 19 09:47 /var/snap/multipass/common/multipass_socket
```
Found result:
![]

Launching the default Mulitpass ubuntu instance:
```
multipass launch
```
I get a `launch failed` error and telling me to run `multipass authenticate` command first.

__But I used:__
```
sudo multipass launch
```
__Reason:__ When running Multipass commands, authentication happens automatically in the background.

Result:

[alt text](<Ss maltipass launch-1.png>)

__Listing instances:__
Command: 
```
sudo multipass list
```
Result:

![alt text](<Screenshot multipass list.png>)

__Multipass instance info:__
Command:
```
sudo multipass info vast-loon
```
Result:

![alt text](<Screenshot info.png>)

__Multipass Shell access:__
Command:
```
sudo multipass shell vast-loon
```
Result:
![alt text](<Screenshot shell.png>)

I created a file in the instance home directory and named it 'hello_world.txt'.
Now trying to read the file outside the instance using the cat command.

Command:
```
sudo multipass exec vast-loon -- cat hello_world.txt
```
Result:
![alt text](cat.png)


__Stopping the instace:__

```
sudo multipass stop vast-loon 
```
Result:

![alt text](<Screenshot stop & delete.png>)

__Delete the instance:__

```
sudo multipass delete vast-loon
```

RUselt:
![alt text](<Screenshot delete2.png>)

```
sudo multipass list
```




__Cloud-init Experiment:__

created the **"cloud-init.yml"** file.

![alt text](nano.png)

The config file will install some basic packeges and after successfully starting the instance it will return "Cloud-init is working!" from the welcome.txt file which will create after running the first time.

__Commnad:__

```
sudo multipass launch --name cloud-init --cloud-init cloud-init.yml
```

![alt text](<lunch list.png>)

Then accessed shell using

```
sudo multipass shell cloud-init
```

![alt text](<Screenshot 12.png>)

Now checking if the user Sabiha_cloud exist.
__command:__

```
cat /etc/passwd | grep Sabiha_cloud
```
__Result:__
![](img/8.1.png)
Verifying welcome.txt
```
sudo cat /home/Sabiha_cloud/welcome.txt
```
__Result:__
![alt text](<Screenshot 1.2.png>)

Using sudo because the file was created by root and was accessable by sabiha_cloud.
![alt text](1.3.png)

created a folder in the host machine named "host_machine" and created another folder in instance called "shared_folder". then mounted the folder on the host machine using:

```
sudo multipass mount ~/host_machine cloud-init:/home/ubuntu/shared_folder 
```
Creating a test file in the host machine.

```
echo "Hello from the host!" > ~/shared-folder/hostfile.txt
```


### Study
LXD is a next-generation system container manager that provides a user-friendly experience for managing Linux containers. It extends LXC, offering a robust API, CLI tools, and the ability to manage both containers and virtual machines.

#### Key Features of LXD
- **Image-based**: LXD uses prebuilt images for various Linux distributions.
- **Security**: Containers run under an unprivileged user, increasing isolation.
- **Scalability**: LXD supports clustering, making it efficient for managing multiple containers.
- **Live Migration**: Containers can be moved between hosts.

#### Setup
To install and enable LXD on Ubuntu 24.04, follow these steps:

``` 
sudo apt update && sudo apt install -y lxd
sudo lxd init
```
During initialization, LXD will prompt for storage, networking, and security configurations.

#### Basic Commands
Below are essential LXD commands to manage containers:

``` 
lxc launch ubuntu:24.04 my-container
lxc list
lxc exec my-container -- bash
lxc stop my-container
lxc delete my-container
```

#### Challenges Faced
- **Storage Backend Configuration**: The setup required choosing a storage backend (ZFS, LVM, or directory). Opted for ZFS for better performance.
- **Network Bridge Setup**: Had to manually create a bridge to allow internet access to containers.

### How to Stick Apps with Docker

#### Installation
To install Docker on Ubuntu 24.04:

``` 
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable --now docker
```
Verify installation:
``` 
docker --version
```

#### Basic Concepts
- **Images**: Read-only templates used to create containers.
- **Containers**: Instances of Docker images.
- **Dockerfile**: A script defining how to build an image.

#### Experiment
To test Docker, I created a simple containerized Nginx server:

``` 
docker run -d -p 8080:80 --name my-nginx nginx
```
Accessing `http://localhost:8080` confirmed it was running successfully.

#### Challenges Faced
- **Permission Issues**: Initially, needed `sudo` for Docker commands. Solved by adding the user to the `docker` group.
``` 
sudo usermod -aG docker $USER
```
- **Port Conflicts**: Another service was using port 80, so I mapped to 8080 instead.

### Snaps for Self-Contained Applications

#### Research
Snaps are self-contained application packages that include dependencies, allowing for easy deployment across Linux distributions.

#### Benefits of Snaps
- **Automatic Updates**: Ensures applications stay updated.
- **Isolation**: Snaps run in a sandboxed environment.
- **Cross-Distro Compatibility**: Works across different Linux distributions.

#### Experiment: Creating a Snap
Installed Snapcraft:
``` 
sudo snap install snapcraft --classic
```

Created a basic snap package:
``` 
mkdir my-snap
cd my-snap
snapcraft init
```
Modified `snapcraft.yaml` to build a simple application. Then, built and installed the snap:
``` 
snapcraft
sudo snap install my-snap_*.snap --dangerous
```
Verified installation:
``` 
snap list | grep my-snap
```

#### Challenges Faced
- **Dependency Issues**: Had to install missing dependencies manually.
- **Permissions**: Required `--dangerous` flag to install locally built snaps.