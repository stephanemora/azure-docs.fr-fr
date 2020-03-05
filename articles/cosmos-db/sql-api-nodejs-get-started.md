---
title: Tutoriel Node.js pour l’API SQL Azure Cosmos DB
description: Didacticiel Node.js qui montre comment se connecter à et interroger Azure Cosmos DB à l’aide de l’API SQL
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: 67f0d79c6b074a822917829eee94c5fd3f6a1ef2
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274033"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Tutoriel : Générer une application console Node.js avec le SDK JavaScript pour gérer des données de l’API SQL Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java asynchrone](sql-api-async-java-get-started.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
> 

En tant que développeur, vous pouvez avoir des applications qui utilisent des données de documents NoSQL. Vous pouvez utiliser un compte d’API SQL dans Azure Cosmos DB pour stocker et accéder à ces données de documents. Ce tutoriel vous explique comment générer une application de console Node.js pour créer des ressources Azure Cosmos DB et les interroger.

Ce didacticiel présente les procédures suivantes :

> [!div class="checklist"]
> * Créer et se connecter à un compte Azure Cosmos DB.
> * Configurer votre application.
> * Créer une base de données.
> * Créez un conteneur.
> * Ajouter des éléments au conteneur.
> * Effectuer des opérations de base sur les éléments, le conteneur et la base de données.

## <a name="prerequisites"></a>Prérequis 

Vérifiez que vous disposez des ressources suivantes :

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit des services Azure](https://azure.microsoft.com/pricing/free-trial/)dès aujourd’hui. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) version 6.0.0 ou ultérieure.

## <a name="create-azure-cosmos-db-account"></a>Créer un compte Azure Cosmos DB

Commençons par créer un compte Azure Cosmos DB. Si vous avez déjà un compte que vous souhaitez utiliser, vous pouvez passer directement à l’étape [Configurer votre application Node.js](#SetupNode). Si vous utilisez l’émulateur Azure Cosmos DB, suivez les étapes de la section [Émulateur Azure Cosmos DB](local-emulator.md) pour le configurer, puis passez directement à l’étape [Configurer votre application Node.js](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Configurer votre application Node.js

Avant de commencer à écrire du code pour générer l’application, vous pouvez générer l’infrastructure de votre application. Suivez les étapes suivantes pour configurer votre application Node.js contenant le code de l'infrastructure :

1. Ouvrez votre terminal préféré.
2. Recherchez le dossier ou le répertoire où vous souhaitez enregistrer votre application Node.js.
3. Créez deux fichiers JavaScript vides avec les commandes suivantes :

   * Windows :
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux/OS X :
     * ```touch app.js```
     * ```touch config.js```

4. Créez et initialisez un fichier `package.json`. Utilisez la commande suivante :
   * ```npm init -y```

5. Installez le module @azure/cosmos via npm. Utilisez la commande suivante :
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>Définir les configurations de votre application

Maintenant que votre application existe, vous devez vous assurer qu’elle peut communiquer avec Azure Cosmos DB. En mettant à jour quelques paramètres de configuration, comme indiqué dans les étapes suivantes, vous pouvez configurer votre application pour communiquer avec Azure Cosmos DB :

1. Ouvrez ```config.js``` dans l’éditeur de texte de votre choix.

1. Copiez et collez l’extrait de code ci-dessous et attribuez aux propriétés ```config.endpoint``` et ```config.key``` l’URI de votre point de terminaison Azure Cosmos DB et votre clé primaire. Ces deux configurations se trouvent dans le [portail Azure](https://portal.azure.com).

   ![Obtenir des clés à partir de la capture d’écran du portail Azure][keys]

   ```javascript
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.key = "~your primary key here~";
   ``` 

1. Copiez et collez les données ```database```, ```container``` et ```items``` dans votre objet ```config``` ci-dessous, où vous avez défini vos propriétés ```config.endpoint``` et ```config.key```. Si vous disposez déjà de données que vous souhaitez stocker dans votre base de données, vous pouvez utiliser l’outil de migration de données d’Azure Cosmos DB au lieu de définir les données ici. Votre fichier config.js doit comporter le code suivant :

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   Le SDK JavaScript utilise les termes génériques *conteneur* et *élément*. Un conteneur peut être une collection, un graphique ou une table. Un élément peut être un document, arête/sommet ou ligne, et correspond au contenu à l’intérieur d’un conteneur. 
   
   Le code `module.exports = config;` est utilisé pour exporter votre objet ```config``` afin de pouvoir le référencer dans le fichier ```app.js```.

## <a id="Connect"></a>Se connecter à un compte Azure Cosmos DB

1. Ouvrez votre fichier ```app.js``` vide dans l’éditeur de texte. Copiez et collez le code ci-dessous pour importer le module ```@azure/cosmos``` et le module ```config``` que vous venez de créer.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   ```

1. Copiez et collez le code pour utiliser les éléments ```config.endpoint``` et ```config.key``` déjà enregistrés pour créer un CosmosClient.

   ```javascript
   const config = require('./config');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });
   ```
   
> [!Note]
> Si vous vous connectez à l’**émulateur Cosmos DB**, désactivez la vérification SSL pour votre processus de nœud :
>   ```
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Maintenant que vous avez le code permettant d’initialiser le client Azure Cosmos DB, voyons comment utiliser les ressources Azure Cosmos DB.

## <a name="create-a-database"></a>Création d'une base de données

1. Copiez et collez le code ci-dessous pour définir l’ID de la base de données et l’ID du conteneur. Le client Azure Cosmos DB utilise ces ID pour trouver la base de données et le conteneur appropriés.

   ```javascript
   const client = new CosmosClient({ endpoint, key });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };
   ```

   Vous pouvez créer une base de données à l’aide de la fonction `createIfNotExists` ou create de la classe **Bases de données**. Une base de données est le conteneur logique d’éléments partitionnés entre les conteneurs. 

2. Copiez et collez les méthodes **createDatabase** et **readDatabase** dans le fichier app.js sous la définition de ```databaseId``` et ```containerId```. La fonction **createDatabase** crée une nouvelle base de données avec l’ID ```FamilyDatabase```, spécifié à partir de l’objet ```config``` s’il n’existe pas encore. La fonction **readDatabase** lit la définition de la base de données pour vérifier que la base de données existe.

   ```javascript
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { resource: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. Copiez et collez le code ci-dessous où vous avez défini les fonctions **createDatabase** et **readDatabase** pour ajouter la fonction d’assistance **exit** qui imprime le message de sortie. 

   ```javascript
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. Copiez et collez le code ci-dessous où vous avez défini la fonction **exit** pour appeler les fonctions **createDatabase** et **readDatabase**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   À ce stade, votre code dans ```app.js``` doit ressembler au code suivant :

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

    /**
    * Create the database if it does not exist
    */
    async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { resource: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : 

   ```bash 
   node app.js
   ```

## <a id="CreateContainer"></a>Créer un conteneur

Créez ensuite un conteneur au sein du compte Azure Cosmos DB afin de stocker et d'interroger les données. 

> [!WARNING]
> La création d'un conteneur a des conséquences tarifaires. Pour plus d'informations, rendez-vous sur notre [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/).

Un conteneur peut être créé à l’aide de la fonction `createIfNotExists` ou create de la classe **Conteneurs**.  Un conteneur est composé d’éléments (des documents JSON dans le cas de l’API SQL) et d’une logique d’application JavaScript associée.

1. Copiez et collez les fonctions **createContainer** et **readContainer** sous la fonction **readDatabase** dans le fichier app.js. La fonction **createContainer** crée un nouveau conteneur avec ```containerId``` spécifié à partir de l’objet ```config``` s’il n’existe pas déjà. La fonction **readContainer** lit la définition du conteneur pour vérifier que le conteneur existe.

   ```javascript
   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { resource: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. Copiez et collez le code sous l’appel à la fonction **readDatabase** pour exécuter les fonctions **createContainer** et **readContainer**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   À ce stade, votre code dans ```app.js``` doit ressembler à ceci :

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { resource: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : 

   ```bash 
   node app.js
   ```

## <a id="CreateItem"></a>Créer un élément

Un élément peut être créé à l’aide de la fonction create de la classe **Éléments**. Lorsque vous utilisez l’API SQL, les éléments sont projetés sous forme de documents, à savoir du contenu JSON (arbitraire) défini par l’utilisateur. Vous pouvez maintenant insérer un élément dans Azure Cosmos DB.

1. Copiez et collez la fonction **createFamilyItem** sous la fonction **readContainer**. La fonction **createFamilyItem** crée les éléments contenant les données JSON enregistrées dans l’objet ```config```. Nous allons nous assurer qu’un élément avec le même ID n’existe pas avant de le créer.

   ```javascript
   /**
   * Create family item
   */
   async function createFamilyItem(itemBody) {
      const { item } = await client.database(databaseId).container(containerId).items.upsert(itemBody);
      console.log(`Created family item with id:\n${itemBody.id}\n`);
   };
   ```

1. Copiez et collez le code sous l’appel à la fonction **readContainer** pour exécuter la fonction **createFamilyItem**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : 

   ```bash 
   node app.js
   ```


## <a id="Query"></a>Interroger les ressources Azure Cosmos DB

Azure Cosmos DB prend en charge les requêtes enrichies sur les documents JSON stockés dans chaque conteneur. L’exemple de code suivant illustre une requête que vous pouvez exécuter sur les documents dans votre conteneur.

1. Copiez et collez la fonction **queryContainer** sous la fonction **createFamilyItem** dans le fichier app.js. Azure Cosmos DB prend en charge des requêtes similaires à SQL, comme indiqué ci-dessous.

   ```javascript
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { resources } = await client.database(databaseId).container(containerId).items.query(querySpec).fetchAll();
    for (var queryResult of resources) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. Copiez et collez le code sous les appels à **createFamilyItem** pour exécuter la fonction **queryContainer**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande :

   ```bash 
   node app.js
   ```


## <a id="ReplaceItem"></a>Remplacer un élément
Azure Cosmos DB prend en charge le remplacement du contenu d’éléments.

1. Copiez et collez la fonction **replaceFamilyItem** sous la fonction **queryContainer** dans le fichier app.js. Notez que nous avons remplacé la propriété « grade » d’un enfant par 6 par rapport à sa valeur précédente de 5.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
     const { item } = await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).replace(itemBody);
   };
   ```

1. Copiez et collez le code sous l’appel à la fonction **queryContainer** pour exécuter la fonction **replaceFamilyItem**. De plus, ajoutez le code permettant d’appeler **queryContainer** à nouveau pour vérifier que l’élément a bien été modifié.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande :

   ```bash 
   node app.js
   ```


## <a id="DeleteItem"></a>Supprimer un élément

Azure Cosmos DB prend en charge la suppression des éléments JSON.

1. Copiez et collez la fonction **deleteFamilyItem** sous la fonction **replaceFamilyItem**.

   ```javascript
   /**
   * Delete the item by ID.
   */
   async function deleteFamilyItem(itemBody) {
     await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. Copiez et collez le code sous l’appel à la deuxième fonction **queryContainer** pour exécuter la fonction **deleteFamilyItem**.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : 

   ```bash 
   node app.js
   ```


## <a id="DeleteDatabase"></a>Supprimer la base de données

Supprimer la base de données créée revient à supprimer la base de données et toutes les ressources enfants (conteneurs, éléments, etc.).

1. Copiez et collez la fonction **cleanup** sous la fonction **deleteFamilyItem** pour supprimer la base de données et toutes ses ressources enfants.

   ```javascript
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. Copiez et collez le code sous l’appel à la fonction **deleteFamilyItem** pour exécuter la fonction **cleanup**.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a id="Run"></a>Exécuter votre application Node.js

Votre code définitif doit ressembler à ceci :

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js":::

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : 

```bash 
node app.js
```

La sortie de votre application de prise en main doit s’afficher. La sortie doit correspondre au texte en exemple ci-dessous.

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

## <a id="GetSolution"></a>Obtenir la solution complète du didacticiel Node.js 

Si vous n’avez pas le temps de suivre les étapes de ce didacticiel, ou que vous voulez simplement télécharger le code, vous pouvez l’obtenir à partir de [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ). 

Pour exécuter la solution de prise en main qui contient l’ensemble du code de cet article, vous devez disposer des éléments suivants : 

* Un [compte Azure Cosmos DB][create-account]. 
* La solution [Prise en main](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) disponible sur GitHub. 

Installez les dépendances du projet via npm. Utilisez la commande suivante : 

* ```npm install``` 

Ensuite, dans le fichier ```config.js```, mettez à jour les valeurs de config.endpoint et de config.key, comme indiqué dans [Étape 3 : Définir les configurations de votre application](#Config).  

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande :  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Dès que vous n’avez plus besoin de ces ressources, vous pouvez supprimer le groupe de ressources, le compte Azure Cosmos DB et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources que vous avez utilisé pour le compte Azure Cosmos DB, sélectionnez **Supprimer**, puis confirmez le nom du groupe de ressources à supprimer.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Surveiller un compte Azure Cosmos DB](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
