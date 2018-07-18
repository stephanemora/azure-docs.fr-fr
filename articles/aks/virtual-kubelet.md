---
title: Exécuter un Virtual Kubelet dans un cluster Azure Kubernetes Service (AKS)
description: Utilisez virtual kubelet pour exécuter des conteneurs Kubernetes sur Azure Container Instances.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/12/2018
ms.author: iainfou
ms.openlocfilehash: 04fdb1620dc6e7147ed10ae6eeeaeb3eeae14b62
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097357"
---
# <a name="virtual-kubelet-with-aks"></a>Virtual Kubelet avec AKS

Azure Container Instances (ACI) fournit un environnement hébergé pour l’exécution de conteneurs dans Azure. Lorsque vous utilisez ACI, il n’est pas nécessaire de gérer l’infrastructure sous-jacente de calcul ; Azure le fait pour vous. Lorsque vous exécutez des conteneurs dans ACI, vous êtes facturés à la seconde d’exécution de chaque conteneur.

Lorsque vous utilisez le fournisseur Virtual Kubelet pour Azure Container Instances, il est possible de programmer à la fois les conteneurs Linux et Windows sur une instance de conteneur, comme s’il s’agissait d’un nœud Kubernetes standard. Cette configuration vous permet de tirer parti à la fois des fonctionnalités de Kubernetes et de la facilité de gestion et des économies des instances de conteneurs.

> [!NOTE]
> Virtual Kubelet est un projet open source expérimental, et doit donc être utilisé dans cette optique. Pour contribuer, signaler des problèmes avec les fichiers, et en savoir plus sur virtual kubelet, consultez la page du [projet Virtual Kubelet GitHub][vk-github].

Ce document détaille comment configurer Virtual Kubelet pour les instances de conteneur sur AKS.

## <a name="prerequisite"></a>Configuration requise

Ce document suppose que vous disposez d’un cluster AKS. Si vous avez besoin d’un cluster AKS, consultez le [Guide de démarrage rapide : déployer un cluster Azure Kubernetes Service (AKS)][aks-quick-start].

Vous devez également disposer d’Azure CLI version **2.0.33** ou version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

[Helm](https://docs.helm.sh/using_helm/#installing-helm) est également requis pour pouvoir installer Virtual Kubelet.

## <a name="installation"></a>Installation

Utilisez la commande [az aks install-connector][aks-install-connector] pour installer Virtual Kubelet. L’exemple suivant permet de déployer les connecteurs Linux et Windows.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

Ces arguments sont disponibles pour la commande `aks install-connector`.

| Argument : | Description | Obligatoire |
|---|---|:---:|
| `--connector-name` | Nom du connecteur ACI.| OUI |
| `--name``-n` | Nom du cluster géré. | OUI |
| `--resource-group``-g` | Nom du groupe de ressources. | OUI |
| `--os-type` | Type de système d’exploitation des instances de conteneur. Valeurs autorisées : Both, Linux, Windows. Par défaut : Linux. | Non  |
| `--aci-resource-group` | Le groupe de ressources dans lequel créer les groupes de conteneurs ACI. | Non  |
| `--location``-l` | L’emplacement de création des groupes de conteneurs ACI. | Non  |
| `--service-principal` | Principal de service utilisé pour l’authentification auprès des API Azure. | Non  |
| `--client-secret` | Secret associé au principal du service. | Non  |
| `--chart-url` | URL du chart Helm qui installe le connecteur ACI. | Non  |
| `--image-tag` | La balise d’image de l’image du conteneur virtual kubelet. | Non  |

## <a name="validate-virtual-kubelet"></a>Valider Virtual Kubelet

Pour valider la version de Virtual Kubelet installée, retournez une liste de nœuds Kubernetes à l’aide de la commande [kubectl get nodes][kubectl-get].

```console
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Exécuter un conteneur Linux

Créez un fichier nommé `virtual-kubelet-linux.yaml` et copiez-y le YAML suivant. Remplacez la valeur `kubernetes.io/hostname` avec le nom du nœud Virtual Kubelet pour Linux. Veuillez noter que [nodeSelector][node-selector] et [toleration][toleration] sont utilisés pour planifier le conteneur au niveau du nœud.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-linux
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

Utilisez la commande [kubectl create][kubectl-create] pour exécuter l’application.

```azurecli-interactive
kubectl create -f virtual-kubelet-linux.yaml
```

Utilisez la commande [kubectl get pods][kubectl-get] avec l’argument `-o wide` pour sortir une liste des pods avec le nœud planifié. Remarquez que le pod `aci-helloworld` a été planifié sur le nœud `virtual-kubelet-virtual-kubelet-linux`.

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Exécuter un conteneur Windows

Créez un fichier nommé `virtual-kubelet-windows.yaml` et copiez-y le YAML suivant. Remplacez la valeur `kubernetes.io/hostname` avec le nom du nœud Virtual Kubelet pour Linux. Veuillez noter que [nodeSelector][node-selector] et [toleration][toleration] sont utilisés pour planifier le conteneur au niveau du nœud.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: nanoserver/iis
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-win
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

Utilisez la commande [kubectl create][kubectl-create] pour exécuter l’application.

```azurecli-interactive
kubectl create -f virtual-kubelet-windows.yaml
```

Utilisez la commande [kubectl get pods][kubectl-get] avec l’argument `-o wide` pour sortir une liste des pods avec le nœud planifié. Remarquez que le pod `nanoserver-iis` a été planifié sur le nœud `virtual-kubelet-virtual-kubelet-win`.

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Supprimer Virtual Kubelet

Utilisez la commande [az aks remove-connector][aks-remove-connector] pour désinstaller Virtual Kubelet. Remplacez les valeurs de l’argument par le nom du connecteur, le cluster AKS, et le groupe de ressources du cluster AKS.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Virtual Kubelet, consultez la page du [projet Virtual Kubelet Github][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az_aks_list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
