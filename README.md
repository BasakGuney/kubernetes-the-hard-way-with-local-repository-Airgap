# kubernetes-the-hard-way with local repository airgap

>This set-up is based on https://github.com/kelseyhightower/kubernetes-the-hard-way/tree/master/docs repository. 

>We assume that your local machine has internet access but your virtual machines don’t have internet access to pull required documents.

## Prequisites 
Unlike the reference repo, we will use Ubuntu Server ( Ubuntu 24.04.1 LTS ) as our VM’s  OS.  

You can download it via: https://ubuntu.com/download/server

| Name | Description | CPU | RAM | Storage |
|:-------------|:--------------:|:--------------:|:--------------:|--------------:|
| jumpbox | Administration host | 1 | 512MB | 10GB |
| server | Kubernetes server | 1 | 2GB | 20GB |
| node-0 | Kubernetes worker node | 1 | 2GB | 20GB |
| node-1 | Kubernetes worker node | 1 | 2GB | 20GB |

Your machines must provide the requirements above.  

Once you have all four machines provisioned, you can continue to next step.

## 1. Permit Root Login

To enable root login with ssh connection, we will make some configuration.  

1. Open the SSH configuration file in a text editor:

```bash
sudo nano /etc/ssh/sshd_config  
```

<br>

2. Locate the following line in the file.:
```bash
PermitRootLogin yes
``` 
<br> 

3. 	If you haven’t already set a password for the root user, you can do so with:
```bash
sudo passwd root
``` 
<br>

4.	Apply the changes by restarting the SSH service:
```bash
sudo systemctl restart ssh
``` 

<br>

Now you have root access in your ssh connection.

## 2. Changing Ubuntu Registry
To change the Ubuntu package registry (mirror) on your Ubuntu system, you can follow these steps:  

1.	Open the /etc/apt/sources.list.d/ubuntu.sources file in a text editor. For example, using nano:
```bash
sudo nano /etc/apt/sources.list.d/ubuntu.sources
``` 

2. Change the URIs parts in the text with your local registry URL.
<br>

3.	After changing the source list, update your package list:
```bash
sudo apt update
``` 
<br>
This should switch your system to the new registry.

## 3. Downloads Binaries Manually 
1. On your local machine, download required binaries in downloads.txt file in https://github.com/kelseyhightower/kubernetes-the-hard-way/tree/master
repository to a folder. Be careful to use appropriate binaries for your VM properties.

2. Then send the binaries in your local machine to jumbox downloads folder(change specified parts with your path to binaries folder and jumpbox IP ):
```bash
scp -r /path/to/local/folder username@jumpbox_IP:/root/kubernetes-the-hard-way/downloads

``` 
3. After that, you can apply the steps 2-5 in https://github.com/kelseyhightower/kubernetes-the-hard-way/tree/master/docs . 


## 4. Encryption Config file
For step 6 in the referenced repository you can use the following yaml as your encryption-config.yaml.
Create a file with encryption-config.yaml name and put it under /configs/ folder.

1. Create a file named encryption-config.yaml:
```bash
touch encryption-config.yaml
``` 
2. Open the file with editor:
```bash
nano encryption-config.yaml
``` 
3. Copy the following to your encryption-config.yaml file:

```txt                                                   
kind: EncryptionConfiguration
apiVersion: apiserver.config.k8s.io/v1
resources:
  - resources:
      - secrets
    providers:s
      - aescbc:
          keys:
            - name: key1
              secret: ${ENCRYPTION_KEY}
      - identity: {}
``` 
> When you check configs/encryption-config.yaml you can see that nothing has changed.
Thats because envsubst command adds secret to encryption-config.yaml in the current directory not in configs/encryption-config.yaml
4. Then you can follow the steps 7-8 in the docs.
 

## 5. Permanent Swap Off
You can follow the steps in step 9. After the swapoff -a command,
we will use an additional command to ensure that swap is always off:

```bash
swapoff -a
``` 
```bash
sudo sed -i.bak '/ swap / s/^/#/' /etc/fstab
``` 
