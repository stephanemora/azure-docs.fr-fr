---
title: Introduction à l’API Azure Cosmos DB pour MongoDB
description: Découvrez comment utiliser Azure Cosmos DB pour stocker et interroger de grandes quantités de données à l’aide de l’API Azure Cosmos DB pour MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/1/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 670020d276b9d4a868f24eb4a3f522581060adca
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "72754977"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>API d’Azure Cosmos DB pour MongoDB

[Azure Cosmos DB](introduction.md) est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale pour les applications stratégiques. Azure Cosmos DB fournit la [distribution mondiale clés en main](distribute-data-globally.md), la [mise à l’échelle élastique du débit et du stockage](partition-data.md), des latences de l’ordre de quelques millisecondes dans le monde entier dans plus de 99 pour cent des cas, et une haute disponibilité garantie, le tout soutenu par nos [contrats de niveau de service de pointe](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexe automatiquement les données](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sans avoir à s’occuper de la gestion des schémas et des index. Il est multi-modèle et prend en charge les modèles de données en colonnes, documents, graphes et clé-valeur. Par défaut, vous pouvez interagir avec Cosmos DB à l’aide de l’API SQL. En outre, le service Cosmos DB implémente les protocoles de transmission des API NoSQL courantes, y compris Cassandra, MongoDB, Gremlin et Stockage Table Azure. Vous pouvez ainsi utiliser les outils et pilotes de clients NoSQL que vous connaissez pour interagir avec votre base de données Cosmos.

## <a name="wire-protocol-compatibility"></a>Compatibilité des protocoles de transmission

Azure Cosmos DB implémente les protocoles de transmission des bases de données NoSQL courantes, y compris Cassandra, MongoDB, Gremlin et Stockage Table Azure. En permettant l’implémentation native des protocoles de transmission directement et efficacement dans Cosmos DB, il simplifie les interactions entre les kits de développement logiciel, les pilotes et les outils clients existants des bases de données NoSQL et Cosmos DB. Cosmos DB n’utilise pas le code source des bases de données pour fournir des API compatibles avec le protocole de transmission des bases de données NoSQL.

Par défaut, les nouveaux comptes créés à l’aide de l’API Azure Cosmos DB pour MongoDB sont compatibles avec la version 3.6 du protocole filaire MongoDB. Les pilotes de client MongoDB comprenant cette version de protocole doivent être en mesure de se connecter de façon native à Cosmos DB.

![API d’Azure Cosmos DB pour MongoDB](./media/mongodb-introduction/cosmosdb-mongodb.png)

## <a name="key-benefits"></a>Principaux avantages

Les principaux avantages de l’utilisation de Cosmos DB comme base de données en tant que service entièrement gérée distribuée à l’échelle mondiale sont décrits [ici](introduction.md). En outre, en implémentant en mode natif des protocoles de transmission d’API NoSQL courantes, Cosmos DB offre les avantages suivants :

* Migrer facilement votre application vers Cosmos DB tout en conservant des parties importantes de la logique d’application.
* Préserver la mobilité de votre application et garder votre indépendance vis-à-vis des fournisseurs.
* Obtenez des contrats de niveau de service soutenus financièrement pour les API NoSQL courantes propulsées par Cosmos DB.
* Profitez d’une mise à l’échelle élastique du débit et du stockage approvisionnés pour vos bases de données Cosmos selon vos besoins et payez uniquement le débit et le stockage dont vous avez besoin. Vous réaliserez ainsi des économies considérables.
* Diffusion mondiale clé en main avec réplication multimaître.

## <a name="cosmos-dbs-api-for-mongodb"></a>API Cosmos DB pour MongoDB

Suivez les guides de démarrage rapide pour créer un compte Azure Cosmos et migrer votre application MongoDB existante afin d’utiliser Azure Cosmos DB, ou en générer une nouvelle :

* [Migrer une application web Node.js MongoDB existante](create-mongodb-nodejs.md).
* [Générer une application web en utilisant l’API Azure Cosmos DB pour MongoDB et le kit de développement logiciel (SDK) .NET](create-mongodb-dotnet.md)
* [Générer une application console en utilisant l’API Azure Cosmos DB pour MongoDB et le kit de développement logiciel (SDK) Java](create-mongodb-java.md)

## <a name="next-steps"></a>Étapes suivantes

Voici quelques conseils pour vous aider à démarrer :

* Pour savoir comment obtenir les informations de chaîne de connexion de votre compte, suivez le didacticiel [Connecter une application MongoDB à Azure Cosmos DB](connect-mongodb-account.md).
* Pour savoir comment créer une connexion entre votre base de données Cosmos et l’application MongoDB dans Studio 3T, suivez le didacticiel [Utiliser Studio 3T avec Azure Cosmos DB](mongodb-mongochef.md).
* Pour importer vos données vers une base de données Cosmos, suivez le didacticiel [Importer des données MongoDB dans Azure Cosmos DB](mongodb-migrate.md).
* Connectez-vous à un compte Cosmos à l’aide de [Robo 3T](mongodb-robomongo.md).
* Découvrez comment [configurer des préférences de lecture pour les applications distribuées dans le monde entier](../cosmos-db/tutorial-global-distribution-mongodb.md).

<sup>Remarque : Cet article décrit une fonctionnalité d’Azure Cosmos DB qui assure la compatibilité des protocoles de transmission avec les bases de données MongoDB. Microsoft n’exécute pas les bases de données MongoDB pour fournir ce service. Azure Cosmos DB n’est pas affilié à MongoDB, Inc.</sup>
