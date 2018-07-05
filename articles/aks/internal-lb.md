---
title: Créer un équilibreur de charge interne Azure Kubernetes Service (AKS)
description: Utiliser un équilibreur de charge interne avec Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 3/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 7606ce574c7ff94caef3ffa89320d682b22d8502
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097918"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Utiliser un équilibreur de charge interne avec Azure Kubernetes Service (AKS)

L’équilibrage de charge interne rend un service Kubernetes accessible aux applications qui s’exécutent dans le même réseau virtuel que le cluster Kubernetes. Ce document définit en détail la création d’un équilibreur de charge interne avec Azure Kubernetes Service (AKS). Azure Load Balancer se décline en deux références SKU : De base et Standard. AKS utilise la référence SKU De base.

## <a name="create-internal-load-balancer"></a>Créer un équilibrage de charge interne

Pour créer un équilibreur de charge interne, générez un manifeste de service avec le type de service `LoadBalancer` et l’annotation `azure-load-balancer-internal` comme indiqué dans l’exemple suivant.

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

Une fois déployé, un équilibrage de charge Azure est créé et mis à disposition sur le même réseau virtuel que le cluster AKS.

![Image d’un équilibrage de charge interne AKS](media/internal-lb/internal-lb.png)

Lors de la récupération des détails sur le service, l’adresse IP dans la colonne `EXTERNAL-IP` est l’adresse IP de l’équilibrage de charge interne.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>Spécifier une adresse IP

Si vous souhaitez utiliser une adresse IP spécifique avec l’équilibrage de charge interne, ajoutez la propriété `loadBalancerIP` à la spécification de l’équilibrage de charge. L’adresse IP spécifiée doit se trouver dans le même sous-réseau que le cluster AKS et ne doit pas déjà être assignée à une ressource.

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

Lors de la récupération des détails sur le service, l’adresse IP sur le `EXTERNAL-IP` doit refléter l’adresse IP spécifiée.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="delete-the-load-balancer"></a>Supprimer l’équilibreur de charge

Lorsque tous les services qui utilisent l’équilibreur de charge interne ont été supprimés, l’équilibreur de charge lui-même est également supprimé.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les services Kubernetes dans la [documentation des services Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
