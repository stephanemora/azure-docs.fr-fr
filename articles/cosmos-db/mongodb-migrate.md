---
title: Utiliser mongoimport et mongorestore avec l’API Azure Cosmos DB pour MongoDB | Microsoft Docs
description: Découvrez comment utiliser mongoimport et mongorestore pour importer des données dans un compte API pour MongoDB
keywords: mongoimport, mongorestore
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: d3a7ddcd4a95660264bdf9609f54af39a05c97b3
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741026"
---
# <a name="tutorial-migrate-your-data-to-azure-cosmos-db-mongodb-api-account"></a>Tutoriel : Migrer vos données vers un compte d’API MongoDB Azure Cosmos DB

Ce tutoriel fournit des instructions sur la migration de données stockées dans MongoDB vers un compte d’API MongoDB Azure Cosmos DB. Si vous importez des données à partir de MongoDB et planifiez de les utiliser avec l’API SQL Azure Cosmos DB, vous devez avoir recours à l’[outil de migration de données](import-data.md) pour importer les données.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Planifier la migration
> * Prérequis pour la migration
> * Migrer des données à l’aide de mongoimport
> * Migrer des données à l’aide de mongorestore

Avant de migrer des données vers un compte d’API MongoDB, assurez-vous d’avoir quelques exemples de données MongoDB. Si vous n’avez pas d’exemple de base de données MongoDB, vous pouvez télécharger et installer le [serveur de la communauté MongoDB](https://www.mongodb.com/download-center), créer un exemple de base de données et utiliser le fichier mongoimport.exe ou mongorestore.exe pour charger des exemples de données. 

## <a name="plan-for-migration"></a>Planifier la migration

1. Créez au préalable vos collections et mettez-les à l’échelle :
        
    * Par défaut, Azure Cosmos DB provisionne une nouvelle collection MongoDB avec 1 000 unités de requête par seconde (RU/s). Avant de commencer la migration à l’aide de mongoimport, mongorestore, créez au préalable l’ensemble des collections à partir du [portail Azure](https://portal.azure.com) ou à partir d’outils et de pilotes MongoDB. Si la taille des données est supérieure à 10 Go, veillez à créer une [collection partitionnée](partition-data.md) avec une clé de partition appropriée.

    * Dans le [portail Azure](https://portal.azure.com), augmentez le débit de vos collections à partir de 1000 RU/s pour une collection à partition unique et de 2 500 RU/s pour une collection partitionnée uniquement pour la migration. Si le débit est plus élevé, vous pouvez éviter la limitation de débit et procéder à une migration plus rapide. Vous pouvez réduire immédiatement le débit à l’issue de la migration afin de réduire les coûts.

    * En plus du provisionnement du nombre de RU/s au niveau de la collection, vous pouvez également provisionner le nombre de RU/s pour un ensemble de collections au niveau de la base de données parente. Cette opération nécessite la création préalable de la base de données et des collections, ainsi que la définition d’une clé de partition pour chaque collection.

    * Vous pouvez créer des collections partitionnées à l’aide de l’outil, du pilote ou du SDK de votre choix. Dans cet exemple, nous utilisons l’interpréteur de commandes Mongo pour créer une collection partitionnée :

        ```bash
        db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
        ```
    
        Résultats :

        ```JSON
        {
            "_t" : "ShardCollectionResponse",
            "ok" : 1,
            "collectionsharded" : "admin.people"
        }
        ```

1. Calculez les frais approximatifs d’unités de requête pour une écriture de document unique :

   a. Connectez-vous à votre compte d’API MongoDB Azure Cosmos DB à partir de l’interpréteur de commandes MongoDB. Pour plus d’informations, voir [Connecter une application MongoDB à Azure Cosmos DB](connect-mongodb-account.md).
    
   b. Exécutez un exemple de commande d’insertion en utilisant l’un des exemples de documents de l’interpréteur de commandes MongoDB :
   
      ```bash
      db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
      ```
        
   c. Exécutez ```db.runCommand({getLastRequestStatistics: 1})```. Vous recevrez une réponse semblable à celle-ci :
     
      ```bash
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
      ```
        
    d. Prenez note de frais de requête.
    
1. Déterminez la latence de votre machine au service cloud Azure Cosmos DB :
    
    a. Activez la journalisation détaillée à partir de l’interpréteur de commandes MongoDB à l’aide de cette commande : ```setVerboseShell(true)```
    
    b. Exécutez une requête simple dans la base de données : ```db.coll.find().limit(1)```. Vous recevrez une réponse semblable à celle-ci :

       ```bash
       Fetched 1 record(s) in 100(ms)
       ```
        
1. Supprimez le document inséré avant la migration pour vous assurer de l’absence de documents en double. Vous pouvez supprimer des documents à l’aide de cette commande : ```db.coll.remove({})```

1. Calculez les valeurs *batchSize* et *numInsertionWorkers* approximatives :

    * Pour la valeur *batchSize*, divisez le total des unités RU approvisionnées par les unités RU consommées par l’écriture de votre document unique à l’étape 3.
    
    * Si la valeur *batchSize* calculée est inférieure ou égale à 24, utilisez-la comme valeur *batchSize*.
    
    * Si la valeur *batchSize* calculée est supérieure à 24, définissez la valeur *batchSize* sur 24.
    
    * Pour *numInsertionWorkers*, utilisez l’équation suivante : *numInsertionWorkers = (débit approvisionné * latence en secondes) / (taille du lot * RU consommées pour une seule écriture)*.
        
    |Propriété|Valeur|
    |--------|-----|
    |batchSize| 24 |
    |RU approvisionnées | 10000 |
    |Latency | 0,100 s |
    |RU facturée pour 1 écriture de document | 10 unités de requête |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10 000 RU x 0,1 s) / (24 x 10 RU) = 4,1666*

1. Exécutez la commande de migration. Les options pour migrer des données sont décrites dans les sections suivantes.

   ```bash
   mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```
   Ou avec mongorestore (assurez-vous que toutes les collections ont le débit défini sur le nombre de RUs utilisé dans les calculs précédents) :
   
   ```bash
   mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
   ```

## <a name="prerequisites-for-migration"></a>Prérequis pour la migration

* **Augmentez le débit :** la durée de la migration des données dépend de la quantité de débit que vous définissez pour une collection spécifique ou un ensemble de collections. Veillez à augmenter le débit pour les migrations de données plus importantes. Une fois que vous avez effectué la migration, diminuez le débit pour réduire les coûts. Pour plus d’informations sur l’augmentation du débit dans le [portail Azure](https://portal.azure.com), consultez les [niveaux de performances et niveaux tarifaires dans Azure Cosmos DB](performance-levels.md).

* **Activer SSL :** Azure Cosmos DB a des spécifications et des standards de sécurité stricts. Veillez à activer SSL lorsque vous interagissez avec votre compte. Les procédures décrites dans la suite de cet article incluent l’activation du protocole SSL pour mongoimport et mongorestore.

* **Créez des ressources Azure Cosmos DB :** avant de commencer à migrer les données, créez au préalable toutes vos collections à partir du Portail Azure. Si vous migrez vers un compte Azure Cosmos DB qui a un débit de niveau base de données, assurez-vous de fournir une clé de partition lors de la création des collections Azure Cosmos DB.

## <a name="get-your-connection-string"></a>Obtenir votre chaîne de connexion 

1. Dans le volet de gauche du [portail Azure](https://portal.azure.com), cliquez sur l’entrée **Azure Cosmos DB**.
1. Dans le volet **Abonnements**, sélectionnez le nom de votre compte.
1. Dans le panneau **Chaîne de connexion**, cliquez sur **Chaîne de connexion**.

   Le volet droit contient toutes les informations dont vous avez besoin pour vous connecter à votre compte.

   ![Panneau Chaîne de connexion](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="migrate-data-by-using-mongoimport"></a>Migrer des données à l’aide de mongoimport

Pour importer des données dans votre compte Azure Cosmos DB, utilisez le modèle suivant. Renseignez *l’hôte*, *le nom d’utilisateur*, et *le mot de passe* avec les valeurs qui sont spécifiques à votre compte.  

Modèle :

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

Exemple :  

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="migrate-data-by-using-mongorestore"></a>Migrer des données à l’aide de mongorestore

Pour restaurer des données dans votre API pour compte MongoDB, utilisez le modèle suivant afin d’exécuter l’importation. Renseignez *l’hôte*, *le nom d’utilisateur*, et *le mot de passe* avec les valeurs qui sont spécifiques à votre compte.

Modèle :

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Exemple :

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez passer au didacticiel suivant et découvrir comment interroger les données MongoDB à l’aide d’Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Comment interroger les données MongoDB ?](../cosmos-db/tutorial-query-mongodb.md)
