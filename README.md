# Run K8s instance on VirtualBox

## Requirements
- Install VirtualBox - https://www.virtualbox.org/wiki/Downloads
- Install Vagrant - https://www.vagrantup.com/downloads

## How to use this configuration

1. Clone repository into you host. 
2. Install required software. 
3. You can copy app apps into apps folder and then k8s deployment files into k8s-deployment folder. These two folders will be mounted into vagrant instance into "/vagrant_data" folder.

## Create/stop/reload/destroy instance

### Start

Run following command inside the repository where Vagrantfile exists.

```
vagrant up
```

This command will take a wile but you should see instances in VirtualBox

### Stop

To stop instance without loosing data please use following command.

```
vagrant halt
```

### Reload

Any Vagrantfile modification like attached another port or share new folder with instance required instance reload.

```
vagrant reload
```


### Destroy

Be aware that this will destroy all configuration that you already deployed into instance and Kubernetes.

```
vagrant destroy
```

## SSH to instance

To access the instance please use following command

```
vagrant ssh
```

## Configuration has been tested on:

- OSX