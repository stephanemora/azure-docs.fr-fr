---
title: Vue d’ensemble d’Azure Arc
description: Découvrez ce qu’est le service Azure Arc et comment il permet aux clients d’activer la gestion et la gouvernance de leurs ressources hybrides avec d’autres services et fonctionnalités Azure.
ms.date: 03/02/2021
ms.topic: overview
ms.openlocfilehash: 33c9d6ca87c3d8d2d8920ff429902f5876bbdc59
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650190"
---
# <a name="azure-arc-overview"></a>Vue d’ensemble d’Azure Arc

Aujourd’hui, les entreprises éprouvent des difficultés à contrôler et à gérer des environnements de plus en plus complexes. Ces environnements s’étendent aux centres de données, à plusieurs clouds et à la périphérie. Chaque environnement et chaque cloud a son propre ensemble d’outils de gestion dont vous devez apprendre à vous servir.

En parallèle, il est difficile d’implémenter de nouveaux modèles opérationnels DevOps et ITOps, car les outils existants ne prennent pas en charge les nouveaux modèles cloud natifs.

Azure Arc simplifie la gouvernance et la gestion en proposant une plateforme de gestion multicloud et locale cohérente. Azure Arc vous permet de gérer l’ensemble de votre environnement, avec un volet unique, en projetant vos ressources existantes dans Azure Resource Manager. Vous pouvez maintenant gérer les machines virtuelles, les clusters Kubernetes et les bases de données comme s’ils s’exécutaient dans Azure. Quel que soit l’endroit où ils résident, vous pouvez utiliser des services et des fonctionnalités de gestion connus d’Azure. Azure Arc vous permet de continuer à utiliser des opérateurs informatiques (ITOps) traditionnels, tout en introduisant des pratiques DevOps pour prendre en charge de nouveaux modèles cloud natifs dans votre environnement.

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Diagramme du plan de contrôle de gestion d’Azure Arc" border="false":::

Aujourd’hui, Azure Arc vous permet de gérer les types de ressources suivants hébergés en dehors d’Azure :

* Serveurs : machines virtuelles et machines physiques exécutant Windows ou Linux.
* Clusters Kubernetes : prise en charge de plusieurs distributions Kubernetes.
* Services de données Azure : services Azure SQL Database et PostgreSQL Hyperscale.

## <a name="what-does-azure-arc-deliver"></a>Que propose Azure Arc ?

Les fonctionnalités clés d’Azure Arc sont les suivantes :

* Implémenter une cohérence au niveau inventaire, gestion, gouvernance et sécurité pour les serveurs dans votre environnement.

* Configurer des [extensions de machine virtuelle Azure](./servers/manage-vm-extensions.md) pour utiliser les services de gestion Azure afin de superviser, de sécuriser et de mettre à jour vos serveurs.

* Gérer et régir les clusters Kubernetes à grande échelle.

* Utilisez GitOps pour déployer la configuration sur un ou plusieurs clusters à partir de dépôts Git.

*  Conformité et configuration sans intervention pour vos clusters Kubernetes à l’aide d’Azure Policy.

* Exécuter les services de données Azure sur n’importe quel environnement Kubernetes comme s’ils s’exécutaient dans Azure (en particulier Azure SQL Managed Instance et Azure Database pour PostgreSQL Hyperscale, avec des avantages tels que les mises à niveau et mises à jour, la sécurité et la supervision). Utiliser la mise à l’échelle élastique, appliquer des mises à jour sans temps d’arrêt de l’application, même sans connexion continue à Azure

* Une expérience unifiée de l’affichage de vos ressources Azure Arc activées, que vous utilisiez le portail Azure, l’interface Azure CLI, Azure PowerShell ou l’API REST Azure.

## <a name="how-much-does-azure-arc-cost"></a>Combien coûte Azure Arc ?

Voici les détails du tarif des fonctionnalités disponibles aujourd’hui avec Azure Arc.

### <a name="arc-enabled-servers"></a>Serveurs avec Arc

Les fonctionnalités suivantes du plan de contrôle Azure Arc sont proposées sans coût supplémentaire :

* Organisation des ressources avec les groupes d'administration et les étiquettes Azure

* Recherche et indexation avec Azure Resource Graph

* Accès et sécurité avec Azure RBAC et des abonnements

* Environnements et automatisation avec des modèles et des extensions

* Gestion des mises à jour

Tout service Azure utilisé sur des serveurs avec Arc, par exemple Azure Security Center ou Azure Monitor, sera facturé sur la base des tarifs de ce service. Pour plus d’informations, consultez la [page des tarifs Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes avec Azure Arc

Tout service Azure qui est utilisé sur Kubernetes avec Arc, par exemple Azure Security Center ou Azure Monitor, sera facturé sur la base des tarifs de ce service. Pour plus d’informations sur les tarifs des configurations Kubernetes avec Azure Arc, consultez la [page des tarifs Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-data-services"></a>Services de données avec Azure Arc

Dans la phase de préversion actuelle, les services de données avec Azure Arc sont proposés sans frais supplémentaires.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les serveurs avec Arc, consultez cette [vue d’ensemble](./servers/overview.md)

* Pour plus d’informations sur Kubernetes avec Arc, consultez cette [vue d’ensemble](./kubernetes/overview.md)

* Pour plus d’informations sur les services de données avec Arc, consultez cette [vue d’ensemble](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/)

* Découvrez les services avec Arc à partir de la [preuve de concept Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/)
