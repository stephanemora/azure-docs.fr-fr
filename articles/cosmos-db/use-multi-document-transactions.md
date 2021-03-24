---
title: Utiliser des transactions multidocuments dans l’API Azure Cosmos DB pour MongoDB
description: Découvrez comment créer un exemple d’application shell Mongo qui peut exécuter une transaction multidocument (sémantique « tout ou rien ») sur une collection fixe dans l’API Azure Cosmos DB pour MongoDB 4.0.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: gahllevy
ms.openlocfilehash: f319db76c8aee5a2a35ff8ca9670c42089350ede
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692468"
---
# <a name="use-multi-document-transactions-in-azure-cosmos-db-api-for-mongodb"></a>Utiliser des transactions multidocuments dans l’API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Dans cet article, vous allez créer une application shell Mongo qui lance une transaction multidocument sur une collection fixe dans l’API Azure Cosmos DB pour MongoDB avec la version serveur 4.0.

## <a name="what-are-multi-document-transactions"></a>Que sont les transactions multidocuments ?

Dans l’API Azure Cosmos DB pour MongoDB, les opérations exécutées sur un seul document sont de type atomique. Les transactions multidocuments permettent aux applications d’exécuter des opérations atomiques sur plusieurs documents. Elles fournissent une sémantique « tout ou rien » aux opérations. À la validation, les modifications effectuées dans les transactions sont conservées et, si une transaction échoue, toutes les modifications au sein de la transaction sont ignorées.

Les transactions multidocuments suivent la sémantique **ACID** :

* Atomicité : toutes les opérations sont traitées comme un tout
* Cohérence : les données validées sont correctes
* Isolation : données isolées des autres opérations
* Durabilité : les données de transaction sont conservées lorsque le client est enjoint à le faire

## <a name="requirements"></a>Spécifications

Les transactions multidocuments sont prises en charge dans une collection non partitionnée dans l’API version 4.0. Les transactions multidocuments ne sont pas prises en charge sur plusieurs collections ou au sein de collections partitionnées dans la version 4.0. Le délai d’expiration des transactions est fixé à cinq secondes.

Tous les pilotes qui prennent en charge le protocole filaire version 4.0 ou ultérieure prennent en charge les transactions multidocuments dans l’API Azure Cosmos DB pour MongoDB.

## <a name="run-multi-document-transactions-in-mongodb-shell"></a>Exécuter des transactions multidocuments dans le shell MongoDB
> [!Note]
> Cet exemple ne fonctionne pas dans la version bêta de MongoSH (interpréteur de commandes) incorporée dans MongoDB Compass.

1. Ouvrez une invite de commandes, et accédez au répertoire où le shell Mongo version 4.0 ou ultérieure est installé :

   ```powershell
   cd <path_to_mongo_shell_>
   ```

2. Créez un script de shell Mongo *connect_friends.js* et ajoutez-y le contenu suivant

   ```javascript
   // insert data into friends collection
   db.getMongo().getDB("users").friends.insert({name:"Tom"})
   db.getMongo().getDB("users").friends.insert({name:"Mike"})
   // start transaction
   var session = db.getMongo().startSession();
   var friendsCollection = session.getDatabase("users").friends;
   session.startTransaction();
   // operations in transaction
   try {
       friendsCollection.updateOne({ name: "Tom" }, { $set: { friendOf: "Mike" } } );
       friendsCollection.updateOne({ name: "Mike" }, { $set: { friendOf: "Tom" } } );
   } catch (error) {
       // abort transaction on error
       session.abortTransaction();
       throw error;
   }

    // commit transaction
    session.commitTransaction();

    ```

3. Exécutez la commande suivante pour exécuter la transaction multidocument. L’hôte, le port, l’utilisateur et la clé sont indiqués dans le portail Azure.

   ```powershell
   mongo "<HOST>:<PORT>" -u "<USER>" -p "KEY" --ssl connect_friends.js
   ```

## <a name="next-steps"></a>Étapes suivantes

Explorez les nouveautés de l’[API Azure Cosmos DB pour MongoDB 4.0](mongodb-feature-support-40.md)
