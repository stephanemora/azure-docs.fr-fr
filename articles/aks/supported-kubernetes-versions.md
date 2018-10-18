---
title: Versions de Kubernetes prises en charge dans Azure Kubernetes Service
description: Comprendre la stratégie de prise en charge des versions de Kubernetes et le cycle de vie des clusters dans Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 09/21/2018
ms.author: saudas
ms.openlocfilehash: 6b55825107ae8872b146b3ad4fde0ef4b917b71d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47046233"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versions de Kubernetes prises en charge dans Azure Kubernetes Service (AKS)

La communauté Kubernetes publie des versions mineures à peu près tous les trois mois. Ces versions contiennent de nouvelles fonctionnalités et des améliorations. Les publications de correctifs sont plus fréquentes (parfois hebdomadaires) et sont destinées seulement aux correctifs de bogues critiques dans une version mineure. Ces publications de correctifs incluent des correctifs pour des vulnérabilités de sécurité ou des bogues majeurs, ayant un impact sur un grand nombre de clients et de produits exécutés en production et basés sur Kubernetes.

Une nouvelle version mineure de Kubernetes est rendue disponible dans [acs-engine][acs-engine] au jour Un. L’objectif de niveau de service d’AKS cible la publication de la version mineure pour les clusters AKS dans les 30 jours, soumis à la stabilité de la version.

## <a name="kubernetes-version-support-policy"></a>Stratégie de prise en charge des versions de Kubernetes

AKS prend en charge quatre versions mineures de Kubernetes :

- La version mineure actuelle qui est publiée en amont (n)
- Trois versions mineures précédentes. Chaque version mineure prise en charge prend également en charge deux correctifs stables.

Par exemple, si AKS introduit *1.11.x* aujourd’hui, la prise en charge est également fournie pour *1.10.a* + *1.10.b*, *1.9.c*  +  *1.9d*, *1.8.e* + *1.8f* (où les versions des correctifs sous forme de lettre représentent les deux builds stables les plus récentes).

Quand une nouvelle version mineure est introduite, la version mineure et les publications des correctifs les plus anciennes prises en charge sont mises hors service. 15 jours avant la publication de la nouvelle version mineure et la mise hors service à venir de la version, une annonce est faite via les canaux de mise à jour d’Azure. Dans l’exemple ci-dessus où *1.11.x* est publiée, les versions mises hors service sont *1.7.g* + *1.7.h*.

Quand vous déployez un cluster AKS dans le portail ou avec Azure CLI, le cluster est toujours défini sur la version mineure n-1 et le dernier correctif. Par exemple, si AKS prend en charge *1.11.x*, *1.10.a* + *1.10.b*, *1.9.c* + *1.9d* , *1.8.e* + *1.8f*, la version par défaut pour les nouveaux clusters est *1.10.b*.

## <a name="faq"></a>Forum Aux Questions

**Que se passe-t-il quand un client met à niveau un cluster Kubernetes avec une version mineure qui n’est pas prise en charge ?**

Si vous êtes sur la version *n-4*, vous êtes en dehors de l’objectif de niveau de service (SLO). Si votre mise à niveau de la version n-4 vers n-3 réussit, vous êtes à nouveau dans le SLO. Par exemple : 

- Si les versions d’AKS prises en charge sont *1.10.a* + *1.10.b*, *1.9.c* + *1.9d*, *1.8.e* + *1.8f*, et que vous utilisez *1.7.g* ou *1.7.h*, vous êtes en dehors du SLO.
- Si la mise à niveau depuis *1.7.g* ou *1.7.h* vers *1.8.e* ou *1.8.f* réussit, vous êtes à nouveau dans le SLO.

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
[acs-engine]: https://github.com/Azure/acs-engine

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md