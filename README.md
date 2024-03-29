# ANSIBLE LAB SETUP USING DOCKER

If you are looking for a quick lab setup to play with ansible then docker is the best choice. This repository contains 4 node lab setup. I have written a brief article on the lab setup which can be accessed through below link.

https://devrunops.com/ansible-lab-setup/

### Prerequisite

- [Docker](https://docs.docker.com/engine/install/)
- [Docker Compose](https://docs.docker.com/compose/install/)

### Lab Setup

This lab setup has 4 nodes where one node will act as controller and three nodes will act as managed nodes.

| NODE TYPE | IMAGE | IMAGE NAME| CONTAINER NAME | NETWORK |
|:-----:|:-----:|:----:|:-----:|:----:|
| controller | ubuntu:latest | anslab/controller | anscontroller| anslab|
| Managed | ubuntu:latest | anslab/controller | ansubuntu| anslab|
| Managed | alpine:latest | anslab/alpine | ansalpine| anslab|
| Managed | rockylinux:9 | anslab/rocky | ansrocky| anslab|

Image and container configuration can be found in `docker-compose.yml` file. 

### Deploy Lab

Clone the repository to your machine.

```sh
$ https://github.com/KarthickSudhakar/Docker_Ansible_lab_Setup.git 
```

Run the following command which will start pulling the base image's and build the containers.

```sh
$ cd Docker_Ansible_Lab_Setup
$ docker-compose up -d
```

### Authentication

User named `ansuser` will be created on all the nodes with passwordless sudo privilige. Both the `root` and `ansuser` password is set to `password123`.

To connect to the controller node run the following command. For any other nodes replace the container name and run the command.

```sh
$ docker container exec -it anscontroller sh
```

Password based authentication is also enabled. You need the IP address to SSH into the container.

```sh
$ docker network inspect anslab
$ ssh ansuser@IPaddress
```

### Bootstrap Script

In the controller node ansible package should be installed and it will be taken care by `script/bootstrap.sh`.

From the controller node SSH Keypair needs to be generated and distributed to all nodes for ansible to communicate. This will also be taken by `script/bootstrap.sh` script which is already copied to `/home/ansuser` in anscontroller node.

```sh
$ docker container exec -it anscontroller sh
$ su - ansuser
$ bash /home/ansuser/bootstrap.sh
```

This script will also create `/home/ansuser/sample_project` directory and run adhoc command for validation. You should be seeing similar output if the script ran fine.

```sh
anscontroller | CHANGED | rc=0 | (stdout) PRETTY_NAME="Ubuntu 22.04 LTS"
ansubuntu | CHANGED | rc=0 | (stdout) PRETTY_NAME="Ubuntu 22.04 LTS"
ansrocky | CHANGED | rc=0 | (stdout) PRETTY_NAME="Rocky Linux 9.0 (Blue Onyx)"
ansalpine | CHANGED | rc=0 | (stdout) PRETTY_NAME="Alpine Linux v3.17"
```

