---
title: 'Démarrage rapide : API Cassandra avec Python - Azure Cosmos DB'
description: Ce guide de démarrage rapide montre comment utiliser l’API Apache Cassandra Azure Cosmos DB pour créer une application de profil avec Python.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: python
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-python
ms.openlocfilehash: 2521a579538b272ac4990c3d390fb1aa6f2e58a0
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876527"
---
# <a name="quickstart-build-a-cassandra-app-with-python-sdk-and-azure-cosmos-db"></a>Démarrage rapide : Générer une application Cassandra avec le SDK Python et Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.JS](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Dans ce démarrage rapide, vous allez créer un compte d’API Cassandra Azure Cosmos DB et utiliser une application Python Cassandra clonée à partir de GitHub pour créer une base de données et un conteneur Cassandra. Azure Cosmos DB est un service de base de données multimodèle qui vous permet de créer et interroger rapidement des bases de données de documents, de tables, de paires clé/valeur et de graphes avec des capacités de distribution mondiale et de mise à l’échelle horizontale.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ou bien [essayez Azure Cosmos DB gratuitement](https://azure.microsoft.com/try/cosmosdb/) sans abonnement Azure.
- [Python 2.7.14+ ou 3.4+](https://www.python.org/downloads/).
- [Git](https://git-scm.com/downloads).
- [Pilote Python pour Apache Cassandra](https://github.com/datastax/python-driver).

## <a name="create-a-database-account"></a>Création d’un compte de base de données

Pour pouvoir créer une base de données de documents, vous devez créer un compte Cassandra avec Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Nous allons maintenant cloner une application API Cassandra à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous pouvez constater à quel point il est facile de travailler par programmation avec des données. 

1. Ouvrez une invite de commandes. Créez un dossier nommé `git-samples`. Ensuite, fermez l’invite de commandes.

    ```bash
    md "C:\git-samples"
    ```

2. Ouvrez une fenêtre de terminal git comme Git Bash et utilisez la commande `cd` pour accéder au nouveau dossier d’installation pour l’exemple d’application.

    ```bash
    cd "C:\git-samples"
    ```

3. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## <a name="review-the-code"></a>Vérifier le code

Cette étape est facultative. Si vous voulez savoir comment le code crée les ressources de base de données, vous pouvez consulter les extraits de code suivants. Tous les extraits de code sont tirés du fichier *pyquickstart.py*. Sinon, vous pouvez passer à l’étape [Mise à jour de votre chaîne de connexion](#update-your-connection-string). 

* Les valeurs de nom d’utilisateur et de mot de passe ont été définies en utilisant la page de chaîne de connexion du portail Azure. `path\to\cert` fournit le chemin d’un certificat X509. 

   ```python
    ssl_opts = {
            'ca_certs': 'path\to\cert',
            'ssl_version': ssl.PROTOCOL_TLSv1_2
            }
    auth_provider = PlainTextAuthProvider( username=cfg.config['username'], password=cfg.config['password'])
    cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider, ssl_options=ssl_opts)
    session = cluster.connect()
   
   ```

* Le `cluster` est initialisé avec les informations contactPoint. Le contactPoint est récupéré à partir du portail Azure.

    ```python
   cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider)
    ```

* Le `cluster` se connecte à l’API Cassandra Azure Cosmos DB.

    ```python
    session = cluster.connect()
    ```

* Un espace de clés est créé.

    ```python
   session.execute('CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }')
    ```

* Une table est créée.

   ```
   session.execute('CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)');
   ```

* Des entités clé/valeur sont insérées.

    ```Python
    insert_data = session.prepare("INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)")
    session.execute(insert_data, [1,'Lybkov','Seattle'])
    session.execute(insert_data, [2,'Doniv','Dubai'])
    session.execute(insert_data, [3,'Keviv','Chennai'])
    session.execute(insert_data, [4,'Ehtevs','Pune'])
    session.execute(insert_data, [5,'Dnivog','Belgaum'])
    ....
    
    ```

* Requête pour obtenir toutes les valeurs de clé.

    ```Python
    rows = session.execute('SELECT * FROM uprofile.user')
    ```  
    
* Requête pour obtenir une paire clé-valeur.

    ```Python
    
    rows = session.execute('SELECT * FROM uprofile.user where user_id=1')
    ```  

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application. La chaîne de connexion permet à votre application de communiquer avec votre base de données hébergée.

1. Dans votre compte Azure Cosmos DB, sur le [portail Azure](https://portal.azure.com/), sélectionnez **Chaîne de connexion**. 

1. Utilisez le bouton :::image type="icon" source="./media/create-cassandra-python/copy.png"::: à droite de l’écran pour copier la valeur supérieure, c’est-à-dire le POINT DE CONTACT. 

   :::image type="content" source="./media/create-cassandra-python/keys.png" alt-text="Afficher et copier un nom d’utilisateur, un mot de passe et un point de contact dans le panneau de chaîne de connexion du portail Azure":::

1. Ouvrez le fichier *config.py*. 

1. Collez la valeur POINT DE CONTACT à partir du portail sur `<FILLME>` à la ligne 10.

    La ligne 10 doit maintenant ressembler à 

    `'contactPoint': 'cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350'`

1. Copiez la valeur NOM D’UTILISATEUR à partir du portail et collez-la sur `<FILLME>` à la ligne 6.

    La ligne 6 doit maintenant ressembler à 

    `'username': 'cosmos-db-quickstart',`
    
1. Copiez la valeur MOT DE PASSE à partir du portail et collez-la sur `<FILLME>` à la ligne 8.

    La ligne 8 doit maintenant ressembler à

    `'password' = '2Ggkr662ifxz2Mg==`';`

1. Enregistrez le fichier *config.py*.
    
## <a name="use-the-x509-certificate"></a>Utiliser le certificat X509

1. Téléchargez le certificat racine Baltimore CyberTrust localement à partir de [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Renommez le fichier avec l’extension de fichier *.cer*.

   Le certificat a le numéro de série `02:00:00:b9` et l’empreinte digitale SHA1 `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Ouvrez *pyquickstart.py* et changez `path\to\cert` pour le faire pointer vers votre nouveau certificat.

3. Enregistrez *pyquickstart.py*.

## <a name="run-the-python-app"></a>Exécuter l’application Python

1. Utilisez la commande cd dans le terminal git pour passer au dossier `azure-cosmos-db-cassandra-python-getting-started`. 

2. Exécutez les commandes suivantes pour installer les modules nécessaires :

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. Exécutez la commande suivante pour démarrer votre application Python :

    ```
    python pyquickstart.py
    ```

3. Vérifiez que les résultats sont corrects à partir de la ligne de commande.

    Appuyez sur CTRL+C pour arrêter l’exécution du programme et fermer la fenêtre de console. 

    :::image type="content" source="./media/create-cassandra-python/output.png" alt-text="Afficher et vérifier la sortie":::
    
4. Dans le portail Azure, ouvrez **l’Explorateur de données** pour interroger, modifier et utiliser ces nouvelles données. 

    :::image type="content" source="./media/create-cassandra-python/data-explorer.png" alt-text="Afficher les données dans l’Explorateur de données":::

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez découvert comment créer un compte Azure Cosmos DB avec l’API Cassandra et exécuter une application Python.js Cassandra qui crée une base de données et un conteneur Cassandra. Vous pouvez maintenant importer des données supplémentaires dans votre compte Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données Cassandra dans Azure Cosmos DB](cassandra-import-data.md)

