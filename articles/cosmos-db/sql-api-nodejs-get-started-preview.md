---
title: Didacticiel Node.js pour l’API SQL d’Azure Cosmos DB | Microsoft Docs
description: Didacticiel Node.js qui montre comment se connecter à et interroger Azure Cosmos DB à l’aide de l’API SQL
keywords: didacticiel node.js, base de données du nœud
services: cosmos-db
author: deborahc
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 07/30/2018
ms.author: dech
ms.openlocfilehash: f959bec42bf225f6893a5f29135dde37e5b9ebba
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43043014"
---
# <a name="nodejs-tutorial-create-a-nodejs-console-application-with-azure-cosmos-db-sql-api-and-javascript-sdk-20-preview"></a>Didacticiel Node.js : créer une application console Node.js avec l’API SQL Azure Cosmos DB et le Kit de développement logiciel (SDK) JavaScript 2.0 (préversion)

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
> * [Node.js - préversion v2.0](sql-api-nodejs-get-started-preview.md)
> * [Node.js pour MongoDB](mongodb-samples.md)
> * [Java](sql-api-java-get-started.md)


Bienvenue dans le didacticiel Node.js pour le Kit de développement logiciel (SDK) JavaScript d’Azure Cosmos DB ! À la fin de ce didacticiel, vous disposerez d’une application de console qui crée et interroge des ressources Azure Cosmos DB.

Nous allons aborder les points suivants :

* Création et connexion à un compte Azure Cosmos DB
* Configuration de votre application
* Création d’une base de données
* Création d’un conteneur
* Ajout d’éléments JSON au conteneur
* Interrogation du conteneur
* Remplacement d’un élément
* Suppression d’un élément
* Suppression de la base de données

Vous n’avez pas le temps ? Ne vous inquiétez pas ! La solution complète est disponible sur [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ). Pour obtenir des instructions rapides, consultez [Obtenir la solution complète](#GetSolution) .

Une fois que vous avez terminé le didacticiel Node.js, utilisez les boutons de vote en haut et en bas de cette page pour nous faire part de vos commentaires. Si vous souhaitez que nous vous contactions directement, n’hésitez pas à inclure votre adresse de messagerie dans vos commentaires.

Commençons dès maintenant !

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Configuration requise pour le didacticiel Node.js

Vérifiez que vous disposez des éléments suivants :

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit des services Azure](https://azure.microsoft.com/pricing/free-trial/)dès aujourd’hui. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) version v6.0.0 ou ultérieure.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Étape 1 : créer un compte Azure Cosmos DB

Commençons par créer un compte Azure Cosmos DB. Si vous avez déjà un compte que vous souhaitez utiliser, vous pouvez passer directement à l’étape [Configurer votre application Node.js](#SetupNode). Si vous utilisez l’émulateur Azure Cosmos DB, suivez les étapes de la section [Émulateur Azure Cosmos DB](local-emulator.md) pour le configurer, puis passez directement à l’étape [Configurer votre application Node.js](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Étape 2 : configurer votre application Node.js

1. Ouvrez votre terminal préféré.
2. Recherchez le dossier ou le répertoire où vous souhaitez enregistrer votre application Node.js.
3. Créez deux fichiers JavaScript vides avec les commandes suivantes :
   * Windows :
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux/OS X :
     * ```touch app.js```
     * ```touch config.js```
4. Installez le module documentdb via npm. Utilisez la commande suivante :
   * ```npm install @azure/cosmos --save```

Parfait ! Vous avez terminé l’installation, nous pouvons donc passer à l’écriture du code.

## <a id="Config"></a>Étape 3 : définir les configurations de votre application

Ouvrez ```config.js``` dans l’éditeur de texte de votre choix.

Puis, copiez et collez l’extrait de code ci-dessous et attribuez aux propriétés ```config.endpoint``` et ```config.primaryKey``` l’URI de votre point de terminaison Azure Cosmos DB et votre clé primaire. Ces deux configurations se trouvent dans le [portail Azure](https://portal.azure.com).

![Didacticiel Node.js : capture d’écran du portail Azure, présentant un compte Azure Cosmos DB, avec le hub ACTIF et le bouton CLÉS mis en surbrillance dans le panneau du compte Azure Cosmos DB, et les valeurs d’URI, de CLÉ PRIMAIRE et de CLÉ SECONDAIRE mises en surbrillance dans le panneau Clés - Base de données de nœud][keys]

```nodejs
// ADD THIS PART TO YOUR CODE
var config = {}

config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
config.primaryKey = "~your primary key here~";
``` 

Copiez et collez les données ```database```, ```container``` et ```items``` dans votre objet ```config``` ci-dessous, où vous avez défini vos propriétés ```config.endpoint``` et ```config.primaryKey```. Si vous disposez déjà de données que vous souhaitez stocker dans votre base de données, vous pouvez utiliser l’[outil de migration de données](import-data.md) d’Azure Cosmos DB au lieu de définir les données ici.

```nodejs
var config = {}

config.endpoint = "~your Azure Cosmos DB account endpoint uri here~";
config.primaryKey = "~your primary key here~";

config.database = {
    "id": "FamilyDatabase"
};

config.container = {
    "id": "FamilyContainer"
};

config.items = {
    "Andersen": {
        "id": "Anderson.1",
        "lastName": "Andersen",
        "parents": [{
            "firstName": "Thomas"
        }, {
                "firstName": "Mary Kay"
            }],
        "children": [{
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [{
                "givenName": "Fluffy"
            }]
        }],
        "address": {
            "state": "WA",
            "county": "King",
            "city": "Seattle"
        }
    },
    "Wakefield": {
        "id": "Wakefield.7",
        "parents": [{
            "familyName": "Wakefield",
            "firstName": "Robin"
        }, {
                "familyName": "Miller",
                "firstName": "Ben"
            }],
        "children": [{
            "familyName": "Merriam",
            "firstName": "Jesse",
            "gender": "female",
            "grade": 8,
            "pets": [{
                "givenName": "Goofy"
            }, {
                    "givenName": "Shadow"
                }]
        }, {
                "familyName": "Miller",
                "firstName": "Lisa",
                "gender": "female",
                "grade": 1
            }],
        "address": {
            "state": "NY",
            "county": "Manhattan",
            "city": "NY"
        },
        "isRegistered": false
    }
};
```
Notez que si vous êtes familier avec la version précédente du Kit de développement logiciel (SDK) JavaScript, vous avez l’habitude de voir les termes « collection » et « document ». Étant donné qu’Azure Cosmos DB prend en charge [plusieurs modèles d’API](https://docs.microsoft.com/azure/cosmos-db/introduction#key-capabilities), les versions ultérieures à 2.0 du Kit de développement logiciel (SDK) JavaScript utilisent les termes génériques « conteneur » et « élément ». Un conteneur peut être une collection, un graphique ou une table. Un élément peut être un document, arête/sommet ou ligne, et correspond au contenu à l’intérieur d’un conteneur. 

Enfin, exportez votre objet ```config``` pour pouvoir y faire référence dans le fichier ```app.js```.
```nodejs
        },
        "isRegistered": false
    }
};

// ADD THIS PART TO YOUR CODE
module.exports = config;
```
## <a id="Connect"></a> Étape 4 : se connecter à un compte Azure Cosmos DB

Ouvrez votre fichier ```app.js``` vide dans l’éditeur de texte. Copiez et collez le code ci-dessous pour importer le module ```@azure/cosmos``` et le module ```config``` que vous venez de créer.

```nodejs
// ADD THIS PART TO YOUR CODE
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');
```

Copiez et collez le code pour utiliser les éléments ```config.endpoint``` et ```config.primaryKey``` déjà enregistrés pour créer un CosmosClient.

```nodejs
const url = require('url');

// ADD THIS PART TO YOUR CODE
const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
```

Maintenant que vous avez le code permettant d’initialiser le client Azure Cosmos DB, voyons comment utiliser les ressources Azure Cosmos DB.

## <a name="step-5-create-a-database"></a>Étape 5 : créer une base de données

Copiez et collez le code ci-dessous pour définir l’état HTTP correspondant à Non trouvé, l’ID de la base de données et l’ID du conteneur. Le client Azure Cosmos DB utilise ces ID pour trouver la base de données et le conteneur appropriés.

```nodejs
const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

// ADD THIS PART TO YOUR CODE
const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;
```

Une [base de données](sql-api-resources.md#databases) peut être créée à l’aide de la fonction [createIfNotExists](/javascript/api/%40azure/cosmos/databases) ou [create](/javascript/api/%40azure/cosmos/databases) de la classe **Bases de données**. Une base de données est le conteneur logique d’éléments partitionnés entre les conteneurs. 

Copiez et collez les fonctions **createDatabase** et **readDatabase** dans le fichier app.js sous la définition de ```databaseId``` et ```containerId```. La fonction **createDatabase** crée une nouvelle base de données avec l’ID ```FamilyDatabase```, spécifié à partir de l’objet ```config``` s’il n’existe pas déjà. La fonction **readDatabase** lit la définition de la base de données pour vérifier que la base de données existe.

```nodejs
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
```

Copiez et collez le code ci-dessous où vous avez défini les fonctions **createDatabase** et **readDatabase** pour ajouter la fonction d’assistance **exit** qui imprime le message de sortie. 

```nodejs
// ADD THIS PART TO YOUR CODE
function exit(message) {
    console.log(message);
    console.log('Press any key to exit');
    process.stdin.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('data', process.exit.bind(process, 0));
};
```
Copiez et collez le code ci-dessous où vous avez défini la fonction **exit** pour appeler les fonctions **createDatabase** et **readDatabase**.

```nodejs
createDatabase()
    .then(() => readDatabase())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
```

À ce stade, votre code dans ```app.js``` doit ressembler à ceci :

```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;


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
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : 
```bash 
node app.js
```

Félicitations ! Vous avez créé une base de données Azure Cosmos DB.

## <a id="CreateContainer"></a>Étape 6 : créer un conteneur

> [!WARNING]
> L’appel de la fonction **createContainer** crée un nouveau conteneur, ce qui implique un côut. Pour plus d’informations, consultez notre [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/).

Un conteneur peut être créé à l’aide de la fonction [createIfNotExists](/javascript/api/%40azure/cosmos/containers) ou [create](/javascript/api/%40azure/cosmos/containers) de la classe **Conteneurs**. 

Un conteneur est composé d’éléments (des documents JSON dans le cas de l’API SQL) et d’une logique d’application JavaScript associée.

Copiez et collez les fonctions **createContainer** et **readContainer** sous la fonction **readDatabase** dans le fichier app.js. La fonction **createContainer** crée un nouveau conteneur avec ```containerId``` spécifié à partir de l’objet ```config``` s’il n’existe pas déjà. La fonction **readContainer** lit la définition du conteneur pour vérifier que le conteneur existe.

```nodejs
/**
 * Create the container if it does not exist
 */
async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}
```

Copiez et collez le code sous l’appel à la fonction **readDatabase** pour exécuter les fonctions **createContainer** et **readContainer**.
```nodejs
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

```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;

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
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
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

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : 

```bash 
node app.js
```

Félicitations ! Vous avez créé un conteneur Azure Cosmos DB.

## <a id="CreateItem"></a>Étape 7 : créer un élément

Un élément peut être créé à l’aide de la fonction [create](/javascript/api/%40azure/cosmos/items) de la classe **Éléments**. Lorsque vous utilisez l’API SQL, les éléments sont projetés sous forme de documents, à savoir du contenu JSON (arbitraire) défini par l’utilisateur. Vous pouvez maintenant insérer un élément dans Azure Cosmos DB.

Copiez et collez la fonction **createFamilyItem** sous la fonction **readContainer**. La fonction **createFamilyItem** crée les éléments contenant les données JSON enregistrées dans l’objet ```config```. Nous allons nous assurer qu’un élément avec le même ID n’existe pas déjà avant de le créer.

```nodejs
/**
 * Create family item if it does not exist
 */
async function createFamilyItem(itemBody) {
    try {
        // read the item to see if it exists
        const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
        console.log(`Item with family id ${itemBody.id} already exists\n`);
    }
    catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
    }
};
```

Copiez et collez le code sous l’appel à la fonction **readContainer** pour exécuter la fonction **createFamilyItem**.
```nodejs
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

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : 

```bash 
node app.js
```

Félicitations ! Vous avez créé un élément Azure Cosmos DB.


## <a id="Query"></a>Étape 8 : interroger les ressources Azure Cosmos DB
Azure Cosmos DB prend en charge les [requêtes enrichies](sql-api-sql-query.md) sur les documents JSON stockés dans chaque conteneur. L’exemple de code suivant illustre une requête que vous pouvez exécuter sur les documents dans votre conteneur.

Copiez et collez la fonction **queryContainer** sous la fonction **createFamilyItem** dans le fichier app.js. Azure Cosmos DB prend en charge des requêtes similaires à SQL, comme indiqué ci-dessous. Pour plus d’informations sur la création de requêtes complexes, consultez [Query Playground](https://www.documentdb.com/sql/demo) et la [documentation relative aux requêtes](sql-api-sql-query.md).

```nodejs
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

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
};
```

Copiez et collez le code sous les appels à **createFamilyItem** pour exécuter la fonction **queryContainer**.

```nodejs
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

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande :

```bash 
node app.js
```

Félicitations ! Vous avez interrogé vos éléments Azure Cosmos DB.

## <a id="ReplaceItem"></a>Étape 9 : remplacer un élément
Azure Cosmos DB prend en charge le remplacement du contenu d’éléments.

Copiez et collez la fonction **replaceFamilyItem** sous la fonction **queryContainer** dans le fichier app.js. Notez que nous avons remplacé la propriété « grade » d’un enfant par 6 par rapport à sa valeur précédente de 5.

```nodejs
// ADD THIS PART TO YOUR CODE
/**
 * Replace the item by ID.
 */
async function replaceFamilyItem(itemBody) {
    console.log(`Replacing item:\n${itemBody.id}\n`);
    // Change property 'grade'
    itemBody.children[0].grade = 6;
    const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
};
```
Copiez et collez le code sous l’appel à la fonction **queryContainer** pour exécuter la fonction **replaceFamilyItem**. De plus, ajoutez le code permettant d’appeler **queryContainer** à nouveau pour vérifier que l’élément a bien été modifié.

```nodejs
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
Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande :

```bash 
node app.js
```

Félicitations ! Vous avez remplacé un élément Azure Cosmos DB.

## <a id="DeleteItem"></a>Étape 10 : supprimer un élément

Azure Cosmos DB prend en charge la suppression des éléments JSON.

Copiez et collez la fonction **deleteFamilyItem** sous la fonction **replaceFamilyItem**.

```nodejs
/**
 * Delete the item by ID.
 */
async function deleteFamilyItem(itemBody) {
    await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
    console.log(`Deleted item:\n${itemBody.id}\n`);
};
```

Copiez et collez le code sous l’appel à la deuxième fonction **queryContainer** pour exécuter la fonction **deleteFamilyItem**.

```nodejs
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

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : 

```bash 
node app.js
```

Félicitations ! Vous avez supprimé un élément Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Étape 11 : supprimer la base de données

Supprimer la base de données créée revient à supprimer la base de données et toutes les ressources enfants (conteneurs, éléments, etc.).

Copiez et collez la fonction **cleanup** sous la fonction **deleteFamilyItem** pour supprimer la base de données et toutes ses ressources enfants.

```nodejs
/**
 * Cleanup the database and container on completion
 */
async function cleanup() {
    await client.database(databaseId).delete();
}
```

Copiez et collez le code sous l’appel à la fonction **deleteFamilyItem** pour exécuter la fonction **cleanup**.

```nodejs
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

## <a id="Run"></a>Étape 12 : Exécution de l’application Node.js

Votre code définitif doit ressembler à ceci :
```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

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
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}

/**
 * Create family item if it does not exist
 */
async function createFamilyItem(itemBody) {
    try {
        // read the item to see if it exists
        const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
        console.log(`Item with family id ${itemBody.id} already exists\n`);
    }
    catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
    }
};

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

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
};

/**
 * Replace the item by ID.
 */
async function replaceFamilyItem(itemBody) {
    console.log(`Replacing item:\n${itemBody.id}\n`);
    // Change property 'grade'
    itemBody.children[0].grade = 6;
    const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
};

/**
 * Delete the item by ID.
 */
async function deleteFamilyItem(itemBody) {
    await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
    console.log(`Deleted item:\n${itemBody.id}\n`);
};

/**
 * Cleanup the database and container on completion
 */
async function cleanup() {
    await client.database(databaseId).delete();
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
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer())
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())
    .then(() => deleteFamilyItem(config.items.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : 

```bash 
node app.js
```

La sortie de votre application de prise en main doit s’afficher. La sortie doit correspondre au texte en exemple ci-dessous.

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

Félicitations ! Vous avez terminé le didacticiel Node.js et disposez à présent de votre première application console Azure Cosmos DB !

## <a id="GetSolution"></a>Obtenir la solution complète du didacticiel Node.js

Si vous n’avez pas le temps de suivre les étapes de ce didacticiel, ou que vous voulez simplement télécharger le code, vous pouvez l’obtenir à partir de [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ).

Pour exécuter la solution de prise en main qui contient l’ensemble du code de cet article, vous devez disposer des éléments suivants :

* Un [compte Azure Cosmos DB][create-account].
* La solution [Prise en main](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) disponible sur GitHub.

Installer le module **@azure/cosmos** via npm. Utilisez la commande suivante :

* ```npm install @azure/cosmos --save```

Ensuite, dans le fichier ```config.js```, mettez à jour les valeurs de config.endpoint et de config.primaryKey, comme indiqué à l’[étape 3 : Définition des configurations de votre application](#Config). 

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : 

```bash 
node app.js
```

Voilà, vous êtes sur la bonne voie ! 

## <a name="next-steps"></a>Étapes suivantes
* Vous voulez un exemple de Node.js plus complexe ? Consultez [Création d’une application web Node.js avec Azure Cosmos DB](sql-api-nodejs-application-preview.md).
* Découvrez comment [surveiller un compte Azure Cosmos DB](monitor-accounts.md).
* Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png