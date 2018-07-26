---
title: Créer un équilibreur de charge interne Azure Kubernetes Service (AKS)
description: Découvrez comment créer et utiliser un équilibreur de charge interne pour exposer vos services avec Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/12/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 123fc08995416e0ff9c7e12a526deadc34b3a4a2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001393"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Utiliser un équilibreur de charge interne avec Azure Kubernetes Service (AKS)

L’équilibrage de charge interne rend un service Kubernetes accessible aux applications qui s’exécutent dans le même réseau virtuel que le cluster Kubernetes. Cet article explique comment créer et utiliser un équilibreur de charge interne avec Azure Kubernetes Service (AKS). Azure Load Balancer se décline en deux références SKU : De base et Standard. AKS utilise la référence SKU De base.

## <a name="create-an-internal-load-balancer"></a>Créer un équilibreur de charge interne

Pour créer un équilibreur de charge interne, créez un manifeste de service avec le type de service *LoadBalancer* et l’annotation *azure-load-balancer-internal*, comme indiqué dans l’exemple suivant :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Une fois déployé avec `kubetctl apply`, un équilibreur de charge Azure est créé et mis à disposition sur le même réseau virtuel que le cluster AKS.

![Image d’un équilibreur de charge interne AKS](media/internal-lb/internal-lb.png)

Lorsque vous affichez les détails du service, l’adresse IP de la colonne *EXTERNAL-IP* est celle de l’équilibreur de charge interne, comme indiqué dans l’exemple suivant :

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>Spécifier une adresse IP

Si vous souhaitez utiliser une adresse IP spécifique avec l’équilibreur de charge interne, ajoutez la propriété *loadBalancerIP* à la spécification de l’équilibreur de charge. L’adresse IP spécifiée doit se trouver dans le même sous-réseau que le cluster AKS et ne doit pas déjà être assignée à une ressource.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Lorsque vous affichez les détails du service, l’adresse IP de la colonne *EXTERNAL-IP* reflète l’adresse IP spécifiée :

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Utiliser des réseaux privés

Lorsque vous créez votre cluster AKS, vous pouvez configurer des paramètres de réseau avancés. Cette méthode vous permet de déployer le cluster sur un réseau virtuel Azure existant et ses sous-réseaux. L’un des scénarios possibles consiste à déployer votre cluster AKS sur un réseau privé connecté à votre environnement local, et à exécuter des services accessibles uniquement en interne. Pour plus d’informations, consultez [Configuration avancée du réseau dans AKS][advanced-networking].

Vous pouvez utiliser les étapes précédentes pour déployer un équilibreur de charge interne dans un cluster AKS qui utilise un réseau privé. L’équilibreur de charge est créé dans le même groupe de ressources que votre cluster AKS, mais il est connecté à votre réseau privé virtuel et à son sous-réseau, comme indiqué dans l’exemple suivant :

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Vous devrez peut-être accorder au principal de service de votre cluster AKS le rôle de *Contributeur de réseaux* pour le groupe de ressources où sont déployées vos ressources de réseau virtuel Azure. Affichez le principal du service avec [az aks show][az-aks-show], par exemple : `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Pour créer une attribution de rôle, utilisez la commande [az role assignment create][az-role-assignment-create].

## <a name="specify-a-different-subnet"></a>Spécifier un autre sous-réseau

Pour spécifier un sous-réseau pour votre équilibreur de charge, ajoutez l’annotation *azure-load-balancer-internal-subnet* à votre service. Le sous-réseau spécifié doit se trouver dans le même réseau virtuel que votre cluster AKS. Une fois déployé, l’adresse *EXTERNAL-IP* de l’équilibreur de charge fait partie du sous-réseau spécifié.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="delete-the-load-balancer"></a>Supprimer l’équilibreur de charge

Lorsque tous les services qui utilisent l’équilibreur de charge interne ont été supprimés, l’équilibreur de charge lui-même est supprimé.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les services Kubernetes dans la [documentation des services Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[advanced-networking]: networking-overview.md
[deploy-advanced-networking]: networking-overview.md#configure-networking---cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create