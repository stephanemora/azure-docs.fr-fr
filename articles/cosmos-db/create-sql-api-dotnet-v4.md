---
title: Gérer les ressources de l’API SQL Azure Cosmos DB à l’aide du Kit de développement logiciel (SDK) .NET V4
description: Utilisez ce guide de démarrage rapide pour créer une application console en utilisant le SDK .NET V4 pour gérer les ressources du compte de l’API SQL d’Azure Cosmos DB.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/07/2021
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.openlocfilehash: 9fe921fff40a3bee7632317e5febe7cd84d3ee17
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112517213"
---
# <a name="quickstart-build-a-console-app-by-using-the-net-v4-sdk-preview-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Démarrage rapide : Créer une application console en utilisant le SDK .NET V4 (préversion) pour gérer les ressources du compte de l’API SQL d’Azure Cosmos DB.
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Kit SDK Java v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Connecteur Spark v3](create-sql-api-spark.md)
> * [Node.JS](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Commencez à utiliser la bibliothèque de client de l’API SQL Azure Cosmos DB pour .NET. Suivez les étapes décrites dans cet article pour installer le package .NET V4 (Azure.Cosmos) et pour créer une application. Ensuite, essayez l’exemple de code pour les opérations CRUD (création, lecture, mise à jour et suppression) de base sur les données stockées dans Azure Cosmos DB.

> [!IMPORTANT]
> Le kit SDK .NET V4 pour Azure Cosmos DB est actuellement disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
>
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB est la base de données NoSQL rapide de Microsoft, avec des API ouvertes pour toute échelle. Vous pouvez utiliser Azure Cosmos DB pour créer et interroger rapidement des bases de données de clés/valeurs, de documents et de graphiques. Utilisez la bibliothèque de client de l’API SQL Azure Cosmos DB pour .NET afin d’effectuer les opérations suivantes :

* Créer une base de données Azure Cosmos et un conteneur
* Ajouter des exemples de données au conteneur.
* Interroger les données. 
* Supprimez la base de données.

[Code source de la bibliothèque](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | [Package (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure. [Créez-en un gratuitement](https://azure.microsoft.com/free/). Vous pouvez aussi [essayer Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sans abonnement Azure, sans aucuns frais ni engagements. 
* [Kit de développement logiciel (SDK) .NET Core 3](https://dotnet.microsoft.com/download/dotnet-core) Pour connaître la version disponible dans votre environnement, exécutez `dotnet --version`.

## <a name="set-up"></a>Configurer

Cette section vous guide dans la création d’un compte Azure Cosmos et dans la configuration d’un projet qui utilise la bibliothèque de client de l’API SQL d’Azure Cosmos DB pour .NET afin de gérer des ressources. 

L’exemple de code décrit dans cet article crée une base de données `FamilyDatabase` et des membres d’une famille au sein de cette base de données. Chaque membre d’une famille est un élément et a des propriétés comme `Id`, `FamilyName`, `FirstName`, `LastName`, `Parents`, `Children` et `Address`. La propriété `LastName` est utilisée en tant que clé de partition pour le conteneur. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Créer un compte Azure Cosmos

Si vous utilisez l’option [Essayer gratuitement Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) pour créer un compte Azure Cosmos, vous devez créer un compte Azure Cosmos de type **API SQL**. Un compte de test Azure Cosmos est déjà créé pour vous. Vous n’avez pas besoin de créer le compte explicitement. Vous pouvez donc ignorer cette section et passer à la section suivante.

Si vous disposez de votre propre abonnement Azure ou si vous avez créé un abonnement gratuitement, vous devez créer un compte Azure Cosmos de manière explicite. Le code suivant crée un compte Azure Cosmos avec une cohérence de session. Le compte est répliqué dans `South Central US` et `North Central US`.  

Vous pouvez utiliser Azure Cloud Shell pour créer le compte Azure Cosmos. Azure Cloud Shell est un interpréteur de commandes interactif, authentifié et accessible par navigateur qui permet de gérer les ressources Azure. Il vous donne la possibilité de choisir l’expérience du shell la mieux adaptée à votre façon de travailler : Bash ou PowerShell. 

Pour ce guide de démarrage rapide, utilisez Bash. Azure Cloud Shell nécessite également un compte de stockage. Vous pouvez en créer un quand vous y êtes invité.

1. Sélectionnez le bouton **Essayer** en regard du code suivant, choisissez le mode **Bash**, sélectionnez **Créer un compte de stockage**, puis connectez-vous à Cloud Shell. 

1. Copiez et collez le code suivant dans Azure Cloud Shell, puis exécutez-le. Le nom du compte Azure Cosmos doit être globalement unique : veillez donc à mettre à jour la valeur de `mysqlapicosmosdb` avant d’exécuter la commande.

   ```azurecli-interactive

   # Set variables for the new SQL API account, database, and container
   resourceGroupName='myResourceGroup'
   location='southcentralus'

   # The Azure Cosmos account name must be globally unique, so be sure to update the `mysqlapicosmosdb` value before you run the command
   accountName='mysqlapicosmosdb'

   # Create a resource group
   az group create \
       --name $resourceGroupName \
       --location $location

   # Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
   az cosmosdb create \
       --resource-group $resourceGroupName \
       --name $accountName \
       --kind GlobalDocumentDB \
       --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
       --default-consistency-level "Session" \
       --enable-multiple-write-locations true

   ```

La création du compte Azure Cosmos prend un certain temps. Une fois l’opération réussie, vous pouvez voir la confirmation. Connectez-vous au [portail Azure](https://portal.azure.com/) et vérifiez que le compte Azure Cosmos portant le nom spécifié existe. Vous pouvez fermer la fenêtre Azure Cloud Shell une fois la ressource créée. 

### <a name="create-a-net-app"></a><a id="create-dotnet-core-app"></a>Créer une application .NET

Créez une application .NET dans votre éditeur ou votre IDE favori. Ouvrez l’invite de commandes Windows ou une fenêtre de terminal à partir de votre ordinateur local. Vous allez exécuter toutes les commandes des sections suivantes à partir de l’invite de commandes ou du terminal.  

Exécutez la commande `dotnet new` suivante pour créer une application nommée `todo`. Le paramètre `--langVersion` définit la propriété `LangVersion` dans le fichier projet créé.

   ```bash
   dotnet new console --langVersion:8 -n todo
   ```

Utilisez les commandes suivantes pour remplacer votre répertoire par le dossier d’application nouvellement créé et pour créer l’application :

   ```bash
   cd todo
   dotnet build
   ```

La sortie attendue de la build doit ressembler à ceci :

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Installer le package Azure Cosmos DB

Tout en restant dans le répertoire de l’application, installez la bibliothèque de client Azure Cosmos DB pour .NET Core en utilisant la commande `dotnet add package`.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Copiez les informations d’identification de votre compte Azure Cosmos à partir du portail Azure

L’exemple d’application doit s’authentifier auprès de votre compte Azure Cosmos. Pour vous authentifier, passez les informations d’identification du compte Azure Cosmos à l’application. Pour obtenir les informations d’identification de votre compte Azure Cosmos, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Accédez à votre compte Azure Cosmos.

1. Ouvrez le volet **Clés** et copiez les valeurs de **URI** et de **CLÉ PRINCIPALE** pour votre compte. Vous allez ajouter les valeurs de l’URI et de la clé à une variable d’environnement dans la procédure suivante.

## <a name="learn-the-object-model"></a><a id="object-model"></a>Découvrir le modèle objet

Avant de continuer à créer l’application, examinons la hiérarchie des ressources dans Azure Cosmos DB, et le modèle objet utilisé pour créer ces ressources et y accéder. Azure Cosmos DB crée les ressources dans l’ordre suivant :

* Compte Azure Cosmos 
* Bases de données 
* Containers 
* Éléments

Pour plus d’informations sur la hiérarchie des entités, consultez l’article [Modèle des ressources d’Azure Cosmos DB](account-databases-containers-items.md). Vous allez utiliser les classes .NET suivantes pour interagir avec ces ressources :

* `CosmosClient`. Cette classe fournit une représentation logique du côté client pour le service Azure Cosmos DB. Ce client est utilisé pour configurer et exécuter des requêtes sur le service.
* `CreateDatabaseIfNotExistsAsync`. Cette méthode crée (si elle n’existe pas) ou obtient (si elle existe déjà) une ressource de base de données via une opération asynchrone. 
* `CreateContainerIfNotExistsAsync`. Cette méthode crée (si elle n’existe pas) ou obtient (si elle existe déjà) un conteneur via une opération asynchrone. Vous pouvez vérifier le code d’état de la réponse pour déterminer si le conteneur vient d’être créé (201) ou si un conteneur existant a été retourné (200). 
* `CreateItemAsync`. Cette méthode crée un élément dans le conteneur.
* `UpsertItemAsync`. Cette méthode crée un élément dans le conteneur s’il n’existe pas déjà ou remplace l’élément s’il existe déjà. 
* `GetItemQueryIterator`. Cette méthode crée une requête pour les éléments sous un conteneur dans une base de données Azure Cosmos en utilisant une instruction SQL avec des valeurs paramétrables. 
* `DeleteAsync`. Cette méthode supprime la base de données spécifiée de votre compte Azure Cosmos.

 ## <a name="configure-code-examples"></a><a id="code-examples"></a>Configurer des exemples de code

L’exemple de code décrit dans cet article crée une base de données de famille dans Azure Cosmos DB. La base de données des familles contient des détails sur des familles, comme le nom, l’adresse, le lieu, les parents, les enfants et les animaux domestiques. 

Avant de renseigner les données dans votre compte Azure Cosmos, définissez les propriétés d’un élément de famille. Créez une classe nommée `Family.cs` au niveau racine de votre exemple d’application, puis ajoutez-y le code suivant :

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives-and-define-the-client-object"></a>Ajouter des directives using et définir l’objet client

Dans le répertoire du projet, ouvrez le fichier *Program.cs* dans votre éditeur et ajoutez les directives `using` suivantes en haut de votre application :

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Ajoutez les variables globales suivantes à votre classe `Program`. Ces variables incluent le point de terminaison et les clés d’autorisation, le nom de la base de données et le conteneur que vous allez créer. Veillez à remplacer les valeurs du point de terminaison et des clés d’autorisation en fonction de votre environnement. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Pour finir, remplacez la méthode `Main` :

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Création d'une base de données 

Définissez la méthode `CreateDatabaseAsync` dans la classe `program.cs`. Cette méthode crée la base de données `FamilyDatabase` si elle n’existe pas.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Créez un conteneur.

Définissez la méthode `CreateContainerAsync` dans la classe `Program`. Cette méthode crée le conteneur `FamilyContainer` s’il n’existe pas déjà. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Créer un élément

Créez un élément de famille en ajoutant la méthode `AddItemsToContainerAsync` avec le code suivant. Vous pouvez utiliser les méthodes `UpsertItemAsync` ou `CreateItemAsync` pour créer un élément.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Interroger les éléments

Après avoir inséré un élément, vous pouvez exécuter une requête pour obtenir les détails de la famille « Andersen ». Le code suivant montre comment exécuter la requête en utilisant directement la requête SQL. La requête SQL pour obtenir les détails de la famille « Andersen » est `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Définissez la méthode `QueryItemsAsync` dans la classe `Program` et ajoutez-y le code suivant :

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Remplacer un élément 

Lisez un élément de famille, puis mettez-le à jour en ajoutant la méthode `ReplaceFamilyItemAsync` avec le code suivant :

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Supprimer un élément 

Supprimez un élément de famille en ajoutant la méthode `DeleteFamilyItemAsync` avec le code suivant :

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Supprimer la base de données 

Vous pouvez supprimer la base de données en ajoutant la méthode `DeleteDatabaseAndCleanupAsync` avec le code suivant :

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Après avoir ajouté toutes les méthodes nécessaires, enregistrez le fichier *Program.cs*. 

## <a name="run-the-code"></a>Exécuter le code

Exécutez l’application pour créer les ressources Azure Cosmos DB :

   ```bash
   dotnet run
   ```

La sortie suivante est générée quand vous exécutez l’application :

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

Vous pouvez vérifier que les données ont été créées en vous connectant au portail Azure et en visualisant les éléments nécessaires dans votre compte Azure Cosmos. 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin du compte Azure Cosmos et du groupe de ressources correspondant, vous pouvez utiliser Azure CLI ou Azure PowerShell pour les supprimer. La commande suivante montre comment supprimer le groupe de ressources à l’aide d’Azure CLI :

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez découvert comment créer un compte Azure Cosmos, créer une base de données et créer un conteneur en utilisant une application .NET Core. Vous pouvez maintenant importer des données supplémentaires dans votre compte Azure Cosmos en utilisant les instructions de l’article suivant : 

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB](import-data.md)
