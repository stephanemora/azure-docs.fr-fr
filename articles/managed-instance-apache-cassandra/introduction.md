---
title: Présentation d’Azure Managed Instance pour Apache Cassandra
description: En savoir plus sur Azure Managed Instance pour Apache Cassandra. Ce service gère le déploiement et la mise à l’échelle des instances open source natives d’Apache Cassandra dans Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 03/02/2021
ms.openlocfilehash: d99c62e7d88510c351f87d85b4f8c5c271767cb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101747421"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra-preview"></a>Qu’est-ce qu’Azure Managed Instance pour Apache Cassandra ? (Préversion)

> [!IMPORTANT]
> Azure Managed Instance pour Apache Cassandra est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le service Azure Managed Instance pour Apache Cassandra offre des opérations de déploiement et de mise à l’échelle automatisées pour les centres de données Apache Cassandra open source managés. Ce service vous aide à accélérer les scénarios hybrides et à réduire la maintenance continue. Lors de sa mise en disponibilité générale, il offrira des fonctionnalités de déplacement de données et d’intégration en profondeur avec l’[API Azure Cosmos DB Cassandra](../cosmos-db/cassandra-introduction.md).

:::image type="content" source="./media/introduction/icon.gif" alt-text="Azure Managed Instance pour Apache Cassandra est un service managé pour Apache Cassandra." border="false":::

## <a name="key-benefits"></a>Principaux avantages

### <a name="hybrid-deployments"></a>Déploiements hybrides

Vous pouvez utiliser ce service pour placer facilement des instances managées de centres de données Apache Cassandra, qui sont déployées automatiquement en tant que groupes de machines virtuelles identiques dans un réseau virtuel Azure nouveau ou existant. Ces centres de données peuvent être ajoutés à votre anneau Apache Cassandra existant exécuté localement via [Azure ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) dans Azure ou un autre environnement cloud.

- **Déploiement simplifié :** une fois la connectivité hybride établie, le déploiement est facile via le protocole gossip.
- **Métriques hébergées :** les métriques sont hébergées dans [Prometheus](https://prometheus.io/docs/introduction/overview/) pour superviser l’activité sur votre cluster.

### <a name="simplified-scaling"></a>Mise à l’échelle simplifiée

Dans l’instance managée, le fait d’effectuer un scale-up et un scale-down des nœuds dans un centre de centres est entièrement managé. Vous entrez le nombre de nœuds dont vous avez besoin et l’outil d’orchestration de la mise à l’échelle s’occupe d’établir leur fonctionnement dans l’anneau Cassandra.

### <a name="managed-and-cost-effective"></a>Managé et économique

Le service fournit des opérations de gestion pour les tâches Apache Cassandra courantes suivantes :

- Mise en service d'un cluster
- Provisionner un centre de données
- Mettre à l’échelle un centre de données
- Supprimer un centre de données
- Démarrer une action de réparation sur un espace de clés
- Modifier la configuration d’un centre de données
- Configurer des sauvegardes

Le modèle de tarification est flexible, à la demande, basé sur l’instance et sans frais de licence. Ce modèle de tarification vous permet de vous adapter à vos besoins spécifiques en matière de charge de travail. Vous choisissez le nombre de cœurs, la référence SKU de machine virtuelle, la taille de mémoire et la taille d’espace disque dont vous avez besoin.

## <a name="next-steps"></a>Étapes suivantes

Démarrez avec l’un de nos guides de démarrage rapide :

* [Créer un cluster Managed Instance à partir du portail Azure](create-cluster-portal.md)
* [Déployer un cluster Apache Spark managé avec Azure Databricks](deploy-cluster-databricks.md)
* [Gérer les ressources Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI](manage-resources-cli.md)
