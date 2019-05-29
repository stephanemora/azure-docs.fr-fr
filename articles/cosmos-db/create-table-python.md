---
title: 'Démarrage rapide : API Table avec Python - Azure Cosmos DB'
description: Ce guide de démarrage rapide montre comment utiliser l’API Table d’Azure Cosmos DB pour créer une application avec le portail Azure et Python
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.openlocfilehash: 2425d14e43abd98d80bef8ce01e1c1c5f73a78a0
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833084"
---
# <a name="quickstart-build-a-table-api-app-with-python-and-azure-cosmos-db"></a>Démarrage rapide : Créer une application d’API Table avec Python et Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.JS](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

Ce guide de démarrage rapide montre comment utiliser Python et [l’API Table](table-introduction.md) d’Azure Cosmos DB pour créer une application en clonant un exemple à partir de GitHub. Ce guide de démarrage rapide vous montre également comment créer un compte Azure Cosmos DB et comment utiliser l’Explorateur de données pour créer des tables et des entités dans le portail web Azure.

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Vous avez la possibilité de créer et d’interroger rapidement des bases de données de documents, de paires clé/valeur, de colonnes larges et de graphes, qui bénéficient toutes des capacités de distribution mondiale et de mise à l’échelle horizontale qui sont au cœur d’Azure Cosmos DB. 

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Par ailleurs :

* Si vous n’avez pas encore installé Visual Studio 2019, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Veillez à sélectionner les charges de travail de **développement Azure** et de **développement Python** lors de l’installation de Visual Studio.
* Sélectionnez également l’option Python 2 dans la charge de travail de **développement Python** ou téléchargez Python 2.7 sur [python.org](https://www.python.org/downloads/release/python-2712/).

## <a name="create-a-database-account"></a>Création d’un compte de base de données

> [!IMPORTANT] 
> Vous devez créer un compte d’API Table pour utiliser les kits SDK d’API Table mis à la disposition générale. Les comptes d’API Table créés pendant la durée de la préversion ne sont pas pris en charge par les kits SDK mis à la disposition générale.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Ajouter une table

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Ajouter un exemple de données

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, nous allons cloner une application Table à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

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
    git clone https://github.com/Azure-Samples/storage-python-getting-started.git
    ```

3. Ouvrez le fichier de solution dans Visual Studio. 

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application. Cette opération permet à votre application de communiquer avec votre base de données hébergée. 

1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Chaîne de connexion**. 

    ![Afficher et copier la CHAÎNE DE CONNEXION dans le volet Chaîne de connexion](./media/create-table-python/connection-string.png)

2. Copiez le NOM DU COMPTE en utilisant le bouton situé à droite.

3. Ouvrez le fichier config.py et à partir du portail collez le NOM DU COMPTE dans la valeur STORAGE_ACCOUNT_NAME de la ligne 19.

4. Revenez au portail et copiez la CLÉ PRIMAIRE.

5. Collez la CLÉ PRIMAIRE à partir du portail dans la valeur STORAGE_ACCOUNT_KEY de la ligne 20.

3. Enregistrez le fichier config.py.

## <a name="run-the-app"></a>Exécution de l'application

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**, sélectionnez l’environnement Python actuel, puis cliquez avec le bouton droit.

2. Sélectionnez Installer le package Python, puis saisissez **azure-storage-table**

3. Lancez F5 pour exécuter l'application. Votre application s’affiche dans votre navigateur. 

Vous pouvez dès à présent revenir à l’Explorateur de données et voir la requête, modifier et travailler avec ces nouvelles données. 

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer une table à l’aide de l’Explorateur de données, et à exécuter une application.  Maintenant, vous pouvez interroger vos données à l’aide de l’API Table.  

> [!div class="nextstepaction"]
> [Importer des données de table dans l’API Table](table-import.md)
