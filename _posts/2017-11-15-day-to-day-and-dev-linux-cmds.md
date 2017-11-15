---
layout: post
title: "Day to day & daily dev linux commands summary"
date: 2017-11-15 16:56 +0800
categories: tech
---

# General
## View macheine kernel
```
uname -a
```

## Change to root
```
sudo -i
sudo su -
```

## Create a folder and grant permissions to current user
```
chmod -R 777 go
sudo chown -R ray go
```
## Processes related
```
ps -ax | grep nginx
```

## Open nautilus with root
gksudo nautilus

## Delete pattern matched files.
```
find /path/to/directory -type f -name '*[0-9]x[0-9]*[0-9]x[0-9]*.jpg' -delete
find /path/to/directory -type f -name '*[0-9]x[0-9]*[0-9]x[0-9]*.jpg' -exec rm {} +
ray@ray-pc:~/go_workspace/src/wholepro$ find ./ -type f -name 'README_*_*.md' -exec rm {} +
```

## View max socket connections
```
ray@ray-pc:~$ ulimit -n
```

## Restart a service
```
sudo systemctl restart apache2
```

## show line number in vim
```
:set number or :set nu
:set nonumber or :set nonu
```

## Mout remote server directory by using sshfs
```
sshfs shendu@192.168.1.240:/shendu/bin ~/workspace/trans
sshfs shendu@192.168.1.240:/shendu/bin ~/workspace/trans
```

## Copy ssh public key to remote server.
```
ray@ray:~$ cat ~/.ssh/id_rsa.pub | ssh root@138.197.209.57 'cat >> .ssh/authorized_keys'
```

## Disable ssh public key access and enable password authentication.
```
$> sudo vim /etc/ssh/sshd_config
```
Change PubkeyAuthentication yes to PubkeyAuthentication no
Change PasswordAuthentication no to PasswordAuthentication yes
Restart sshd service
```
$> sudo systemctl restart sshd
```

## Change device name
```
sudo hostname dock-regis-svr
sudo vim /etc/hostname
```

## Count rows of results
```
sudo docker images | tee >(wc -l)
```
### Or
```
sudo docker images | awk '{print} END {print NR}'
```

## Run process in the background by using nohup
```
nohup ./hello &
```

## View directory in tree structure
```
tree dir
```

## View tar.gz file structure
```
tar -tf nsq-1.0.0-compat.linux-amd64.go1.8.tar.gz
```

## Aliases
```
INFANTGRPC=$GOPATH/src/infant/vendor/github.com/golang/protobuf/protoc-gen-go
#SDGRPC=$GOPATH/src/shendu.com/vendor/github.com/golang/protobuf/protoc-gen-go
SDGRPC=$GOPATH/bin
MICROGRPC=$GOPATH/src/wholepro/vendor/protoc-gen-go
alias iprotoc='PATH=$PATH:$INFANTGRPC /usr/local/bin/protoc -I . --go_out=plugins=grpc:.'
alias sdprotoc='/usr/local/bin/protoc --plugin=$SDGRPC/protoc-gen-go -I . --go_out=plugins=grpc:.'
alias mprotoc='$GOPATH/src/wholepro/vendor/protoc/protoc --plugin=$MICROGRPC/protoc-gen-go -I . --go_out=plugins=micro:.'
```

## supervisor
```
sudo apt-get install supervisor
```
### create supervisord.conf under dir /etc/supervisor
```
cd /etc/sueprvisor
cp echo_suerpvisord_conf > supervisord.conf
```
### Start supervisor service
```
sudo supervisord
```

## ip
### View ip addresses
```
ip address
```

## Create USB bootable disk (Especially for CentOS)
### Download CentOS image
https://www.centos.org/download/
### unetbootin
https://tecadmin.net/how-to-create-bootable-linux-usb-using-ubuntu-or-linuxmint/#
```
$ sudo add-apt-repository ppa:gezakovacs/ppa
$ sudo apt-get update
$ sudo apt-get install unetbootin
```
### dd ddutility (Very good for USB & SD card reader)
https://github.com/thefanclub/dd-utility
https://www.thefanclub.co.za/how-to/dd-utility-write-and-backup-operating-system-img-and-iso-files-memory-card-or-disk

## After installed centos-7 minimal, set up the network to support networking
```
systemctl enable NetworkManager
systemctl start NetworkManager
nmcli conn show
nmcli conn up <name>
```


# Kubernetes related
## Reference
https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/
https://kubernetes.io/docs/setup/independent/install-kubeadm/
https://kubernetes.io/docs/tasks/tools/install-kubectl/

## View cluster config
```
kubectl config view
```

## ssh into minikube node
You can ssh into the VM by finding the IP (from kubectl config view) and using username "docker" password "tcuser":
ssh docker@192.168.XX.XX

## Do not forget the following commands show in the `kubeadm init` command result.Run below commands after kubeadm init to start using your cluster, you need to run (as a regular user):
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## If something wrong, you may run the following commands.
```
mkdir /etc/cni/net.d
systemctl start kubelet.service
```

## Expose a service
```
kubectl expose deployment hello-cors-app --type=LoadBalancer --name=hello-cors-app-loadbalancer --external-ip=165.227.17.223 --port=8090
```

## You can view pods healthy status by using comman `kubectl get pods --all-namespaces`, and it can show whether kube-dns and kube-flannel were installed correctly.
```
kubectl get pods --all-namespaces
```

## Join to the master (Notice: you have to make sure you have started docker.service and kubele.serivce, or the slave node join successfully but doesn't show on the master node. And there have chances you may meet the `x509: certificate assigned by unknown authority` issue)
```
kubeadm join --token b301f4.8f335802e86164fe 138.197.197.194:6443 --discovery-token-ca-cert-hash sha256:5de7774e494956996f33aa98a4a543e24fa59b678f150f23b70bc6998c308e8c
```

## Start docker.service and restart kubelet.service
```
systemctl start docker.service
systemctl restart kubelet.service
```

## Solve the nodes notready issue(The issue is produced by pod network in my situation.) Don't use weave-net as the network, just pick up the flannel(I tried, it works.)
```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel-rbac.yml
```

## Solve the creation of pod in pending status issue When You Need to Deploy Apps on the Mater Node.
```
$kubectl create -f hello-cors-app-deploy.yaml
```

```
Events:
  FirstSeen	LastSeen	Count	From			SubObjectPath	Type		Reason			Message
  ---------	--------	-----	----			-------------	--------	------			-------
  38s		7s		7	default-scheduler			Warning		FailedScheduling	No nodes are available that match all of the following predicates:: PodToleratesNodeTaints (1).
```

## Execute the below command and recreate resources to solve the problem.
```
kubectl taint nodes kube-master node-role.kubernetes.io/master:NoSchedule-
```

## Get all pods all namespaces.
```
kubectl --kubeconfig ./admin.conf get pods --all-namespaces -o wide
```

## Remove last kubernetes config
```
rm ~/.kube/config
```

## Tear down (Drain, delete, reset).
```
kubectl drain kube-node-01 --delete-local-data --force --ignore-daemonsets
kubectl delete node kube-node-02
kubeadm reset
```

## View kubelet logs
```
journalctl -u kubelet
```

## View deployment detail information in namespace `default`
```
kubectl get deploy hello-cors-app -n default -oyaml
```

## Run from images
```
kubectl run hiapi --image=ray-xyz.com:9090/hiapi --port=6767
kubectl run hiapix --image=ray-xyz.com:9090/hiapi --labels='app=hiapi'
```

## Scale deployments
```
root@kube-master:~# kubectl scale deployments/com-shendu-service-usercenter-user --replicas=3

```

## Expose deployment to service
```
kubectl expose deployment hiapi --type=NodePort
kubectl expose deployment shendu-service-sdmicro-server --type=NodePort --port=9090 --target-port=9090 --labels='app=shendu-service-sdmicro-server'
```

## Select service by labels
```
kubectl get svc -l='app=hiapi'
```

## Access kubernetes pods from the outside of a cluster (Local test)
### Reference
https://docs.giantswarm.io/guides/accessing-services-from-the-outside/
```
kubectl port-forward -n default hiapi-1365250305-x8lb2 6767:6767
```

## Minikube ssh into a kubenetes docker machine
```
eval $(minikube docker-env)
eval $(minikube docker-env -u)
docker attach 970939b30548
```

## View pods logs with level(and you can view program running logs. Yeah!!!).
```
kubectl logs -f nginx-ingress-controller-2156363272-f3qfm --v=10
```

## Attach to container in a pod.
```
kubectl exec -it  YOUR_CONTAINER/POD_NAME bash
```

## Describe svc as json format
```
kubectl -n default -ojson get service sdmicro
```

## The simplist way to creat a kubernetes cluster.
1. Create some VPS

2. Install docker.
```
sudo apt-get install docker.io
```

3. Install kubectl.
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
```

4. Install kubeadm & kubelet.
```
apt-get update && apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm
```

5. Init kubeadm on the master nodes.
```
kubeadm init --pod-network-cidr=10.244.0.0/1
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

6. Add CNI(e.g. => flannel container network interface) on the master nodes.
```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

7. Deploy apps
```
kubectl run nginx --image=nginx
kubectl run hiapi --image=ray-xyz.com:9090/hiapi
```

8. Expose services
```
kubectl expose deployment nginx --external-ip=165.227.9.89 --type=LoadBalancer --port=80
kubectl expose deployment hiapi --type=LoadBalancer --external-ip=165.227.9.89 --port=6767
kubectl expose deployment realmicrokube --type=LoadBalancer --external-ip=165.227.16.169 --port=80 --target-port=7878
```

9. Access services outside of cluster.
```
curl http://165.227.9.89
curl http://165.227.9.89:6767
```
or
```
curl http://api.ray-xyz.com/
curl http://api.ray-xyz.com:6767/
```

10. If you encounter problems which are unsolvable of setting up the cluster or you just want to clear everything you have
setup(Tear down the cluster), just run the following commands.
```
kubectl drain <node name> --delete-local-data --force --ignore-daemonsets
kubectl delete node <node name>
kubeadm reset
```

11. View pods and know where are they deployed.
```
root@kube-master:~# kubectl get pods -o wide
NAME                     READY     STATUS    RESTARTS   AGE       IP           NODE
hiapi-1313114994-x0q51   1/1       Running   0          5h        10.244.2.2   kube-slave-02
```

## RBAC Auth
#### If you cannot use kube client api, and the log shows like `2017/09/29 08:00:46 http: panic serving 10.244.0.0:53336: User "system:serviceaccount:default:default" cannot list pods in the namespace "default". (get pods)
`, then you should grant auth to the current role. Eg:
```
touch rbac-default.yaml
sudo vim rbac-default.yaml
#Content =>
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: rbac-default
subjects:
  - kind: ServiceAccount
    # Reference to upper's `metadata.name`
    name: default
    # Reference to upper's `metadata.namespace`
    namespace: default
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
##
```
Apply the rbac-default.yaml
```
kubectl apply -f rbac-default.yaml
```

## Upgrade the Minikube
```
minikube delete
sudo rm -rf ~/.minikube
```
re-install minikube and it should work

## Access kubernetes service in Minikube cluster
```
minikube service sdmicro
```

## Image pull policy
```
kubectl run sdmicro --image=ray-xyz.com:9090/sdmicro --image-pull-policy=IfNotPresent
```

## Demos
### Deployment
```
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: sdmicro
spec:
  selector:
    matchLabels:
      app: sdmicro
  replicas: 1
  template: # create pods using pod definition in this template
    metadata:
      labels:
        app: sdmicro
    spec:
      containers:
      - name: sdmicro
        image: ray-xyz.com:9090/sdmicro
        ports:
        - containerPort: 7878
        volumeMounts: # For docker in docker, mount host volume to container
        - name: host-docker-volume
          mountPath: /var/run/
        imagePullPolicy: IfNotPresent
      volumes:
      - name: host-docker-volume
        hostPath:
          path: /var/run
```

# Docker related
### Everyday repos
#### MySQL Server CE
sudo docker pull mysql/mysql-server:5.7
sudo docker run --name mysql-server -e MYSQL_ROOT_PASSWORD=123456 mysql/mysql-server:5.7

### Reference
https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
https://docs.docker.com/registry/deploying/

## Restart docker
```
systemctl restart docker
```

## Set up a docker registry server on the local network
### Reference
http://www.itzgeek.com/how-tos/linux/centos-how-tos/how-to-setup-docker-private-registry-on-centos-7-ubuntu-16-04.html
### Steps to set up local networked docker registry server
```
1. Install docker
2. mkdir -p /certs
2. Create self signed certificate
	openssl req -newkey rsa:4096 -nodes -sha256 -keyout /certs/ca.key -x509 -days 365 -out /certs/ca.crt
4. If the registry server doesn't have the file `/etc/resolv.conf`, then create it.
5. Run the registry server
	docker run -d -p 5000:5000 --restart=always --name registry -v /certs:/certs -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/ca.crt -e REGISTRY_HTTP_TLS_KEY=/certs/ca.key registry:2
6. Copy the /certs/ca.crt from the registry server to docker build client(The machine you work on with docker daemon).
7. Create entry in /etc/hosts
8. Restart the docker engine service on both server and client.
	systemctl restart docker
9. Now, you can work as normal
```

## Run docker registry with external accessible ability
### Reference
```
https://gist.github.com/PieterScheffers/63e4c2fd5553af8a35101b5e868a811e
letsencrypt installing => https://certbot.eff.org/#ubuntuxenial-other
letsencrypt path => /etc/letsencrypt
```

## Docker registry doc
https://docs.docker.com/registry/deploying/

## Run registry container
```
docker run -d --restart=always --name registry -v ~/certs/ray-xyz.com:/certs -v /opt/docker-registry:/var/lib/registry  -e REGISTRY_HTTP_ADDR=0.0.0.0:9090 -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key -p 9090:9090 registry:2
```

## Run Docker-in-Docker => dind, mount host volume on container.
```
sudo docker run -v /var/run/:/var/run ray-xyz.com:9090/sdmicro
```

## Login to registry server
```
docker login ray-xyz.com:9090
```

## Working with images
### Pull a base image
```
sudo docker pull ubuntu:16.04
```

### Build an image by using Dockerfile
```
FROM ray-xyz.com:9090/ubuntu1604
MAINTAINER Raywang
ADD bin/consul /usr/local/bin
ENTRYPOINT consul agent -dev
sudo docker build -t ray-xyz.com:9090/consul .
```

### Tag an image
```
docker tag image username/repository:tag => sudo docker tag ubuntu:16.04 ray-xyz.com/ubuntu1604
```

### Push images to ray-xyz.com:9090
```
docker push ray-xyz.com:9090/ubuntu1604
```
### View repositories on registry server
https://www.ray-xyz.com:9090/v2/_catalog

### List repositories on registry server
```
curl --insecure https://localhost:5000/v2/_catalog
```

### Pull a image
```
docker pull ray-xyz.com:9090/ubuntu1604
```

## Stop and remove all containers(commands and bash script)
```
sudo docker stop $(sudo docker ps -a -q)
sudo docker rm $(sudo docker ps -a -q)
```

```
sudo docker rm --force $(sudo docker ps -a | awk '{print $1}')
```

```
#!/bin/bash
containers=($(sudo docker ps -a | awk '{print $2}'))
containerids=($(sudo docker ps -a | awk '{print $1}'))
len_containers=${#containers[@]}
for (( i=1; i<${len_containers}; i++ ));
do
    echo "container id => ${containerids[$i]}, container => ${containers[$i]}"
    if (( "${containers[$i]}" == "usercenter" )); then
        echo "Container usercenter already exists, it will be deleted."
        sudo docker stop ${containerids[$i]}
        sudo docker rm --force ${containerids[$i]}
    fi
done
```

## Delete all images
```
sudo docker image rm $(sudo docker images)
```

## Communication between containers
### View or set ip-forwarding of the host machine
#### View
```
sysctl net.ipv4.conf.all.forwarding
```
#### Set => 1 to true, 0 for false
```
sysctl net.ipv4.conf.all.forwarding=1
```

### The ip_forward setting doesn't affect container when using the host net stack => --net=host
#### Using the network of the host.
```
sudo docker run --net=host ray-xyz.com:9090/hi
```

## View bridge info
```
sudo docker network inspect bridge
```

## View container info
```
sudo docker ps
sudo docker inspect 6f3e8d085c66
```

# Git
## List configs
```
git config -l
```

## Create a branch and check it out.
```
git checkout -b ray
```

## Ignore some file modified and added it to the .gitignore but still showing under the command `git status`
```
git rm -r --cached github.com/golang/protobuf/protoc-gen-go/protoc-gen-go
```

## Reset add
```
git reset HEAD github.com/golang/protobuf/protoc-gen-go/protoc-gen-go
```

## Reset commit
```
git reset --soft HEAD^
```

## Credential caching
```
git config credential.helper store
```
### With specific time to expire (eg.: 2hrs)
```
git config --global credential.helper 'cache --timeout 7200'
```

## Merge
Merge branches fixes and enhancements on top of the current branch, making an octopus merge:
```
$ git merge fixes enhancements
```

Merge branch obsolete into the current branch, using ours merge strategy:
```
$ git merge -s ours obsolete
```

Merge branch maint into the current branch, but do not make a new commit automatically:
```
$ git merge --no-commit maint
```

## Force checkout branch
```
git checkout -f another-branch
```

## Change the remote
```
git remote
git remote set-url origin git@192.168.1.252:wangrui/sdmicro.git
```

## Forcefully overrite the remote repo with local one
```
git push origin dev -f
```

## Check size of Git proj
```
git count-objects -vH
```

# Mysql
## Completely remove MySQL and reinstall mysql-server without backing up.
```
sudo apt-get purge mysql-server mysql-common mysql-client-5.7
sudo rm -rf /var/lib/mysql /etc/mysql/ /var/log/mysql*
sudo apt-get autoremove
sudo apt-get autoclean
```
reboot
  |
  ^
```
sudo apt-get install mysql-server
```

Change some config, restart the service
```
service mysql restart
```

# consul
### Run on local
```
consul agent -dev -enable-script-checks
```

### Run in Docker
```
sudo docker run --net=host ray-xyz.com:9090/consul
```

## Cloning into 'mailman-bundler'... fatal: unable to access
'https://gitlab.com/mailman/mailman-bundler.git/': Problem with the SSL CA cert (path? access rights?)

```
apt-get install ca-certificates
```







