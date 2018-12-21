---
title: 'Azure Cosmos DB : Créer une application avec Python et l’API SQL'
description: Cet article présente un exemple de code Python que vous pouvez utiliser pour vous connecter à l’API SQL d’Azure Cosmos DB et pour l’interroger.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.custom: quick start connect, mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: sngun
ms.openlocfilehash: 6f3f1f6eb89127e6b0ac0e2983a27dfd731651cb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094234"
---
# <a name="azure-cosmos-db-build-a-sql-api-app-with-python-and-the-azure-portal"></a>Azure Cosmos DB : Créer une application d’API SQL avec Python et le portail Azure

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (préversion)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.JS](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce guide de démarrage rapide explique comment créer, à l’aide du portail Azure, un compte [d’API SQL](sql-api-introduction.md) Azure Cosmos DB, une base de données de documents, ainsi qu’un conteneur. Ensuite, vous créez et exécutez une application console à l’aide du kit SDK Python pour [l’API SQL](sql-api-sdk-python.md). Ce guide de démarrage rapide utilise la version 3.0 du [kit SDK Python].(https://pypi.org/project/azure-cosmos)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)][!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Prérequis

* [Python 3.6](https://www.python.org/downloads/) avec \<l’emplacement d’installation\>\Python36 et \<l’emplacement d’installation>\Python36\Scripts ajoutés à votre chemin d’accès. 
* [Visual Studio Code](https://code.visualstudio.com/)
* [Extension Python pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Création d’un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Ajouter une collection

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Ajouter un exemple de données

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Interroger vos données

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Nous allons à présent cloner une application API SQL à partir de GitHub, définir la chaîne de connexion et l’exécuter.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  
    
## <a name="review-the-code"></a>Vérifier le code

Cette étape est facultative. Pour savoir comment les ressources de base de données sont créées dans le code, vous pouvez examiner les extraits de code suivants. Sinon, vous pouvez passer à l’étape [Mise à jour de votre chaîne de connexion](#update-your-connection-string). 

Notez que si vous avez déjà utilisé la version précédente du kit SDK Python, vous connaissez peut-être les termes « collection » et « document ». Étant donné qu’Azure Cosmos DB prend en charge plusieurs modèles d’API, les versions 3.0 et ultérieures du kit SDK Python utilisent les termes génériques « conteneur » (qui peut être une collection, un graphe ou une table) et « élément » (qui décrit le contenu du conteneur).

Tous les extraits suivants proviennent du fichier `CosmosGetStarted.py`.

* Le CosmosClient est initialisé.

    ```python
    # Initialize the Cosmos client
    client = cosmos_client.CosmosClient(url_connection=config['ENDPOINT'], auth={'masterKey': config['MASTERKEY']})
    ```

* Une nouvelle base de données est créée.

    ```python
    # Create a database
    db = client.CreateDatabase({ 'id': config['DATABASE'] })
    ```

* Une nouvelle collection est créée.

    ```python
    # Create collection options
    options = {
        'offerThroughput': 400
    }

    # Create a container
    container = client.CreateContainer(db['_self'], container_definition, options)
    ```

* Certains éléments sont ajoutés au conteneur.

    ```python
    # Create and add some items to the container
    item1 = client.CreateItem(container['_self'], {
        'serverId': 'server1',
        'Web Site': 0,
        'Cloud Service': 0,
        'Virtual Machine': 0,
        'message': 'Hello World from Server 1!'
        }
    )

    item2 = client.CreateItem(container['_self'], {
        'serverId': 'server2',
        'Web Site': 1,
        'Cloud Service': 0,
        'Virtual Machine': 0,
        'message': 'Hello World from Server 2!'
        }
    )
    ```

* Une requête est effectuée grâce à SQL

    ```python
    query = {'query': 'SELECT * FROM server s'}

    options = {}
    options['enableCrossPartitionQuery'] = True
    options['maxItemCount'] = 2

    result_iterable = client.QueryItems(container['_self'], query, options)
    for item in iter(result_iterable):
        print(item['message'])
    ```

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application.

1. Dans le [portail Azure](https://portal.azure.com/), dans votre compte Azure Cosmos DB, cliquez sur **Clés** dans le volet de navigation de gauche. Vous utiliserez les boutons de copie sur le côté droit de l’écran pour copier **l’URI** et la **Clé primaire** dans le fichier `CosmosGetStarted.py` à l’étape suivante.

    ![Affichage et copie d’une clé d’accès rapide dans le portail Azure, panneau Clés](./media/create-sql-api-dotnet/keys.png)

2. Ouvrez le fichier `CosmosGetStarted.py` dans C:\git-samples\azure-cosmos-db-python-getting-started dans Visual Studio Code.

3. Copiez la valeur de votre **URI** dans le portail (à l’aide du bouton Copier), puis définissez-la comme valeur de la clé du point de terminaison (**endpoint**) dans ``CosmosGetStarted.py``. 

    `'ENDPOINT': 'https://FILLME.documents.azure.com',`

4. Copiez ensuite la valeur de votre **CLÉ PRIMAIRE** à partir du portail et définissez-la comme valeur de **config.PRIMARYKEY** dans ``CosmosGetStarted.py``. Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 

    `'PRIMARYKEY': 'FILLME',`

5. Enregistrez le fichier ``CosmosGetStarted.py``.
    
## <a name="run-the-app"></a>Exécution de l'application

1. Dans Visual Studio Code, sélectionnez **Affichage**>**Palette de commandes**. 

2. À l’invite, entrez **Python: Select Interpreter**, puis sélectionnez la version de Python à utiliser.

    Le pied de page dans Visual Studio Code affiche désormais l’interpréteur sélectionné. 

3. Sélectionnez **Affichage** > **Terminal intégré** pour ouvrir le terminal intégré Visual Studio Code.

4. Dans la fenêtre du terminal intégré, vérifiez que vous êtes bien dans le dossier azure-cosmos-db-python-getting-started. Si ce n’est pas le cas, exécutez la commande suivante pour passer au dossier d’exemple. 

    ```
    cd "C:\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Exécutez la commande suivante pour installer le package azure-cosmos. 

    ```
    pip3 install azure-cosmos
    ```

    Si vous obtenez une erreur d’accès refusé quand vous tentez d’installer azure-cosmos, vous devrez [exécuter VS Code en tant qu’administrateur](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Exécutez la commande suivante pour exécuter l’exemple, et créer et stocker de nouveaux documents dans Azure Cosmos DB.

    ```
    python CosmosGetStarted.py
    ```

7. Pour vérifier que les éléments ont été créés et enregistrés, dans le portail Azure, sélectionnez **Explorateur de données**, développez **coll**, développez **Documents**, puis sélectionnez le document **server1**. Le contenu du document server1 correspond au contenu retourné dans la fenêtre du terminal intégré. 

    ![Affichage des nouveaux documents dans le portail Azure](./media/create-sql-api-python/azure-cosmos-db-confirm-documents.png)

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer une collection à l’aide de l’Explorateur de données, et à exécuter une application. Vous pouvez maintenant importer des données supplémentaires à votre compte Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB pour l’API SQL](import-data.md)


