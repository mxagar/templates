# Docker Howto

This file was created while following the Udemy course by Bret Fisher
Docker Mastery: with Kubernetes +Swarm from a Docker Captain.

Mikel Sagardia, 2021.
No warranties.

## Section 1: Introduction

### What is Docker? Why Docker?

Created in 2013 as open source project, now maintained by Docker Inc.

Paradigm shifts have happened in the past, requiring migrations:
- 90's: shift to PC networks
- 00's: virtualization
- 10's: Cloud
- Now: we are going from hosts to containers

Why docker?
- Like always, it's about speed in all the steps of the lifecycle.
- Containers reduce complexity: we package our code in a container and it runs everywhere
- Applications usually don't need to be changed to be used on docker containers, but they need to be packaged

### Resources


Original repo in

https://github.com/bretfisher/udemy-docker-mastery

Forked to

https://github.com/mxagar/udemy-docker-mastery

Additional material in my Dropbox:

- Cheat Sheet
- Slides
- Commads TXT files

See also the **FAQ** section.

## Section 2: Setting Up: Installation & Co.

### 2.1 Versions / Editions

- Each operating system has its editions and versions
- There is the Community Edition (CE, free), and the Enterprise Edition (EE, not free)
	- The EE has extra products and support, and it is certified
	- CE edition is for free and has Stable vs Edge versions
		- Edge is beta and comes out every month, supported for a smonth
		- Stable comes once per quarter (that's the recommended usually), suppported for 4 months
		- What prevviously was called 'Docker Engine' is now 'Docker CE'
		- We can extend the support period with EE
- The version matters because the industry is advancing very fast!
- Three types of installations
	1. Direct local: Linux only; each kernel and architecture has its version/edition, do not use default generic packages!
	2. Mac/Windows suite: they do not support Docker natively, but need to start a small virtual machine; however, we get a suite of tools and a GUI for interacting with Docker
	3. Cloud: AWS/Azure/Google; these are usually teh Linux version but with extended features for cloud platforms
- Since 2017 and version `1.13`, the `YY.MM` versioning convention is used, like with Ubuntu: `17.03`, `17.04`, ...


### 2.2 Installation

#### Windows / Mac: General notes

- Download from https://www.docker.com/products/docker-desktop
	- Preferably use the Stable version
- Download installer and install docker
- Assuming Windows 10 Pro; Windows Home Editions need extra installations and use the Docker Toolbox
- The Windows version
	- requires Hyper-V, a native virtual machine platform for Windows
	- requires Powershell
	- has specific **Windows containers** if the newest editions are used; all other containers are Linux containers
- The Mac version
	- works on Yosemite, so even in very old Macs; if older, we need Docker Toolbox
	- don't use the brew install, because it's only the CLI

#### Mac installation and setup steps

- Install img
- Menu icon appears
- Menu > Preferences
	- Resources: File sharing: Shared volumes should appear all here
	- Resources: Advanced: we see the specs of the native virtual machine running docker; note that resources are not used unless required
- Use the terminal, and check docker is running: `docker version`
- Install docker-machine with the commad line in: https://docs.docker.com/machine/install-machine/
- Command+TAB completion installation (really cool util that shows options with TABx2): https://docs.docker.com/docker-for-mac/#install-shell-completion
```bash
	# Install bash completion utility with brew
	brew install bash-completion
	# Link files
	etc=/Applications/Docker.app/Contents/Resources/etc
	ln -s $etc/docker.bash-completion $(brew --prefix)/etc/bash_completion.d/docker
	ln -s $etc/docker-compose.bash-completion $(brew --prefix)/etc/bash_completion.d/docker-compose
	# Add to ~/.bash_profile
	[ -f /usr/local/etc/bash_completion ] && . /usr/local/etc/bash_completion
	# Load again 
	source ~/.bash_profile
```

#### Linux

- Docker was made for Linux, no emulation is done under the hood, as with Windows or Mac
- Docker works for most distributions, but each needs a spcefic edition
- Do not use the default packages with `apt`, or any other pre-installed setups!
- Instead, go to the docker page and follow th einstructions, e.g., for Ubuntu
- Another option: run (Edge will be installed)
	`curl -sSL https://get.docker.com/ | sh`
- Add the user to the docker group to avoid doing sudo every time (but that is not allowed in all Linux versions)
	`sudo usermod -aG docker mxagar`
- For Linux, Docker Machine and Docker Compose need to be installed additionally, get the command lined from
	- https://docs.docker.com/compose/install/
	- https://docs.docker.com/machine/install-machine/
	- Note that docker machine and compose need to be updated manually and regularly

#### Resources

- Windows: cmder is a nice cmd prompt: https://cmder.net/
- Mac: Bret uses iTerm2
- **Visual Studio Code is recommended**: Docker plugins are available, **install them**!
- Github repo: clone it (I forked it): https://github.com/BretFisher/udemy-docker-mastery
- Docker Desktop for Mac: Manual: https://docs.docker.com/docker-for-mac/

## Section 3: Creating and Using Containers

- Containers are the building blocks of docker
- Example: check docker is running by querying its version

```bash
# Version information, check if docker is installed and runnig
docker version

# A lot of config info
docker info

# Get all possible commands
# Some of them are management commands that accept a subcommand
# These were introduced later as the number of commands increased
# docker <command> <subcommand>
# Example: 
# (old, but backward compatible) docker run
# (new style) docker container run
docker
```

- Images vs. containers
	- The image is the collection of binaries and libraries of our application
	- The container is a running instance of the image: you can have many containers running the same image
- Registries: as Github is a registry for code, the registry of docker images is Docker Hub: [hub.docker.com](hub.docker.com)
	- Official SW providers create and maintain their images, which can be downoaded/pulled and started locally as docker containers
	- Examples: nginx, ubuntu, python, golang, node, mongodb, postgres, ...
- We run a container with `docker container run` and the following happens
	- Docker looks for the image locally, if not found, it downloads it from Docker Hub; it is stored in the image cache
	- Latest version is download by default, but we can specify concrete versions
	- When a container is run, a new layer of changes is generated on the image, the image is not copied
	- A running container has a virtual IP on a private network and we can forward data to its ports
	- We can define a `CMD` in the image and pass the command when starting/running the container
- Example: Running an nginx server container:

```bash
# Docker looks for an image called nginx
# nginx is a web server
# Docker pulls the nginx image from Docker Hub
# and it starts it as a new process/container
# It opens the port 80 the host IP and sends all traffic through it
# to the port 80 of our container
# if host port 80 is taken, we could try 8888:80
docker container run --publish 80:80 nginx
# Open browser and go to localhost (or localhost:8888)
# nginx server notification appears

# Run in the background
# We get the container id back, to stop it with docker stop
docker container run --publish 80:80 --detach nginx

# If we open VS Code, containers & images appear in the tab

# List running containers shown, with id
docker container ls
docker ps

# Stop with Ctrl+C, or if detached
# docker stop <container id/name>
# BUT: we only stop the container, not remove it
# Note: using the first 3 letters of the id is enough
docker stop fecc962e3e6e
docker container stop fecc962e3e6e

# Show all containers that run at some point: running now and exited
# We get a table
# CONTAINER_ID, IMAGE, COMMAND, CREATED, STATUS, PORTS, NAMES
# Names are automatic (hacker & cypher names)
# unless we choose one with --name
docker container ls -a

# Start a nginx server container with name webhost
docker container run --publish 80:80 --detach --name webhost nginx

# Use container to generate some logs, e.g.,
# with nginx refresh the browser
# Display logs of container with name webhost
docker container logs webhost

# See top processes running in container named webhost
docker container top webhost

# See all subcommands commands for container
docker container --help

# Remove containers with their id; they won't appear with ls -a
# If running, we need to stop and rm, or force with rm -f
docker container ls -a
docker container rm 8db fec d76 880 889
docker container rm -f 3fd
```

- Containers are not really virtual machines, they are in fact just processes!
	- On Mac, a mini virtual machine is started and the container run in there
	- But on Linux, even containers are processes running on the host, not inside a virtual machine
	- Windows containers are different to the other (linux) containers: since 2017, Windows containers are EXEs running on the Windows kernel
	- Related links
		- [https://www.youtube.com/watch?v=sK5i-N34im8&list=PLBmVKD7o3L8v7Kl_XXh3KaJl9Qw2lyuFl](https://www.youtube.com/watch?v=sK5i-N34im8&list=PLBmVKD7o3L8v7Kl_XXh3KaJl9Qw2lyuFl)
		- [https://github.com/mikegcoleman/docker101/blob/master/Docker_eBook_Jan_2017.pdf](https://github.com/mikegcoleman/docker101/blob/master/Docker_eBook_Jan_2017.pdf)
		- [https://www.bretfisher.com/docker-for-mac-commands-for-getting-into-local-docker-vm/](https://www.bretfisher.com/docker-for-mac-commands-for-getting-into-local-docker-vm/)
		- [https://www.bretfisher.com/getting-a-shell-in-the-docker-for-windows-vm/](https://www.bretfisher.com/getting-a-shell-in-the-docker-for-windows-vm/)

```bash
# Start a MongoDB
# -d = --detach
docker run --name mongo -d mongo

# See the mongo container process: mongod
docker container top mongo

# On Linux, we'd see that mongod is a process on th ehost itself
ps aux | grep mongo

# If we stop and rm the container
# No mongod process will appear with ls -a nor with ps aux
docker container rm -f mongo
```

Exercise: managing several containers with env variables and looking for log output
```bash
# Start 3 containers with their typical ports open: nginx, https, mysql
# If images not available, downloaded from registries
# MySQL: we pass an environment variable with --env = -e
docker container run -d --name nginx --publish 80:80 nginx
docker container run -d --name mysql --publish 3306:3306 --env MYSQL_RANDOM_ROOT_PASSWORD=yes mysql
docker container run -d --name httpd --publish 8080:80 httpd

# For MySQL, we need to copy the random password generated to log in to it
# We must look at the logs for that
# ... "GENERATED ROOT PASSWORD: eilei1shohngohYaig4CohNohpouta1o"
docker container logs myql

# Stop and remove containers; check they are removed
# We can use the name or the ID, which is obtained also tabbing after stop
docker container stop mysql nginx https
docker container rm mysql nginx https
docker container ls -a
```

### CLI Process Monitoring



## Section 4: Container Images
