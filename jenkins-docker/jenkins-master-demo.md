## Hands-On Time

---

# Jenkins Master Service


## Running Containers

---

```bash
ssh -i workshop.pem docker@$CLUSTER_IP

docker container run -d --name jenkins -p 8080:8080 jenkins:alpine

docker container ls

PRIVATE_IP=[...] # e.g. 172.31.21.216

curl -i "http://$PRIVATE_IP:8080"

docker container rm -f jenkins

docker container ls
```


## Running Containers

---

* No high availability
* No fault tolerance
* It's not 2014


## Running Services

---

```bash
docker service create --name jenkins -p 8080:8080 jenkins:alpine

docker service ps jenkins

exit

open "http://$CLUSTER_DNS:8080"

ssh -i workshop.pem docker@$CLUSTER_IP

docker service rm jenkins
```


## Running Jenkins Stack

---

```bash
curl -L -o jenkins.yml https://goo.gl/xfkzFG

cat jenkins.yml

docker stack deploy -c jenkins.yml jenkins

docker stack ps jenkins

exit

open "http://$CLUSTER_DNS:8080/jenkins"

ssh -i workshop.pem docker@$CLUSTER_IP

docker stack rm jenkins
```


## Running DFP Stack

---

```bash
curl -L -o proxy.yml https://goo.gl/2XcNEK

cat proxy.yml

docker network create -d overlay proxy

docker stack deploy -c proxy.yml proxy

docker stack ps proxy
```


## Running Jenkins Through DFP

---

```bash
curl -L -o jenkins.yml https://goo.gl/SuD4nc

cat jenkins.yml

docker stack deploy -c jenkins.yml jenkins

exit

open "http://$CLUSTER_DNS/jenkins"

ssh -i workshop.pem docker@$CLUSTER_IP

docker stack rm jenkins

exit
```


## Git In A Container

---

```bash
open https://goo.gl/PyAzAg

ssh -i workshop.pem docker@$CLUSTER_IP

docker container run --rm -it -v $PWD:/repos vfarcic/git \
    git clone https://github.com/vfarcic/docker-flow-stacks

docker container ls

ls -l
```


## Building Jenkins Image

---

```bash
cd docker-flow-stacks/jenkins

cat Dockerfile

cat security.groovy

cat plugins.txt

DOCKER_HUB_USER=[...]

docker image build -t $DOCKER_HUB_USER/jenkins:workshop .

docker login

docker image push $DOCKER_HUB_USER/jenkins:workshop
```


## Running Jenkins Without Manual Setup

---

```bash
cat vfarcic-jenkins-df-proxy.yml

echo "admin" | docker secret create jenkins-user -

echo "admin" | docker secret create jenkins-pass -

export HUB_USER=[...]

TAG=workshop docker stack deploy \
    -c vfarcic-jenkins-df-proxy.yml jenkins

exit

open "http://$CLUSTER_DNS/jenkins"
```


## Simulating Failure

---

```bash
# Create a job

open "http://$CLUSTER_DNS/jenkins/exit"

open "http://$CLUSTER_DNS/jenkins"

ssh -i workshop.pem docker@$CLUSTER_IP

docker stack ps jenkins

docker stack rm jenkins
```


## Preserving State

---

```bash
cd docker-flow-stacks/jenkins

cat vfarcic-jenkins-df-proxy-aws.yml

export HUB_USER=[...]

TAG=workshop docker stack deploy \
    -c vfarcic-jenkins-df-proxy-aws.yml jenkins
```


## Simulating Failure

---

```bash
exit

open "http://$CLUSTER_DNS/jenkins"

# Create a job

open "http://$CLUSTER_DNS/jenkins/exit"

open "http://$CLUSTER_DNS/jenkins"
```