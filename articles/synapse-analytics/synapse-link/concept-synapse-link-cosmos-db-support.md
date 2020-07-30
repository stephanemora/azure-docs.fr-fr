---
title: Fonctionnalités prises en charge par Azure Synapse Link (préversion) pour Azure Cosmos DB
description: Présentation de la liste des actions prises en charge par Azure Synapse Link pour Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 7fbc7b1cb8119a6ee9403bf0139380aa5dcd0613
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089122"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>Fonctionnalités prises en charge par Azure Synapse Link (préversion) pour Azure Cosmos DB

Cet article décrit les fonctionnalités actuellement prises en charge par Azure Synapse Link pour Azure Cosmos DB.

## <a name="azure-synapse-support"></a>Prise en charge d’Azure Synapse

Il existe deux types de conteneurs dans Azure Cosmos DB :
* Le conteneur HTAP : il s’agit d’un conteneur prenant en charge Synapse Link. Ce conteneur a à la fois un magasin transactionnel et un magasin analytique. 
* Le conteneur OLTP : il s’agit d’un conteneur doté uniquement d’un magasin de transactions. Il ne prend pas en charge Synapse Link. 

> [!IMPORTANT]
> Azure Synapse Link pour Azure Cosmos DB est actuellement pris en charge pour les espaces de travail pour lesquels le réseau virtuel managé n’est pas activé. 

Vous pouvez vous connecter à un conteneur Azure Cosmos DB sans activer Synapse Link, auquel cas vous pouvez uniquement lire/écrire dans le magasin transactionnel. La liste ci-dessous répertorie les fonctionnalités actuellement prises en charge dans Synapse Link pour Azure Cosmos DB. 

| Category              | Description |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL serverless](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| **Prise en charge à l’exécution** |Prise en charge de la lecture ou de l’écriture à l’exécution d’Azure Synapse| ✓ | [Nous contacter](mailto:AskSynapse@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB)|
| **Prise en charge de l’API Azure Cosmos DB** : |Prise en charge de l’API en tant que Synapse Link| SQL / MongoDB | SQL / MongoDB |
| **Object**  |Possibilité de créer des objets (tels qu’une table), qui pointent directement vers le conteneur Azure Cosmos DB| Affichage, Table | Affichage |
| **Lire**    |Lecture des données d’un conteneur Azure Cosmos DB| OLTP / HTAP | HTAP  |
| **Écrire**   |Écriture des données dans un conteneur Azure Cosmos DB au démarrage| OLTP | n/a |

* Si vous écrivez des données dans un conteneur Azure Cosmos DB à partir de Spark, ce processus se déroule dans le magasin transactionnel d’Azure Cosmos DB et aura un impact sur les performances transactionnelles d’Azure Cosmos DB en consommant des unités de requête.
* L’intégration de pool SQL par le biais de tables externes n’est pas prise en charge actuellement.

## <a name="supported-code-generated-actions-for-spark"></a>Actions générées par le code prises en charge pour Spark

| Mouvement              | Description |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Charger dans un DataFrame** |Charger et lire des données dans un DataFrame Spark |X| ✓ |
| **Créer une table Spark** |Créer une table pointant vers un conteneur Azure Cosmos DB|X| ✓ |
| **Écrire un DataFrame dans un conteneur** |Écrire des données dans un conteneur|✓| ✓ |
| **Charger un DataFrame de streaming à partir d’un conteneur** |Streamer des données à l’aide du flux de modification Azure Cosmos DB|✓| ✓ |
| **Écrire un DataFrame de streaming dans le conteneur** |Streamer des données à l’aide du flux de modification Azure Cosmos DB|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>Actions générées par le code prises en charge pour SQL serverless

| Mouvement              | Description |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Sélectionner les 100 premiers** |Prévisualiser les 100 premiers éléments d’un conteneur|X| ✓ |
| **Créer une vue** |Créer une vue pour avoir un accès BI direct dans un conteneur via Synapse SQL|X| ✓ |

## <a name="next-steps"></a>Étapes suivantes

* [Découvrir comment se connecter à Synapse Link pour Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Découvrir comment interroger un magasin analytique avec Spark](how-to-query-analytical-store-spark.md)
