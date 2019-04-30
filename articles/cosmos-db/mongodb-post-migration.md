---
title: Étapes de post-migration optimisation lors de l’utilisation des API d’Azure Cosmos DB pour MongoDB
description: Ce document fournit les techniques d’optimisation d’après la migration à partir de MongoDB à Azure Cosmos DB APi pour Mongodb.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c0c761fef481a1fdaa027f1329e9a4e72d62985a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331208"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Étapes de post-migration optimisation lors de l’utilisation des API d’Azure Cosmos DB pour MongoDB 

Après avoir migré les données stockées dans la base de données MongoDB vers l’API Azure Cosmos DB pour MongoDB, vous pouvez vous connecter à Azure Cosmos DB et gérer les données. Ce guide fournit les étapes que vous devez envisager après la migration. Consultez le [migrer MongoDB à Azure Cosmos DB : API pour MongoDB didacticiel](../dms/tutorial-mongodb-cosmos-db.md) pour connaître les étapes de migration.

Dans ce guide, vous allez apprendre à :
- [Connecter votre application](#connect-account)
- [Optimiser la stratégie d’indexation](#indexing)
- [Configurer la distribution mondiale pour les API d’Azure Cosmos DB pour MongoDB](#distribute-data)
- [Définir le niveau de cohérence](#consistency)

> [!NOTE]
> L’étape post-migration uniquement obligatoire de votre niveau d’application change la chaîne de connexion dans votre application pour pointer vers votre nouveau compte Azure Cosmos DB. Toutes les autres étapes de migration sont recommandées des optimisations.
>

## <a id="connect-account"></a>Connecter votre application 

1. Dans une nouvelle fenêtre de connexion dans le [portail Azure](https://www.portal.azure.com/)
2. À partir de la [portail Azure](https://www.portal.azure.com/), dans le volet gauche ouvert le **toutes les ressources** menu et recherchez le compte Azure Cosmos DB à laquelle vous avez migré vos données.
3. Ouvrez le **chaîne de connexion** panneau. Le volet droit contient toutes les informations dont vous avez besoin pour vous connecter à votre compte.
4. Utilisez les informations de connexion dans la configuration de votre application (ou d’autres emplacements pertinents) afin de refléter le d’Azure Cosmos DB API pour la connexion de MongoDB dans votre application. 
![Chaîne de connexion](./media/mongodb-post-migration/connection-string.png)

Pour plus d’informations, consultez le [connecter une application MongoDB à Azure Cosmos DB](connect-mongodb-account.md) page.

## <a id="indexing"></a>Optimiser la stratégie d’indexation

Tous les champs de données sont automatiquement indexés par défaut, lors de la migration de données à Azure Cosmos DB. Dans de nombreux cas, cette valeur par défaut de stratégie d’indexation est acceptable. En général, la suppression d’index optimise les requêtes d’écriture et avoir la valeur par défaut (par exemple, l’indexation automatique) de la stratégie d’indexation optimise les demandes de lecture.

Pour plus d’informations sur l’indexation, consultez [l’indexation de données dans l’API d’Azure Cosmos DB pour MongoDB](mongodb-indexing.md) ainsi que le [l’indexation dans Azure Cosmos DB](index-overview.md) articles.

## <a id="distribute-data"></a>Distribuer globalement vos données

Azure Cosmos DB est disponible dans toutes les [régions Azure](https://azure.microsoft.com/regions/#services) dans le monde entier. Après avoir sélectionné le niveau de cohérence par défaut pour votre compte Azure Cosmos DB, vous pouvez associer une ou plusieurs régions Azure (selon vos besoins de la distribution mondiale). Pour une haute disponibilité et continuité d’activité, nous recommandons toujours en cours d’exécution dans les régions au moins 2. Vous pouvez consulter les conseils pour [optimiser le coût des déploiements dans plusieurs régions dans Azure Cosmos DB](optimize-cost-regions.md).

Pour distribuer globalement vos données, consultez [distribuer des données dans le monde entier sur les API d’Azure Cosmos DB pour MongoDB](tutorial-global-distribution-mongodb.md). 

## <a id="consistency"></a>Définir le niveau de cohérence
Azure Cosmos DB offre bien défini de 5 [niveaux de cohérence](consistency-levels.md). Pour en savoir plus sur le mappage entre les niveaux de cohérence MongoDB et Azure Cosmos DB, lire [niveaux de cohérence et des API Azure Cosmos DB](consistency-levels-across-apis.md). Le niveau de cohérence par défaut est le niveau de cohérence de session. Modification du niveau de cohérence est facultative et vous pouvez l’optimiser pour votre application. Pour modifier le niveau de cohérence à l’aide du portail Azure :

1. Accédez à la **cohérence par défaut** panneau, sous paramètres.
2. Sélectionnez votre [niveau de cohérence](consistency-levels.md)

La plupart des utilisateurs laisser leur niveau de cohérence sur le paramètre de cohérence de session par défaut. Toutefois, il existe [compromis de performances et disponibilité pour différents niveaux de cohérence](consistency-levels-tradeoffs.md). 

## <a name="next-steps"></a>Étapes suivantes

* [Connecter une application MongoDB à Azure Cosmos DB](connect-mongodb-account.md)
* [Se connecter au compte Azure Cosmos DB à l’aide de Studio 3 t](mongodb-mongochef.md)
* [Comment distribuer globalement des lectures à l’aide des API d’Azure Cosmos DB pour MongoDB](mongodb-readpreference.md)
* [Faire expirer les données avec l’API d’Azure Cosmos DB pour MongoDB](mongodb-time-to-live.md)
* [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md)
* [Indexation dans Azure Cosmos DB](index-overview.md)
* [Unités de requête dans Azure Cosmos DB](request-units.md)





