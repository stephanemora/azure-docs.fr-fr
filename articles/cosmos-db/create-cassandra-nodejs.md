---
title: 'Démarrage rapide : API Cassandra avec Node.js - Azure Cosmos DB'
description: Ce guide de démarrage rapide montre comment utiliser l’API Cassandra Azure Cosmos DB pour créer une application de profil avec Node.js
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.custom: quick start connect, mvc
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: e1015331af3b31fa34170db6c1729b8e38bcbd04
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53251048"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-and-azure-cosmos-db"></a>Démarrage rapide : Créer une application Cassandra avec Node.js et Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.JS](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Ce guide de démarrage rapide montre comment utiliser Node.js et [l’API Cassandra](cassandra-introduction.md) Azure Cosmos DB pour créer une application de profil en clonant un exemple de GitHub. Ce guide de démarrage rapide vous montre également comment utiliser le portail Azure web pour créer un compte Azure Cosmos DB.

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Vous pouvez rapidement créer et interroger des bases de données de documents, de tables, de paires clé/valeur et de graphes, lesquelles bénéficient toutes des fonctionnalités de distribution mondiale et de mise à l’échelle horizontale d’Azure Cosmos DB. 

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Vous pouvez également [essayer Azure Cosmos DB gratuitement](https://azure.microsoft.com/try/cosmosdb/) sans abonnement Azure, ni frais ni engagement.

Par ailleurs, vous devez avoir :
* [Node.js](https://nodejs.org/en/) version v0.10.29 ou ultérieure
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Création d’un compte de base de données

Pour pouvoir créer une base de données de documents, vous devez créer un compte Cassandra avec Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Nous allons maintenant cloner une application API Cassandra à partir de GitHub, configurer la chaîne de connexion et l'exécuter. Vous pouvez constater à quel point il est facile de travailler par programmation avec des données. 

1. Ouvrez une invite de commandes. Créez un dossier nommé `git-samples`. Ensuite, fermez l’invite de commandes.

    ```bash
    md "C:\git-samples"
    ```

2. Ouvrez une fenêtre de terminal git, comme git bash. Utilisez la commande `cd` pour ouvrir le nouveau dossier d’installation de l’exemple d’application.

    ```bash
    cd "C:\git-samples"
    ```

3. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Vérifier le code

Cette étape est facultative. Si vous voulez savoir comment le code crée les ressources de base de données, vous pouvez consulter les extraits de code suivants. Les extraits de code sont tous tirés du fichier `uprofile.js` dans le dossier `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started`. Sinon, vous pouvez passer à l’étape [Mise à jour de votre chaîne de connexion](#update-your-connection-string). 

* Les valeurs de nom d’utilisateur et de mot de passe ont été définies en utilisant la page de chaîne de connexion du portail Azure. `path\to\cert` fournit le chemin d’un certificat X509. 

   ```nodejs
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* Le `client` est initialisé avec les informations contactPoint. Le contactPoint est récupéré à partir du portail Azure.

    ```nodejs
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* Le `client` se connecte à l’API Cassandra Azure Cosmos DB.

    ```nodejs
    client.connect(next);
    ```

* Un espace de clés est créé.

    ```nodejs
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* Une table est créée.

   ```nodejs
   function createTable(next) {
    var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* Des entités clé/valeur sont insérées.

    ```nodejs
    ...
       {
          query: 'INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)',
          params: [5, 'IvanaV', 'Belgaum', '2017-10-3136']
        }
    ];
    client.batch(queries, { prepare: true}, next);
    ```

* Requête pour obtenir toutes les valeurs de clé.

    ```nodejs
   var query = 'SELECT * FROM uprofile.user';
    client.execute(query, { prepare: true}, function (err, result) {
      if (err) return next(err);
      result.rows.forEach(function(row) {
        console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
      }, this);
      next();
    });
    ```  
    
* Requête pour obtenir une paire clé-valeur.

    ```nodejs
    function selectById(next) {
        console.log("\Getting by id");
        var query = 'SELECT * FROM uprofile.user where user_id=1';
        client.execute(query, { prepare: true}, function (err, result) {
        if (err) return next(err);
            result.rows.forEach(function(row) {
            console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
        }, this);
        next();
        });
    }
    ```  

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application. La chaîne de connexion permet à votre application de communiquer avec votre base de données hébergée.

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez **Chaîne de connexion**. 

    Utilisez le ![bouton Copier](./media/create-cassandra-nodejs/copy.png) à droite de l’écran pour copier la valeur supérieure, c’est-à-dire le POINT DE CONTACT.

    ![Affichez et copiez les valeurs POINT DE CONTACT, NOM D’UTILISATEUR et MOT DE PASSE à partir de la page de chaîne de connexion du portail Azure](./media/create-cassandra-nodejs/keys.png)

2. Ouvrez le fichier `config.js` . 

3. Collez la valeur POINT DE CONTACT à partir du portail sur `<FillMEIN>` à la ligne 4.

    La ligne 4 doit maintenant ressembler à 

    `config.contactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350"`

4. Copiez la valeur NOM D’UTILISATEUR à partir du portail et collez-la sur `<FillMEIN>` à la ligne 2.

    La ligne 2 doit maintenant ressembler à 

    `config.username = 'cosmos-db-quickstart';`
    
5. Copiez la valeur MOT DE PASSE à partir du portail et collez-la sur `<FillMEIN>` à la ligne 3.

    La ligne 3 doit maintenant ressembler à

    `config.password = '2Ggkr662ifxz2Mg==';`

6. Enregistrez le fichier `config.js`.
    
## <a name="use-the-x509-certificate"></a>Utiliser le certificat X509

1. Téléchargez le certificat racine Baltimore CyberTrust localement à partir de [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Renommez le fichier avec l’extension de fichier `.cer`.

   Le certificat a le numéro de série `02:00:00:b9` et l’empreinte digitale SHA1 `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Ouvrez `uprofile.js` et changez le `path\to\cert` pour pointer vers votre nouveau certificat.

3. Enregistrez `uprofile.js`.

## <a name="run-the-nodejs-app"></a>Exécuter l’application Node.js

1. Dans la fenêtre de terminal git, exécutez `npm install` pour installer les modules npm nécessaires.

2. Exécutez `node uprofile.js` pour démarrer votre application Node.

3. Vérifiez que les résultats sont corrects à partir de la ligne de commande.

    ![Consulter et vérifier la sortie](./media/create-cassandra-nodejs/output.png)

    Appuyez sur CTRL + C pour arrêter l’exécution du programme et fermer la fenêtre de console. 

4. Dans le portail Azure, ouvrez **l’Explorateur de données** pour interroger, modifier et utiliser ces nouvelles données. 

    ![Afficher les données dans l’Explorateur de données](./media/create-cassandra-nodejs/data-explorer.png) 

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer un conteneur à l’aide de l’Explorateur de données, et à exécuter une application. Vous pouvez maintenant importer des données supplémentaires à votre compte Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données Cassandra dans Azure Cosmos DB](cassandra-import-data.md)


