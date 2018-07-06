---
title: Trafic de sortie sur liste verte d’un cluster Azure Kubernetes Service (AKS)
description: Trafic de sortie sur liste verte d’un cluster Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: 2394b61fb84a2f22af036f35819b87074a1dbd2d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100143"
---
# <a name="azure-kubernetes-service-aks-egress"></a>Sortie d’Azure Kubernetes Service (AKS)

Par défaut, l’adresse de la sortie d’un cluster Azure Kubernetes Service (AKS) est assignée aléatoirement. Cette configuration n’est pas idéale lorsque vous avez besoin d’identifier une adresse IP pour accéder à des services externes. Ce document décrit en détail comment créer et tenir à jour une adresse IP de sortie assignée de manière statique dans un cluster AKS.

## <a name="egress-overview"></a>Vue d’ensemble de la sortie

Le trafic sortant à partir d’un cluster AKS suit les conventions d’équilibrage de charge Azure, qui sont expliquées [ici][outbound-connections]. Avant la création du premier service Kubernetes de type `LoadBalancer`, les nœuds d’agent ne font partie d’aucun pool d’équilibrage de charge Azure. Dans cette configuration, les nœuds n’ont pas d’adresse IP publique au niveau de l’instance. Azure translate le flux sortant vers une adresse IP publique source qui n’est pas configurable ou déterministe.

Après la création d’un service Kubernetes de type `LoadBalancer`, les nœuds d’agent sont ajoutés à un pool d’équilibrage de charge Azure. Azure translate le flux sortant vers la première adresse IP publique configurée sur l’équilibreur de charge.

## <a name="create-a-static-public-ip"></a>Créer une adresse IP publique statique

Pour empêcher l’utilisation d’adresses IP aléatoires, créez une adresse IP statique et assurez-vous que l’équilibreur de charge utilise cette adresse. L’adresse IP doit être créée dans le groupe de ressources du **nœud** AKS.

Obtenez le nom du groupe de ressources avec la commande [az resource show][az-resource-show]. Mettez à jour le nom du groupe de ressources et le nom du cluster pour qu’ils correspondent à votre environnement.

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Utilisez ensuite la commande [az network public-ip create][public-ip-create] pour créer une adresse IP publique statique. Mettez à jour le nom du groupe de ressources pour le faire correspondre au nom récupéré à la dernière étape.

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>Créer un service avec l’adresse IP statique

Maintenant que vous avez une adresse IP, créez un service Kubernetes avec le type `LoadBalancer` et assignez l’adresse IP au service.

Créez un fichier nommé `egress-service.yaml` et copiez-y le YAML suivant. Mettez à jour l’adresse IP en fonction de votre environnement.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-egress
spec:
  loadBalancerIP: 23.101.128.81
  type: LoadBalancer
  ports:
  - port: 8080
```

Créez le service et le déploiement avec la commande `kubectl apply`.

```console
$ kubectl apply -f egress-service.yaml

service "aks-egress" created
```

La création de ce service configure une nouvelle adresse IP de serveur frontal l’équilibreur de charge Azure. Si vous n’avez pas d’autres adresses IP configurées, **tout** le trafic sortant doit désormais utiliser cette adresse. Lorsque plusieurs adresses sont configurées sur l’équilibreur de charge Azure, la sortie utilise la première adresse IP de cet équilibreur de charge.

## <a name="verify-egress-address"></a>Vérifier l’adresse de sortie

Pour vérifier que l’adresse IP publique est utilisée, employez un service tel que `checkip.dyndns.org`.

Démarrer et attacher à un pod :

```console
$ kubectl run -it --rm aks-ip --image=debian
```

Si nécessaire, installez curl dans le conteneur :

```console
$ apt-get update && apt-get install curl -y
```

Curl `checkip.dyndns.org`, qui renvoie l’adresse IP de sortie :

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

L’adresse IP doit correspond à l’adresse IP statique attachée à l’équilibreur de charge Azure.

## <a name="ingress-controller"></a>Contrôleur d’entrée

Pour éviter la gestion de plusieurs adresses IP publiques sur l’équilibreur de charge Azure, envisagez d’utiliser un contrôleur d’entrée. Les contrôleurs d’entrée fournissent des avantages tels que l’équilibrage de charge, l’arrêt de SSL/TLS, la prise en charge des réécritures d’URI et le chiffrement SSL/TLS en amont. Pour plus d’informations sur les contrôleurs d’entrée dans AKS, consultez le guide de [configuration d’un contrôleur d’entrée NGINX dans un cluster AKS][ingress-aks-cluster].

## <a name="next-steps"></a>Étapes suivantes

Découvrez le logiciel mentionné dans ce document.

- [Interface CLI Helm][helm-cli-install]
- [Contrôleur d’entrée NGINX][nginx-ingress]
- [Connexions sortantes de l’équilibreur de charge Azure][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
