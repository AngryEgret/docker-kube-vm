# Kubernetes Usage

```
$ vagrant up
$ kubectl config set-cluster local --server=http://172.31.255.254:8080
$ kubectl config set-context local --cluster=local
$ kubectl config use-context local
```
# Docker Usage

```
$ export DOCKER_HOST=tcp://172.31.255.254:2375
```
