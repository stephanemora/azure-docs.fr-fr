---
title: Présentation de l’API etcd Azure Cosmos DB
description: Cet article fournit une présentation et les avantages clés de l’API etcd dans Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 16aac5c765c36c49919685ee58e8034786ddf1ae
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797355"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Présentation de l’API etcd Azure Cosmos DB (préversion)

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale pour les applications stratégiques. Il fournit la distribution mondiale clés en main, la mise à l’échelle élastique du débit et du stockage, des latences de l’ordre de quelques millisecondes dans plus de 99 pour cent des cas, et une haute disponibilité garantie, le tout soutenu par nos contrats de niveau de service de pointe.

[ETCD](https://github.com/etcd-io/etcd) est un magasin de clés/valeurs distribué. Dans [Kubernetes](https://kubernetes.io/), etcd est utilisé pour stocker l’état et la configuration des clusters Kubernetes. Garantir la disponibilité, la fiabilité et les performances d’etcd est essentiel pour l’intégrité globale du cluster, l’extensibilité, la disponibilité de l’élasticité et les performances d’un cluster Kubernetes.

L'API etcd d'Azure Cosmos DB vous permet d’utiliser Azure Cosmos DB en tant que magasin principal pour Azure Kubernetes. Dans Azure Cosmos DB, l'API etcd est actuellement préversion. Azure Cosmos DB implémente le protocole filaire etcd. Grâce à l’API etcd dans Azure Cosmos DB, les développeurs obtiennent automatiquement un cluster Kubernetes hautement fiable, [disponible](high-availability.md) et [distribué à l’échelle mondiale](distribute-data-globally.md). Cette API permet aux développeurs de mettre à l’échelle la gestion de l’état de Kubernetes sur un service PaaS natif cloud complètement managé. 

> [!NOTE]
> Contrairement à d’autres API dans Azure Cosmos DB, vous ne pouvez pas approvisionner un compte d’API etcd via le Portail Azure, la CLI ou les Kits de développement logiciel (SDK). Vous pouvez approvisionner un compte d’API etcd en déployant le modèle Resource Manager uniquement ; pour des instructions détaillées, consultez l’article [How to provision Azure Kubernetes with Azure Cosmos DB](bootstrap-kubernetes-cluster.md) (Comment approvisionner Azure Kubernetes avec Azure Cosmos DB). L’API etcd Azure Cosmos DB est actuellement en préversion limitée. Vous pouvez vous [inscrire à la préversion](https://aka.ms/cosmosetcdapi-signup) en remplissant le formulaire d’inscription.

## <a name="wire-level-compatibility"></a>Compatibilité de niveau filaire

Azure Cosmos DB implémente le protocole filaire etcd version 3 et permet aux serveurs d’API du [nœud principal](https://kubernetes.io/docs/concepts/overview/components/) d’utiliser Azure Cosmos DB comme ils le feraient dans un environnement etcd installé localement. L’API etcd prend en charge l’authentification mutuelle TLS. 

Le diagramme suivant montre les composants d’un cluster Kubernetes. Dans le maître du cluster, le serveur d’API utilise l’API etcd Azure Cosmos DB au lieu d’etcd installé localement. 

![Azure Cosmos DB implémentant le protocole filaire etcd](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Principaux avantages

### <a name="no-etcd-operations-management"></a>Aucune gestion des opérations etcd

Dans la mesure où il s’agit d’un service cloud natif complètement managé, Azure Cosmos DB supprime la nécessité pour les développeurs de Kubernetes de configurer et de gérer etcd. L’API etcd dans Azure Cosmos DB est évolutive, hautement disponible, tolérante aux pannes et offre de hautes performances. La surcharge de configuration de la réplication entre plusieurs nœuds, d’exécution de mises à jour de déploiement, de correctifs de sécurité et de surveillance de l’intégrité d’etcd est gérée par Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Diffusion mondiale et haute disponibilité 

Grâce à l’API etcd, Azure Cosmos DB garantit une disponibilité à 99,99 % pour les lectures et écritures de données dans une même région, avec une disponibilité à 99,999 % dans plusieurs régions. 

### <a name="elastic-scalability"></a>Extensibilité élastique

Azure Cosmos DB offre une extensibilité élastique de lecture et d’écritures de requêtes dans différentes régions.
À mesure que le cluster Kubernetes évolue, le compte d’API etcd dans Azure Cosmos DB s’adapte en toute flexibilité sans temps d’arrêt. Le stockage de données etcd dans Azure Cosmos DB, au lieu des nœuds principaux Kubernetes, permet également une mise à l’échelle plus flexible du nœud principal. 

### <a name="security--enterprise-readiness"></a>Sécurité et préparation pour l’entreprise

Lorsque des données etcd sont stockées dans Azure Cosmos DB, les développeurs Kubernetes obtiennent automatiquement le [chiffrement intégré au repos](database-encryption-at-rest.md), les [certifications et conformité](compliance.md) et les [fonctionnalités de sauvegarde et restauration](online-backup-and-restore.md) pris en charge par Azure Cosmos DB. 

## <a name="next-steps"></a>Étapes suivantes

* [Comment utiliser Azure Kubernetes avec Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Principaux avantages d’Azure Cosmos DB](introduction.md)
* [Guide de démarrage rapide du moteur AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)