---
title: Versions de Kubernetes prises en charge dans Azure Kubernetes Service
description: Comprendre la stratégie de prise en charge des versions de Kubernetes et le cycle de vie des clusters dans Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: b6dd91dda559f778eaa8f5a17b46a22020dd8373
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484055"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versions de Kubernetes prises en charge dans Azure Kubernetes Service (AKS)

La communauté Kubernetes publie des versions mineures à peu près tous les trois mois. Ces versions contiennent de nouvelles fonctionnalités et des améliorations. Les publications de correctifs sont plus fréquentes (parfois hebdomadaires) et sont destinées seulement aux correctifs de bogues critiques dans une version mineure. Ces publications de correctifs incluent des correctifs pour des vulnérabilités de sécurité ou des bogues majeurs, ayant un impact sur un grand nombre de clients et de produits exécutés en production et basés sur Kubernetes.

AKS a pour objectif de certifier et de publier de nouvelles versions de Kubernetes dans les 30 jours suivant une version amont, sous réserve de la stabilité de la version.

## <a name="kubernetes-versions"></a>Version de Kubernetes

Kubernetes utilise le schéma de contrôle de version standard [Semantic Versioning](https://semver.org/). Cela signifie que chaque version de Kubernetes suit ce schéma de numérotation :

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Chaque chiffre de la version indique la compatibilité générale avec la version précédente :

* Les versions principales changent lorsque des modifications incompatibles de l’API ou la rétrocompatibilité peuvent être rompues.
* Les versions mineures changent lorsque des modifications de fonctionnalités rétrocompatibles avec les autres versions mineures sont apportées.
* Les versions des correctifs changent lorsque des corrections de bogues rétrocompatibles sont apportées.

En général, les utilisateurs doivent s’efforcer d’exécuter la dernière version de correctif de la version mineure qu’ils utilisent, par exemple si votre cluster de production est sur *1.12.14* et *1.12.15* est la dernière version de correctif disponible pour la série *1.12*, vous devez passer à *1.12.15* dès que vous êtes en mesure d’assurer la compatibilité et le bon état de votre cluster.

## <a name="kubernetes-version-support-policy"></a>Stratégie de prise en charge des versions de Kubernetes

> [!NOTE]
> À partir du 9 décembre 2019, AKS passe aux (N) - 2 versions les plus récentes de Kubernetes prises en charge. Ce changement est conforme à la fenêtre de prise en charge en amont des versions de Kubernetes et permet de s’assurer que les versions les plus récentes et les plus sécurisées sont utilisées. Pour en savoir plus, lisez l’[annonce ici](https://azure.microsoft.com/updates/azure-kubernetes-service-will-be-retiring-support-for-kubernetes-versions-1-11-and-1-12/).

AKS prend en charge quatre versions mineures de Kubernetes :

* La version mineure actuelle qui est publiée dans AKS (N)
* Trois versions mineures précédentes. Chaque version mineure prise en charge prend également en charge deux correctifs stables.

C’est ce qu’on appelle « N-3 » - (N (dernière publication) - 3 (versions mineures)).

Par exemple, si AKS introduit *1.13.a* aujourd’hui, la prise en charge est assurée pour les versions suivantes :

Nouvelle version mineure    |    Liste des versions prises en charge
-----------------    |    ----------------------
1.13.a               |    1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b

« .a » et « .b » sont des versions de correctif représentatives : « .a » de la version 1.13.a peut être différent de 1.12.a. Par exemple, 1.13.9 et 1.12.8.

Pour plus de détails sur les communications concernant les changements de version et les attentes, voir « Communications » ci-dessous.

Quand une nouvelle version mineure est introduite, la version mineure et les publications des correctifs les plus anciennes prises en charge sont déconseillées et mises hors service. Par exemple, si la liste des versions actuellement supportées est :

```
1.12.a
1.12.b
1.11.a
1.11.b
1.10.a
1.10.b
1.9.a
1.9.b
```

Si AKS publie la version 1.13. *, cela signifie que les versions 1.9.* (toutes les versions 1.9) seront mises hors service et ne seront plus prises en charge.

> [!NOTE]
> Veuillez noter que si les clients utilisent une version de Kubernetes non supportée, il leur sera demandé de mettre à jour lors de leur demande de support pour le cluster. Les clusters exécutant des versions de Kubernetes non supportées ne sont pas couverts par les politiques de support [AKS](https://docs.microsoft.com/azure/aks/support-policies).


En plus de ce qui précède sur les versions mineures, AKS supporte les deux dernières versions *patch** d’une version mineure donnée. Par exemple, étant donné les versions supportées suivantes :

```
Current Supported Version List
------------------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5
```

Si Kubernetes a publié en amont les versions 1.12.3 et 1.11.6 et qu’AKS publie ces versions de patch, les versions de patch les plus anciennes sont dépréciées et supprimées, et la liste des versions supportées devient :

```
New Supported Version List
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*
```

> [!NOTE]
> Les clients ne doivent pas associer la création de cluster, de CI ou d’autres tâches automatisées à des versions de correctifs spécifiques. 

### <a name="communications"></a>Communications

* Pour les nouvelles versions **mineures** de Kubernetes
  * Tous les utilisateurs sont informés publiquement de la nouvelle version et de la version qui sera supprimée.
  * Lorsqu’une nouvelle version de patch est publiée, la version la plus ancienne est supprimée en même temps.
  * Les clients ont **60 jours** à compter de la date de notification publique pour mettre à niveau vers une version mineure prise en charge.
* Pour les nouvelles versions de **correctif** de Kubernetes
  * Tous les utilisateurs sont informés de la sortie de la nouvelle version du correctif et de la mise à niveau vers la dernière version du correctif.
  * Les utilisateurs ont **30 jours** pour mettre à niveau vers une nouvelle version de patch supportée. Les utilisateurs ont **30 jours** pour mettre à niveau vers une version de correctif prise en charge avant que le plus ancien ne soit supprimé.

L’AKS définit « publié » comme une mise à disponibilité générale, activée dans toutes les mesures SLO/Qualité de service et disponible dans toutes les régions.

> [!NOTE]
> Les clients sont avertis des versions de Kubernetes et des suppressions de version, lorsqu’une version mineure est dépréciée/supprimée, les utilisateurs disposent de 60 jours pour mettre à niveau vers une version supportée. Dans le cas des versions de correctifs, les clients disposent de 30 jours pour effectuer la mise à niveau vers une version prise en charge.

#### <a name="notification-channels-for-aks-changes"></a>Canaux de notification pour les changements AKS

AKS publie une mise à jour de service hebdomadaire qui récapitule les nouvelles versions Kubernetes, les changements de service et les mises à jour de composants qui ont été publiés sur le service sur [GitHub](https://github.com/Azure/AKS/releases).

Ces changements sont mises à la disposition de tous les clients dans le cadre d’une maintenance régulière qui est proposée dans le service managé. Certains nécessitent des mises à niveau explicites, tandis que d’autres ne nécessitent aucune action.

Les notifications sont aussi envoyées via :

* Les [notes de publication d’AKS](https://aka.ms/aks/releasenotes)
* Notifications du portail Azure
* Le [canal de mise à jour Azure][azure-update-channel]

### <a name="policy-exceptions"></a>Exceptions de stratégie

AKS se réserve le droit d’ajouter ou de supprimer de nouvelles versions ou des versions existantes qui ont été identifiées comme ayant une ou plusieurs productions critiques ayant un impact sur des bogues ou des problèmes de sécurité sans préavis.

Certaines versions de correctifs spécifiques peuvent être ignorées, ou le déploiement peut être accéléré en fonction de la gravité du bogue ou du problème de sécurité.

### <a name="azure-portal-and-cli-default-versions"></a>Versions par défaut du Portail Microsoft Azure et de l’interface de ligne de commande

Quand vous déployez un cluster AKS dans le portail ou avec Azure CLI, le cluster est toujours défini sur la version mineure n-1 et le dernier correctif. Par exemple, si AKS prend en charge *1.13.a*, *1.12.a* + *1.12.b*, *1.11.a* + *1.11.b*, *1.10.a* + *1.10b*, la version par défaut pour les nouveaux clusters est *1.12.b*.

AKS utilise par défaut N-1 (minor.latestPatch, par exemple 1.12.b) pour fournir aux clients une version connue, stable et patchée par défaut.

## <a name="list-currently-supported-versions"></a>Répertorier les versions prises en charge

Pour savoir quelles sont les versions disponibles pour vos abonnement et région, utilisez la commande [az aks get-versions][az-aks-get-versions]. L’exemple suivant répertorie les versions Kubernetes disponibles pour la région *EastUS* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

La sortie est similaire à l'exemple suivant, qui montre que la version *1.14.6* de Kubernetes est la version la plus récente disponible :

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.14.6               None available
1.14.5               1.14.6
1.13.10              1.14.5, 1.14.6
1.13.9               1.13.10, 1.14.5, 1.14.6
1.12.8               1.13.9, 1.13.10
1.12.7               1.12.8, 1.13.9, 1.13.10
1.11.10              1.12.7, 1.12.8
1.11.9               1.11.10, 1.12.7, 1.12.8
1.10.13              1.11.9, 1.11.10
1.10.12              1.10.13, 1.11.9, 1.11.10
```

## <a name="faq"></a>Forum Aux Questions

**Que se passe-t-il quand un client met à niveau un cluster Kubernetes avec une version mineure qui n’est pas prise en charge ?**

Si vous êtes sur la version *n-4*, vous êtes en dehors du support et serez invité à mettre à jour. Si votre mise à niveau de la version n-4 à la version n-3 réussit, vous bénéficiez maintenant de notre politique de support. Par exemple :

- Si les versions AKS prises en charge sont *1.13.a*, *1.12.b* + *1.12.c*, *1.11.d* + *1.11.e* et *1.10.f* + *1.10.g*, et que vous utilisez *1.9.h* ou *1.9.i*, vous ne bénéficiez plus de la prise en charge.
- Si la mise à niveau depuis *1.9.h* ou *1.9.i* vers *1.10.f* ou *1.10.g* réussit, vous bénéficiez de nouveau de notre politique de support.

Les mises à niveau vers des versions antérieures à *n-4* ne sont pas prises en charge. Dans ce cas, nous recommandons aux clients de créer de nouveaux clusters AKS et de redéployer leurs charges de travail.

**Que signifie « ne plus disposer du support technique »**

« Ne plus disposer du support technique » signifie que la version que vous utilisez ne figure pas dans la liste des versions prises en charge et qu’il vous sera demandé de mettre à niveau le cluster vers une version prise en charge lors de la demande de support. De plus, AKS n’offre aucune garantie d’exécution ou autre pour les clusters en dehors de la liste des versions supportées.

**Que se passe-t-il quand un client met à l’échelle un cluster Kubernetes avec une version mineure qui n’est pas prise en charge ?**

Pour les versions mineures non prises en charge par AKS, le scale-in ou le scale-out continue à fonctionner sans problème.

**Un client peut-il rester toujours sur une même version de Kubernetes ?**

Oui. Cependant, si le cluster n’est pas sur une des versions prises en charge par AKS, le cluster est en dehors des politiques de support d’AKS. Azure ne met pas à niveau ni ne supprime automatiquement votre cluster.

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
