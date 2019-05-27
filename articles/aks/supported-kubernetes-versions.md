---
title: Versions de Kubernetes prises en charge dans Azure Kubernetes Service
description: Comprendre la stratégie de prise en charge des versions de Kubernetes et le cycle de vie des clusters dans Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: a7b3176e39ccaa0f9ddb1ef45c33ec6902e62f1c
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956328"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versions de Kubernetes prises en charge dans Azure Kubernetes Service (AKS)

La communauté Kubernetes publie des versions mineures à peu près tous les trois mois. Ces versions contiennent de nouvelles fonctionnalités et des améliorations. Les publications de correctifs sont plus fréquentes (parfois hebdomadaires) et sont destinées seulement aux correctifs de bogues critiques dans une version mineure. Ces publications de correctifs incluent des correctifs pour des vulnérabilités de sécurité ou des bogues majeurs, ayant un impact sur un grand nombre de clients et de produits exécutés en production et basés sur Kubernetes.

Une nouvelle version mineure de Kubernetes est rendue disponible dans [aks-engine][aks-engine] au jour Un. L’objectif de niveau de service d’AKS cible la publication de la version mineure pour les clusters AKS dans les 30 jours, soumis à la stabilité de la version.

## <a name="kubernetes-version-support-policy"></a>Stratégie de prise en charge des versions de Kubernetes

AKS prend en charge quatre versions mineures de Kubernetes :

- La version mineure actuelle qui est publiée en amont (n)
- Trois versions mineures précédentes. Chaque version mineure prise en charge prend également en charge deux correctifs stables.

Par exemple, si AKS introduit *1.13.x* aujourd'hui, prise en charge est également fournie pour *1.12.a* + *1.12.b*, *1.11.c*  +  *d 1.11*, *1.10.e* + *1.10F* (où les versions de correctifs lettres sont deux builds stables les plus récentes).

Quand une nouvelle version mineure est introduite, la version mineure et les publications des correctifs les plus anciennes prises en charge sont mises hors service. 30 jours avant la publication de la nouvelle version mineure et la suppression de la version à venir, une annonce est effectuée via le [canaux de mise à jour Azure][azure-update-channel]. Dans l’exemple ci-dessus où *1.13.x* est publié, les versions mis hors service sont *1.9.g* + *1.9.h*.

Quand vous déployez un cluster AKS dans le portail ou avec Azure CLI, le cluster est toujours défini sur la version mineure n-1 et le dernier correctif. Par exemple, si AKS prend en charge *1.13.x*, *1.12.a* + *1.12.b*, *1.11.c*  +   *1.11d*, *1.10.e* + *1.10F*, est la version par défaut pour les nouveaux clusters *1.11.b*.

## <a name="list-currently-supported-versions"></a>Répertorier les versions prises en charge

Pour savoir quelles sont les versions disponibles pour vos abonnement et région, utilisez la commande [az aks get-versions][az-aks-get-versions]. L’exemple suivant répertorie les versions Kubernetes disponibles pour la région *EastUS* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

La sortie est similaire à l’exemple suivant, qui montre que la version Kubernetes *question 1.13.5* est la version la plus récente disponible :

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.13.5               None available
1.12.7               1.13.5
1.12.6               1.12.7, 1.13.5
1.11.9               1.12.6, 1.12.7
1.11.8               1.11.9, 1.12.6, 1.12.7
1.10.13              1.11.8, 1.11.9
1.10.12              1.10.13, 1.11.8, 1.11.9
```

## <a name="faq"></a>Forum Aux Questions

**Que se passe-t-il quand un client met à niveau un cluster Kubernetes avec une version mineure qui n’est pas prise en charge ?**

Si vous êtes sur la version *n-4*, vous êtes en dehors de l’objectif de niveau de service (SLO). Si votre mise à niveau de la version n-4 vers n-3 réussit, vous êtes à nouveau dans le SLO. Exemple :

- Si les versions prises en charge AKS *1.13.x*, *1.12.a* + *1.12.b*, *1.11.c*  +  *d 1.11*, et *1.10.e* + *1.10F* et que vous utilisez *1.9.g* ou *1.9.h*, vous êtes hors du SLO.
- Si la mise à niveau à partir de *1.9.g* ou *1.9.h* à *1.10.e* ou *1.10.f* réussit, vous êtes de retour dans l’objectifs du contrat.

Les mises à niveau vers des versions antérieures à *n-4* ne sont pas prises en charge. Dans ce cas, nous recommandons aux clients de créer de nouveaux clusters AKS et de redéployer leurs charges de travail.

**Que se passe-t-il quand un client met à l’échelle un cluster Kubernetes avec une version mineure qui n’est pas prise en charge ?**

Pour les versions mineures non prises en charge par AKS, le scale-in ou le scale-out continue à fonctionner sans problème.

**Un client peut-il rester toujours sur une même version de Kubernetes ?**

Oui. Cependant, si le cluster n’est pas sur une des versions prises en charge par AKS, le cluster est en dehors du SLO AKS. Azure ne met pas à niveau ni ne supprime automatiquement votre cluster.

**Quelle version le maître prend-t-il en charge si l’agent du cluster n’est pas dans une des versions d’AKS prises en charge ?**

Le maître est automatiquement mis à jour avec la version prise en charge la plus récente.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la mise à niveau de votre cluster, consultez [Mettre à niveau un cluster Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
