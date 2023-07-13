# INSTALAÇÃO O-RAN SC Near-RT RIC
# Requisitos:
Memoria: 12-16gB RAM

CPU: 2-4 vCPUs

Armazenamento: 50-120gB

OS: Ubuntu 20.04

## Atualizar Bibliotecas
```
sudo apt update -y
sudo apt upgrade -y
```

## Configurar repositório do Docker
```
sudo apt-get install ca-certificates curl gnupg
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

## Instalar Docker
```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
## Verificar se o docker está em execução

```
sudo docker ps -a
```

O retorno deve ser: "CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES".

## Clone o Repositório O-RAN SC Near-RT RIC 
```
git clone "https://gerrit.o-ran-sc.org/r/ric-plt/ric-dep"
```

## Desative a memória swap para usar Kubernetes
```
sudo swapoff –a
```
Se não funcionar, apenas execute `swapoff -a` como usuário root.

## Instalar Helm and Kubernetes
```
cd ric-dep/bin
sudo bash install_k8s_and_helm.sh
sudo docker run --rm -u 0 -it -d -p 8090:8879 -e DEBUG=1 -e STORAGE=local -e STORAGE_LOCAL_ROOTDIR=/charts -v $(pwd)/charts:/charts chartmuseum/chartmuseum:latest
sudo bash install_common_templates_to_helm.sh
```

## Update Docker e Kubernetes
```
sudo apt-mark hold kubeadm kubectl kubelet kubernetes-cni docker.io
```

## Copiar RECIPES e instalar O-RAN-SC Near-RT RIC Release F

```
mkdir ../RECIPE_EXAMPLE/PLATFORM
cp ../RECIPE_EXAMPLE/* ../RECIPE_EXAMPLE/PLATFORM
sudo bash install -f ../RECIPE_EXAMPLE/PLATFORM/example_recipe_oran_f_release.yaml 
```

## Verificar se os pods estão em execução e a integridade da instalação
```
sudo kubectl get pods -n ricplt
curl -v http://localhost:32080/appmgr/ric/v1/health/ready
```
