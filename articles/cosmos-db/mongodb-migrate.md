---
title: Migrer vos données MongoDB vers Azure Cosmos DB à l’aide de mongoimport et mongorestore
description: Vous allez apprendre à utiliser mongoimport et mongorestore pour importer des données dans Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
Customer intent: As a developer, I want to migrate the data from my existing MongoDB to Cosmos DB.
ms.openlocfilehash: 9226a49af67659ebd7bebd9beca397830ee808bb
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039044"
---
# <a name="migrate-your-mongodb-data-to-azure-cosmos-db"></a>Migrer vos données MongoDB dans Azure Cosmos DB

 Ce tutoriel fournit des instructions sur la migration de données stockées dans MongoDB vers une base de données Azure Cosmos DB configurée pour utiliser l’API Cosmos DB pour MongoDB. Si vous importez des données à partir de MongoDB et envisagez de les utiliser avec l’API SQL Azure Cosmos DB, vous devez avoir recours à [l’outil de migration de données](import-data.md) pour importer les données.

Ce didacticiel présente les procédures suivantes :

> [!div class="checklist"]
> * Préparer un plan de migration.
> * Migrer des données à l’aide de mongoimport.
> * Migrer des données à l’aide de mongorestore.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Avant de commencer la migration, prenez connaissance des prérequis suivants.

### <a name="plan-for-the-migration"></a>Planifier la migration

Cette section explique comment planifier la migration des données. Nous allons estimer les frais associés aux unités de requête, déterminer la latence entre votre ordinateur et le service cloud, et calculer la taille de lot et le nombre de workers d’insertion.


#### <a name="pre-create-and-scale-your-collections"></a>Précréer et mettre à l’échelle vos collections

Avant de migrer à l’aide de mongoimport ou de mongorestore, créez au préalable l’ensemble de vos collections à partir du [Portail Azure](https://portal.azure.com) ou à l’aide d’outils et de pilotes MongoDB. 

Dans le [Portail Azure](https://portal.azure.com), augmentez le débit des collections pour la migration. Si le débit est plus élevé, vous pouvez éviter la limitation de débit et procéder à une migration plus rapide. Vous pouvez réduire immédiatement le débit à l’issue de la migration afin de réduire les coûts.

Au-delà de la configuration du débit au niveau d’une collection, vous pouvez également configurer le débit au niveau de la base de données pour permettre à un ensemble de collections de partager le débit configuré. Vous devez créer au préalable la base de données et les collections, puis définir une clé de partition pour chaque collection dans la base de données à débit partagé.

Vous pouvez créer des collections partitionnées à l’aide de l’outil, du pilote ou du SDK de votre choix. Dans cet exemple, nous utilisons l’interpréteur de commandes Mongo pour créer une collection partitionnée :

```bash
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Cette commande retourne les résultats suivants :

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

#### <a name="calculate-the-approximate-ru-charge-for-a-single-document-write"></a>Calculer les frais approximatifs d’unités de requête pour une écriture de document unique

À partir de l’interpréteur de commandes MongoDB, connectez-vous à votre compte Cosmos configuré pour utiliser l’API Cosmos DB pour MongoDB. Pour plus d’informations, voir [Connecter une application MongoDB à Azure Cosmos DB](connect-mongodb-account.md).

Exécutez ensuite un exemple de commande d’insertion en utilisant l’un des exemples de documents :
   
```bash
db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
```
        
Exécutez la commande `db.runCommand({getLastRequestStatistics: 1})`.

Vous recevrez une réponse semblable à ce qui suit :
     
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
        
Prenez note de frais de requête.
    
#### <a name="determine-the-latency-from-your-machine-to-cosmos-db"></a>Déterminer la latence entre votre ordinateur et Cosmos DB
    
Activez la journalisation détaillée à partir de l’interpréteur de commandes MongoDB à l’aide de la commande : `setVerboseShell(true)`.
    
Exécutez une requête de base sur la base de données à l’aide de la commande `db.coll.find().limit(1)`.

Vous recevrez une réponse semblable à ce qui suit :

```bash
Fetched 1 record(s) in 100(ms)
```
        
Avant de lancer la migration, supprimez le document inséré pour vous assurer de l’absence de documents en double. Vous pouvez supprimer des documents à l’aide de la commande `db.coll.remove({})`.

#### <a name="calculate-the-approximate-values-for-the-batchsize-and-numinsertionworkers-properties"></a>Calculer les valeurs approximatives des propriétés batchSize et numInsertionWorkers

Pour la propriété **batchSize**, divisez le débit total configuré (RU/s) par le nombre d’unités de requête (RU) consommées pour une écriture de document unique, comme indiqué dans la section « Déterminer la latence entre votre ordinateur et Cosmos DB ». Si la valeur calculée est inférieure ou égale à 24, utilisez-la comme valeur de propriété. Si la valeur calculée est supérieure à 24, définissez la valeur de propriété sur 24.
    
Pour la valeur de la propriété **numInsertionWorkers**, utilisez l’équation suivante :

`numInsertionWorkers = (Provisioned RUs throughput * Latency in seconds) / (batchSize * Consumed RUs for a single write)`

Nous pouvons utiliser les valeurs suivantes pour calculer une valeur pour la propriété **numInsertionWorkers** :

| Propriété | Valeur |
|--------|-----|
| **batchSize** | 24 |
| Unités de requête approvisionnées | 10 000 |
| Latence | 0,100 s |
| Unités de requête consommées | 10 unités de requête |
| **numInsertionWorkers** | (10 000 RU x 0,100 s) / (24 x 10 RU) = **4,1666** |

Exécutez la commande de migration **monogoimport**. Les paramètres de la commande sont décrites plus loin dans cet article.

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
```

Vous pouvez également utiliser la commande **monogorestore**. Assurez-vous que toutes les collections ont le débit défini sur le nombre de RU utilisé dans les calculs précédents.
   
```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
```

### <a name="complete-the-prerequisites"></a>Remplir les conditions préalables

Une fois votre migration planifiée, procédez comme suit : 

* **Obtenir des exemples de données** : veillez à disposer de quelques exemples de données avant de commencer la migration. 

* **Augmenter le débit** : la durée de la migration des données dépend de la quantité de débit que vous configurez pour une collection ou une base de donnée spécifique. Veillez à augmenter le débit pour les migrations de données plus importantes. Une fois que vous avez effectué la migration, diminuez le débit pour réduire les coûts. 

* **Activer le protocole SSL** :  Cosmos DB obéit à des normes et à des exigences strictes en matière de sécurité. Veillez à activer SSL lorsque vous interagissez avec votre compte Cosmos. Les procédures décrites dans cet article incluent l’activation du protocole SSL pour les commandes mongoimport et mongorestore.

* **Créer des ressources Cosmos DB** : avant de commencer à migrer les données, créez au préalable toutes vos collections à partir du Portail Azure. Si vous migrez vers un compte Cosmos qui a un débit de niveau base de données, veillez à fournir une clé de partition au moment de créer les collections.

* **Obtenir votre chaîne de connexion** : Dans le [Portail Azure](https://portal.azure.com), cliquez sur l’entrée **Azure Cosmos DB** à gauche de l’écran. Sous **Abonnements**, sélectionnez le nom de votre compte. Sous **Chaîne de connexion**, sélectionnez **Chaîne de connexion**. Le côté droit du portail affiche les informations dont vous avez besoin pour vous connecter à votre compte :

    ![Informations de chaîne de connexion](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="use-mongoimport"></a>Utiliser mongoimport

Pour importer des données dans votre compte Cosmos, utilisez le modèle suivant.

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

Remplacez les paramètres \<your_hostname>, \<your_username> et \<your_password> par les valeurs spécifiques de votre compte. Dans l’exemple suivant, nous utilisons **sampleDB** comme valeur pour \<your_database> et **sampleColl** comme valeur pour \<your_collection> :

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="use-mongorestore"></a>Utiliser mongorestore

Pour restaurer des données dans votre compte Cosmos configuré avec l’API Cosmos DB pour MongoDB, utilisez le modèle suivant afin d’exécuter l’importation.

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Remplacez les paramètres \<your_hostname>, \<your_username> et \<your_password> par les valeurs spécifiques de votre compte. Dans l’exemple suivant, nous utilisons **./dumps/dump-2016-12-07** comme valeur pour \<path_to_backup> :

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --db mydatabase --collection mycollection --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Dès que vous n’avez plus besoin des ressources, vous pouvez supprimer le groupe de ressources, le compte Cosmos et toutes les ressources associées. Procédez comme suit pour supprimer le groupe de ressources :

1. Accédez au groupe de ressources dans lequel vous avez créé le compte Cosmos.
1. Sélectionnez **Supprimer le groupe de ressources**.
1. Confirmez le nom du groupe de ressources à supprimer et sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Passez au didacticiel suivant pour savoir comment interroger les données à l’aide de l’API Azure Cosmos DB pour MongoDB. 

> [!div class="nextstepaction"]
> [Comment interroger les données MongoDB](../cosmos-db/tutorial-query-mongodb.md)
