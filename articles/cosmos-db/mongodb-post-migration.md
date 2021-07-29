---
title: Étapes d’optimisation post-migration avec l’API Azure Cosmos DB pour MongoDB
description: Ce document fournit les techniques d’optimisation post-migration de MongoDB vers l’API Azure Cosmos DB pour Mongo DB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 05/19/2021
ms.author: chrande
ms.openlocfilehash: a0e072df074da5df238ab6047ee659c91ff3fdde
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110471155"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Étapes d’optimisation post-migration lors de l’utilisation de l’API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> Veuillez lire ce guide dans son intégralité avant de suivre les étapes postmigration.
>

Ce guide de postmigration de MongoDB fait partie d'une série consacrée à la migration de MongoDB. Les principales étapes de migration de MongoDB sont la [prémigration](mongodb-pre-migration.md), la migration proprement dite et la postmigration, comme indiqué ci-dessous.

![Schéma des étapes de migration.](./media/mongodb-pre-migration/overall-migration-steps.png)

## <a name="overview-of-post-migration"></a>Présentation de la postmigration

Après avoir migré les données stockées dans la base de données MongoDB vers l’API Azure Cosmos DB pour MongoDB, vous pouvez vous connecter à Azure Cosmos DB et gérer les données. Ce guide fournit les étapes que vous pouvez effectuer après la migration. Pour connaître les étapes de migration, consultez le tutoriel [Migrer MongoDB vers l’API Azure Cosmos DB pour MongoDB](../dms/tutorial-mongodb-cosmos-db.md).

Suivez les étapes ci-dessous pour effectuer une postmigration :

- [Optimiser la stratégie d’indexation](#optimize-the-indexing-policy)
- [Configurer la distribution mondiale à l’aide de l’API Azure Cosmos DB pour MongoDB](#globally-distribute-your-data)
- [Définir le niveau de cohérence](#set-consistency-level)
- [Connecter (basculer) votre application](#connect-or-cutover-your-application)
- [Ajuster pour bénéficier de performances optimales](#tune-for-optimal-performance)

> [!NOTE]
> La seule étape obligatoire après la migration consiste à modifier la chaîne de connexion dans votre application pour qu'elle pointe vers votre nouveau compte Azure Cosmos DB. Toutes les autres étapes postmigration sont des optimisations recommandées pour améliorer les performances de la couche de données. *Toutefois*, si vous procédez immédiatement au basculement de l'application sans passer par les autres étapes, votre application verra immédiatement l'impact d'une indexation et d'une cohérence non optimales. Plus précisément, si vous procédez au basculement avant de configurer vos index, l'application peut voir une baisse immédiate de prix/performances. Sachez que ce problème peut être résolu – [une fois l'index optimisé](#optimize-the-indexing-policy), Azure Cosmos DB devrait souvent surpasser la solution de statu quo en termes de prix et de performances.
>

## <a name="pre-requisites"></a>Conditions préalables

Dans ce guide, nous supposons que vous tenez un registre de la progression de votre migration à l'aide d'un document de suivi tel qu'une feuille de calcul. Si ce n'est déjà fait, nous vous recommandons de lire le [Guide de prémigration](mongodb-pre-migration.md) afin d'obtenir des conseils sur la création d'une *feuille de calcul de migration du patrimoine de données*, la découverte de vos ressources MongoDB existantes et la planification de votre migration.

## <a name="optimize-the-indexing-policy"></a>Optimiser la stratégie d’indexation

Afin d'optimiser le prix et les performances, nous vous recommandons de passer en revue votre feuille de calcul de migration du patrimoine de données et de concevoir une configuration d'index pour chaque ressource. 
1. Nous vous recommandons en fait de [planifier vos index pendant la phase de prémigration](mongodb-pre-migration.md#post-migration). Ajoutez une colonne à votre feuille de calcul de migration du patrimoine de données pour les paramètres d'index. 
   * L'API Azure Cosmos DB pour les versions 3.6 et ultérieures du serveur MongoDB indexe automatiquement le champ _id (et uniquement ce champ). Ce champ ne peut pas être supprimé. Elle applique automatiquement l'unicité du champ _id par clé de partition. Pour indexer des champs supplémentaires, appliquez les commandes de gestion d’index MongoDB. Cette stratégie d’indexation par défaut est différente de celle de l’API SQL Azure Cosmos DB, qui indexe tous les champs par défaut.

   * Avec l'API Azure Cosmos DB pour la version 3.2 du serveur MongoDB, tous les champs de données sont automatiquement indexés par défaut lors de la migration des données vers Azure Cosmos DB. Dans de nombreux cas, cette stratégie d’indexation par défaut est acceptable. En général, la suppression des index optimise les requêtes d’écriture, et l’application de la stratégie d’indexation par défaut (autrement dit, l’indexation automatique) optimise les requêtes de lecture.

   * Les capacités d’indexation fournies par Azure Cosmos DB incluent l’ajout d’index composites, d’index uniques et d’index de durée de vie (index TTL). L'interface de gestion des index est mappée avec la commande createIndex(). Pour en savoir plus, consultez Indexation dans Azure Cosmos DB et Indexation dans l'API Azure Cosmos DB pour MongoDB.
2. Appliquez ces paramètres d'index pendant la postmigration.
   * [Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) migre automatiquement les collections MongoDB avec des index uniques. Toutefois, les index uniques doivent être créés avant la migration. Azure Cosmos DB ne prend pas en charge la création d’index uniques quand il existe déjà des données dans vos collections. Pour plus d’informations, consultez [Clés uniques dans Azure Cosmos DB](unique-keys.md).

## <a name="globally-distribute-your-data"></a>Distribuer vos données dans le monde

Azure Cosmos DB est disponible dans toutes les [régions Azure](https://azure.microsoft.com/regions/#services) à travers le monde. 
1. Suivez les instructions de l'article [Distribuer les données à l'échelle mondiale sur l'API Azure Cosmos DB pour MongoDB](tutorial-global-distribution-mongodb.md) afin de distribuer vos données dans le monde entier. Après avoir sélectionné le niveau de cohérence par défaut pour votre compte Azure Cosmos DB, vous pouvez associer une ou plusieurs régions Azure (en fonction de vos besoins de distribution mondiale). Pour une haute disponibilité et une continuité d’activité, nous recommandons toujours une exécution dans au moins deux régions. Vous pouvez consulter les conseils d’[optimisation du coût des déploiements dans plusieurs régions dans Azure Cosmos DB](optimize-cost-regions.md).

## <a name="set-consistency-level"></a>Définir le niveau de cohérence

Azure Cosmos DB offre cinq [niveaux de cohérence](consistency-levels.md) bien définis. Pour en savoir plus sur les correspondances entre les niveaux de cohérence MongoDB et Azure Cosmos DB, consultez [Niveaux de cohérence et API Azure Cosmos DB](./consistency-levels.md). Le niveau de cohérence par défaut est le niveau de cohérence de la session. Le changement du niveau de cohérence est facultatif, et vous pouvez l’optimiser pour votre application. Pour changer le niveau de cohérence à l’aide du portail Azure :

1. Accédez au panneau **Cohérence par défaut** sous Paramètres.
2. Sélectionnez votre [niveau de cohérence](consistency-levels.md)

La plupart des utilisateurs laissent le niveau de cohérence sur le paramètre de cohérence de session par défaut. Toutefois, il existe des [compromis entre disponibilité et performances pour différents niveaux de cohérence](./consistency-levels.md).

## <a name="connect-or-cutover-your-application"></a>Connecter ou basculer votre application

Le traitement du basculement ou de la connexion de votre application vous permet de basculer votre application pour qu'elle utilise Azure Cosmos DB une fois la migration terminée. Effectuez les étapes ci-dessous :

1. Dans une nouvelle fenêtre, connectez-vous au [portail Azure](https://www.portal.azure.com/).
2. Dans le [portail Azure](https://www.portal.azure.com/), dans le volet gauche, ouvrez le menu **Toutes les ressources** et recherchez le compte Azure Cosmos DB vers lequel vous avez migré vos données.
3. Ouvrez le panneau **Chaîne de connexion**. Le volet droit contient toutes les informations dont vous avez besoin pour vous connecter à votre compte.
4. Utilisez les informations de connexion dans la configuration de votre application (ou d’autres emplacements pertinents) afin de refléter la connexion de l’API Azure Cosmos DB pour MongoDB dans votre application.
:::image type="content" source="./media/mongodb-post-migration/connection-string.png" alt-text="Capture d’écran montrant les paramètres d’une chaîne de connexion.":::

Pour plus d’informations, consultez la page [Connecter une application MongoDB à Azure Cosmos DB](connect-mongodb-account.md).

## <a name="tune-for-optimal-performance"></a>Ajuster pour bénéficier de performances optimales

L'[indexation](#optimize-the-indexing-policy), la [distribution à l'échelle mondiale](#globally-distribute-your-data) et la [cohérence](#set-consistency-level) peuvent toutes être facilement configurées et reconfigurées en fonction de vos besoins. Par conséquent, après avoir basculé votre application, nous vous recommandons de surveiller ses performances et, si nécessaire, d'ajuster ces paramètres en fonction de ses exigences.

## <a name="next-steps"></a>Étapes suivantes

* [Connecter une application MongoDB à Azure Cosmos DB](connect-mongodb-account.md)
* [Se connecter à un compte Azure Cosmos DB à l’aide de Studio 3T](mongodb-mongochef.md)
* [Guide pratique pour distribuer à l’échelle mondiale des lectures par l’intermédiaire de l’API Azure Cosmos DB pour MongoDB](mongodb-readpreference.md)
* [Faire expirer des données avec l’API Azure Cosmos DB pour MongoDB](mongodb-time-to-live.md)
* [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md)
* [Indexation dans Azure Cosmos DB](index-overview.md)
* [Unités de requête dans Azure Cosmos DB](request-units.md)