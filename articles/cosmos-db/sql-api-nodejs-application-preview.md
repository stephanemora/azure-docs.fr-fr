---
title: Générer une application web Node.js pour Azure Cosmos DB | Microsoft Docs
description: Ce didacticiel Node.js explique comment utiliser Microsoft Azure Cosmos DB pour stocker des données et y accéder à partir d’une application web Express Node.js hébergée sur les sites web Azure.
keywords: Développement d’applications, didacticiel de base de données, apprendre Node.js, didacticiel Node.js
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 07/31/2018
ms.author: sngun
ms.openlocfilehash: 9f7744c7743107b3587bd63a6e7681a6f1e42c00
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39608968"
---
# <a name="_Toc395783175"></a>Créer une application web Node.js avec Azure Cosmos DB et le Kit de développement logiciel (SDK) Node.js (Préversion)
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.JS](sql-api-nodejs-application.md)
> * [Node.js - préversion v2.0](sql-api-nodejs-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

Ce didacticiel Node.js vous explique comment utiliser le compte API SQL d’Azure Cosmos DB pour stocker des données et y accéder à partir d’une application Express Node.js hébergée sur les sites web Azure. Dans ce didacticiel, vous générerez une simple application basée sur le web (application de tâches), qui vous permet de créer, récupérer, et terminer des tâches. Ces dernières sont stockées en tant que documents JSON dans AzureCosmos DB. L’illustration suivante montre une capture d’écran de l’application de tâches :

![Capture d’écran de l’application My Todo List créée dans ce didacticiel Node.js](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

Ce didacticiel montre comment créer un compte API SQL d’Azure Cosmos DB à l’aide du portail Azure. Vous générerez et exécuterez ensuite une application web basée sur le Kit de développement logiciel (SDK) Node.js pour créer une base de données, un conteneur et ajouter des éléments au conteneur. Ce didacticiel utilise la version 2.0 du Kit de développement logiciel (SDK) Node.js, qui est actuellement en préversion.

Vous pouvez également obtenir l’exemple terminé sur [GitHub][GitHub]. Lire simplement le fichier [Lisez-moi](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md) pour obtenir des instructions sur l’exécution de l’application.

## <a name="_Toc395783176"></a>Configuration requise

Avant de suivre les instructions de cet article, vérifiez que les éléments suivants sont installés :

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] version 6.10 ou ultérieure.
* [Générateur Express](http://www.expressjs.com/starter/generator.html) (installation possible via `npm install express-generator -g`)
* [Git][Git].

## <a name="_Toc395637761"></a>Étape 1 : création d’un compte de base de données Azure Cosmos DB
Commençons par créer un compte Azure Cosmos DB. Si vous possédez déjà un compte ou si vous utilisez l’émulateur Azure Cosmos DB pour ce didacticiel, vous pouvez passer à [l’étape 2 : création d’une application Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Étape 2 : création d’une application Node.js
Voyons maintenant comment créer un projet Node.js « Hello World » de base à l’aide de l’infrastructure [Express](http://expressjs.com/) .

1. Ouvrez votre terminal préféré, par exemple l’invite de commande Node.js.
2. Accédez au répertoire dans lequel vous souhaitez stocker la nouvelle application.
3. Utilisez le générateur Express pour générer une nouvelle application appelée **todo**.

   ```bash
   express todo
   ```
4. Ouvrez votre nouveau répertoire **todo** et installez les dépendances.

   ```bash
   cd todo
   npm install
   ```
5. Exécutez votre nouvelle application.

   ```bash
   npm start
   ```

6. Vous pouvez afficher votre nouvelle application en accédant à l’adresse [http://localhost:3000](http://localhost:3000) dans votre navigateur.
   
    ![Découverte de Node.js - Capture d’écran de l’application Hello World dans une fenêtre de navigateur](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

 Arrêtez l’application en appuyant sur CTRL + C dans la fenêtre de terminal, puis cliquez sur **y** pour arrêter le programme de traitement par lots.

## <a name="_Toc395783179"></a>144tape 3 : Installer les modules requis

Le fichier **package.json** est l'un des fichiers créés à la racine du projet. Il contient une liste de modules supplémentaires qui sont nécessaires pour les applications Node.js. Ensuite, lorsque vous déployez cette application sur des sites web Azure, ce fichier permet de déterminer quels modules doivent être installés sur Azure pour la prise en charge de votre application. Vous avez besoin d'installer deux autres packages pour ce didacticiel.

1. Ouvrez le terminal, installez le module **async** via npm.

   ```bash
   npm install async --save
   ```

2. Installer le module **@azure/cosmos** via npm. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>Étape 4 : Utiliser le service Azure Cosmos DB dans une application Node
Maintenant que vous avez terminé l’installation et la configuration initiales, ensuite vous écrirez du code qui est requis par l’application de tâches pour communiquer avec Azure Cosmos DB.

### <a name="create-the-model"></a>Création du modèle
1. À la racine du répertoire de projet, créez un répertoire nommé **models**  

2. Dans le répertoire **models**, créez un fichier nommé **taskDao.js**. Ce fichier contient le code nécessaire pour créer la base de données ainsi que le conteneur et définit des méthodes pour lire, mettre à jour, créer et rechercher les tâches dans Azure Cosmos DB. 

3. Copiez le code suivant dans le fichier **taskDao.js**

   ```nodejs
   // @ts-check
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const debug = require("debug")("todo:taskDao");
   class TaskDao {
     /**
      * Manages reading, adding, and updating Tasks in Cosmos DB
      * @param {CosmosClient} cosmosClient
      * @param {string} databaseId
      * @param {string} containerId
      */
     constructor(cosmosClient, databaseId, containerId) {
       this.client = cosmosClient;
       this.databaseId = databaseId;
       this.collectionId = containerId;

       this.database = null;
       this.container = null;
     }

     async init() {
       debug("Setting up the database...");
       const dbResponse = await this.client.databases.createIfNotExists({
         id: this.databaseId
       });
       this.database = dbResponse.database;
       debug("Setting up the database...done!");
       debug("Setting up the container...");
       const coResponse = await this.database.containers.createIfNotExists({
         id: this.collectionId
       });
       this.container = coResponse.container;
       debug("Setting up the container...done!");
     }

     async find(querySpec) {
       debug("Querying for items from the database");
       if (!this.container) {
         throw new Error("Collection is not initialized.");
       }
       const { result: results } = await this.container.items
        .query(querySpec)
        .toArray();
      return results;
    }

    async addItem(item) {
      debug("Adding an item to the database");
      item.date = Date.now();
      item.completed = false;
      const { body: doc } = await this.container.items.create(item);
      return doc;
    }

    async updateItem(itemId) {
      debug("Update an item in the database");
      const doc = await this.getItem(itemId);
      doc.completed = true;

      const { body: replaced } = await this.container.item(itemId).replace(doc);
      return replaced;
    }

    async getItem(itemId) {
      debug("Getting an item from the database");
      const { body } = await this.container.item(itemId).read();
      return body;
    }
  }

   module.exports = TaskDao;
   ```
4. Enregistrez et fermez le fichier **taskDao.js** .  

### <a name="create-the-controller"></a>Création du contrôleur

1. Dans le répertoire **routes** de votre projet, créez un fichier nommé **tasklist.js**.  

2. Ajoutez le code suivant dans **tasklist.js**. Ce code charge CosmosClient et les modules asynchrones, qui sont utilisés par **tasklist.js**. Cela définit également la classe **TaskList** à qui est transmise en tant qu’instance de l’objet **TaskDao** défini précédemment :
   
   ```nodejs
   const TaskDao = require("../models/TaskDao");

   class TaskList {
     /**
      * Handles the various APIs for displaying and managing tasks
      * @param {TaskDao} taskDao
     */
    constructor(taskDao) {
    this.taskDao = taskDao;
    }
    async showTasks(req, res) {
      const querySpec = {
        query: "SELECT * FROM root r WHERE r.completed=@completed",
        parameters: [
          {
            name: "@completed",
            value: false
          }
        ]
      };

      const items = await this.taskDao.find(querySpec);
      res.render("index", {
        title: "My ToDo List ",
        tasks: items
      });
    }

    async addTask(req, res) {
      const item = req.body;

      await this.taskDao.addItem(item);
      res.redirect("/");
    }

    async completeTask(req, res) {
      const completedTasks = Object.keys(req.body);
      const tasks = [];

      completedTasks.forEach(task => {
        tasks.push(this.taskDao.updateItem(task));
      });

      await Promise.all(tasks);

      res.redirect("/");
    }
  }

  module.exports = TaskList;
   ```

3. Enregistrez et fermez le fichier **tasklist.js** .

### <a name="add-configjs"></a>Ajout de config.js

1. À la racine du répertoire de projet, créez un fichier nommé **config.js**. 

2. Ajoutez le code suivant au fichier **config.js**. Ce code définit les paramètres de configuration et les valeurs nécessaires à notre application.
   
   ```nodejs
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. Dans le fichier **config.js**, mettez à jour les valeurs des paramètres HOST et AUTH_KEY en indiquant les valeurs trouvées dans la page Clés de votre compte Azure Cosmos DB, dans le [portail Microsoft Azure](https://portal.azure.com). 

4. Enregistrez et fermez le fichier **config.js** .

### <a name="modify-appjs"></a>Modification de app.js
1. Dans le répertoire du projet, ouvrez le fichier **app.js** . Ce fichier a été créé précédemment lors de la création de l'application web Express.  

2. Ajoutez le code suivant au fichier **app.js**. Ce code définit le fichier de configuration à utiliser et procède à la lecture des valeurs de ce fichier dans des variables que nous utiliserons prochainement. 
   
   ```nodejs
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const config = require("./config");
   const TaskList = require("./routes/tasklist");
   const TaskDao = require("./models/taskDao");

   const express = require("express");
   const path = require("path");
   const logger = require("morgan");
   const cookieParser = require("cookie-parser");
   const bodyParser = require("body-parser");

   const app = express();

   // view engine setup
   app.set("views", path.join(__dirname, "views"));
   app.set("view engine", "jade");

   // uncomment after placing your favicon in /public
   //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
   app.use(logger("dev"));
   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(cookieParser());
   app.use(express.static(path.join(__dirname, "public")));

   //Todo App:
   const cosmosClient = new CosmosClient({
     endpoint: config.host,
     auth: {
       masterKey: config.authKey
     }
   });
   const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId);
   const taskList = new TaskList(taskDao);
   taskDao
     .init(err => {
       console.error(err);
     })
     .catch(err => {
       console.error(err);
       console.error("Shutting down because there was an error settinig up the database.");
       process.exit(1);
     });

   app.get("/", (req, res, next) => taskList.showTasks(req, res).catch(next));
   app.post("/addtask", (req, res, next) => taskList.addTask(req, res).catch(next));
   app.post("/completetask", (req, res, next) => taskList.completeTask(req, res).catch(next));
   app.set("view engine", "jade");

   // catch 404 and forward to error handler
   app.use(function(req, res, next) {
     const err = new Error("Not Found");
     err.status = 404;
     next(err);
   });

   // error handler
   app.use(function(err, req, res, next) {
     // set locals, only providing error in development
     res.locals.message = err.message;
     res.locals.error = req.app.get("env") === "development" ? err : {};

     // render the error page
     res.status(err.status || 500);
     res.render("error");
   });

   module.exports = app;
   ```

3. Enfin, enregistrez et fermez le fichier **app.js**. Nous avons presque terminé.

## <a name="_Toc395783181"></a>Étape 5 : création d'une interface utilisateur
Intéressons-nous à présent à la création de l'interface utilisateur pour permettre à un utilisateur d'interagir réellement avec notre application. L'application Express que nous avons créée utilise **Jade** comme moteur de vue. Pour plus d’informations sur Jade, voir [http://jade-lang.com/](http://jade-lang.com/).

1. Le fichier **layout.jade** du répertoire **views** sert de modèle global aux autres fichiers **.jade**. Dans cette étape, vous allez le modifier pour utiliser [Twitter Bootstrap](https://github.com/twbs/bootstrap), qui est un kit de ressources qui facilite la conception d'un site web bien présenté.  

2. Ouvrez le fichier **layout.jade** trouvé dans le dossier **views** et remplacez le contenu par le code suivant :

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Ce code demande au moteur **Jade** de générer un rendu HTML pour notre application et crée un **bloc** intitulé **content** dans lequel nous pouvons fournir la mise en page de nos pages de contenu.

    Enregistrez et fermez ce fichier **layout.jade** .

3. Ouvrez maintenant le fichier **index.jade** , la vue qui sera utilisée par l'application, et remplacez le contenu du fichier par le code suivant :

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
       form.well(action="/addtask", method="post")
        label Item Name:
        input(name="name", type="textbox")
        label Item Category:
        input(name="category", type="textbox")
        br
        button.btn(type="submit") Add item
   ```

Ce code étend la mise en page et fournit du contenu pour l’espace réservé **content** que nous avons vu plus haut dans le fichier **layout.jade**.
   
Dans cette mise en page, nous avons créé deux fichiers HTML.

Le premier formulaire contient un tableau pour nos données et un bouton qui permet de mettre à jour des éléments en appelant la méthode **/completeTask** de notre contrôleur.
    
Le deuxième formulaire contient deux champs d'entrée et un bouton qui permet de créer un élément en appelant la méthode **/addtask** de notre contrôleur.

Ceci devrait être suffisant pour que notre application puisse fonctionner.

## <a name="_Toc395783181"></a>Étape 6 : exécution locale de l'application
1. Pour tester l’application sur votre ordinateur local, exécutez la commande `npm start` dans le terminal pour démarrer votre application, puis actualisez la page [http://localhost:3000](http://localhost:3000) dans votre de navigateur. La page doit maintenant ressembler à l’image ci-dessous :
   
    ![Capture d'écran de l'application MyTodo List dans une fenêtre de navigateur](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Si vous recevez une erreur liée à la mise en retrait dans le fichier layout.jade ou index.jade, assurez-vous que les deux premières lignes des deux fichiers sont justifiées à gauche et ne présentent aucun espace. Supprimez tout espace éventuel dans les deux premières lignes, enregistrez les deux fichiers, puis actualisez la fenêtre du navigateur. 

2. Utilisez les champs Élément, Nom d’élément et Catégorie pour saisir une nouvelle tâche, puis cliquez sur **Ajouter des éléments**. Cette opération permet de créer un document présentant ces propriétés dans Azure Cosmos DB. 
3. La page doit se mettre à jour et afficher le nouvel élément créé dans la liste des tâches.
   
    ![Capture d'écran de l'application avec un nouvel élément dans la liste de tâches](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)
4. Pour terminer une tâche, activez simplement la case à cocher dans la colonne Complete, puis cliquez sur **Update tasks**. Cette opération met à jour le document que vous avez déjà créé et le supprime de la vue.

5. Pour arrêter l’application, appuyez sur CTRL + C dans la fenêtre de terminal, puis saisissez **Y** pour arrêter le traitement par lots.

## <a name="_Toc395783182"></a>Étape 7 : Déploiement de votre projet de développement d’application sur Sites Web Azure
1. Si vous ne l'avez pas encore fait, activez un référentiel git pour votre site web Azure. Vous trouverez des instructions sur la marche à suivre dans la rubrique [Déploiement Git local vers Azure App Service](../app-service/app-service-deploy-local-git.md) .
2. Ajoutez votre site web Azure en tant que git distant.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Procédez au déploiement par un envoi au git distant.
   
        git push azure master
4. En quelques secondes, git achève la publication de votre application web et lance un navigateur, dans lequel vous pouvez voir votre réalisation exécutée dans Azure.

    Félicitations ! Vous venez de créer votre première application web Express Node.js avec Azure Cosmos DB et de la publier sur les sites web Azure.

    Si vous souhaitez télécharger ou vous référer à l’application de référence complète de ce didacticiel, vous pouvez la télécharger à partir de [GitHub][GitHub].

## <a name="_Toc395637775"></a>Étapes suivantes

* Vous voulez effectuer un test des performances et de la mise à l’échelle avec Azure Cosmos DB ? Consultez la page [Test des performances et de la mise à l’échelle avec Azure Cosmos DB](performance-testing.md).
* Découvrez comment [surveiller un compte Azure Cosmos DB](monitor-accounts.md).
* Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).
* Parcourez la [documentation Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

