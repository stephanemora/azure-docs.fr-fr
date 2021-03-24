---
title: Différences entre Azure Managed Instance pour Apache Cassandra et l’API Cassandra d’Azure Cosmos DB
description: Découvrez les différences entre Azure Managed Instance pour Apache Cassandra et l’API Cassandra dans Azure Cosmos DB. Découvrez également quels avantages offrent chacun de ces services et dans quels cas choisir l’un plutôt que l’autre.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 3050dda4b3c0e1a05d751a4f5969bba69ad0506d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101747688"
---
# <a name="differences-between-azure-managed-instance-for-apache-cassandra-preview-and-azure-cosmos-db-cassandra-api"></a>Différences entre Azure Managed Instance pour Apache Cassandra (préversion) et l’API Cassandra d’Azure Cosmos DB 

Dans cet article, vous allez découvrir les différences entre Azure Managed Instance pour Apache Cassandra et l’API Cassandra dans Azure Cosmos DB. Cet article explique comment faire votre choix entre les deux services et quand héberger votre propre environnement Apache Cassandra.

> [!IMPORTANT]
> Azure Managed Instance pour Apache Cassandra est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-differences"></a>Différences clés

Azure Managed Instance pour Apache Cassandra permet d’automatiser le déploiement, la mise à l’échelle et les opérations afin de maintenir les nœuds dans un état sain pour les instances Apache Cassandra dans Azure. Cette solution offre également la possibilité d’effectuer un scale-out de la capacité des clusters Apache Cassandra existants, qu’ils soient locaux ou autohébergés dans le cloud. Le scale-out s’effectue par l’ajout de centres de données Cassandra managés à l’anneau de cluster existant.

L’[API Cassandra](../cosmos-db/cassandra-introduction.md) dans Azure Cosmos DB est une couche de compatibilité au-dessus d’[Azure Cosmos DB](../cosmos-db/index.yml), le service de base de données cloud natif de Microsoft distribué à l’échelle mondiale. La combinaison de ces services dans Azure offre un continuum de choix pour ceux qui utilisent Apache Cassandra dans des environnements de cloud hybride complexes.

## <a name="how-to-choose"></a>Comment choisir ?

Le tableau suivant présente les scénarios courants, les exigences de charge de travail et les attentes que peuvent remplir chacune de ces approches de déploiement :

| |Apache Cassandra autohébergé en local ou dans Azure | Azure Managed Instance pour Apache Cassandra | API Cassandra Azure Cosmos DB |
|---------|---------|---------|---------|
|**Type de déploiement**| Vous avez un déploiement d’Apache Cassandra hautement personnalisé avec des « snitch » ou des correctifs personnalisés. | Vous avez un déploiement d’Apache Cassandra open source standard sans code personnalisé. | Vous souhaitez une plateforme non dépendante d’Apache Cassandra, mais qui soit conforme à tous les pilotes clients open source au niveau du [protocole filaire](../cosmos-db/cassandra-support.md). |
| **Surcharge opérationnelle**| Vous avez des experts Cassandra qui peuvent déployer, configurer et gérer vos clusters.  | Vous souhaitez réduire la surcharge opérationnelle pour maintenir l’intégrité des nœuds Apache Cassandra, mais garder le contrôle des configurations au niveau de la plateforme, telles que la réplication et la cohérence. | Vous souhaitez éliminer la surcharge opérationnelle en utilisant une base de données PaaS (Platform as a Service) complètement managée dans le cloud. |
| **Système d'exploitation requis**| Vous devez conserver des images personnalisées ou finales (golden) du système d’exploitation des machines virtuelles. | Vous pouvez utiliser des images standard (vanilla), mais vous souhaitez contrôler les références SKU, la mémoire, les disques et les IOPS. | Vous souhaitez simplifier le provisionnement de la capacité et l’exprimer sous la forme d’une métrique normalisée unique, avec une relation un-à-un avec le débit, comme les [unités de requête](../cosmos-db/request-units.md) dans Azure Cosmos DB. |
| **Modèle de tarification**| Vous souhaitez utiliser une solution de gestion telle que les outils Datastax, et vous êtes satisfait des coûts de licences. | Vous préférez des licences entièrement open source et un modèle tarifaire par instance de machine virtuelle. | Vous souhaitez utiliser le modèle tarifaire du cloud natif, qui inclut les offres de [mise à l’échelle automatique](../cosmos-db/manage-scale-cassandra.md#use-autoscale) et [serverless](../cosmos-db/serverless.md). |
| **Analyse**| Vous souhaitez avoir un contrôle total du provisionnement des pipelines analytiques, quelle que soit la surcharge nécessaire pour les créer et les gérer. | Vous souhaitez utiliser des services cloud d’analytique comme Azure Databricks. | Vous souhaitez avoir une analytique transactionnelle hybride en quasi-temps réel intégrée à la plateforme avec [Azure Synapse Link pour Cosmos DB](../cosmos-db/synapse-link.md). |
| **Modèle de charge de travail**| Votre charge de travail est relativement stable et vous n’avez pas souvent besoin de mettre à l’échelle les nœuds dans le cluster. | Votre charge de travail est volatile et vous souhaitez pouvoir facilement effectuer un scale-up ou un scale-down des nœuds dans un centre de données ou bien ajouter ou supprimer des centres de données. | Votre charge de travail est souvent volatile et vous souhaitez pouvoir effectuer un scale-up ou un scale-down rapidement et à grande échelle. |
| **Contrats SLA**| Vous êtes satisfait de vos processus de gestion des contrats SLA en ce qui concerne la cohérence, le débit, la disponibilité et la reprise d’activité après sinistre. | Vous êtes satisfait de vos processus de gestion des contrats SLA en ce qui concerne la cohérence, le débit et la disponibilité, mais vous avez besoin d’aide pour les sauvegardes. | Vous souhaitez avoir des contrats SLA complets pour la cohérence, le débit, la disponibilité et la reprise d’activité après sinistre. |

## <a name="next-steps"></a>Étapes suivantes

Démarrez avec l’un de nos guides de démarrage rapide :

* [Créer un cluster Managed Instance à partir du portail Azure](create-cluster-portal.md)