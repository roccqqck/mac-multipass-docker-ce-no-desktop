# mac-multipass-docker-ce-no-desktop

# 1. install multipass and ubuntu

For macOS, you can download the installers [from GitHub](https://github.com/canonical/multipass/releases) or [use Homebrew](https://github.com/Homebrew/brew):

```
# Note, this may require you to enter your password for some sudo operations during install
brew install --cask multipass
```

# Usage

## Find available images
```
$ multipass find
```
```
Image                       Aliases           Version          Description
18.04                       bionic            20211129         Ubuntu 18.04 LTS
20.04                       focal,lts         20211129         Ubuntu 20.04 LTS
21.04                       hirsute           20211130         Ubuntu 21.04
21.10                       impish            20211103         Ubuntu 21.10
anbox-cloud-appliance                         latest           Anbox Cloud Appliance
minikube                                      latest           minikube is local Kubernete
```

## Launch a fresh instance of the current Ubuntu LTS
```
$ multipass launch 20.04 --name ubuntu20 --cpus 4 --mem 8G --disk 128G
```
```
Launching ubuntu20...
Downloading Ubuntu 20.04 LTS..........
Launched: dancing ubuntu20
```

## Check out the running instances
```
$ multipass list
```
```
Name                    State             IPv4             Image
ubuntu20                Running           192.168.64.12    Ubuntu 20.04 LTS
ubuntu18                Running           192.168.64.13    Ubuntu 18.04 LTS
```

## Learn more about the VM instance you just launched
```
$ multipass info ubuntu20
```
```
Name:           ubuntu20
State:          Running
IPv4:           192.168.64.12
Release:        Ubuntu 20.04.3 LTS
Image hash:     27cecebaf8c6 (Ubuntu 20.04 LTS)
Load:           0.68 0.38 0.15
Disk usage:     1.9G out of 123.9G
Memory usage:   205.3M out of 7.8G
Mounts:         --
```

## Connect to a running instance

```
$ multipass shell ubuntu20
```
```
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun Dec  5 11:53:00 CST 2021

  System load:  0.35               Processes:                155
  Usage of /:   1.6% of 123.88GB   Users logged in:          0
  Memory usage: 3%                 
  Swap usage:   0%                 IPv4 address for enp0s2:  192.168.64.12


0 updates can be applied immediately.


Last login: Sat Dec  4 23:14:12 2021 from 192.168.64.1
ubuntu@ubuntu:~$
```


## Stop an instance to save resources
```
$ multipass stop ubuntu20
```

## Delete the instance
```
$ multipass delete ubuntu20
```
It will now show up as deleted:
```$ multipass list
Name                    State             IPv4             Release
ubuntu18                STOPPED           --               Ubuntu Snapcraft builder for Core 18
ubuntu20                DELETED           --               Not Available
```

And when you want to completely get rid of it:

```
$ multipass purge
```



# 2. ubuntu install docker-ce
```
$ multipass shell ubuntu20
```
```
sudo apt-get remove docker docker-engine docker.io containerd runc

sudo apt-get update

sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

curl -fsSL https://get.docker.com -o get-docker.sh

sudo sh get-docker.sh
```


# 3. mac install docker-cli (not docker desktop)
```
brew install docker
```




# 4. set up ssh-keygen 
You could use the key from ```multipass shell```
```
cp /var/root/Library/"Application Support"/multipassd/ssh-keys/id_rsa   ~/.ssh
```
or generate new one
https://github.com/roccqqck/certificates-notes/blob/main/ssh-keygen.md

```
$ multipass list
```
```
Name                    State             IPv4             Image
ubuntu20                Running           192.168.64.12    Ubuntu 20.04 LTS
```
```
vim ~/.ssh/config
```
```
Host            multipass_ubuntu20                # ??????
Hostname        192.168.64.12        # IP or Domain name
Port            22                # ????????????
User            ubuntu                # ???????????????
identityfile    ~/.ssh/id_rsa    # ????????????
```





```
ssh multipass_ubuntu20
```



add mac env variable ```~/.zshrc```
```
export DOCKER_HOST="ssh://multipass_ubuntu20"
```
check docker command 
```
docker version
docker info
```

```
docker run -itd -p 8080:80 --name nginx nginx:stable
```
http://192.168.64.12:8080



vscode ```setting.json``` add one line ```{"docker.host": "ssh://multipass_ubuntu20"}```

vscode -> Remote Explorer -> Containers -> click ```nginx:stable```


# 5.set local.driver
https://multipass.run/docs/using-virtualbox-in-multipass-windows

https://multipass.run/docs/using-virtualbox-in-multipass-macos
```
sudo multipass set local.driver=virtualbox
sudo multipass set local.driver=hyperkit
multipass set local.driver=hyperv
```