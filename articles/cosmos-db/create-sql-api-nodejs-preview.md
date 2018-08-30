---
title: 'Azure Cosmos DB : Créer une application avec Node.js et l’API SQL | Microsoft Docs'
description: Cet article présente un exemple de code Node.js que vous pouvez utiliser pour vous connecter à l’API SQL d’Azure Cosmos DB et pour l’interroger.
services: cosmos-db
author: deborahc
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.custom: quick start connect, mvc
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 07/30/2018
ms.author: dech
ms.openlocfilehash: 96d09ff6c43a26f1ebf36fa5d5d113627cbc06e9
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43043251"
---
# <a name="azure-cosmos-db-build-a-nodejs-app-using-sql-api-javascript-sdk-20-preview-and-the-azure-portal"></a>Azure Cosmos DB : Créer une application Node.js à l’aide de l’API SQL, du Kit de développement logiciel (SDK) JavaScript 2.0 (préversion) et du portail Azure 

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce démarrage rapide explique comment créer, à l’aide du portail Azure, un compte [API SQL](sql-api-introduction.md) d’Azure Cosmos DB, une base de données de documents, ainsi qu’un conteneur. Vous allez ensuite créer et exécuter une application console basée sur le [Kit de développement logiciel (SDK) JavaScript SQL](sql-api-sdk-node.md). Ce démarrage rapide utilise la version 2.0 du [Kit de développement logiciel (SDK) JavaScript](https://www.npmjs.com/package/@azure/cosmos), qui est actuellement en préversion. 

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Par ailleurs :
    * [Node.js](https://nodejs.org/en/) version v6.0.0 ou ultérieure
    * [Git](http://git-scm.com/)

## <a name="create-a-database-account"></a>Création d’un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Ajouter une collection

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Ajouter un exemple de données

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Interroger vos données

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, nous allons cloner une application API SQL à partir de Github, configurer la chaîne de connexion et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

1. Ouvrez une invite de commandes, créez un nouveau dossier nommé git-samples, puis fermez l’invite de commandes.

    ```bash
    md "C:\git-samples"
    ```

2. Ouvrez une fenêtre de terminal git comme Git Bash et utilisez la commande `cd` pour accéder au nouveau dossier d’installation pour l’exemple d’application.

    ```bash
    cd "C:\git-samples"
    ```

3. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Vérifier le code

Cette étape est facultative. Pour savoir comment les ressources de base de données sont créées dans le code, vous pouvez examiner les extraits de code suivants. Sinon, vous pouvez passer à l’étape [Mise à jour de votre chaîne de connexion](#update-your-connection-string). 

Notez que si vous êtes familier avec la version précédente du Kit de développement logiciel (SDK) JavaScript, vous avez l’habitude de voir les termes « collection » et « document ». Étant donné qu’Azure Cosmos DB prend en charge [plusieurs modèles d’API](https://docs.microsoft.com/azure/cosmos-db/introduction#key-capabilities), la version 2.0 et versions ultérieures du Kit de développement logiciel (SDK) JavaScript utilise les termes génériques « conteneur », qui peut être une collection, un graphique, ou une table, et « élément » pour décrire le contenu du conteneur.

Tous les extraits de code suivants proviennent du fichier **app.js**.

* Le `CosmosClient` est initialisé.

    ```nodejs
    const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
    ```

* Une nouvelle base de données est créée.

    ```nodejs
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* Un nouveau conteneur (collection) est créé.

    ```nodejs
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* Un élément (document) est créé.

    ```nodejs
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* Une requête SQL sur JSON est effectuée.

    ```nodejs
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
    ```    

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application.

1. Dans le [portail Azure](http://portal.azure.com/), dans votre compte Azure Cosmos DB, dans le volet de navigation de gauche, cliquez sur **Clés**, puis sur **Clés en lecture-écriture**. Vous utiliserez les boutons Copier sur le côté droit de l’écran pour copier l’URI et la clé primaire dans le fichier `config.js` à l’étape suivante.

    ![Affichage et copie d’une clé d’accès dans le portail Azure, panneau Clés](./media/create-sql-api-dotnet/keys.png)

2. Ouvrez le fichier `config.js`. 

3. Copiez votre valeur URI à partir du portail (à l’aide du bouton Copier) et définissez-la comme la valeur de la clé du point de terminaison dans `config.js`. 

    `config.endpoint = "https://FILLME.documents.azure.com"`

4. Puis, copiez votre valeur de clé primaire à partir du portail et définissez-la comme la valeur de `config.primaryKey` dans `config.js`. Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 

    `config.primaryKey = "FILLME"`
    
## <a name="run-the-app"></a>Exécution de l'application
1. Exécutez `npm install` dans un terminal afin d’installer les modules npm requis.

2. Exécutez `node app.js` sur un terminal pour démarrer votre application Node.

Vous pouvez dès à présent revenir à l’Explorateur de données et voir la requête, modifier et travailler avec ces nouvelles données. 

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer une collection à l’aide de l’Explorateur de données, et à exécuter une application. Vous pouvez maintenant importer des données supplémentaires dans votre compte Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB](import-data.md)


