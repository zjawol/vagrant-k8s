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

## Accessing app deployed into Kubernetes

Deploy your application into K8s. Nginx-ingress will automatically create access into your application service configuration. 

To verify which ports are defined for your load balancer use the command:

```
kubectl get services -n ingress-nginx ingress-nginx-controller

#Output should be like this 

NAME                       TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller   LoadBalancer   10.109.249.86   <pending>     80:31979/TCP,443:32566/TCP   18h
```
In this example 31979 and 32255

Define you application ingress hostname in you local "/etc/hosts" mapping apps dns into private IP defined in Vagrantfile. This IP can be different if has been changed for your environment.

Vagrantfile
```
...
  config.vm.network "private_network", ip: "192.168.56.10"
...
```
/etc/hosts

```
192.168.56.10 ingress.local 
192.168.56.10 my-app.ingress.local
```

Now you should be able to access you application via browser

http://my-app.ingress.local:31979/index


## Configuration has been tested on:

- OSX