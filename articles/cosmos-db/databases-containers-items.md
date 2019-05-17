---
title: Travailler avec les bases de données, les conteneurs et les éléments dans Azure Cosmos DB
description: Cet article décrit comment créer et utiliser des bases de données, les conteneurs et les éléments dans Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: ea3ba91859bbfb1a7c589cdb36e9fb87b52a89b8
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560284"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Travailler avec les bases de données, les conteneurs et les éléments dans Azure Cosmos DB

La création d’un [compte Azure Cosmos DB](account-overview.md) dans votre abonnement Azure vous permet d’y gérer des données à l’aide de bases de données, de conteneurs et d’éléments. Cet article décrit chacune de ces entités. 

L’illustration suivante montre la hiérarchie des différentes entités dans un compte Azure Cosmos DB :

![Entités du compte Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Bases de données Azure Cosmos

Vous pouvez créer un ou plusieurs bases de données Azure Cosmos sous votre compte. Une base de données est analogue à un espace de noms. Une base de données est l’unité de gestion pour un ensemble de conteneurs d’Azure Cosmos. Le tableau suivant montre comment une base de données Azure Cosmos est mappée à différentes entités d’API :

| Entité Cosmos Azure | API SQL | API Cassandra | API Azure Cosmos DB pour MongoDB | API Gremlin | API de table |
| --- | --- | --- | --- | --- | --- |
|Base de données Azure Cosmos | Base de données | Espace de clés | Base de données | Base de données | N/D |

> [!NOTE]
> Avec les comptes de l’API de Table, lorsque vous créez votre première table, une base de données par défaut est automatiquement créé dans votre compte Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Actions effectuées sur une base de données Azure Cosmos

Vous pouvez interagir avec une base de données Azure Cosmos Azure Cosmos API comme décrit dans le tableau suivant :

| Opération | Azure CLI | API SQL | API Cassandra | API Azure Cosmos DB pour MongoDB | API Gremlin | API de table |
| --- | --- | --- | --- | --- | --- | --- |
|Énumérer toutes les bases de données| Oui | Oui | Oui (la base de données est mappée sur un espace de clés) | Oui | N/D | N/D |
|Lire le contenu de la base de données| Oui | Oui | Oui (la base de données est mappée sur un espace de clés) | Oui | N/D | N/D |
|Créer une base de données| Oui | Oui | Oui (la base de données est mappée sur un espace de clés) | Oui | N/D | N/D |
|Mettre à jour la base de données| Oui | Oui | Oui (la base de données est mappée sur un espace de clés) | Oui | N/D | N/D |


## <a name="azure-cosmos-containers"></a>Conteneurs Cosmos Azure

Un conteneur Azure Cosmos est l’unité de l’évolutivité à la fois pour le stockage et débit approvisionné. Un conteneur est partitionné horizontalement, puis répliqué dans plusieurs régions. Les éléments que vous ajoutez au conteneur, et le débit que vous approvisionnez pour celui-ci, sont automatiquement distribués sur un ensemble de partitions logiques en fonction de la clé de partition. Pour en savoir plus sur le partitionnement et clés de partition, consultez [partitionner les données](partition-data.md). 

Lorsque vous créez un conteneur Azure Cosmos, vous configurez le débit dans un des modes suivants :

* **Mode de débit approvisionné dédié**: Le débit approvisionné sur un conteneur est exclusivement réservé pour ce conteneur et il est sauvegardé par les contrat SLA. Pour plus d’informations, consultez [comment configurer le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).

* **Mode de débit approvisionné partagé**: Ces conteneurs partagent le débit approvisionné avec les autres conteneurs dans la même base de données (à l’exclusion des conteneurs qui ont été configurés avec un débit approvisionné dédié). En d’autres termes, le débit approvisionné sur la base de données est partagé entre tous les conteneurs de « débit partagé ». Pour plus d’informations, consultez [comment configurer le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).

Un conteneur Azure Cosmos peut évoluer en toute flexibilité, si vous créez des conteneurs en utilisant les modes de débit approvisionné dédiée ou partagée.

Un conteneur Azure Cosmos est un conteneur d’éléments indépendant de tout schéma. Éléments dans un conteneur peuvent avoir des schémas arbitraires. Par exemple, un élément qui représente une personne et un élément qui représente une voiture peuvent être placés dans le *même conteneur*. Par défaut, tous les éléments que vous ajoutez à un conteneur sont automatiquement indexés sans index explicite ou la gestion des schémas. Vous pouvez personnaliser le comportement d’indexation en configurant le [stratégie d’indexation](index-overview.md) sur un conteneur. 

Vous pouvez définir [la durée de vie (TTL)](time-to-live.md) sur les éléments sélectionnés dans un conteneur Azure Cosmos ou pour l’intégralité du conteneur normalement supprimer définitivement ces éléments dans le système. Azure Cosmos DB supprime automatiquement les éléments lors de leur expiration. Elle garantit également que les éléments expirés dans une limite fixe ne retourne pas une requête effectuée sur le conteneur. Pour plus d’informations, consultez [configurer la durée de vie sur votre conteneur](how-to-time-to-live.md).

Vous pouvez utiliser [le flux de modification](change-feed.md) pour s’abonner dans le journal des opérations qui est géré pour chaque partition logique de votre conteneur. Flux de modification fournit le journal de toutes les mises à jour effectuées sur le conteneur, ainsi que les images des éléments avant et après. Pour plus d’informations, consultez [créer des applications réactives à l’aide de flux de modification](serverless-computing-database.md). Vous pouvez également configurer la durée de rétention pour le flux à l’aide de la modification de stratégie sur le conteneur de flux de modification. 

Vous pouvez inscrire [les procédures stockées, déclencheurs, fonctions définies par l’utilisateur (UDF)](stored-procedures-triggers-udfs.md), et [fusion procédures](how-to-manage-conflicts.md) pour votre conteneur Azure Cosmos. 

Vous pouvez spécifier un [contrainte de clé unique](unique-keys.md) sur votre conteneur Azure Cosmos. En créant une stratégie de clé unique, vous garantissez l’unicité d’une ou de plusieurs valeurs par clé de partition logique. Si vous créez un conteneur à l’aide d’une stratégie de clé unique, aucun élément nouveau ou mis à jour avec les valeurs qui dupliquent les valeurs spécifiées par la contrainte de clé unique ne peut être créé. Pour plus d’informations, consultez [Contraintes de clés uniques](unique-keys.md).

Un conteneur Azure Cosmos est spécialisé dans les entités spécifiques de l’API, comme indiqué dans le tableau suivant :

| Entité Cosmos Azure | API SQL | API Cassandra | API Azure Cosmos DB pour MongoDB | API Gremlin | API de table |
| --- | --- | --- | --- | --- | --- |
|Conteneur Azure Cosmos | Collection | Table | Collection | Graphe | Table |

### <a name="properties-of-an-azure-cosmos-container"></a>Propriétés d’un conteneur Azure Cosmos

Un conteneur Azure Cosmos possède un ensemble de propriétés définies par le système. En fonction de l’API que vous utilisez, certaines propriétés ne pas être directement exposées. Le tableau suivant décrit la liste des propriétés définies par le système :

| Propriété définie par le système | Configurables par l’utilisateur ou générée par le système | Objectif | API SQL | API Cassandra | API Azure Cosmos DB pour MongoDB | API Gremlin | API de table |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | Généré par le système | Identificateur unique du conteneur | Oui | Non  | Non  | Non  | Non |
|\_etag | Généré par le système | Étiquette d’entité utilisée pour le contrôle de l’accès concurrentiel optimiste | Oui | Non  | Non  | Non  | Non |
|\_TS | Généré par le système | Dernière mise à jour de l’horodatage du conteneur | Oui | Non  | Non  | Non  | Non |
|\_self | Généré par le système | URI adressable du conteneur | Oui | Non  | Non  | Non  | Non |
|id | Configurables par l’utilisateur | Nom unique du conteneur défini par l’utilisateur | Oui | OUI | OUI | OUI | Oui |
|indexingPolicy | Configurables par l’utilisateur | Fournit la possibilité de modifier le chemin d’accès de l’index, le type d’index et le mode d’indexation | Oui | Non  | Non  | Non  | Oui |
|TimeToLive | Configurables par l’utilisateur | Offre la possibilité de supprimer automatiquement des éléments à partir d’un conteneur après un laps de temps défini. Pour plus d’informations, consultez [Time to Live](time-to-live.md). | Oui | Non  | Non  | Non  | Oui |
|changeFeedPolicy | Configurables par l’utilisateur | Utilisé pour lire les modifications apportées aux éléments d’un conteneur. Pour plus d’informations, consultez [le flux de modification](change-feed.md). | Oui | Non  | Non  | Non  | Oui |
|uniqueKeyPolicy | Configurables par l’utilisateur | Permet de garantir l’unicité d’une ou plusieurs valeurs dans une partition logique. Pour plus d’informations, consultez [contraintes de clé Unique](unique-keys.md). | Oui | Non  | Non  | Non  | Oui |

### <a name="operations-on-an-azure-cosmos-container"></a>Actions effectuées sur un conteneur Azure Cosmos

Un conteneur Azure Cosmos prend en charge les opérations suivantes lorsque vous utilisez les API Azure Cosmos :

| Opération | Azure CLI | API SQL | API Cassandra | API Azure Cosmos DB pour MongoDB | API Gremlin | API de table |
| --- | --- | --- | --- | --- | --- | --- |
| Énumérer les conteneurs d’une base de données | Oui | OUI | OUI | Oui | N/D | N/D |
| Lire le contenu d’un conteneur | Oui | OUI | OUI | Oui | N/D | N/D |
| Créer un conteneur | Oui | OUI | OUI | Oui | N/D | N/D |
| Mettre à jour d’un conteneur | Oui | OUI | OUI | Oui | N/D | N/D |
| Supprimer un conteneur | Oui | OUI | OUI | Oui | N/D | N/D |

## <a name="azure-cosmos-items"></a>Éléments Azure Cosmos

En fonction de l’API que vous utilisez, un élément d’Azure Cosmos peut représenter soit un document dans une collection, une ligne dans une table, ou un nœud ou arêtes dans un graphique. Le tableau suivant montre le mappage d’entités d’API spécifiques à un élément d’Azure Cosmos :

| Entité de COSMOS | API SQL | API Cassandra | API Azure Cosmos DB pour MongoDB | API Gremlin | API de table |
| --- | --- | --- | --- | --- | --- |
|Élément Azure Cosmos | Document | Ligne | Document | Nœuds ou arêtes | Item |

### <a name="properties-of-an-item"></a>Propriétés d’un élément

Chaque élément d’Azure Cosmos a les propriétés suivantes définies par le système. En fonction de l’API que vous utilisez, certains d'entre eux ne pas être directement exposé.

| Propriété définie par le système | Configurables par l’utilisateur ou générée par le système| Objectif | API SQL | API Cassandra | API Azure Cosmos DB pour MongoDB | API Gremlin | API de table |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | Généré par le système | Identificateur unique de l’élément | Oui | Non  | Non  | Non  | Non |
|\_etag | Généré par le système | Étiquette d’entité utilisée pour le contrôle de l’accès concurrentiel optimiste | Oui | Non  | Non  | Non  | Non |
|\_TS | Généré par le système | Horodatage de la dernière mise à jour de l’élément | Oui | Non  | Non  | Non  | Non |
|\_self | Généré par le système | URI adressable de l’élément | Oui | Non  | Non  | Non  | Non |
|id | Vous pouvez soit utiliser | Nom unique défini par l’utilisateur dans une partition logique. Si l’utilisateur ne spécifie pas l’ID, le système génère automatiquement un. | Oui | OUI | OUI | OUI | Oui |
|Propriétés arbitraires définies par l’utilisateur | Défini par l'utilisateur | Propriétés définies par l’utilisateur représentées dans la représentation sous forme de l’API natives (y compris JSON, BSON et CQL) | Oui | OUI | OUI | OUI | Oui |

### <a name="operations-on-items"></a>Actions possibles sur les éléments

Éléments Cosmos Azure prend en charge les opérations suivantes. Vous pouvez utiliser les API Azure Cosmos pour effectuer les opérations.

| Opération | Azure CLI | API SQL | API Cassandra | API Azure Cosmos DB pour MongoDB | API Gremlin | API de table |
| --- | --- | --- | --- | --- | --- | --- |
| Insérer, remplacer, supprimer, upsert, lire | Non | OUI | OUI | OUI | OUI | OUI |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur ces concepts et tâches :

* [Approvisionner le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md)
* [Approvisionner le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md)
* [Travailler avec des partitions logiques](partition-data.md)
* [Configurer la durée de vie sur un conteneur Azure Cosmos](how-to-time-to-live.md)
* [Créer des applications réactives à l’aide de flux de modification](change-feed.md)
* [Configurer une contrainte de clé unique sur votre conteneur Azure Cosmos](unique-keys.md)
