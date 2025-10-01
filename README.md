# **🚀 Ambiente Kubernetes com Kind**

Este repositório contém a configuração para criar um cluster Kubernetes local com Kind, utilizando Calico como CNI, Ingress Controller (NGINX) e Metrics Server.
Além disso, estão definidos manifests para duas aplicações exemplo: conversao-temperatura e kube-news (com banco de dados).

## **📋 Pré-requisitos**

- [Docker](https://docs.docker.com/get-docker/)
- [Kind](https://kind.sigs.k8s.io/)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/)

## **📂 Estrutura do projeto**

k8s/
├─ cluster/                     # Configurações de cluster
│  ├─ kind-config.yaml
│  ├─ deploy-ingress-controller.yaml
│  ├─ metrics-server.yaml
│  └─ namespaces.yaml
│
├─ common/                      # Recursos comuns a múltiplos namespaces
│  └─ default-deny-all.yaml
│
├─ conversao-temperatura/       # Aplicação 1
│  ├─ deploy/
│  ├─ autoscaling/
│  ├─ ingress/
│  └─ network/
│
└─ kube-news/                   # Aplicação 2 + banco
   ├─ db/
   ├─ deploy/
   ├─ autoscaling/
   ├─ ingress/
   ├─ network/
   └─ secrets/

## **🌐 Criação do cluster**

### Criar cluster com Kind:
```bash
kind create cluster --name cluster-k8s --config ./cluster/kind-config.yaml
```

### Instalar Calico (CNI):
```bash
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.3/manifests/tigera-operator.yaml
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.3/manifests/custom-resources.yaml
```

### Instalar Ingress Controller (NGINX):
```bash
kubectl apply -f ./cluster/deploy-ingress-controller.yaml
```

### Instalar Metrics Server:
```bash
kubectl apply -f ./cluster/metrics-server.yaml
```

## **📦 Deploy do ambiente**

### Criar namespaces
```bash
kubectl apply -f ./k8s/cluster/namespaces.yaml
```

### Aplicar políticas de rede default-deny
```bash
kubectl apply -f ./k8s/common/default-deny-all.yaml -n conversao-temperatura
kubectl apply -f ./k8s/common/default-deny-all.yaml -n kube-news
```

## **🧩 Aplicações**

### conversao-temperatura
```bash
kubectl apply -f ./k8s/conversao-temperatura/deploy/deployment.yaml
kubectl apply -f ./k8s/conversao-temperatura/deploy/service.yaml
kubectl apply -f ./k8s/conversao-temperatura/autoscaling/horizontalPodAutoscaler.yaml
kubectl apply -f ./k8s/conversao-temperatura/ingress/ingress.yaml
kubectl apply -f ./k8s/conversao-temperatura/network/network-policy.yaml
```

### kube-news (Banco de Dados)
```bash
kubectl apply -f ./k8s/kube-news/db/statefulset.yaml
kubectl apply -f ./k8s/kube-news/db/service.yaml
kubectl apply -f ./k8s/kube-news/db/network-policy.yaml
```

### kube-news (Aplicação)
```bash
kubectl apply -f ./k8s/kube-news/deploy/configmap.yaml
kubectl apply -f ./k8s/kube-news/deploy/secrets.yaml
kubectl apply -f ./k8s/kube-news/deploy/deployment.yaml
kubectl apply -f ./k8s/kube-news/deploy/service.yaml
kubectl apply -f ./k8s/kube-news/autoscaling/horizontalPodAutoscaler.yaml
kubectl apply -f ./k8s/kube-news/ingress/ingress.yaml
kubectl apply -f ./k8s/kube-news/network/network-policy.yaml
```

## ✅ Verificação e Testes

### Verificar se os pods estão rodando:
```bash
kubectl get pods -A
```

### Testar os ingress:
```bash
kubectl get ingress -A
```
