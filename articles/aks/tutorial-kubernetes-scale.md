---
title: Didacticiel Kubernetes sur Azure – Mise à l’échelle d’une application
description: Dans ce didacticiel Azure Kubernetes Service (AKS), vous découvrez comment mettre à l’échelle des nœuds et pods dans Kubernetes, et comment implémenter la mise à l’échelle automatique de pod horizontal.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 5ffe7b4c7830500e5eeeeb61c57730d9a0d9df47
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2018
ms.locfileid: "41919898"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>Didacticiel : mettre à l’échelle des applications dans Azure Kubernetes Service (AKS)

Si vous avez suivi les didacticiels, vous disposez d’un cluster Kubernetes opérationnel dans ACS, et avez déployé l’application Azure Voting. Dans ce didacticiel (issu d’une série de sept didacticiels), vous allez augmenter le nombre de pods dans l’application et essayer la mise à l’échelle automatique des pods. Vous allez également apprendre à mettre à l’échelle le nombre de nœuds de machine virtuelle Azure afin de modifier la capacité du cluster pour l’hébergement des charges de travail. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Mettre à l’échelle les nœuds Kubernetes
> * Mettre à l’échelle manuellement des pods Kubernetes qui exécutent votre application
> * Configurer la mise à l’échelle automatique des pods qui exécutent le serveur frontal d’applications

Dans les didacticiels suivants, une nouvelle version de l’application Azure Vote est installée.

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans une image conteneur, l’image a été chargée dans Azure Container Registry et un cluster Kubernetes a été créé. L’application a ensuite été exécutée sur le cluster Kubernetes. Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Tutoriel 1 – Créer des images conteneur][aks-tutorial-prepare-app].

Ce didacticiel nécessite que vous exécutiez Azure CLI version 2.0.38 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli-install].

## <a name="manually-scale-pods"></a>Mettre à l’échelle des pods manuellement

Lorsque le serveur frontal Azure Vote et l’instance Redis ont été déployés dans des didacticiels précédents, un seul réplica a été créé. Pour afficher le nombre et l’état de pods dans votre cluster, utilisez la commande [kubectl get][kubectl-get] comme suit :

```console
kubectl get pods
```

L’exemple de sortie suivant montre un pod de serveur frontal et un pod de serveur principal :

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Pour modifier manuellement le nombre de pods dans le déploiement *azure-vote-front*, utilisez la commande [kubectl scale][kubectl-scale]. L’exemple suivant augmente le nombre de pods de serveur frontal à *5* :

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Exécutez à nouveau la commande [kubectl get pods][kubectl-get] pour vérifier que Kubernetes crée les pods supplémentaires. Au bout d’une minute environ, les pods supplémentaires sont disponibles dans votre cluster :

```console
$ kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Mettre à l’échelle les pods automatiquement

Kubernetes prend en charge la [mise à l’échelle automatique des pods horizontaux][kubernetes-hpa] pour ajuster le nombre de pods dans un déploiement en fonction de l’utilisation du processeur ou d’autres métriques. Le [serveur de mesures][metrics-server] est utilisé pour fournir des données sur l’utilisation des ressources à Kubernetes. Pour installer le serveur de mesures, clonez le référentiel GitHub `metrics-server` et installez les définitions des exemples de ressources. Pour afficher le contenu de ces définitions YAML, consultez [Metrics Server for Kubernetes 1.8+][metrics-server-github] (Serveur de mesures pour Kubernetes 1.8+).

```console
git clone https://github.com/kubernetes-incubator/metrics-server.git
kubectl create -f metrics-server/deploy/1.8+/
```

Pour utiliser la mise à l’échelle automatique, vos pods doivent avoir des demandes et limites de processeur définies. Dans le déploiement `azure-vote-front`, le conteneur frontal demande 0,25 processeur, avec une limite de 0,5 processeur. Les paramètres s’apparentent aux suivants :

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

L’exemple suivant utilise la commande [kubectl autoscale][kubectl-autoscale] pour mettre automatiquement à l’échelle le nombre de pods dans le déploiement *azure-vote-front*. Si l’utilisation du processeur dépasse 50 %, la mise à l’échelle automatique augment le nombre de pods jusqu’à un maximum de 10 instances :

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Pour voir l’état de la mise à l’échelle automatique, utilisez la commande `kubectl get hpa` comme suit :

```
$ kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Au bout de quelques minutes, avec une charge minimale sur l’application Azure Vote, le nombre de réplicas de pods descend automatiquement à trois. Vous pouvez utiliser à nouveau `kubectl get pods` pour voir les pods inutiles en cours de suppression.

## <a name="manually-scale-aks-nodes"></a>Mettre manuellement à l’échelle les nœuds AKS

Si vous avez créé votre cluster Kubernetes à l’aide des commandes dans le didacticiel précédent, le cluster comporte un nœud. Vous pouvez ajuster le nombre de nœuds manuellement si vous prévoyez davantage ou moins de charges de travail de conteneur sur votre cluster.

L’exemple suivant permet d’augmenter le nombre de nœuds à trois dans le cluster Kubernetes nommé *myAKSCluster*. Quelques minutes sont nécessaires pour exécuter la commande.

```azurecli
az aks scale --resource-group=myResourceGroup --name=myAKSCluster --node-count 3
```

Le résultat ressemble à ce qui suit :

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé différentes fonctionnalités de mise à l’échelle dans votre cluster Kubernetes. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Mettre à l’échelle les nœuds Kubernetes
> * Mettre à l’échelle manuellement des pods Kubernetes qui exécutent votre application
> * Configurer la mise à l’échelle automatique des pods qui exécutent le serveur frontal d’applications

Passez au didacticiel suivant pour savoir comment mettre à jour une application dans Kubernetes.

> [!div class="nextstepaction"]
> [Mettre à jour une application dans Kubernetes][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-incubator/metrics-server/tree/master/deploy/1.8%2B
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/core-metrics-pipeline/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[azure-cli-install]: /cli/azure/install-azure-cli
