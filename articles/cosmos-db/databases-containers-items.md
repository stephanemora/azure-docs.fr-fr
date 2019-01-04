---
title: Utilisation des bases de données, des conteneurs et des éléments Azure Cosmos DB
description: Cet article explique comment créer et utiliser des bases de données, des conteneurs et des éléments Azure Cosmos DB.
author: dharmas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 39de7453c9d3b0335748cd37e4b1eef91b64b207
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409539"
---
# <a name="working-with-azure-cosmos-databases-containers-and-items"></a>Utilisation des bases de données, des conteneurs et des éléments Azure Cosmos

La création d’un [compte Azure Cosmos DB](account-overview.md) dans votre abonnement Azure vous permet d’y gérer des données à l’aide de bases de données, de conteneurs et d’éléments. Cet article décrit chacune des entités suivantes : bases de données, conteneurs et éléments. L’illustration suivante montre la hiérarchie de ces différentes entités dans un compte Azure Cosmos :

![Entités du compte Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Bases de données Azure Cosmos

Vous pouvez créer une ou plusieurs bases de données Azure Cosmos dans votre compte. Une base de données est comparable à un espace de noms. En effet, elle agit comme l’unité de gestion d’un ensemble de conteneurs Azure Cosmos. Le tableau suivant montre comment une base de données Azure Cosmos est mappée à différentes entités d’API :

| **Entité Azure Cosmos** | **API SQL** | **API Cassandra** | **API MongoDB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- |
|Base de données Azure Cosmos | Base de données | Espace de clés | Base de données | Base de données | N/D |

> [!NOTE]
> Avec les comptes d’API Table, lorsque vous créez votre première table, une base de données par défaut est automatiquement créée dans votre compte Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Actions effectuées sur une base de données Azure Cosmos

Vous pouvez interagir avec une base de données Azure Cosmos à l’aide des API Azure Cosmos suivantes :

| **opération** | **Interface de ligne de commande Azure**|**API SQL** | **API Cassandra** | **API MongoDB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- | --- |
|Énumérer toutes les bases de données| Oui | Oui | Oui (la base de données est mappée sur un espace de clés) | Oui | N/D | N/D |
|Lire le contenu de la base de données| Oui | Oui | Oui (la base de données est mappée sur un espace de clés) | Oui | N/D | N/D |
|Create new database| Oui | Oui | Oui (la base de données est mappée sur un espace de clés) | Oui | N/D | N/D |
|Mettre à jour la base de données| Oui | Oui | Oui (la base de données est mappée sur un espace de clés) | Oui | N/D | N/D |


## <a name="azure-cosmos-containers"></a>Conteneurs Cosmos Azure

Un conteneur Azure Cosmos correspond à l’unité de scalabilité des éléments de débit et de stockage provisionnés. Un conteneur est partitionné horizontalement, puis répliqué dans plusieurs régions. Les éléments que vous ajoutez au conteneur, et le débit que vous provisionnez pour celui-ci, sont automatiquement distribués sur un ensemble de partitions logiques basées sur la clé de partition. Pour plus d’informations sur le partitionnement et la clé de partition, consultez l’article [Partitions logiques](partition-data.md). 

Lorsque vous créez un conteneur Azure Cosmos, vous configurez le débit avec l’un des modes suivants :

* Mode **Débit provisionné dédié** : le débit provisionné pour un conteneur lui est exclusivement réservé ; ce débit est défini par les contrats SLA. Pour plus d’informations, consultez [Provisionnement du débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).

* Mode **Débit provisionné partagé** : ces conteneurs partagent le débit provisionné avec d’autres conteneurs de la même base de données (à l’exclusion des conteneurs qui ont été configurés pour le débit provisionné dédié). En d’autres termes, le débit provisionné pour la base de données est partagé entre tous les conteneurs « partagés ». Pour plus d’informations, consultez [Comment configurer le débit provisionné pour une base de données Azure Cosmos](how-to-provision-database-throughput.md).

Les conteneurs Azure Cosmos sont élastiques, qu’ils aient un débit « partagé » ou « dédié ».

Un conteneur Azure Cosmos est un conteneur d’éléments indépendant de tout schéma. Les éléments d’un conteneur peuvent avoir des schémas différents. Par exemple, un élément représentant une personne et un élément représentant une voiture peuvent être placés dans le même conteneur. Par défaut, tous les éléments que vous ajoutez à un conteneur sont automatiquement indexés, sans que vous ayez à effectuer une gestion explicite d’index ou de schéma. Vous pouvez personnaliser le comportement d’indexation en configurant la stratégie d’indexation du conteneur. 

Vous pouvez définir la durée de vie (TTL) de certains éléments du conteneur Azure Cosmos ou de l’intégralité du conteneur afin de supprimer définitivement ces éléments du système. Azure Cosmos DB supprime automatiquement les éléments lorsque ceux-ci expirent. Cela permet également d’éviter qu’une requête envoyée au conteneur ne retourne des éléments ayant expiré. Pour plus d’informations, consultez [Guide pratique pour configurer la durée de vie de votre conteneur](how-to-time-to-live.md).

À l’aide du flux de modification, vous pouvez vous abonner au journal des opérations de chacune des partitions logiques de votre conteneur. Le flux de modification fournit le journal de toutes les mises à jour appliquées au conteneur ainsi que les images des éléments avant et après mise à jour. Consultez [Comment créer des applications réactives à l’aide du flux de modification](change-feed.md). Vous pouvez également configurer la durée de conservation du flux de modification à l’aide de la stratégie de flux de modification du conteneur. 

Vous pouvez inscrire des procédures stockées, des déclencheurs, des fonctions définies par l’utilisateur et des procédures de fusion dans votre conteneur Azure Cosmos. 

Vous pouvez spécifier une clé unique dans votre conteneur Azure Cosmos. En créant une stratégie de clé unique, vous garantissez l’unicité d’une ou de plusieurs valeurs par clé de partition logique. Une fois qu’un conteneur a été créé avec une stratégie de clé unique, il empêche la création de tout nouvel élément ou de tout élément mis à jour avec des valeurs dupliquant des valeurs spécifiées par la contrainte de clé unique. Pour plus d’informations, consultez [Contraintes de clés uniques](unique-keys.md).

Un conteneur Azure Cosmos est spécialisé dans les entités d’API :

| **Entité Azure Cosmos** | **API SQL** | **API Cassandra** | **API MongoDB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- |
|Conteneur Azure Cosmos | Collection | Table | Collection | Graph | Table |

### <a name="properties-of-an-azure-cosmos-container"></a>Propriétés d’un conteneur Azure Cosmos

Un conteneur Azure Cosmos comprend un ensemble de propriétés définies par le système. En fonction de l’API, certaines de ces propriétés peuvent ne pas être exposées directement. Le tableau suivant répertorie les propriétés définies par le système qui sont prises en charge :

| **Propriété définie par le système** | **Générée par le système ou paramétrable par l’utilisateur** | **Objectif** | **API SQL** | **API Cassandra** | **API MongoDB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|__rid | Générée par le système | Identificateur unique du conteneur | Oui | Non  | Non  | Non  | Non  |
|__etag | Générée par le système | Étiquette d’entité utilisée pour le contrôle de l’accès concurrentiel optimiste | Oui | Non  | Non  | Non  | Non  |
|__ts | Générée par le système | Dernière mise à jour de l’horodatage du conteneur | Oui | Non  | Non  | Non  | Non  |
|__self | Générée par le système | URI adressable du conteneur | Oui | Non  | Non  | Non  | Non  |
|id | Configurable par l’utilisateur | Nom unique du conteneur défini par l’utilisateur | Oui | OUI | OUI | OUI | Oui |
|indexingPolicy | Configurable par l’utilisateur | Permet de modifier le chemin de l’index, sa précision et son modèle de cohérence. | Oui | Non  | Non  | Non  | Oui |
|TimeToLive | Configurable par l’utilisateur | Permet de supprimer automatiquement les éléments d’un conteneur après une période déterminée. Pour plus d’informations, consultez l’article [Durée de vie](time-to-live.md). | Oui | Non  | Non  | Non  | Oui |
|changeFeedPolicy | Configurable par l’utilisateur | Utilisé pour lire les modifications apportées aux éléments d’un conteneur. Pour plus d’informations, consultez l’article [Flux de modification](change-feed.md). | Oui | Non  | Non  | Non  | Oui |
|uniqueKeyPolicy | Configurable par l’utilisateur | Avec les clés uniques, vous garantissez l’unicité d’une ou plusieurs valeurs au sein d’une partition logique. Pour plus d’informations, consultez l’article [Clés uniques](unique-keys.md). | Oui | Non  | Non  | Non  | Oui |

### <a name="operations-on-an-azure-cosmos-container"></a>Actions effectuées sur un conteneur Azure Cosmos

Un conteneur Azure Cosmos prend en charge les opérations suivantes, qui peuvent être effectuées avec l’une des API Azure Cosmos.

| **opération** | **Interface de ligne de commande Azure** | **API SQL** | **API Cassandra** | **API MongoDB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Énumérer les conteneurs d’une base de données | Oui* | Oui | OUI | Oui | N/D | N/D |
| Lire le contenu d’un conteneur | Oui | OUI | OUI | Oui | N/D | N/D |
| Créer un conteneur | Oui | OUI | OUI | Oui | N/D | N/D |
| Mettre à jour un conteneur | Oui | OUI | OUI | Oui | N/D | N/D |
| Supprimer un conteneur | Oui | OUI | OUI | Oui | N/D | N/D |

## <a name="azure-cosmos-items"></a>Éléments Azure Cosmos

En fonction de l’API, un élément Azure Cosmos peut représenter un document d’une collection, une ligne de table, un nœud de graphe ou une arête de graphe. Le tableau suivant présente le mappage d’entités d’API à un élément Azure Cosmos :

| **Entité Cosmos** | **API SQL** | **API Cassandra** | **API MongoDB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- |
|Élément Azure Cosmos | Document | Ligne | Document | Nœud ou arête | Item |

### <a name="properties-of-an-item"></a>Propriétés d’un élément

Chaque élément Azure Cosmos comprend des propriétés définies par le système. En fonction de l’API, certaines de ces propriétés peuvent ne pas être exposées directement.

|**Propriété définie par le système** | **Générée par le système ou paramétrable par l’utilisateur**| **Objectif** | **API SQL** | **API Cassandra** | **API MongoDB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|__id | Générée par le système | Identificateur unique d’un élément | Oui | Non  | Non  | Non  | Non  |
|__etag | Générée par le système | Étiquette d’entité utilisée pour le contrôle de l’accès concurrentiel optimiste | Oui | Non  | Non  | Non  | Non  |
|__ts | Générée par le système | Dernière mise à jour de l’horodatage de l’élément | Oui | Non  | Non  | Non  | Non  |
|__self | Générée par le système | URI adressable de l’élément | Oui | Non  | Non  | Non  | Non  |
|id | Vous pouvez soit utiliser | Nom unique défini par l’utilisateur au sein d’une partition logique. Si l’utilisateur ne spécifie pas d’ID, le système en génère un automatiquement. | Oui | OUI | OUI | OUI | Oui |
|Propriétés arbitraires définies par l’utilisateur | Défini par l’utilisateur | Propriétés définies par l’utilisateur représentées sous forme d’API native (JSON, BSON, CQL, etc.) | Oui | OUI | OUI | OUI | Oui |

### <a name="operations-on-items"></a>Actions possibles sur les éléments

Un élément Azure Cosmos prend en charge les opérations suivantes, qui peuvent être effectuées avec l’une des API Azure Cosmos.

| **opération** | **Interface de ligne de commande Azure** | **API SQL** | **API Cassandra** | **API MongoDB** | **API Gremlin** | **API de table** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Insérer, remplacer, supprimer, upsert, lire | Non  | OUI | OUI | OUI | OUI | OUI |

## <a name="next-steps"></a>Étapes suivantes

À présent, découvrez comment provisionner le débit d’un compte Azure Cosmos ou découvrez d’autres concepts :

* [Comment configurer le débit provisionné pour une base de données Azure Cosmos](how-to-provision-database-throughput.md)
* [Comment configurer le débit provisionné pour un conteneur Azure Cosmos](how-to-provision-container-throughput.md)
* [Partitions logiques](partition-data.md)
* [Comment configurer la durée de vie d’un conteneur Azure Cosmos](how-to-time-to-live.md)
* [Comment créer des applications réactives à l’aide du flux de modification](change-feed.md)
* [Comment configurer une contrainte de clé unique dans votre conteneur Azure Cosmos](unique-keys.md)
