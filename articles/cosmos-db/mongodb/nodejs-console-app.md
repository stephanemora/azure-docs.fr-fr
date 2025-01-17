---
title: Utiliser l’API Azure Cosmos DB pour MongoDB pour générer une application Node.js
description: Tutoriel qui crée une base de données en ligne à l’aide de l’API Azure Cosmos DB pour MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.custom: devx-track-js
ms.openlocfilehash: 9edcb24280db6c3fb102b9d1c28d7b2a0e537294
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123031615"
---
# <a name="build-an-app-using-nodejs-and-azure-cosmos-dbs-api-for-mongodb"></a>Générer une application à l’aide de Node.js et de l’API Azure Cosmos DB pour MongoDB 
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](../sql-api-get-started.md)
> * [.NET Core](../sql-api-get-started.md)
> * [Java](../create-sql-api-java.md)
> * [Node.js pour MongoDB](nodejs-console-app.md)
> * [Node.JS](../sql-api-nodejs-get-started.md)
>

Cet exemple montre comment générer une application console à l’aide de Node.js et de l’API Azure Cosmos DB pour MongoDB.

Pour utiliser cet exemple, vous devez :

* [Créer](create-mongodb-dotnet.md#create-account) un compte Cosmos configuré pour utiliser l’API Azure Cosmos DB pour MongoDB.
* Récupérer vos informations de [chaîne de connexion](connect-mongodb-account.md).

## <a name="create-the-app"></a>Créer l’application

1. Créez un fichier *app.js* et copiez puis collez le code ci-dessous.

    ```javascript
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Facultatif** : si vous utilisez le **pilote MongoDB.Node.js 2.2**, remplacez l’extrait de code suivant :

    Ressource d’origine :

    ```javascript
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    À remplacer par :

    ```javascript
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. Modifiez les variables suivantes dans le fichier *app.js* selon les paramètres de votre compte (découvrez comment rechercher votre [chaîne de connexion](connect-mongodb-account.md)) :

    > [!IMPORTANT]
    > Le **pilote MongoDB Node.js 3.0** nécessite l’encodage des caractères spéciaux dans le mot de passe de Cosmos DB. Veillez à encoder les caractères '=' sous la forme % 3D
    >
    > Exemple : le mot de passe *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* est encodé *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*
    >
    > Le **pilote MongoDB Node.js 2.2** ne nécessite pas l’encodage des caractères spéciaux dans le mot de passe de Cosmos DB.
    >
    >
   
    ```javascript
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Ouvrez votre terminal préféré, exécutez **npm install mongodb --save**, puis exécutez votre application avec **node app.js**

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser Studio 3T](connect-using-mongochef.md) avec l’API Azure Cosmos DB pour MongoDB.
- Découvrez comment [utiliser Robo 3T](connect-using-robomongo.md) avec l’API Azure Cosmos DB pour MongoDB.
- Explorez les [exemples](nodejs-console-app.md) MongoDB avec l’API Azure Cosmos DB pour MongoDB.
- Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existant pour la planification de la capacité.
    - Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existantes, lisez l’article sur l’[estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](../convert-vcore-to-request-unit.md) 
    - Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez la section concernant l’[estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-capacity-planner.md)