---
title: 'Azure Cosmos DB : Créer une application avec Python et l’API SQL'
description: Cet article présente un exemple de code Python que vous pouvez utiliser pour vous connecter à l’API SQL d’Azure Cosmos DB et pour l’interroger.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: f085ca6de2c04caa90c34bc1293da1210d2883eb
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020154"
---
# <a name="azure-cosmos-db-build-a-python-application-using-azure-cosmos-db-sql-api-account"></a>Azure Cosmos DB : Générer une application Python à l’aide du compte d’API SQL Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.JS](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Vous pouvez rapidement créer et interroger des documents ainsi que des bases de données de clés/valeurs, à colonnes larges et de graphiques. Toutes ces opérations bénéficient de la distribution et la mise à l’échelle d’Azure Cosmos DB.

Ce démarrage rapide explique comment créer, à l’aide du portail Azure, un compte [API SQL](sql-api-introduction.md) d’Azure Cosmos DB, une base de données de documents, ainsi qu’un conteneur. Ensuite, vous créez et exécutez une application console à l’aide du kit SDK Python pour [l’API SQL](sql-api-sdk-python.md). Ce guide de démarrage rapide utilise la version 3.0 du [SDK Python](https://pypi.org/project/azure-cosmos).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)][!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Prérequis

* [Python 3.6](https://www.python.org/downloads/), avec l’exécutable `python` disponible dans votre `PATH`.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Extension Python pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Création d’un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Ajouter un conteneur

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Ajouter un exemple de données

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Interroger vos données

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Nous allons à présent cloner une application API SQL à partir de GitHub, définir la chaîne de connexion et l’exécuter.

1. Ouvrez une invite de commandes, créez un nouveau dossier nommé git-samples, puis fermez l’invite de commandes.

    ```cmd
    md "git-samples"
    ```
   Si vous utilisez une invite de commandes bash, utilisez plutôt la commande suivante :

   ```bash
   mkdir "git-samples"
   ```

2. Ouvrez une fenêtre de terminal git comme Git Bash et utilisez la commande `cd` pour accéder au nouveau dossier d’installation pour l’exemple d’application.

    ```bash
    cd "git-samples"
    ```

3. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application.

1. Sur le [portail Azure](https://portal.azure.com/), dans votre compte Azure Cosmos, dans le volet de navigation gauche, sélectionnez **Clés**. Vous utiliserez les boutons de copie sur le côté droit de l’écran pour copier **l’URI** et la **Clé primaire** dans le fichier `CosmosGetStarted.py` à l’étape suivante.

    ![Affichage et copie d’une clé d’accès rapide dans le portail Azure, panneau Clés](./media/create-sql-api-dotnet/keys.png)

2. Ouvrez le fichier `CosmosGetStarted.py` dans \git-samples\azure-cosmos-db-python-getting-started dans Visual Studio Code.

3. Copiez la valeur de votre **URI** dans le portail (à l’aide du bouton Copier), puis définissez-la comme valeur de la clé du point de terminaison (**endpoint**) dans ``CosmosGetStarted.py``. 

    `'ENDPOINT': 'https://FILLME.documents.azure.com',`

4. Copiez ensuite la valeur de votre **CLÉ PRIMAIRE** à partir du portail et définissez-la comme valeur de **config.PRIMARYKEY** dans ``CosmosGetStarted.py``. Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 

    `'PRIMARYKEY': 'FILLME',`

5. Enregistrez le fichier ``CosmosGetStarted.py``.

## <a name="review-the-code"></a>Vérifier le code

Cette étape est facultative. En savoir plus sur les ressources de base de données créées dans le code, ou passer directement à [Mettre à jour votre chaîne de connexion](#update-your-connection-string).

Notez que si vous avez déjà utilisé la version précédente du kit SDK Python, vous connaissez peut-être les termes « collection » et « document ». Étant donné qu’Azure Cosmos DB prend en charge plusieurs modèles d’API, les versions 3.0 et ultérieures du kit SDK Python utilisent les termes génériques « conteneur » (qui peut être une collection, un graphe ou une table) et « élément » (qui décrit le contenu du conteneur).

Tous les extraits suivants proviennent du fichier `CosmosGetStarted.py`.

* Le CosmosClient est initialisé. Veillez à mettre à jour les valeurs de « point de terminaison » et de « clé principale », comme décrit à la section [Mise à jour de votre chaîne de connexion](#update-your-connection-string). 

    ```python
    # Initialize the Cosmos client
    client = cosmos_client.CosmosClient(url_connection=config['ENDPOINT'], auth={'masterKey': config['MASTERKEY']})
    ```

* Une nouvelle base de données est créée.

    ```python
    # Create a database
    db = client.CreateDatabase({ 'id': config['DATABASE'] })
    ```

* Un nouveau conteneur est créé.

    ```python
    # Create container options
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
   
## <a name="run-the-app"></a>Exécution de l'application

1. Dans Visual Studio Code, sélectionnez **Affichage** > **Palette de commandes**. 

2. À l’invite, entrez **Python: Select Interpreter**, puis sélectionnez la version de Python à utiliser.

    Le pied de page dans Visual Studio Code affiche désormais l’interpréteur sélectionné. 

3. Sélectionnez **Affichage** > **Terminal intégré** pour ouvrir le terminal intégré Visual Studio Code.

4. Dans la fenêtre du terminal intégré, vérifiez que vous êtes bien dans le dossier azure-cosmos-db-python-getting-started. Si ce n’est pas le cas, exécutez la commande suivante pour passer au dossier d’exemple. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Exécutez la commande suivante pour installer le package azure-cosmos. 

    ```python
    pip3 install azure-cosmos
    ```

    Si vous obtenez une erreur d’accès refusé quand vous tentez d’installer azure-cosmos, vous devrez [exécuter VS Code en tant qu’administrateur](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Exécutez la commande suivante pour exécuter l’exemple, et créer et stocker de nouveaux documents dans Azure Cosmos DB.

    ```python
    python CosmosGetStarted.py
    ```

7. Pour vérifier que les éléments ont été créés et enregistrés, dans le portail Azure, sélectionnez **Explorateur de données**, développez **coll**, développez **Documents**, puis sélectionnez le document **server1**. Le contenu du document server1 correspond au contenu retourné dans la fenêtre du terminal intégré. 

    ![Affichage des nouveaux documents dans le portail Azure](./media/create-sql-api-python/azure-cosmos-db-confirm-documents.png)

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos, à créer un conteneur à l’aide de l’Explorateur de données et à exécuter une application. Vous pouvez maintenant importer des données supplémentaires à votre compte Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB pour l’API SQL](import-data.md)


