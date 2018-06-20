---
title: Utiliser mongoimport et mongorestore avec l’API Azure Cosmos DB pour MongoDB | Microsoft Docs
description: Découvrez comment utiliser mongoimport et mongorestore pour importer des données dans un compte API pour MongoDB
keywords: mongoimport, mongorestore
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: cacda277082f62c9d98a7459cb5dbf74375bfd87
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795344"
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Azure Cosmos DB : importer des données MongoDB 

Pour migrer des données de MongoDB vers un compte Azure Cosmos DB en vue d’une utilisation avec l’API pour MongoDB, vous devez :

* Téléchargez le serveur de la communauté depuis le [centre de téléchargement MongoDB](https://www.mongodb.com/download-center) et installez-le.
* Utilisez les fichiers mongoimport.exe ou mongorestore.exe qui sont installés dans le répertoire « dossier d’installation/bin ». 
* Obtenir votre [API pour la chaîne de connexion MongoDB](connect-mongodb-account.md).

Si vous importez des données à partir de MongoDB et planifiez de les utiliser avec l’API SQL Azure Cosmos DB, vous devez avoir recours à l’[outil de migration de données](import-data.md) pour importer les données.

Ce didacticiel décrit les tâches suivantes :

> [!div class="checklist"]
> * Récupération de votre chaîne de connexion
> * Importation des données MongoDB à l’aide de mongoimport
> * Importation des données MongoDB à l’aide de mongorestore

## <a name="prerequisites"></a>Prérequis

* Augmentez le débit : la durée de la migration des données dépend de la quantité de débit que vous définissez pour une collection spécifique ou un ensemble de collections. Veillez à augmenter le débit pour les migrations de données plus importantes. Une fois que vous avez effectué la migration, diminuez le débit pour réduire les coûts. Pour plus d’informations sur l’augmentation du débit dans le [portail Azure](https://portal.azure.com), consultez les [niveaux de performances et niveaux tarifaires dans Azure Cosmos DB](performance-levels.md).

* Activez SSL : Azure Cosmos DB obéit à des normes et à des exigences strictes en matière de sécurité. Veillez à activer SSL lorsque vous interagissez avec votre compte. Les procédures décrites dans la suite de cet article incluent l’activation du protocole SSL pour mongoimport et mongorestore.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Recherche des informations de votre chaîne de connexion (hôte, port, nom d’utilisateur et mot de passe)

1. Dans le volet de gauche du [portail Azure](https://portal.azure.com), cliquez sur l’entrée **Azure Cosmos DB**.
2. Dans le volet **Abonnements**, sélectionnez le nom de votre compte.
3. Dans le panneau **Chaîne de connexion**, cliquez sur **Chaîne de connexion**.

   Le volet droit contient toutes les informations dont vous avez besoin pour vous connecter à votre compte.

   ![Panneau Chaîne de connexion](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>Importer des données dans l’API pour MongoDB avec mongoimport

Pour importer des données dans votre compte Azure Cosmos DB, utilisez le modèle suivant. Renseignez *l’hôte*, *le nom d’utilisateur*, et *le mot de passe* avec les valeurs qui sont spécifiques à votre compte.  

Modèle :

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Exemple :  

    mongoimport.exe --host comsosdb-mongodb-account.documents.azure.com:10255 -u comsosdb-mongodb-account -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\admin\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>Importer des données dans l’API pour MongoDB avec mongorestore

Pour restaurer des données dans votre API pour compte MongoDB, utilisez le modèle suivant afin d’exécuter l’importation. Renseignez *l’hôte*, *le nom d’utilisateur*, et *le mot de passe* avec les valeurs qui sont spécifiques à votre compte.

Modèle :

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Exemple :

    mongorestore.exe --host comsosdb-mongodb-account.documents.azure.com:10255 -u comsosdb-mongodb-account -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>Guide pour une migration réussie

1. Créez au préalable vos collections et mettez-les à l’échelle :
        
    * Par défaut, Azure Cosmos DB provisionne une nouvelle collection MongoDB avec 1 000 unités de requête par seconde (RU/s). Avant de commencer la migration à l’aide de mongoimport, mongorestore ou mongomirror, créez au préalable l’ensemble des collections à partir du [portail Azure](https://portal.azure.com) ou à partir d’outils et de pilotes MongoDB. Si la taille de votre collection est supérieure à 10 Go, veillez à créer une [collection partitionnée](partition-data.md) avec une clé de partition appropriée.

    * Dans le [portail Azure](https://portal.azure.com), augmentez le débit de vos collections à partir de 1 000 RU/s pour une collection à partition unique et de 2 500 RU/s pour une collection partitionnée uniquement pour la migration. Si le débit est plus élevé, vous pouvez éviter la limitation et procéder à une migration plus rapide. Avec une facturation à l’heure dans Azure Cosmos DB, vous pouvez réduire immédiatement le débit à l’issue de la migration afin de réduire les coûts.

    * En plus du provisionnement du nombre de RU/s au niveau de la collection, vous pouvez également provisionner le nombre de RU/s pour un ensemble de collections au niveau de la base de données parente. Cette opération nécessite la création préalable de la base de données et des collections, ainsi que la définition d’une clé de partition pour chaque collection.

    * Vous pouvez créer des collections partitionnées à l’aide de l’outil, du pilote ou du SDK de votre choix. Dans cet exemple, nous utilisons l’interpréteur de commandes Mongo pour créer une collection partitionnée :

        ```
        db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
        ```
    
        Résultats :

        ```JSON
        {
            "_t" : "ShardCollectionResponse",
            "ok" : 1,
            "collectionsharded" : "admin.people"
        }
        ```

2. Calculez les frais approximatifs d’unités de requête pour une écriture de document unique :

    a. Connectez-vous à votre base de données Azure Cosmos DB MongoDB à partir de l’interpréteur de commandes MongoDB. Pour plus d’informations, voir [Connecter une application MongoDB à Azure Cosmos DB](connect-mongodb-account.md).
    
    b. Exécutez un exemple de commande d’insertion en utilisant l’un des exemples de documents de l’interpréteur de commandes MongoDB :
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. Exécutez ```db.runCommand({getLastRequestStatistics: 1})```. Vous recevrez une réponse semblable à celle-ci :
     
        ```
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
    
3. Déterminez la latence de votre machine au service cloud Azure Cosmos DB :
    
    a. Activez la journalisation détaillée à partir de l’interpréteur de commandes MongoDB à l’aide de cette commande : ```setVerboseShell(true)```
    
    b. Exécutez une requête simple dans la base de données : ```db.coll.find().limit(1)```. Vous recevrez une réponse semblable à celle-ci :

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. Supprimez le document inséré avant la migration pour vous assurer de l’absence de documents en double. Vous pouvez supprimer des documents à l’aide de cette commande : ```db.coll.remove({})```

5. Calculez les valeurs *batchSize* et *numInsertionWorkers* approximatives :

    * Pour la valeur *batchSize*, divisez le total des unités RU approvisionnées par les unités RU consommées par l’écriture de votre document unique à l’étape 3.
    
    * Si la valeur *batchSize* calculée est inférieure ou égale à 24, utilisez-la comme valeur *batchSize*.
    
    * Si la valeur *batchSize* calculée est supérieure à 24, définissez la valeur *batchSize* sur 24.
    
    * Pour *numInsertionWorkers*, utilisez l’équation suivante : *numInsertionWorkers = (débit approvisionné * latence en secondes) / (taille du lot * RU consommées pour une seule écriture)*.
        
    |Propriété|Valeur|
    |--------|-----|
    |batchSize| 24 |
    |RU approvisionnées | 10000 |
    |Latency | 0,100 s |
    |RU facturée pour 1 écriture de document | 10 unités de requête |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10 000 RU x 0,1 s) / (24 x 10 RU) = 4,1666*

6. Exécutez la commande de migration finale :

   ```
   mongoimport.exe --host comsosdb-mongodb-account.documents.azure.com:10255 -u comsosdb-mongodb-account -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```
   Ou avec mongorestore (assurez-vous que toutes les collections ont le débit défini sur la quantité de RUs utilisée dans les calculs précédents) :
   
   ```
   mongorestore.exe --host comsosdb-mongodb-account.documents.azure.com:10255 -u comsosdb-mongodb-account -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
   ```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez passer au didacticiel suivant et découvrir comment interroger les données MongoDB à l’aide d’Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Comment interroger les données MongoDB ?](../cosmos-db/tutorial-query-mongodb.md)
