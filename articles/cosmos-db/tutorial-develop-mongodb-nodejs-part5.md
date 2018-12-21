---
title: Application Angular Node.Js à l’aide de l’API MongoB (Partie 5)
titleSuffix: Azure Cosmos DB
description: Il s’agit de la partie 5 de cette série de didacticiels sur la création d’une application MongoDB avec Angular et Node sur Azure Cosmos DB à l’aide des mêmes API que celles utilisées pour MongoDB
author: johnpapa
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18
ms.openlocfilehash: bda500c07e2ecccc317b5b669a947a415aaf147f
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53134129"
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-5-connect-to-azure-cosmos-db"></a>Créer une application MongoDB avec Angular et Azure Cosmos DB - Partie 5 : Se connecter à Azure Cosmos DB 

Ce didacticiel en plusieurs parties montre comment créer une application Node.js avec Express et Angular ainsi que comment se connecter à un compte de l’[API MongoDB d’Azure Cosmos DB](mongodb-introduction.md).

La partie 5 de ce didacticiel est basée sur la [partie 4](tutorial-develop-mongodb-nodejs-part4.md) et aborde les tâches suivantes :

> [!div class="checklist"]
> * Utiliser Mongoose pour se connecter à Azure Cosmos DB
> * Obtenir vos informations de chaîne de connexion Cosmos DB
> * Créer le modèle héros
> * Créer le service héros pour obtenir des données héros
> * Exécutez l’application localement.

## <a name="video-walkthrough"></a>Vidéo de procédure pas à pas

Vous pouvez visionner la vidéo suivante pour appendre rapidement les étapes décrites dans ce document : 

> [!VIDEO https://www.youtube.com/embed/sI5hw6KPPXI]


## <a name="prerequisites"></a>Prérequis

Avant de commencer cette partie du didacticiel, assurez-vous d’avoir effectué les étapes de la [partie 4](tutorial-develop-mongodb-nodejs-part4.md) du didacticiel.

> [!TIP]
> Ce didacticiel vous guide à travers les étapes pour générer l’application pas à pas. Si vous souhaitez télécharger le projet terminé, vous pouvez obtenir l’application complète à partir du [référentiel cosmosdb-angular](https://github.com/Azure-Samples/angular-cosmosdb) sur GitHub.

## <a name="use-mongoose-to-connect-to-azure-cosmos-db"></a>Utiliser Mongoose pour se connecter à Azure Cosmos DB

1. Installez le module npm mongoose. Cette API est normalement utilisée pour communiquer avec MongoDB.

    ```bash
    npm i mongoose --save
    ```

2. Créez un nouveau fichier nommé **mongo.js** dans le dossier **serveur**. Vous ajouterez les détails de connexion de votre compte Cosmos DB à ce fichier.

3. Copiez le code suivant dans **mongo.js**. Ce code :

    * Requiert Mongoose.

    * Remplace la promesse Mongo consistant à utiliser la promesse de base intégrée à ES6/ES2015 et versions ultérieures.

    * Appelle un fichier env afin de configurer certaines choses si vous êtes chargé du processus de site, de la production ou du développement. Vous allez créer ce fichier dans la section suivante.

    * Ajoutez la chaîne de connexion MongoDB définie dans le fichier env.

    * Crée une fonction de connexion nommée Mongoose.

    ```javascript
    const mongoose = require('mongoose');
    /**
     * Set to Node.js native promises
     * Per https://mongoosejs.com/docs/promises.html
     */
    mongoose.Promise = global.Promise;

    const env = require('./env/environment');

    // eslint-disable-next-line max-len
    const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

    function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
    }

    module.exports = {
      connect,
      mongoose
    };
    ```
    
4. Dans le volet Explorateur, créez un dossier dans le dossier **serveur** nommé **environnement**, et dans le dossier **environnement** créez un fichier nommé **environment.js** .

5. Grâce au fichier mongo.js, nous savons que nous devons inclure `dbName`, `key` et `cosmosPort`. Vous devez donc copier le code suivant dans **environment.js**.

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string-information"></a>Obtenir les informations de chaîne de connexion

1. Dans **environment.js**, remplacez la valeur de `port` par 10255. (Vous pouvez trouver votre port Cosmos DB dans le portail Azure)

    ```javascript
    const port = 10255;
    ```

2. Dans **environment.js**, remplacez la valeur de `accountName` par le nom de compte Azure Cosmos DB que vous avez créé à l’[étape 4](tutorial-develop-mongodb-nodejs-part4.md). 

3. Récupérez la clé primaire du compte Azure Cosmos DB en entrant la commande CLI suivante dans la fenêtre du terminal : 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    * `<cosmosdb-name>` est le nom du compte Azure Cosmos DB que vous avez créé à l’[étape 4](tutorial-develop-mongodb-nodejs-part4.md).

4. Copiez la clé primaire dans le fichier environment.js en tant que valeur `key`.

    Votre application possède maintenant toutes les informations nécessaires pour se connecter à Azure Cosmos DB. Ces informations peuvent également être récupérées dans le portail. Pour plus d’informations, consultez [Obtenir la chaîne de connexion MongoDB à personnaliser](connect-mongodb-account.md#GetCustomConnection). Dans le portail, le nom d’utilisateur correspond au dbName du fichier environments.js. 

## <a name="create-a-hero-model"></a>Créer un modèle héros

1. Dans le volet Explorateur, créez le fichier **hero.model.js** dans le dossier **serveur**.

2. Copiez le code suivant dans **hero.model.js**. Ce code fournit les fonctionnalités suivantes :

   * Requiert Mongoose.
   * Crée un nouveau schéma avec un ID, un nom et un message.
   * Crée un modèle à l’aide du schéma.
   * Exporte le modèle. 
   * Nomme la collection « Heroes » (au lieu de Heros, le nom par défaut de la collection selon les règles d’affectation de noms au pluriel de Mongoose).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Créer un service héros

1. Dans le volet Explorateur, créez le fichier **hero.service.js** dans le dossier **serveur**.

2. Copiez le code suivant dans **hero.service.js**. Ce code :

   * Obtient le modèle que vous venez de créer
   * Se connecte à la base de données
   * Crée une variable docquery qui utilise la méthode hero.find pour définir une requête qui renvoie tous les héros.
   * Exécute une requête avec docquery.exec avec la promesse d’obtenir une liste de tous les héros, où l’état de la réponse est 200. 
   * Renvoie le message d’erreur si l’état est 500
   * Étant donné que nous utilisons des modules, nous obtenons les héros. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="add-the-hero-service-to-routesjs"></a>Ajouter le service héros à routes.js

1. Dans Visual Studio Code, dans **routes.js**, commentez la fonction `res.send` qui envoie des exemples de données héros et ajoutez une ligne pour appeler la fonction `heroService.getHeroes` à la place.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

2. Dans **routes.js** le service héros est requis :

    ```javascript
    const heroService = require('./hero.service'); 
    ```

3. Dans **hero.service.js**, mettez à jour la fonction getHeroes pour obtenir les paramètres `req` et `res` comme suit :

    ```javascript
    function getHeroes(req, res) {
    ```

    Prenons quelques instants pour examiner et parcourir la chaîne d’appel. Tout d’abord, nous commençons par `index.js` qui configure le serveur de nœud. Nous remarquons qu’il configure et définit vos itinéraires. Votre fichier routes.js communique alors avec le service héros pour lui demander d’obtenir vos fonctions, comme getHeroes, et de transmettre les demandes et les réponses. Ici hero.service.js va extraire le modèle et se connecter à Mongo. Puis, il va exécuter getHeroes lorsqu’il est appelé et va retourner une réponse de 200. Cette opération se propage ensuite à travers la chaîne. 

## <a name="run-the-app"></a>Exécution de l'application

1. À présent, nous allons de nouveau exécuter l’application. Dans Visual Studio Code, enregistrez les modifications, cliquez sur le bouton **Déboguer** ![Icône de débogage dans Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png) sur le côté gauche, puis cliquez sur le bouton **Démarrer le débogage** ![Icône de débogage dans Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png).

3. Retournez dans le navigateur pour ouvrir les outils de développement et l’onglet Réseau. Accédez à l’adresse http://localhost:3000 pour trouver l’application.

    ![Nouveau compte Azure Cosmos DB dans le portail Azure](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

   Aucun héros est actuellement stocké dans l’application, mais à la prochaine étape du didacticiel nous ajouterons les fonctionnalités placer, envoyer et supprimer afin d’ajouter, mettre à jour et supprimer des héros de l’interface utilisateur à l’aide des connexions Mongoose à la base de données Azure Cosmos DB. 

## <a name="next-steps"></a>Étapes suivantes

Dans cette partie du didacticiel, vous avez effectué les tâches suivantes :

> [!div class="checklist"]
> * Utilisé les API Mongoose pour connecter les applications héros à Azure Cosmos DB 
> * Ajouté la fonctionnalité pour obtenir des héros à l’application

Vous pouvez passer à la partie suivante du didacticiel pour ajouter les fonctions Publier, Placer et Supprimer à l’application.

> [!div class="nextstepaction"]
> [Ajouter les fonctions Publier, Placer et Supprimer à l’application](tutorial-develop-mongodb-nodejs-part6.md)
