---
title: Introduction à l’API d’etcd Azure Cosmos DB
description: Cet article fournit une avantages clés et présentation d’etcd API dans Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 30d18e1e23767ce3a179221714e001574f8e9778
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075489"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Introduction à l’etcd Azure Cosmos DB API (version préliminaire)

Azure Cosmos DB est un service de base de données multimodèle globalement distribué de Microsoft pour les applications stratégiques. Il offre une distribution mondiale, élastique mise à l’échelle du débit et du stockage, des latences en quelques millisecondes au 99e centile et une haute disponibilité, le tout régie par des SLA leader du secteur.

[ETCD](https://github.com/etcd-io/etcd) est un magasin de clés/valeurs distribuées. Dans [Kubernetes](https://kubernetes.io/), etcd est utilisé pour stocker l’état et la configuration des clusters Kubernetes. Garantir la disponibilité, fiabilité et performances d’etcd est cruciale pour l’intégrité globale du cluster, d’évolutivité, disponibilité de l’élasticité et performances d’un cluster Kubernetes. 

Etcd API dans Azure Cosmos DB vous permet d’utiliser Azure Cosmos DB comme magasin principal pour [Azure Kubernetes](../aks/index.yml). ETCD API dans Azure Cosmos DB est actuellement en version préliminaire. Azure Cosmos DB implémente le protocole etcd. Avec etcd API dans Azure Cosmos DB, les développeurs obtiennent automatiquement hautement fiables, [disponible](high-availability.md), [globalement distribuées](distribute-data-globally.md) Kubernetes. Cette API permet aux développeurs à l’échelle de la gestion de l’état Kubernetes sur un service PaaS natif de cloud entièrement géré. 

> [!NOTE]
> Contrairement à d’autres API dans Azure Cosmos DB, vous ne pouvez pas configurer un compte d’API etcd via le portail Azure, CLI ou les kits de développement logiciel. Vous pouvez configurer un compte d’API etcd en déployant le modèle Resource Manager uniquement ; pour des instructions détaillées, consultez [comment approvisionner Azure Kubernetes avec Azure Cosmos DB](bootstrap-kubernetes-cluster.md) article.  

## <a name="wire-level-compatibility"></a>Compatibilité au niveau du câble

Azure Cosmos DB implémente le protocole filaire d’etcd version 3 et permet la [du nœud maître](https://kubernetes.io/docs/concepts/overview/components/) serveurs d’API à utiliser Azure Cosmos DB comme il le feriez dans un environnement etcd installée localement. L’API etcd prend en charge l’authentification mutuelle TLS. 

Le diagramme suivant illustre les composants d’un cluster Kubernetes. Dans le maître du cluster, le serveur d’API utilise des API d’etcd Azure Cosmos DB, au lieu d’etcd installée localement. 

![Azure Cosmos DB implémentant le protocole filaire d’etcd](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Principaux avantages

### <a name="no-etcd-operations-management"></a>Aucune gestion des opérations etcd

Comme un service cloud native entièrement géré, Azure Cosmos DB supprime la nécessité pour les développeurs de Kubernetes configurer et gérer etcd. Etcd API dans Azure Cosmos DB est évolutive, hautement disponible, une tolérance de panne et offre des performances élevées. La surcharge de la configuration de la réplication entre plusieurs nœuds, effectuer le déploiement de mises à jour, correctifs de sécurité, et surveiller l’intégrité etcd sont gérées par Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Haute disponibilité et la distribution mondiale 

En utilisant les API etcd, Azure Cosmos DB garantit une disponibilité de 99,99 % pour les lectures de données et les écrit dans une seule région, avec une disponibilité à 99,999 % dans plusieurs régions. 

### <a name="elastic-scalability"></a>Extensibilité élastique

Azure Cosmos DB offre une évolutivité élastique pour lire et écrire des requêtes dans différentes régions.
Au fur et à mesure du cluster Kubernetes, le compte etcd API dans Azure Cosmos DB s’adapte en toute flexibilité sans interruption de service. Stocker des données d’etcd dans Azure Cosmos DB, au lieu des nœuds principaux Kubernetes permet également l’évolution de nœud principal plus flexible. 

### <a name="security--enterprise-readiness"></a>Préparation de sécurité et d’entreprise

Lorsque les données etcd sont stockées dans Azure Cosmos DB, les développeurs Kubernetes obtiennent automatiquement le [chiffrement intégré au repos](database-encryption-at-rest.md), [certifications et conformité](compliance.md), et [sauvegarde et restauration fonctionnalités](online-backup-and-restore.md) pris en charge par Azure Cosmos DB. 

## <a name="next-steps"></a>Étapes suivantes

* [Comment utiliser Azure Kubernetes avec Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Principaux avantages d’Azure Cosmos DB](introduction.md)
* [Guide de démarrage rapide du moteur ACS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)