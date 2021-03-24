---
title: 'Démarrage rapide : API Cassandra avec Node.js - Azure Cosmos DB'
description: Ce guide de démarrage rapide montre comment utiliser l’API Cassandra Azure Cosmos DB pour créer une application de profil avec Node.js
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/10/2021
ms.custom: devx-track-js
ms.openlocfilehash: 126ece1327fa92c9b92c587922f1b8d9335d1a01
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100559285"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Démarrage rapide : Créer une application Cassandra avec le SDK Node.js et Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.JS](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Dans ce guide de démarrage rapide, vous allez créer un compte d’API Cassandra Azure Cosmos DB et utiliser une application Node.js Cassandra clonée à partir de GitHub pour créer une base de données et un conteneur Cassandra. Azure Cosmos DB est un service de base de données multimodèle qui vous permet de créer et interroger rapidement des bases de données de documents, de tables, de paires clé/valeur et de graphes avec des capacités de distribution mondiale et de mise à l’échelle horizontale.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Vous pouvez également [essayer Azure Cosmos DB gratuitement](https://azure.microsoft.com/try/cosmosdb/) sans abonnement Azure, ni frais ni engagement.

Par ailleurs, vous devez avoir :
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) version v0.10.29 ou ultérieure
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Création d’un compte de base de données

Pour pouvoir créer une base de données de documents, vous devez créer un compte Cassandra avec Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Nous allons maintenant cloner une application API Cassandra à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous pouvez constater à quel point il est facile de travailler par programmation avec des données. 

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

1. Installez les dépendances Node.js avec npm.

    ```bash
    npm install
    ```

## <a name="review-the-code"></a>Vérifier le code

Cette étape est facultative. Si vous voulez savoir comment le code crée les ressources de base de données, vous pouvez consulter les extraits de code suivants. Les extraits de code sont tous tirés du fichier `uprofile.js` dans le dossier `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started`. Sinon, vous pouvez passer à l’étape [Mise à jour de votre chaîne de connexion](#update-your-connection-string). 

* Les valeurs de nom d’utilisateur et de mot de passe ont été définies en utilisant la page de chaîne de connexion du portail Azure. 

   ```javascript
    let authProvider = new cassandra.auth.PlainTextAuthProvider(
        config.username,
        config.password
    );
   ```

* Le `client` est initialisé avec les informations contactPoint. Le contactPoint est récupéré à partir du portail Azure.

    ```javascript
    let client = new cassandra.Client({
        contactPoints: [`${config.contactPoint}:10350`],
        authProvider: authProvider,
        localDataCenter: config.localDataCenter,
        sslOptions: {
            secureProtocol: "TLSv1_2_method"
        },
    });
    ```

* Le `client` se connecte à l’API Cassandra Azure Cosmos DB.

    ```javascript
    client.connect();
    ```

* Un espace de clés est créé.

    ```javascript
    var query =
        `CREATE KEYSPACE IF NOT EXISTS ${config.keySpace} WITH replication = {'class': 'NetworkTopologyStrategy', 'datacenter' : '1' }`;
    await client.execute(query);
  }
    ```

* Une table est créée.

   ```javascript
    query =
        `CREATE TABLE IF NOT EXISTS ${config.keySpace}.user (user_id int PRIMARY KEY, user_name text, user_bcity text)`;
    await client.execute(query);
   },
   ```

* Des entités clé/valeur sont insérées.

    ```javascript
    const arr = [
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (1, 'AdrianaS', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (2, 'JiriK', 'Toronto')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (3, 'IvanH', 'Mumbai')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (4, 'IvanH', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (5, 'IvanaV', 'Belgaum')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (6, 'LiliyaB', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (7, 'JindrichH', 'Buenos Aires')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (8, 'AdrianaS', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (9, 'JozefM', 'Seattle')`,
    ];
    for (const element of arr) {
        await client.execute(element);
    }
    ```

* Requête pour obtenir toutes les valeurs de clé.

    ```javascript
    query = `SELECT * FROM ${config.keySpace}.user`;
    const resultSelect = await client.execute(query);

    for (const row of resultSelect.rows) {
        console.log(
            "Obtained row: %d | %s | %s ",
            row.user_id,
            row.user_name,
            row.user_bcity
        );
    }
    ```  

* Requête pour obtenir une paire clé-valeur.

    ```javascript
    query = `SELECT * FROM ${config.keySpace}.user where user_id=1`;
    const resultSelectWhere = await client.execute(query);

    for (const row of resultSelectWhere.rows) {
        console.log(
            "Obtained row: %d | %s | %s ",
            row.user_id,
            row.user_name,
            row.user_bcity
        );
    }
    ```  

* Fermez la connexion. 

    ```javascript
    client.shutdown();
    ```  

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application. La chaîne de connexion permet à votre application de communiquer avec votre base de données hébergée.

1. Dans votre compte Azure Cosmos DB, sur le [portail Azure](https://portal.azure.com/), sélectionnez **Chaîne de connexion**. 

1. Utilisez le bouton :::image type="icon" source="./media/create-cassandra-nodejs/copy.png"::: à droite de l’écran pour copier la valeur supérieure, c’est-à-dire le POINT DE CONTACT.

    :::image type="content" source="./media/create-cassandra-nodejs/keys.png" alt-text="Afficher et copier les valeurs de POINT DE CONTACT, NOM D’UTILISATEUR et MOT DE PASSE à partir de la page de chaîne de connexion du portail Azure":::

1. Ouvrez le fichier `config.js` . 

1. Collez la valeur POINT DE CONTACT à partir du portail sur `'CONTACT-POINT` à la ligne 9.

    La ligne 9 doit maintenant se présenter comme ceci : 

    `contactPoint: "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com",`

1. Copiez la valeur NOM D’UTILISATEUR à partir du portail et collez-la sur `<FillMEIN>` à la ligne 2.

    La ligne 2 doit maintenant ressembler à 

    `username: 'cosmos-db-quickstart',`

1. Copiez la valeur MOT DE PASSE à partir du portail et collez-la sur `USERNAME` à la ligne 8.

    La ligne 8 doit maintenant ressembler à

    `password: '2Ggkr662ifxz2Mg==',`

1. Remplacez REGION par la région Azure où vous avez créé cette ressource.

1. Enregistrez le fichier `config.js`.


## <a name="run-the-nodejs-app"></a>Exécuter l’application Node.js

1. Dans la fenêtre de terminal, vérifiez que vous êtes bien dans l’exemple de répertoire que vous avez cloné précédemment :

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

1. Exécutez votre application Node :

    ```bash
    npm start
    ```

4. Vérifiez que les résultats sont corrects à partir de la ligne de commande.

    :::image type="content" source="./media/create-cassandra-nodejs/output.png" alt-text="Afficher et vérifier la sortie":::

    Appuyez sur CTRL+C pour arrêter l’exécution du programme et fermer la fenêtre de console. 

5. Dans le portail Azure, ouvrez **l’Explorateur de données** pour interroger, modifier et utiliser ces nouvelles données. 

    :::image type="content" source="./media/create-cassandra-nodejs/data-explorer.png" alt-text="Afficher les données dans l’Explorateur de données"::: 

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez découvert comment créer un compte Azure Cosmos DB avec l’API Cassandra et exécuter une application Node.js Cassandra qui crée une base de données et un conteneur Cassandra. Vous pouvez maintenant importer des données supplémentaires dans votre compte Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données Cassandra dans Azure Cosmos DB](cassandra-import-data.md)