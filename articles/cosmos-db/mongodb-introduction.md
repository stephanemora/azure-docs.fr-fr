---
title: Introduction à l’API Azure Cosmos DB pour MongoDB
description: Découvrez comment utiliser Azure Cosmos DB pour stocker et interroger de grandes quantités de données à l’aide de l’API Azure Cosmos DB pour MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 04/22/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: ead8bf6620bbe53af6c28870fa94b7a16490fcb1
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202782"
---
# <a name="azure-cosmos-db-api-for-mongodb"></a>API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

L’API Azure Cosmos DB pour MongoDB permet d’utiliser facilement Cosmos DB comme s’il s’agissait d’une base de données MongoDB. Vous pouvez tirer parti de votre expérience MongoDB et continuer à utiliser vos pilotes, kits SDK et outils MongoDB préférés en faisant pointer votre application sur la chaîne de connexion du compte de l’API pour MongoDB.

## <a name="why-choose-the-api-for-mongodb"></a>Pourquoi choisir l’API pour MongoDB

Reposant sur [Azure Cosmos DB](introduction.md), l’API pour MongoDB présente de nombreux avantages par rapport aux offres de service telles que MongoDB Atlas :

* **Scalabilité instantanée** : En activant la fonctionnalité de [mise à l’échelle automatique](provision-throughput-autoscale.md), votre base de données peut effectuer un scale-up/scale-down avec une période de préparation nulle.
* **Partitionnement automatique et transparent** : l’API pour MongoDB gère l’ensemble de l’infrastructure pour vous. Cela comprend le partitionnement et le nombre de partitions, contrairement à d’autres offres MongoDB comme MongoDB Atlas qui vous demandent de spécifier et de gérer le partitionnement pour une mise à l’échelle horizontale. Vous avez ainsi plus de temps pour vous concentrer sur le développement d’applications pour vos utilisateurs.
* **Une disponibilité à cinq 9** : Une [disponibilité de 99,999 %](high-availability.md) est facilement configurable pour garantir que vos données seront toujours là pour vous.  
* **Scalabilité économique, précise et illimitée** : les collections partitionnées peuvent être mises à l’échelle à n’importe quelle taille, contrairement aux autres offres de service MongoDB. Les utilisateurs de l’API pour MongoDB exécutent des bases de données avec plus de 600 To de stockage aujourd’hui. La mise à l’échelle s’effectue de façon économique, puisque contrairement à d’autres offres de service MongoDB, la plateforme Cosmos DB peut évoluer par incréments aussi petits que 1/100e de machine virtuelle en raison des économies d’échelle et de la gouvernance des ressources.
* **Déploiements serverless** : contrairement à MongoDB Atlas, l’API pour MongoDB est une base de données native cloud qui offre un [mode de capacité serverless](serverless.md). Avec le mode [serverless](serverless.md), vous êtes facturé uniquement par opération et ne payez pas pour la base de données quand vous ne l’utilisez pas.
* **Niveau gratuit** : avec le niveau gratuit Azure Cosmos DB, vous recevez gratuitement les premiers 400 RU/s et 5 Go de stockage dans votre compte sans limite de durée, appliqués au niveau du compte.
* **Mises à niveau en quelques secondes** : Toutes les versions d’API sont contenues dans un seul codebase, ce qui rend le changement de versions aussi simple que d’[appuyer sur un bouton](mongodb-version-upgrade.md), sans temps d’arrêt.
* **Analytique en temps réel (HTAP) à n’importe quelle échelle** : l’API pour MongoDB offre la possibilité d’exécuter des requêtes analytiques complexes pour des cas d’usage tels que le décisionnel sur les données de votre base de données en temps réel, sans aucun impact sur cette dernière. Cette fonctionnalité est rapide et économique, en raison de l’utilisation du magasin analytique natif cloud en colonnes, sans pipelines ETL. Découvrez-en plus sur [Azure Synapse Link](synapse-link.md).

> [!NOTE]
> [Vous pouvez utiliser l’API Azure Cosmos DB pour MongoDB gratuitement avec le niveau gratuit.](how-pricing-works.md) Avec le niveau gratuit Azure Cosmos DB, vous recevez gratuitement les premiers 400 RU/s et 5 Go de stockage dans votre compte, appliqués au niveau du compte.


## <a name="how-the-api-works"></a>Comment fonctionne l’API

L’API Azure Cosmos DB pour MongoDB implémente le protocole filaire pour MongoDB. Cette implémentation permet une compatibilité transparente avec les outils, les pilotes et les kits SDK clients MongoDB natifs. Azure Cosmos DB n’héberge pas le moteur de base de données MongoDB. Tout pilote client MongoDB compatible avec la version d’API que vous utilisez doit être en mesure de se connecter, sans configuration spéciale.

Compatibilité des fonctionnalités MongoDB :

L’API Azure Cosmos DB pour MongoDB est compatible avec les versions suivantes du serveur MongoDB :
- [Version 4.0](mongodb-feature-support-40.md)
- [Version 3.6](mongodb-feature-support-36.md)
- [Version 3.2](mongodb-feature-support.md)

Toutes les versions de l’API pour MongoDB s’exécutent sur le même codebase. Ainsi, les mises à niveau constituent une tâche simple pouvant être effectuée en quelques secondes sans temps d’arrêt. Azure Cosmos DB inverse simplement quelques indicateurs de fonctionnalité pour passer d’une version à une autre.  Les indicateurs de fonctionnalité permettent également la prise en charge continue d’anciennes versions d’API, telles que 3.2 et 3.6. Vous pouvez choisir la version de serveur qui vous convient le mieux.

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="API pour MongoDB d’Azure Cosmos DB" border="false":::

## <a name="what-you-need-to-know-to-get-started"></a>Éléments à savoir pour commencer

* Les machines virtuelles figurant dans un cluster ne vous sont pas facturées. Les [tarifs](how-pricing-works.md) sont basés sur le débit dans les unités de requête (RU) configurées par base de données ou par collection. Les 400 premières RU par seconde sont gratuites avec le [niveau gratuit](how-pricing-works.md).

* Il existe trois façons de déployer l’API Azure Cosmos DB pour MongoDB :
     * [Débit provisionné](set-throughput.md) : Définissez un nombre de RU/s et changez-le manuellement. Ce modèle est le mieux adapté aux charges de travail régulières.
     * [Mise à l’échelle automatique](provision-throughput-autoscale.md) : Définissez une limite supérieure pour le débit dont vous avez besoin. Le débit est instantanément mis à l’échelle selon vos besoins. Ce modèle est le mieux adapté aux charges de travail qui changent fréquemment, et optimise leurs coûts.
     * [Serverless](serverless.md) (préversion) : Payez uniquement pour le débit que vous utilisez. Ce modèle est le mieux adapté aux charges de travail de développement et de test. 

* Les performances d’un cluster partitionné dépendent de la clé de partition que vous choisissez lors de la création d’une collection. Choisissez une clé de partition avec soin pour vous assurer que vos données seront réparties uniformément entre les partitions.

## <a name="quickstart"></a>Démarrage rapide

* [Migrer une application web Node.js MongoDB existante](create-mongodb-nodejs.md).
* [Générer une application web en utilisant l’API Azure Cosmos DB pour MongoDB et le kit de développement logiciel (SDK) .NET](create-mongodb-dotnet.md)
* [Générer une application console en utilisant l’API Azure Cosmos DB pour MongoDB et le kit de développement logiciel (SDK) Java](create-mongodb-java.md)

## <a name="next-steps"></a>Étapes suivantes

* Pour savoir comment obtenir les informations de chaîne de connexion de votre compte, suivez le didacticiel [Connecter une application MongoDB à Azure Cosmos DB](connect-mongodb-account.md).
* Pour savoir comment créer une connexion entre votre base de données Cosmos et l’application MongoDB dans Studio 3T, suivez le didacticiel [Utiliser Studio 3T avec Azure Cosmos DB](mongodb-mongochef.md).
* Pour importer vos données vers une base de données Cosmos, suivez le didacticiel [Importer des données MongoDB dans Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json).
* Connectez-vous à un compte Cosmos à l’aide de [Robo 3T](mongodb-robomongo.md).
* Découvrez comment [configurer des préférences de lecture pour les applications distribuées dans le monde entier](../cosmos-db/tutorial-global-distribution-mongodb.md).
* Recherchez les solutions aux erreurs couramment détectées dans notre [guide de résolution des problèmes](mongodb-troubleshoot.md)


<sup>Remarque : Cet article décrit une fonctionnalité d’Azure Cosmos DB qui assure la compatibilité des protocoles de transmission avec les bases de données MongoDB. Microsoft n’exécute pas les bases de données MongoDB pour fournir ce service. Azure Cosmos DB n’est pas affilié à MongoDB, Inc.</sup>