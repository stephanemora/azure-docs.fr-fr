---
title: Versions de Kubernetes prises en charge dans Azure Kubernetes Service
description: Comprendre la stratégie de prise en charge des versions de Kubernetes et le cycle de vie des clusters dans Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 77d64391888957a1697a5823a1485413686682d1
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593442"
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

Les utilisateurs doivent s’efforcer d’exécuter la dernière version du correctif de la version mineure qu’ils utilisent, par exemple si votre cluster de production est sur *1.12.14* et que *1.12.15* est la dernière version du correctif disponible pour la série *1.12*, vous devez mettre à niveau vers *1.12.15* dès que possible pour garantir la compatibilité et le bon état de votre cluster.

## <a name="kubernetes-version-support-policy"></a>Stratégie de prise en charge des versions de Kubernetes

AKS prend en charge trois versions mineures de Kubernetes :

* La version mineure actuelle qui est publiée dans AKS (N)
* Deux versions mineures précédentes. Chaque version mineure prise en charge prend également en charge deux correctifs stables.

C’est ce que l’on appelle « N-2 » : (N (dernière publication) - 2 (versions mineures)).

Par exemple, si AKS introduit *1.15.a* aujourd’hui, la prise en charge est assurée pour les versions suivantes :

Nouvelle version mineure    |    Liste des versions prises en charge
-----------------    |    ----------------------
1.15.a               |    1.15.a, 1.15.b, 1.14.c, 1.14.d, 1.13.e, 1.13.f

où « .lettre » est représentatif des versions correctives.

Pour plus de détails sur les communications concernant les changements de version et les attentes, voir « Communications » ci-dessous.

Quand une nouvelle version mineure est introduite, la version mineure et les publications des correctifs les plus anciennes prises en charge sont déconseillées et mises hors service. Par exemple, si la liste des versions actuellement prises en charge est :

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

et que AKS publie la version 1.16. *, cela signifie que les versions 1.13.* (toutes les versions 1.13) seront supprimées et qu'elles ne seront plus prises en charge.

> [!NOTE]
> Veuillez noter que si les clients utilisent une version de Kubernetes non supportée, il leur sera demandé de mettre à jour lors de leur demande de support pour le cluster. Les clusters exécutant des versions de Kubernetes non supportées ne sont pas couverts par les politiques de support [AKS](https://docs.microsoft.com/azure/aks/support-policies).

En plus de ce qui précède sur les versions mineures, AKS prend en charge les deux dernières versions **correctives** d’une version mineure donnée. Par exemple, étant donné les versions supportées suivantes :

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Si Kubernetes a publié en amont les versions 1.15.3 et 1.14.6, et qu’AKS publie ces versions de patch, les versions correctives les plus anciennes sont dépréciées et supprimées, et la liste des versions prises en charge devient :

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Communications

* Pour les nouvelles versions **mineures** de Kubernetes
  * Tous les utilisateurs sont informés publiquement de la nouvelle version et de la version qui sera supprimée.
  * Lorsqu’une nouvelle version de patch est publiée, la version la plus ancienne est supprimée en même temps.
  * Les clients ont **30 jours** à partir de la date de notification publique pour effectuer une mise à niveau vers une version mineure prise en charge.
* Pour les nouvelles versions de **correctif** de Kubernetes
  * Tous les utilisateurs sont informés de la sortie de la nouvelle version du correctif et de la mise à niveau vers la dernière version du correctif.
  * Les utilisateurs ont **30 jours** pour procéder à une mise à niveau vers une version plus récente et prise en charge du correctif avant que le plus ancien ne soit supprimé.

AKS définit une « version publiée » comme étant les versions en disponibilité générale, activées dans toutes les mesures SLO/Qualité de service et disponible dans toutes les régions. AKS peut également prendre en charge les préversions qui sont explicitement étiquetées et soumises aux conditions générales des préversions.

#### <a name="notification-channels-for-aks-changes"></a>Canaux de notification pour les changements AKS

AKS publie des mises à jour de service régulières qui récapitulent les nouvelles versions de Kubernetes, les modifications du service et les mises à jour de composants qui ont été publiées sur le service sur [GitHub](https://github.com/Azure/AKS/releases).

Ces changements sont mises à la disposition de tous les clients dans le cadre d’une maintenance régulière qui est proposée dans le service managé. Certains nécessitent des mises à niveau explicites, tandis que d’autres ne nécessitent aucune action.

Les notifications sont aussi envoyées via :

* Les [notes de publication d’AKS](https://aka.ms/aks/releasenotes)
* Notifications du portail Azure
* Le [canal de mise à jour Azure][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Exceptions de la politique des versions prises en charge

AKS se réserve le droit d’ajouter ou de supprimer de nouvelles versions ou des versions existantes qui ont été identifiées comme ayant une ou plusieurs productions critiques ayant un impact sur des bogues ou des problèmes de sécurité sans préavis.

Certaines versions de correctifs spécifiques peuvent être ignorées, ou le déploiement peut être accéléré en fonction de la gravité du bogue ou du problème de sécurité.

### <a name="azure-portal-and-cli-default-versions"></a>Versions par défaut du Portail Microsoft Azure et de l’interface de ligne de commande

Quand vous déployez un cluster AKS dans le portail ou avec Azure CLI, le cluster est toujours défini par défaut sur la version mineure n-1 et le dernier correctif. Par exemple, si AKS prend en charge *1.15.a*, *1.15.b*, *1.14.c*, *1.14.d*,  *1.13.e* et *1.13.f*, la version par défaut sélectionnée est *1.14.c*.

AKS utilise par défaut N-1 pour fournir aux clients une version par défaut connue, stable et avec les correctifs nécessaires.

## <a name="list-currently-supported-versions"></a>Répertorier les versions prises en charge

Pour savoir quelles sont les versions disponibles pour vos abonnement et région, utilisez la commande [az aks get-versions][az-aks-get-versions]. L’exemple suivant répertorie les versions Kubernetes disponibles pour la région *EastUS* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>Questions fréquentes (FAQ)

**Que se passe-t-il quand un client met à niveau un cluster Kubernetes avec une version mineure qui n’est pas prise en charge ?**

Si vous êtes sur la version *n-3*, vous êtes hors support et vous serez invité à effectuer une mise à niveau. Si votre mise à niveau de la version n-3 à la version n-2 réussit, vous bénéficiez maintenant de nos politiques de support. Par exemple :

- Si la version AKS la plus ancienne prise en charge est *1.13.a* et que vous êtes sur *1.12.b* ou une version antérieure, vous êtes hors support.
- Si la mise à niveau de *1.12.b* vers *1.13.a* ou version ultérieure réussit, vous bénéficiez à nouveau de nos stratégies de support.

Les mises à niveau vers des versions antérieures à la fenêtre de support de *N-2* ne sont pas prises en charge. Dans ce cas, nous recommandons aux clients de créer de nouveaux clusters AKS et de redéployer leurs charges de travail avec des versions dans la fenêtre de support.

**Que signifie « Ne plus disposer du support technique » ?**

« Ne plus disposer du support technique » signifie que la version que vous utilisez ne figure pas dans la liste des versions prises en charge et qu’il vous sera demandé de mettre à niveau le cluster vers une version prise en charge lors de la demande de support. De plus, AKS n’offre aucune garantie d’exécution ou autre pour les clusters en dehors de la liste des versions supportées.

**Que se passe-t-il quand un client met à l’échelle un cluster Kubernetes avec une version mineure qui n’est pas prise en charge ?**

Pour les versions mineures non prises en charge par AKS, le scale-in ou le scale-out doit continuer à fonctionner, mais il est fortement recommandé d’effectuer la mise à niveau pour que votre cluster bénéficie à nouveau du support.

**Un client peut-il rester toujours sur une même version de Kubernetes ?**

Oui. Cependant, si le cluster n’est pas sur une des versions prises en charge par AKS, le cluster est en dehors des politiques de support d’AKS. Azure ne met pas à niveau ni ne supprime automatiquement votre cluster.

**Quelle version le plan de contrôle prend-il en charge si le pool de nœuds n’est pas dans une des versions d’AKS prises en charge ?**

Le plan de contrôle doit se trouver dans une fenêtre de versions pour tous les pools de nœuds. Pour plus d’informations sur la mise à niveau du plan de contrôle ou des pools de nœuds, consultez la documentation sur la [mise à niveau des pools de nœuds](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la mise à niveau de votre cluster, consultez [Mettre à niveau un cluster Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
