---
title: Tutoriel d’application web ASP.NET Core MVC avec Azure Cosmos DB
description: Tutoriel ASP.NET Core MVC pour créer une application web MVC à l’aide d’Azure Cosmos DB. Vous allez stocker JSON et accéder aux données à partir d’une application todo hébergée sur Azure App Service - Tutoriel étape par étape ASP NET Core MVC.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.openlocfilehash: b0da9f2f2d14c0487e61c1927b5456d09052cff3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444928"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Tutoriel : Développer une application web ASP.NET Core MVC avec Azure Cosmos DB à l’aide du kit SDK .NET

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.JS](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

Ce tutoriel vous montre comment utiliser Azure Cosmos DB pour stocker des données et y accéder à partir d’une application ASP.NET MVC qui est hébergée sur Azure. Dans ce tutoriel, vous allez utiliser le SDK .NET V3. L’image suivante illustre la page web que vous allez créer à partir de l’exemple de cet article :

![Capture d’écran de l’application web MVC de liste des tâches (todo) créée dans ce tutoriel - Tutoriel étape par étape pour ASP.NET Core MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Si vous n’avez pas le temps de suivre ce tutoriel, vous pouvez télécharger l’exemple de projet complet à partir de [GitHub][GitHub].

Ce didacticiel contient les sections suivantes :

> [!div class="checklist"]
>
> * Création d’un compte Azure Cosmos
> * Création d’une application ASP.NET Core MVC
> * Connexion de l’application à Azure Cosmos DB
> * Exécution d’opérations de création, lecture, mise à jour et suppression (CRUD) sur les données

> [!TIP]
> Ce tutoriel part du principe que vous avez déjà utilisé ASP.NET Core MVC et Azure App Service. Si vous débutez avec ASP.NET Core ou avec les [outils prérequis](#prerequisites), nous vous recommandons de télécharger l’exemple de projet complet à partir de [GitHub][GitHub], d’ajouter les packages NuGet nécessaires et de l’exécuter. Une fois le projet créé, vous pouvez consulter cet article pour obtenir des informations sur le code dans le contexte du projet.

## <a name="prerequisites"></a>Configuration requise

Avant de suivre les instructions de cet article, vérifiez que vous disposez bien des ressources suivantes :

* Un compte Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Toutes les captures d’écran de cet article sont tirées de Microsoft Visual Studio Community 2019. Si vous utilisez une version différente, il se peut que vos écrans et options ne soient pas tout à fait les mêmes. La solution devrait fonctionner si vous respectez les prérequis.

## <a name="create-an-azure-cosmos-account"></a>Étape 1 : Créer un compte Azure Cosmos

Commençons par créer un compte Azure Cosmos. Si vous avez déjà un compte d’API SQL Azure Cosmos DB ou si vous utilisez l’émulateur Azure Cosmos DB, passez à l’[Étape 2 : Créer une application ASP.NET MVC](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

Dans la section suivante, vous allez créer une application ASP.NET Core MVC.

## <a name="create-a-new-mvc-application"></a>Étape 2 : Créer une application ASP.NET Core MVC

1. Ouvrez Visual Studio et sélectionnez **Créer un projet**.

1. Dans **Créer un projet**, recherchez et sélectionnez **Application web ASP.NET Core** pour C#. Sélectionnez **Suivant** pour continuer.

   ![Créer un projet d’application web ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. Dans **Configurer votre nouveau projet**, nommez le projet *todo* et sélectionnez **Créer**.

1. Dans **Créer une application web ASP.NET Core**, choisissez **Application web (Model-View-Controller)** . Sélectionnez **Créer** pour continuer.

   Visual Studio crée une application MVC vide.

1. Sélectionnez **Déboguer** > **Démarrer le débogage** ou appuyez sur F5 pour exécuter votre application ASP.NET localement.

## <a name="add-nuget-packages"></a>Étape 3 : Ajouter le package NuGet Azure Cosmos DB au projet

Maintenant que nous avons presque tout le code de framework ASP.NET Core MVC dont nous avons besoin pour cette solution, ajoutons les packages NuGet nécessaires pour se connecter à Azure Cosmos DB.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Gérer les packages NuGet**.

1. Dans le **Gestionnaire de package NuGet**, recherchez et sélectionnez **Microsoft.Azure.Cosmos**. Sélectionnez **Installer**.

   ![Installer le package NuGet](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   Visual Studio télécharge et installe le package Azure Cosmos DB et ses dépendances.

   Vous pouvez aussi utiliser la **Console du Gestionnaire de package** pour installer le package NuGet. Pour cela, sélectionnez **Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**. À l'invite de commande, tapez la commande suivante :

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="set-up-the-mvc-application"></a>Étape 4 : Configurer l’application ASP.NET Core MVC

Ajoutons à présent les modèles, les vues et les contrôleurs à cette application MVC.

### <a name="add-a-model"></a> Ajouter un modèle

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Models**, puis sélectionnez **Ajouter** > **Classe**.

1. Dans **Ajouter un nouvel élément**, nommez votre nouvelle classe *Item.cs*, puis sélectionnez **Ajouter**.

1. Remplacez le contenu de la classe *Item.cs* par le code suivant :

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]

Azure Cosmos DB utilise JSON pour déplacer et stocker des données. Vous pouvez utiliser l’attribut `JsonProperty` pour contrôler la façon dont JSON sérialise et désérialise les objets. La classe `Item` offre une démonstration de l’attribut `JsonProperty`. Ce code contrôle le format du nom de propriété qui va dans JSON. Il renomme aussi la propriété .NET `Completed`.

### <a name="add-views"></a>Ajout de vues

Ensuite, créons les trois vues suivantes.

* Ajouter une vue d’éléments de liste
* Ajouter une vue de nouvel élément
* Ajouter une vue de modification d’élément

#### <a name="AddItemIndexView"></a>Ajouter une vue d’éléments de liste

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Views**, puis sélectionnez **Ajouter** > **Nouveau dossier**. Nommez le dossier *Item*.

1. Cliquez avec le bouton droit sur le dossier vide **Item**, puis sélectionnez **Ajouter** > **Vue**.

1. Dans **Ajouter une vue MVC**, fournissez les valeurs suivantes :

   * Dans **Nom de la vue**, entrez *Index*.
   * Dans **Modèle**, sélectionnez **Liste**.
   * Dans **Classe de modèle**, sélectionnez **Item (todo.Models)** .
   * Sélectionnez **Use a layout page** (Utiliser une page de disposition) et entrez *~/Views/Shared/_Layout.cshtml*.

   ![Capture d’écran présentant la boîte de dialogue Ajouter une vue MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. Après avoir ajouté ces valeurs, sélectionnez **Ajouter** et laissez Visual Studio créer une vue de modèle.

Une fois l’opération terminée, Visual Studio ouvre le fichier *cshtml* qu’il a créé. Vous pouvez fermer ce fichier dans Visual Studio. Nous y reviendrons plus tard.

#### <a name="AddNewIndexView"></a>Ajouter une vue de nouvel élément

De la même manière que vous avez créé une vue pour lister des éléments, créez une vue qui permet de créer des éléments en suivant ces étapes :

1. Dans l’**Explorateur de solutions**, cliquez de nouveau avec le bouton droit sur le dossier **Item**, sélectionnez **Ajouter** > **Vue**.

1. Dans **Ajouter une vue MVC**, apportez les modifications suivantes :

   * Dans **Nom de la vue**, entrez *Create*.
   * Dans **Modèle**, sélectionnez **Create**.
   * Dans **Classe de modèle**, sélectionnez **Item (todo.Models)** .
   * Sélectionnez **Use a layout page** (Utiliser une page de disposition) et entrez *~/Views/Shared/_Layout.cshtml*.
   * Sélectionnez **Ajouter**.

#### <a name="AddEditIndexView"></a>Ajouter une vue de modification d’élément

Et finalement, ajoutez une vue qui permet de modifier un élément en suivant ces étapes :

1. À partir de l’**Explorateur de solutions**, cliquez de nouveau avec le bouton droit sur le dossier **Item**, sélectionnez **Ajouter** > **Vue**.

1. Dans **Ajouter une vue MVC**, apportez les modifications suivantes :

   * Dans la zone **Nom de la vue**, tapez *Edit*.
   * Dans la zone **Modèle**, sélectionnez **Edit**.
   * Dans la zone **Classe de modèle**, sélectionnez **Élément (todo.Models)** .
   * Sélectionnez **Use a layout page** (Utiliser une page de disposition) et entrez *~/Views/Shared/_Layout.cshtml*.
   * Sélectionnez **Ajouter**.

Une fois ces étapes terminées, fermez tous les documents *cshtml* dans Visual Studio. Vous reviendrez à ces vues plus tard.

### <a name="add-a-controller"></a>Ajout d'un contrôleur

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Controllers**, sélectionnez **Ajouter** > **Contrôleur**.

1. Dans **Ajouter une structure**, cliquez sur **Contrôleur MVC - vide**, puis sélectionnez **Ajouter**.

   ![Sélectionner Contrôleur MVC - Vide dans Ajouter une structure](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nommez votre nouveau contrôleur *ItemController*.

1. Remplacez le contenu de *ItemController.cs* par le code suivant :

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

L’attribut **ValidateAntiForgeryToken** est utilisé ici pour protéger cette application contre les attaques de type falsification de requête intersites. Vos vues doivent aussi fonctionner avec ce jeton anti-contrefaçon. Pour plus d’informations et d’exemples, consultez [Prévention des attaques par falsification de requête intersites (CSRF) dans une application ASP.NET MVC][Preventing Cross-Site Request Forgery]. Le code source fourni sur [GitHub][GitHub] comporte l’implémentation complète.

Nous utilisons également l’attribut **Bind** sur le paramètre de méthode pour établir une protection contre les attaques par sur-validation. Pour plus d’informations, consultez [Didacticiel : Implémentez la fonctionnalité CRUD avec Entity Framework dans ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="connect-to-cosmosdb"></a>Étape 5 : Se connecter à Azure Cosmos DB

Maintenant que nous nous sommes occupés des éléments de base MVC, ajoutons le code pour se connecter à Azure Cosmos DB et effectuer des opérations CRUD.

### <a name="perform-crud-operations"></a>Effectuer des opérations CRUD sur les données

Pour commencer, nous allons ajouter une classe qui contient la logique permettant de se connecter à Azure Cosmos DB et de l’utiliser. Pour ce tutoriel, nous allons encapsuler cette logique dans une classe nommée `CosmosDBService` et une interface nommée `ICosmosDBService`. Ce service effectue les opérations CRUD. Il effectue aussi des opérations de flux de lecture, comme lister les éléments incomplets, créer, modifier et supprimer les éléments.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter** > **Nouveau dossier**. Nommez le dossier *Services*.

1. Cliquez avec le bouton droit sur le dossier **Services**, sélectionnez **Ajouter** > **Classe**. Nommez la nouvelle classe *CosmosDBService* et sélectionnez **Ajouter**.

1. Remplacez le contenu de *CosmosDBService.cs* par le code suivant :

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Répétez les deux étapes précédentes, mais cette fois-ci, utilisez le nom *ICosmosDBService*, puis utilisez le code suivant :

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]

1. Dans le gestionnaire **Configure les services**, ajoutez la ligne suivante :

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

    Le code de l’étape précédente reçoit `CosmosClient` avec le constructeur. Suivant le pipeline ASP.Net Core, nous devons accéder au fichier *Startup.cs* du projet. Le code de cette étape initialise le client en fonction de la configuration en tant qu’instance singleton à injecter via l’[Injection de dépendances dans ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

1. Dans le même fichier, ajoutez la méthode **InitializeCosmosClientInstanceAsync** suivante, qui lit la configuration et initialise le client.

    [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)]

1. Définissez la configuration dans le fichier *appsettings.json* du projet. Ouvrez le fichier et ajoutez une section nommée **CosmosDb** :

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```

Si vous exécutez l’application, le pipeline d’ASP.NET Core instancie **CosmosDbService** et conserve une seule instance comme singleton. Quand **ItemController** traite les demandes côté client, il reçoit cette instance unique et peut l’utiliser pour les opérations CRUD.

Si vous générez et exécutez ce projet maintenant, vous devriez voir ce qui suit :

![Capture d’écran de l’application web de liste des tâches (todo list) créée dans ce tutoriel de base de données](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

## <a name="run-the-application"></a>Étape 6 : Exécuter l’application localement

Pour tester l’application sur votre ordinateur local, effectuez les étapes suivantes :

1. Sélectionnez F5 dans Visual Studio pour générer l’application en mode débogage. Cette opération doit générer l'application et lancer un navigateur avec la page de grille vide que nous avons vue auparavant :

   ![Capture d’écran de l’application web de liste des tâches créée par ce tutoriel](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)

1. Sélectionnez le lien **Créer nouveau** et ajoutez des valeurs aux champs **Nom** et **Description**. Laissez la case **Terminé** décochée. Si vous la sélectionnez, l’application ajoute le nouvel élément dans un état terminé. L’élément n’apparaît plus dans la liste initiale.

1. Sélectionnez **Create** (Créer). L’application vous renvoie dans la vue **Index** et votre élément apparaît dans la liste. Vous pouvez ajouter quelques éléments supplémentaires à votre liste **À faire**.

    ![Capture d’écran de la vue Index](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. Sélectionnez **Modifier** en regard d’un **élément** de la liste. L’application ouvre la vue **Modifier** où vous pouvez mettre à jour les propriétés de votre objet, notamment l’indicateur **Terminé**. Si vous sélectionnez **Terminé** et **Enregistrer**, l’application présente l’**élément** comme étant terminé dans la liste.

   ![Capture d’écran de la vue Index avec la case Terminé cochée](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Vérifiez l’état des données dans le service Azure Cosmos DB à l’aide de [Cosmos Explorer](https://cosmos.azure.com) ou de l’Explorateur de données de l’émulateur Azure Cosmos DB.

1. Une fois que vous avez testé l'application, sélectionnez Ctrl+F5 pour arrêter le débogage de l’application. Vous êtes prêt à déployer.

## <a name="deploy-the-application-to-azure"></a>Étape 7 : Déployer l’application

Maintenant que l’application complète fonctionne correctement avec Azure Cosmos DB, nous allons déployer cette application web vers Azure App Service.  

1. Pour publier cette application, dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet et sélectionnez **Publier**.

1. Dans **Choisir une cible de publication**, sélectionnez **App Service**.

1. Pour utiliser un profil App Service, choisissez **Sélectionner**, puis sélectionnez **Publier**.

1. Dans **App service**, sélectionnez un **Abonnement**. Utilisez le filtre **Vue** pour trier par groupe de ressources ou type de ressource.

1. Recherchez votre profil, puis sélectionnez **OK**. Recherchez ensuite le service Azure App Service voulu et sélectionnez **OK**.

   ![Boîte de dialogue App Service dans Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

Une autre option consiste à créer un nouveau profil :

1. Comme dans la procédure précédente, cliquez avec le bouton droit sur le projet dans l’**Explorateur de solutions**, puis sélectionnez **Publier**.
  
1. Dans **Choisir une cible de publication**, sélectionnez **App Service**.

1. Dans **Choisir une cible de publication**, sélectionnez **Créer nouveau**, puis sélectionnez **Publier**.

1. Dans **App Service**, entrez le nom de votre application web ainsi que l’abonnement, le groupe de ressources et le plan d’hébergement appropriés, puis sélectionnez **Créer**.

   ![Créer une boîte de dialogue App Service dans Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

En l’espace de quelques secondes, Visual Studio publie votre application web et lance un navigateur dans lequel vous pouvez voir votre projet s’exécuter dans Azure !

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment générer une application web ASP.NET Core MVC. Votre application peut accéder aux données stockées dans Azure Cosmos DB. Vous pouvez maintenant poursuivre avec ces ressources :

* [Partitioning in Azure Cosmos DB](./partitioning-overview.md) (Partitionnement dans Azure Cosmos DB)
* [Bien démarrer avec les requêtes SQL](./how-to-sql-query.md)
* [Guide pratique pour modéliser et partitionner des données sur Azure Cosmos DB à l’aide d’un exemple concret](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
