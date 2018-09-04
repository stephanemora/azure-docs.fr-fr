---
title: Importer des données Cassandra dans Azure Cosmos DB | Microsoft Docs
description: Découvrez comment utiliser la commande CQL Copy pour copier des données Cassandra dans Azure Cosmos DB.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: 55a6fec1b6ac018b4b24c0d27dcfdd5812455800
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189674"
---
# <a name="migrate-your-data-to-azure-cosmos-db-cassandra-api-account"></a>Migrer vos données vers un compte d’API Cassandra Azure Cosmos DB

Ce tutoriel fournit des instructions sur l’importation de données Cassandra dans Azure Cosmos DB à l’aide de la commande COPY du langage de requête Cassandra (CQL). 

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Récupération de votre chaîne de connexion
> * Importation de données à l’aide de la commande cqlsh COPY
> * Importation à l’aide du connecteur Spark 

# <a name="prerequisites"></a>Prérequis

* Installez [Apache Cassandra](http://cassandra.apache.org/download/) et vérifiez plus particulièrement que *cqlsh* est présent.  

* Augmentez le débit : la durée de la migration des données dépend de la quantité de débit que vous configurez pour vos tables. Veillez à augmenter le débit pour les migrations de données plus importantes. Une fois que vous avez effectué la migration, diminuez le débit pour réduire les coûts. Pour plus d’informations sur l’augmentation du débit dans le [portail Azure](https://portal.azure.com), consultez [Définir le débit des conteneurs Azure Cosmos DB](set-throughput.md).  

* Activez SSL : Azure Cosmos DB obéit à des normes et à des exigences strictes en matière de sécurité. Veillez à activer SSL lorsque vous interagissez avec votre compte. Lorsque vous utilisez CQL avec SSH, vous pouvez fournir des informations sur SSL. 

## <a name="get-your-connection-string"></a>Obtenir votre chaîne de connexion

1. Tout à gauche dans le [portail Azure](https://portal.azure.com), cliquez sur **Azure Cosmos DB**.

2. Dans le volet **Abonnements**, sélectionnez le nom de votre compte.

3. Cliquez sur **Chaîne de connexion**. Le volet droit contient toutes les informations dont vous avez besoin pour vous connecter à votre compte.

    ![Page de la chaîne de connexion](./media/cassandra-import-data/keys.png)

## <a name="migrate-data-by-using-cqlsh-copy"></a>Migrer des données à l’aide de Cqlsh COPY

Pour importer des données Cassandra dans Azure Cosmos DB à utiliser avec l’API Cassandra, suivez les instructions ci-dessous :

1. Connectez-vous à cqhsh en utilisant les informations de connexion à partir du portail.
2. Utilisez la [commande CQL COPY](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) pour copier des données locales sur le point de terminaison d’API Apache Cassandra. Vérifiez que la source et la cible se trouvent dans le même centre de données afin de réduire les problèmes de latence.

### <a name="steps-to-move-data-with-cqlsh"></a>Étapes pour déplacer des données avec cqlsh

1. Créez au préalable votre table et mettez-la à l’échelle :
    * Par défaut, Azure Cosmos DB configure une nouvelle table d’API Cassandra avec 1 000 unités de requête par seconde (RU/s) (la création basée sur CQL est configurée sur 400 RU/s). Avant de commencer la migration à l’aide de cqlsh, créez au préalable toutes vos tables à partir du [portail Azure](https://portal.azure.com) ou de cqlsh. 

    * Dans le [portail Azure](https://portal.azure.com), augmentez le débit de vos tables du débit par défaut (400 ou 1 000 RU/s) à 10 000 RU/s pour la durée de la migration. Si le débit est plus élevé, vous pouvez éviter la limitation de débit et procéder à une migration plus rapide. Avec une facturation à l’heure dans Azure Cosmos DB, vous pouvez réduire immédiatement le débit à l’issue de la migration afin de réduire les coûts.

2. Déterminez les frais de RU pour une opération. Pour cela, utilisez le Kit de développement logiciel (SDK) d’API Cassandra Azure Cosmos DB de votre choix. Cet exemple montre la version .NET pour obtenir les frais de RU. 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. Déterminez la latence de votre machine au service Azure Cosmos DB. Si vous utilisez un centre de données Azure, la latence doit être un petit nombre de millisecondes à un seul chiffre. Si vous n’utilisez pas le centre de données Azure, vous pouvez utiliser psping ou azurespeed.com pour obtenir la latence approximative à partir de votre emplacement.   

4. Calculez les valeurs appropriées des paramètres (NUMPROCESS, INGESTRATE, MAXBATCHSIZE ou MINBATCHSIZE) qui présentent de bonnes performances. 

5. Exécutez la commande de migration finale. L’exécution de cette commande suppose que vous avez démarré cqlsh en utilisant les informations de chaîne de connexion.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-by-using-spark"></a>Migrer des données à l’aide de Spark

Pour les données résidant dans un cluster existant dans des machines virtuelles Azure, l’importation de données à l’aide de Spark est également une option envisageable. Spark doit être configuré en tant qu’intermédiaire pour l’ingestion unique ou régulière. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris comment effectuer les tâches suivantes :

> [!div class="checklist"]
> * Récupération de votre chaîne de connexion
> * Importer des données à l’aide de la commande cql copy
> * Importer à l’aide du connecteur Spark 

Vous pouvez maintenant passer à la section Concepts pour plus d’informations sur Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Niveaux de cohérence des données paramétrables dans Azure Cosmos DB](../cosmos-db/consistency-levels.md)
