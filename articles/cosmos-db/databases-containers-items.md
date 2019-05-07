---
title: Utilisation des bases de données, des conteneurs et des éléments Azure Cosmos DB
description: Cet article explique comment créer et utiliser des bases de données, des conteneurs et des éléments Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 215ae29c571e56b7e3af589149123083223023c7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153456"
---
# <a name="work-with-databases-containers-and-items"></a>Utiliser des bases de données, des conteneurs et des éléments

Après avoir créé un [compte Azure Cosmos](account-overview.md) sous votre abonnement Azure, vous pouvez gérer des données dans votre compte en créant des bases de données, les conteneurs et les éléments. Cet article décrit chacune des entités suivantes : bases de données, conteneurs et éléments. L’illustration suivante montre la hiérarchie de ces différentes entités dans un compte Azure Cosmos :

![Entités du compte Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Bases de données Azure Cosmos

Vous pouvez créer une ou plusieurs bases de données Azure Cosmos dans votre compte. Une base de données est analogue à un espace de noms. Il est l’unité de gestion pour un ensemble de conteneurs d’Azure Cosmos. Le tableau suivant montre comment une base de données Azure Cosmos est mappée à différentes entités d’API :

| **Entité Azure Cosmos** | **API SQL** | **API Cassandra** | **API pour MongoDB d’Azure Cosmos DB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- |
|Base de données Azure Cosmos | Base de données | Espace de clés | Base de données | Base de données | N/D |

> [!NOTE]
> Avec les comptes de l’API de Table, lorsque vous créez votre première table, une base de données par défaut est automatiquement créé dans votre compte Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Actions effectuées sur une base de données Azure Cosmos

Vous pouvez interagir avec une base de données Azure Cosmos Azure Cosmos API comme suit :

| **opération** | **Interface de ligne de commande Azure**|**API SQL** | **API Cassandra** | **API pour MongoDB d’Azure Cosmos DB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- | --- |
|Énumérer toutes les bases de données| Oui | Oui | Oui (la base de données est mappée sur un espace de clés) | Oui | N/D | N/D |
|Lire le contenu de la base de données| Oui | Oui | Oui (la base de données est mappée sur un espace de clés) | Oui | N/D | N/D |
|Create new database| Oui | Oui | Oui (la base de données est mappée sur un espace de clés) | Oui | N/D | N/D |
|Mettre à jour la base de données| Oui | Oui | Oui (la base de données est mappée sur un espace de clés) | Oui | N/D | N/D |


## <a name="azure-cosmos-containers"></a>Conteneurs Cosmos Azure

Un conteneur Azure Cosmos est l’unité d’évolutivité pour le stockage et débit approvisionné. Un conteneur est partitionné horizontalement, puis répliqué dans plusieurs régions. Les éléments que vous ajoutez au conteneur, et le débit que vous approvisionnez pour celui-ci, sont automatiquement distribués sur un ensemble de partitions logiques en fonction de la clé de partition. Pour en savoir plus sur le partitionnement et clés de partition, consultez [cela](partition-data.md) article. 

Lorsque vous créez un conteneur Azure Cosmos, vous configurez le débit avec l’un des modes suivants :

* Mode **Débit provisionné dédié** : Le débit approvisionné sur un conteneur est exclusivement réservé pour ce conteneur et il est sauvegardé par les contrat SLA. Pour plus d’informations, consultez [Provisionnement du débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).

* Mode **Débit provisionné partagé** : Ces conteneurs partagent le débit approvisionné avec les autres conteneurs dans la même base de données (à l’exclusion de ces conteneurs qui ont été configurés avec un débit approvisionné dédié). En d’autres termes, le débit approvisionné sur la base de données est partagé entre tous les conteneurs de « débit partagé ». Pour plus d’informations, consultez [Comment configurer le débit provisionné pour une base de données Azure Cosmos](how-to-provision-database-throughput.md).

Les conteneurs Azure Cosmos sont élastiques, qu’ils aient un débit « partagé » ou « dédié ».

Un conteneur Azure Cosmos est un conteneur d’éléments indépendant de tout schéma. Les éléments d’un conteneur peuvent avoir des schémas différents. Par exemple, un élément qui représente une personne, un élément représentant une voiture peut être placé dans le *même conteneur*. Par défaut, tous les éléments que vous ajoutez à un conteneur sont automatiquement indexés, sans que vous ayez à effectuer une gestion explicite d’index ou de schéma. Vous pouvez personnaliser le comportement d’indexation en configurant le [stratégie d’indexation](index-overview.md) sur un conteneur. 

Vous pouvez définir [durée de vie (TTL)](time-to-live.md) sur les éléments sélectionnés au sein d’un conteneur Azure Cosmos ou pour l’intégralité du conteneur normalement supprimer définitivement ces éléments hors du système. Azure Cosmos DB supprime automatiquement les éléments lorsque ceux-ci expirent. Cela permet également d’éviter qu’une requête envoyée au conteneur ne retourne des éléments ayant expiré. Pour plus d’informations, consultez [Guide pratique pour configurer la durée de vie de votre conteneur](how-to-time-to-live.md).

À l’aide de [de flux de modification](change-feed.md), vous pouvez vous abonner dans le journal des opérations qui est géré pour chacune des partitions logiques de votre conteneur. Le flux de modification fournit le journal de toutes les mises à jour appliquées au conteneur ainsi que les images des éléments avant et après mise à jour. Consultez [comment créer des applications réactives à l’aide de flux de modification](serverless-computing-database.md). Vous pouvez également configurer la durée de rétention pour le flux de modification à l’aide de la stratégie sur le conteneur de flux de modification. 

Vous pouvez inscrire [les procédures stockées, déclencheurs, fonctions définies par l’utilisateur (UDF)](stored-procedures-triggers-udfs.md) et [fusion procédures](how-to-manage-conflicts.md) avec votre conteneur Azure Cosmos. 

Vous pouvez spécifier un [contrainte de clé unique](unique-keys.md) sur votre conteneur Azure Cosmos. En créant une stratégie de clé unique, vous garantissez l’unicité d’une ou de plusieurs valeurs par clé de partition logique. Une fois qu’un conteneur a été créé avec une stratégie de clé unique, il empêche la création de tout nouvel élément ou de tout élément mis à jour avec des valeurs dupliquant des valeurs spécifiées par la contrainte de clé unique. Pour plus d’informations, consultez [Contraintes de clés uniques](unique-keys.md).

Un conteneur Azure Cosmos est spécialisé dans les entités d’API :

| **Entité Azure Cosmos** | **API SQL** | **API Cassandra** | **API pour MongoDB d’Azure Cosmos DB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- |
|Conteneur Azure Cosmos | Collection | Table | Collection | Graph | Table |

### <a name="properties-of-an-azure-cosmos-container"></a>Propriétés d’un conteneur Azure Cosmos

Un conteneur Azure Cosmos possède un ensemble de propriétés définies par le système. En fonction de l’API, certaines de ces propriétés peuvent ne pas être exposées directement. Le tableau suivant décrit la liste des propriétés définies par le système :

| **Propriété définie par le système** | **Système généré ou configurables par l’utilisateur** | **Objectif** | **API SQL** | **API Cassandra** | **API pour MongoDB d’Azure Cosmos DB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_id | Générée par le système | Identificateur unique du conteneur | Oui | Non  | Non  | Non  | Non  |
|_etag | Générée par le système | Étiquette d’entité utilisée pour le contrôle de l’accès concurrentiel optimiste | Oui | Non  | Non  | Non  | Non  |
|_ts | Générée par le système | Dernière mise à jour de l’horodatage du conteneur | Oui | Non  | Non  | Non  | Non  |
|_self | Générée par le système | URI adressable du conteneur | Oui | Non  | Non  | Non  | Non  |
|id | Configurable par l’utilisateur | Nom unique du conteneur défini par l’utilisateur | Oui | OUI | OUI | OUI | Oui |
|indexingPolicy | Configurable par l’utilisateur | Fournit la possibilité de modifier le chemin d’accès de l’index, le type d’index et le mode d’indexation. | Oui | Non  | Non  | Non  | Oui |
|TimeToLive | Configurable par l’utilisateur | Permet de supprimer automatiquement les éléments d’un conteneur après une période déterminée. Pour plus d’informations, consultez l’article [Durée de vie](time-to-live.md). | Oui | Non  | Non  | Non  | Oui |
|changeFeedPolicy | Configurable par l’utilisateur | Utilisé pour lire les modifications apportées aux éléments d’un conteneur. Pour plus d’informations, consultez le [de flux de modification](change-feed.md) article. | Oui | Non  | Non  | Non  | Oui |
|uniqueKeyPolicy | Configurable par l’utilisateur | Permet de garantir l’unicité d’une ou plusieurs valeurs dans une partition logique. Pour plus d’informations, consultez le [les contraintes de clé Unique](unique-keys.md) article. | Oui | Non  | Non  | Non  | Oui |

### <a name="operations-on-an-azure-cosmos-container"></a>Actions effectuées sur un conteneur Azure Cosmos

Un conteneur Azure Cosmos prend en charge les opérations suivantes, qui peuvent être effectuées avec l’une des API Azure Cosmos.

| **opération** | **Interface de ligne de commande Azure** | **API SQL** | **API Cassandra** | **API pour MongoDB d’Azure Cosmos DB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- | --- |
| Énumérer les conteneurs d’une base de données | Oui | OUI | OUI | Oui | N/D | N/D |
| Lire le contenu d’un conteneur | Oui | OUI | OUI | Oui | N/D | N/D |
| Créer un conteneur | Oui | OUI | OUI | Oui | N/D | N/D |
| Mettre à jour un conteneur | Oui | OUI | OUI | Oui | N/D | N/D |
| Supprimer un conteneur | Oui | OUI | OUI | Oui | N/D | N/D |

## <a name="azure-cosmos-items"></a>Éléments Azure Cosmos

En fonction de l’API, un élément Azure Cosmos peut représenter un document d’une collection, une ligne de table, un nœud de graphe ou une arête de graphe. Le tableau suivant présente le mappage d’entités d’API à un élément Azure Cosmos :

| **Entité Cosmos** | **API SQL** | **API Cassandra** | **API pour MongoDB d’Azure Cosmos DB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- |
|Élément Azure Cosmos | Document | Ligne | Document | Nœud ou arête | Item |

### <a name="properties-of-an-item"></a>Propriétés d’un élément

Chaque élément Azure Cosmos comprend des propriétés définies par le système. En fonction de l’API, certaines de ces propriétés peuvent ne pas être exposées directement.

|**Propriété définie par le système** | **Système généré ou configurables par l’utilisateur**| **Objectif** | **API SQL** | **API Cassandra** | **API pour MongoDB d’Azure Cosmos DB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_id | Générée par le système | Identificateur unique d’un élément | Oui | Non  | Non  | Non  | Non  |
|_etag | Générée par le système | Étiquette d’entité utilisée pour le contrôle de l’accès concurrentiel optimiste | Oui | Non  | Non  | Non  | Non  |
|_ts | Générée par le système | L’horodatage de la dernière mise à jour de l’élément | Oui | Non  | Non  | Non  | Non  |
|_self | Générée par le système | URI adressable de l’élément | Oui | Non  | Non  | Non  | Non  |
|id | Vous pouvez soit utiliser | Nom unique défini par l’utilisateur au sein d’une partition logique. Si l’utilisateur ne spécifie pas l’ID, le système génère automatiquement un. | Oui | OUI | OUI | OUI | Oui |
|Propriétés arbitraires définies par l’utilisateur | Défini par l’utilisateur | Propriétés définies par l’utilisateur représentées sous forme d’API native (JSON, BSON, CQL, etc.) | Oui | OUI | OUI | OUI | Oui |

### <a name="operations-on-items"></a>Actions possibles sur les éléments

Un élément Azure Cosmos prend en charge les opérations suivantes, qui peuvent être effectuées avec l’une des API Azure Cosmos.

| **opération** | **Interface de ligne de commande Azure** | **API SQL** | **API Cassandra** | **API pour MongoDB d’Azure Cosmos DB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- | --- |
| Insérer, remplacer, supprimer, upsert, lire | Non  | OUI | OUI | OUI | OUI | OUI |

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant passer à apprendre les concepts suivants :

* [Comment configurer le débit approvisionné sur une base de données Azure Cosmos](how-to-provision-database-throughput.md)
* [Comment configurer le débit approvisionné sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md)
* [Partitions logiques](partition-data.md)
* [Comment configurer la durée de vie d’un conteneur Azure Cosmos](how-to-time-to-live.md)
* [Comment créer des applications réactives à l’aide du flux de modification](change-feed.md)
* [Comment configurer une contrainte de clé unique dans votre conteneur Azure Cosmos](unique-keys.md)
