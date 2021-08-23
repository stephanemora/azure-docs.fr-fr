---
title: Rechercher les frais d’unités de requête pour les opérations de l’API Azure Cosmos DB pour MongoDB
description: Découvrez comment trouver les frais d’unités de requête pour les requêtes de MongoDB exécutées sur un conteneur Azure Cosmos. Vous pouvez utiliser les pilotes du portail Azure, MongoDB .NET, Java, Node.js.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.custom: devx-track-js
ms.openlocfilehash: 5b3a43805e77157b875948ca093c5f04935ffe68
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524106"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>Rechercher les frais des unités de requête pour les opérations exécutées dans l’API pour MongoDB d’Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Azure Cosmos DB prend en charge de nombreuses API, notamment SQL, MongoDB, Cassandra, Gremlin et Table. Chaque API possède son propre ensemble d’opérations de base de données, qui vont de simples opérations ponctuelles de lecture et d'écriture à des requêtes complexes. Chaque opération de base de données consomme des ressources système en fonction de sa complexité.

Le coût de toutes les opérations de base de données, normalisé par Azure Cosmos DB, est exprimé en unités de requête (RU). Les frais de requête sont les unités de requête consommées par toutes vos opérations de base de données. Les RU correspondent en quelque sorte à une devise de performances, faisant abstraction des ressources système (UC, IOPS, mémoire, etc.) requises pour effectuer les opérations de base de données prises en charge par Azure Cosmos DB. Quelle que soit l’API utilisée pour interagir avec le conteneur Azure Cosmos, les coûts sont toujours mesurés en unités de requête, Que l’opération de base de données soit une opération d’écriture, de lecture de point ou de requête, les coûts sont toujours mesurés en unités de requête. Pour plus d’informations, consultez l’article [Unités de requête et considérations](../request-units.md).

Cet article présente les différentes façons de rechercher la consommation d’[unités de requête](../request-units.md) pour toute opération exécutée sur un conteneur dans l’API Azure Cosmos DB pour MongoDB. Si vous utilisez une autre API, consultez les articles [API SQL](../find-request-unit-charge.md), [API Cassandra](../cassandra/find-request-unit-charge-cassandra.md), [API Gremlin](../find-request-unit-charge-gremlin.md) et [API Table](../table/find-request-unit-charge.md) pour trouver les frais de RU/s.

Les frais d’unités de requête sont exposés par une [commande de base de données](https://docs.mongodb.com/manual/reference/command/) personnalisée nommée `getLastRequestStatistics`. Cette commande retourne un document contenant le nom de la dernière opération exécutée, ses frais de demande et sa durée. Si vous utilisez l’API Azure Cosmos DB pour MongoDB, vous disposez de plusieurs options pour récupérer les frais d’unités de requête.

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) et remplissez-le avec des données, ou sélectionnez un compte existant qui contient déjà des données.

1. Accédez au volet **Explorateur de données**, puis sélectionnez le conteneur sur lequel vous voulez travailler.

1. Sélectionnez les points de suspension ( **...** ) en regard du nom du conteneur, puis choisissez **Nouvelle requête**.

1. Entrez une requête valide, puis sélectionnez **Exécuter la requête**.

1. Sélectionnez **Statistiques des requêtes** pour afficher les frais réels de la demande que vous avez exécutée. Cet éditeur de requête vous permet d’exécuter et d’afficher les frais d’unité de demande uniquement pour les prédicats de requête. Vous ne pouvez pas utiliser cet éditeur pour des commandes de manipulation de données telles que les instructions insert.

   :::image type="content" source="../media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Capture d’écran des frais de demande de requête MongoDB sur le portail Azure":::

1. Pour obtenir les frais de demande pour des commandes de manipulation de données, exécutez la commande `getLastRequestStatistics` à partir d’une interface utilisateur basée sur un interpréteur de commandes, tel que l’interpréteur de commandes Mongo, [Robo 3T](connect-using-robomongo.md), [MongoDB Compass](connect-using-compass.md) ou une extension VS Code avec des scripts d’interpréteur de commandes.

   `db.runCommand({getLastRequestStatistics: 1})`

## <a name="use-the-mongodb-net-driver"></a>Utiliser le pilote .NET MongoDB

Lorsque vous utilisez le [pilote .NET MongoDB officiel](https://docs.mongodb.com/ecosystem/drivers/csharp/), vous pouvez exécuter des commandes en appelant la méthode `RunCommand` sur un objet `IMongoDatabase`. Cette méthode nécessite une implémentation de la classe abstraite `Command<>` :

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Pour plus d’informations, consultez [Démarrage rapide : Générer une application web .NET à l’aide d’une API Azure Cosmos DB pour MongoDB](create-mongodb-dotnet.md).

## <a name="use-the-mongodb-java-driver"></a>Utiliser le pilote Java MongoDB


Lorsque vous utilisez le [pilote Java MongoDB officiel](https://mongodb.github.io/mongo-java-driver/), vous pouvez exécuter des commandes en appelant la méthode `runCommand` sur un objet `MongoDatabase` :

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Pour plus d’informations, consultez [Démarrage rapide : Générer une application web à l’aide d’une API Azure Cosmos DB pour MongoDB et le Kit de développement logiciel (SDK) Java](create-mongodb-java.md).

## <a name="use-the-mongodb-nodejs-driver"></a>Utiliser le pilote Node.js MongoDB

Lorsque vous utilisez le [pilote Node.js MongoDB officiel](https://mongodb.github.io/node-mongodb-native/), vous pouvez exécuter des commandes en appelant la méthode `command` sur un objet `db` :

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Pour plus d’informations, consultez [Démarrage rapide : Migrer une application web Node.js MongoDB existante vers Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’optimisation de votre consommation d’unités de requête, consultez les articles suivants :

* [Unités de requête et débit dans Azure Cosmos DB](../request-units.md)
* [Optimiser le coût du débit approvisionné dans Azure Cosmos DB](../optimize-cost-throughput.md)
* [Optimiser le coût de requête dans Azure Cosmos DB](../optimize-cost-reads-writes.md)