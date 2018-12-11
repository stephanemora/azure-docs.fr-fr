---
title: Tutoriel pour développer une application web ASP.NET MVC avec Azure Cosmos DB à l’aide du kit SDK .Net (préversion).
description: Ce tutoriel décrit la création d’une application web ASP.NET MVC avec Azure Cosmos DB. Vous allez stocker et accéder aux données JSON à partir d’une application todo hébergée sur Azure.
author: deborahc
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2018
ms.author: dech
ms.openlocfilehash: ef805ff82b8f44f4caeeafdc8867d851f4501894
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852652"
---
# <a name="tutorial-develop-an-aspnet-mvc-web-application-with-azure-cosmos-db-by-using-net-preview-sdk"></a>Tutoriel : Développer une application web ASP.NET MVC avec Azure Cosmos DB à l’aide du kit SDK .Net (préversion) 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [.NET (préversion)](sql-api-dotnet-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Node.JS](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


Ce tutoriel vous montre comment utiliser Azure Cosmos DB pour stocker des données et y accéder à partir d’une application ASP.NET MVC qui est hébergée sur Azure. Dans ce tutoriel, vous utilisez le kit SDK .Net V3 qui est actuellement disponible en préversion. L’image suivante illustre la page web que vous allez créer à l’aide de l’exemple de cet article :
 
![Capture d’écran de l’application web todo list MVC créée dans ce didacticiel - Didacticiel étape par étape ASP.NET MVC](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-image01.png)

Si vous n’avez le temps de suivre ce tutoriel, vous pouvez télécharger l’exemple de projet complet à partir de [GitHub][GitHub]. 

Ce didacticiel contient les sections suivantes :

> [!div class="checklist"]
> * Création d’un compte Azure Cosmos
> * Création d’une application ASP.NET MVC
> * Connexion de l’application à Azure Cosmos DB 
> * Réalisation d’opérations CRUD sur les données

> [!TIP]
> Ce didacticiel suppose que vous disposez d'une expérience préalable de l'utilisation d'ASP.NET MVC et d'Azure Websites. Si vous débutez avec ASP.NET ou avec les [outils prérequis](#prerequisites), nous vous recommandons de télécharger l’exemple de projet complet à partir de [GitHub][GitHub], d’ajouter les packages NuGet nécessaires et de l’exécuter. Une fois le projet créé, vous pouvez consulter cet article pour obtenir des informations sur le code dans le contexte du projet.

## <a name="prerequisites"></a>Configuration requise 

Avant de suivre les instructions de cet article, veillez à disposer des ressources suivantes :

* **Un compte Azure actif :** si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

* Kit de développement logiciel Microsoft Azure SDK pour .NET de Visual Studio 2017, disponible via Visual Studio Installer.

Toutes les captures d’écran dans cet article ont été effectuées à l’aide de Microsoft Visual Studio Community 2017. Si votre système est configuré avec une version différente, il est possible que vos écrans et options ne correspondent pas totalement. Toutefois, si vous respectez les conditions préalables ci-dessus, cette solution devrait fonctionner.

## <a name="create-an-azure-cosmos-account"></a>Étape 1 : Créer un compte Azure Cosmos

Commençons par créer un compte Azure Cosmos. Si vous disposez déjà d’un compte d’API SQL Azure Cosmos DB, ou si vous utilisez l’émulateur Azure Cosmos DB pour ce tutoriel, vous pouvez passer à la section [Créer une application ASP.NET MVC](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

Dans la section suivante, vous créez une application ASP.NET MVC. 

## <a name="create-a-new-mvc-application"></a>Étape 2 : Créer une application ASP.NET MVC

1. Dans Visual Studio, à partir du menu **Fichier**, choisissez **Nouveau**, puis sélectionnez **Projet**. La boîte de dialogue **Nouveau projet** apparaît.

2. Dans la fenêtre **Nouveau projet**, développez les modèles **Installés**, **Visual C#**, **Web**, puis sélectionnez **Application web ASP.NET**. 

   ![Créer un projet d’application web ASP.NET](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-new-project-dialog.png)

3. Dans la zone **Nom** , tapez le nom du projet. Ce didacticiel utilise le nom « todo ». Si vous choisissez d’utiliser un autre nom, corrigez les exemples de code fournis chaque fois que ce tutoriel fait référence à l’espace de noms todo, de façon à utiliser le nom que vous avez attribué à votre application. 

4. Sélectionnez **Parcourir** pour accéder au dossier dans lequel vous souhaitez créer le projet, puis choisissez **.Net framework 4.6.1** ou une version supérieure. Sélectionnez **OK**. 

5. La boîte de dialogue **Nouvelle application web ASP.NET** s’affiche. Dans le volet Modèles, sélectionnez **MVC**.

6. Sélectionnez **OK** et laissez Visual Studio structurer le modèle ASP.NET MVC vide. 

7. Lorsque Visual Studio a terminé la création de l’application MVC réutilisable, vous disposez d’une application ASP.NET vide que vous pouvez exécuter localement.

## <a name="add-nuget-packages"></a>Étape 3 : Ajouter le package NuGet Azure Cosmos DB au projet

À présent que nous disposons pratiquement de tout le code framework ASP.NET MVC dont nous avons besoin pour cette solution, ajoutons les packages NuGet nécessaires pour se connecter à Azure Cosmos DB.

1. Le Kit de développement logiciel (SDK) .NET Azure Cosmos DB est empaqueté et distribué en tant que package NuGet. Pour obtenir le package NuGet dans Visual Studio, utilisez le gestionnaire de package NuGet dans Visual Studio en cliquant avec le bouton droit sur le projet dans **l’Explorateur de solutions**, puis en sélectionnant **Gérer les packages NuGet**.
   
   ![Capture d’écran des options contextuelles pour le projet d’application web dans l’Explorateur de solutions, avec Gérer les packages NuGet mis en surbrillance.](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-manage-nuget.png)
   
2. La boîte de dialogue **Gérer les packages NuGet** apparaît. Dans la zone **Parcourir** de NuGet, tapez **Microsoft.Azure.Cosmos**. À partir des résultats, installez **Microsoft.Azure.Cosmos** 3.0.0.1-préversion. Le package Azure Cosmos DB et ses dépendances, telles que Newtonsoft.Json, sont téléchargés et installés. Sélectionnez **OK** dans la fenêtre **Aperçu**, puis **J’accepte** dans la fenêtre **Acceptation de la licence** pour terminer l’installation.
   
   Vous pouvez aussi utiliser la console du Gestionnaire de package pour installer le package NuGet. Pour cela, dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet**, puis **Console du Gestionnaire de package**. À l'invite de commande, tapez la commande suivante :
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos -Version 3.0.0.1-preview
   ```        

3. Après l’installation du package, votre solution Visual Studio doit contenir les deux nouvelles références de bibliothèque à Microsoft.Azure.Cosmos.Client et Newtonsoft.Json.
  
## <a name="set-up-the-mvc-application"></a>Étape 4 : Configurer l'application ASP.NET MVC

À présent, ajoutons les modèles, vues et contrôleurs à cette application MVC :

* [Ajout d'un modèle](#add-a-model).
* [Ajout d'un contrôleur](#add-a-controller).
* [Ajout de vues](#add-views).

### <a name="add-a-model"></a> Ajouter un modèle

1. À partir de l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Modèles**, sélectionnez **Ajouter**, puis **Classe**. La boîte de dialogue **Ajouter un nouvel élément** s'affiche.

1. Nommez votre nouvelle classe **TodoItem.cs** et sélectionnez **Ajouter**. 

1. Ensuite, remplacez le code de la classe « Todoitem » par le code suivant :

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Models/TodoItem.cs)]
   
   Les données stockées dans Azure Cosmos DB sont transmises et stockées au format JSON. Pour contrôler la méthode JSON.NET de sérialisation/désérialisation de vos objets, vous pouvez utiliser l’attribut **JsonProperty**, comme indiqué dans la classe **TodoItem** que vous avez créée. En plus de contrôler le format du nom de propriété qui est transmis à JSON, vous pouvez également renommer vos propriétés .NET, comme vous l’avez fait avec la propriété **Description**. 

### <a name="add-a-controller"></a>Ajouter un contrôleur

1. À partir de l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Contrôleurs**, sélectionnez **Ajouter**, puis **Contrôleur**. La boîte de dialogue **Ajouter une structure** s'affiche.

1. Sélectionnez **Contrôleur MVC 5 - Vide**, puis **Ajouter**.

   ![Capture d'écran de la boîte de dialogue Ajouter une structure avec l'option Contrôleur MVC 5 - Vide mise en surbrillance](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nommez votre nouveau contrôleur, ** ItemController et remplacez le code de ce fichier par le code suivant :

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Controllers/ItemController.cs)]

   L’attribut **ValidateAntiForgeryToken** est utilisé ici pour protéger cette application contre les attaques de type falsification de requête intersites. Et il ne s’agit pas que d’un simple ajout de cet attribut, vos vues devraient également fonctionner avec ce jeton anti-falsification. Pour plus d’informations sur le sujet, et pour obtenir des exemples illustrant une implémentation adéquate, consultez [Prévention des falsifications de requête intersites][Preventing Cross-Site Request Forgery]. Le code source fourni sur [GitHub][GitHub] comporte l’implémentation complète.
   
   Nous utilisons également l’attribut **Bind** sur le paramètre de méthode pour établir une protection contre les attaques par sur-validation. Pour plus d’informations, consultez la rubrique [Opérations CRUD de base dans ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].
    
### <a name="add-views"></a>Ajouter des vues

Ensuite, créons les trois vues suivantes : 

* [Ajout d’une vue d’éléments de liste](#AddItemIndexView).
* [Ajout d’une vue de nouvel élément](#AddNewIndexView).
* [Ajout d’une vue de modification d’élément](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Ajouter une vue d’éléments de liste

1. Dans **l’Explorateur de solutions**, développez le dossier **Vues**, cliquez avec le bouton droit sur le dossier vide **Élément** créé automatiquement par Visual Studio quand vous avez ajouté **ItemController** précédemment ; cliquez ensuite sur **Ajouter**, puis sur **Vue**.
   
   ![Capture d’écran de l’Explorateur de solutions présentant le dossier créé par Visual Studio avec les commandes Ajouter/Vue mises en surbrillance](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view.png)

2. Dans la boîte de dialogue **Ajouter une vue**, mettez à jour les valeurs suivantes :
   
   * Dans la zone **Nom de la vue**, tapez ***Index***.
   * Dans la zone **Modèle**, sélectionnez ***Liste***.
   * Dans la zone **Classe de modèle**, sélectionnez ***Élément (todo.Models)***.
   * Dans la zone de la page de disposition, tapez ***~/Views/Shared/_Layout.cshtml***.
     
   ![Capture d'écran présentant la boîte de dialogue Ajouter une vue](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view-dialog.png)

3. Après avoir ajouté ces valeurs, sélectionnez **Ajouter** et laissez Visual Studio créer une vue de modèle. Une fois terminé, il ouvre le fichier cshtml qui est créé. Vous pouvez fermer ce fichier dans Visual Studio. Vous y reviendrez ultérieurement.

#### <a name="AddNewIndexView"></a>Ajouter une vue de nouvel élément

De la même manière que vous avez créé une vue pour lister des éléments, créez une vue qui permet de créer des éléments en suivant ces étapes :

1. À partir de l’**Explorateur de solutions**, cliquez de nouveau avec le bouton droit sur le dossier **Élément**, sélectionnez **Ajouter**, puis **Vue**.

1. Dans la boîte de dialogue **Ajouter une vue**, mettez à jour les valeurs suivantes :
   
   * Dans la zone **Nom de la vue**, tapez ***Create***.
   * Dans la zone **Modèle**, sélectionnez ***Create***.
   * Dans la zone **Classe de modèle**, sélectionnez ***Élément (todo.Models)***.
   * Dans la zone de la page de disposition, tapez ***~/Views/Shared/_Layout.cshtml***.
   * Sélectionnez **Ajouter**.
   
#### <a name="AddEditIndexView"></a>Ajouter une vue de modification d’élément

Et finalement, ajoutez une vue qui permet de modifier un élément en suivant ces étapes :

1. À partir de l’**Explorateur de solutions**, cliquez de nouveau avec le bouton droit sur le dossier **Élément**, sélectionnez **Ajouter**, puis **Vue**.

1. Dans la boîte de dialogue **Ajouter une vue** , procédez comme suit :
   
   * Dans la zone **Nom de la vue**, tapez ***Edit***.
   * Dans la zone **Modèle**, sélectionnez ***Edit***.
   * Dans la zone **Classe de modèle**, sélectionnez ***Élément (todo.Models)***.
   * Dans la zone de la page de disposition, tapez ***~/Views/Shared/_Layout.cshtml***.
   * Sélectionnez **Ajouter**.

Une fois cette opération effectuée, fermez tous les documents cshtml dans Visual Studio. Vous reviendrez à ces vues plus tard.

## <a name="connect-to-cosmosdb"></a>Étape 5 : Se connecter à Azure Cosmos DB 

Maintenant que nous nous sommes occupés des éléments de base de MVC, ajoutons le code pour se connecter à Azure Cosmos DB et effectuer des opérations CRUD. 

### <a name="perform-crud-operations"></a> Effectuer des opérations CRUD sur les données

La première chose à faire ici est d’ajouter une classe qui contient la logique permettant de se connecter à Azure Cosmos DB et de l’utiliser. Pour ce tutoriel, nous allons encapsuler cette logique dans une classe appelée TodoItemService.cs. Ce code lit les valeurs du point de terminaison Azure Cosmos DB à partir du fichier de configuration, et exécute des opérations CRUD, telles que le recensement des éléments incomplets, ou la création, la modification et la suppression des éléments. 

1. À partir de l’**Explorateur de solutions**, créez un dossier sous votre projet nommé **Services**.

1. Cliquez avec le bouton droit sur le dossier **Services**, sélectionnez **Ajouter**, puis **Classe**. Nommez la nouvelle classe **TodoItemService** et sélectionnez **Ajouter**.

1. Ajoutez le code suivant à la classe **TodoItemService** et remplacez le code de ce fichier par le code suivant :

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Services/TodoItemService.cs)]
 
1. Le code précédent lit les valeurs de chaîne de connexion à partir du fichier de configuration. Pour mettre à jour les valeurs de chaîne de connexion de votre compte Azure Cosmos, ouvrez le fichier **Web.config** dans votre projet, puis ajoutez les lignes suivantes sous la section `<AppSettings>` :

   ```csharp
    <add key="endpoint" value="<enter the URI from the Keys blade of the Azure Portal>" />
    <add key="primaryKey" value="<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>" />
    <add key="database" value="Tasks" />
    <add key="container" value="Items" />
   ```
 
1. Mettez à jour les valeurs du point de terminaison et de la clé primaire avec celles récupérées dans le panneau **Clés** du portail Azure. Utilisez l’**URI** du panneau Clés comme valeur du paramètre de point de terminaison, et utilisez la valeur de **CLÉ PRIMAIRE** ou de **CLÉ SECONDAIRE** du panneau Clés pour le paramètre des clés. Cette opération assure la connexion d’Azure Cosmos DB à l’application. Ajoutons à présent la logique d’application.

1. Ouvrez **Global.asax.cs**, puis ajoutez la ligne suivante à la méthode **Application_Start**. 
   
   ```csharp
   TodoItemService.Initialize().GetAwaiter().GetResult();
   ```

   À ce stade, votre solution doit pouvoir générer votre projet sans erreur. Si vous exécutiez l’application maintenant, le **HomeController** et la vue **Index** de ce contrôleur s’ouvriraient. Il s’agit du comportement par défaut du projet de modèle MVC que nous avons choisi au début. Modifions le routage de cette application MVC pour changer ce comportement.

1. Ouvrez ***App\_Start\RouteConfig.cs***. Recherchez la ligne commençant par « defaults: », puis mettez-la à jour avec le code qui suit :

   ```csharp
   defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }
   ```

  Ce code indique maintenant à ASP.NET MVC que si vous n’avez pas spécifié de valeur dans l’URL pour contrôler le comportement de routage, au lieu de **Home**, il utilise **Item** comme contrôleur, et **Index** comme vue.

Maintenant, si vous exécutez l’application, il appelle votre **ItemController**, qui appelle les méthodes GetItems depuis la classe TodoItemService que vous allez définir à la section suivante. 

Si vous créez et exécutez ce projet maintenant, vous devriez voir ce qui suit :    

![Capture d’écran de l’application web todo list créée dans ce didacticiel de base de données](./media/sql-api-dotnet-application-preview/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Étape 6 : Exécuter l'application en local

Pour tester l’application sur votre machine locale, procédez comme suit :

1. Appuyez sur F5 dans Visual Studio pour générer l’application en mode débogage. Cette opération doit générer l'application et lancer un navigateur avec la page de grille vide que nous avons vue auparavant :
   
   ![Capture d’écran de l’application web todo list créée dans ce didacticiel de base de données](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Cliquez sur le lien **Créer nouveau** et ajoutez des valeurs aux champs **Nom** et **Description**. Ne cochez pas la case **Terminé**, sinon le nouvel élément est ajouté avec l’état terminé et n’apparaît pas dans la liste initiale.
   
3. Cliquez sur **Créer**. Vous êtes alors redirigé vers la vue **Index**, et votre élément apparaît dans la liste. Vous pouvez ajouter quelques éléments supplémentaires à votre liste.

    ![Capture d'écran de la vue Index](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item.png)
  
4. Cliquez sur **Modifier** en regard d’un **élément** de la liste. Vous êtes alors dirigé vers la vue **Edit** où vous pouvez mettre à jour les propriétés de votre objet, notamment l’indicateur **Completed**. Si vous marquez l’indicateur **Completed** et cliquez sur **Enregistrer**, **l’élément** est supprimé de la liste des tâches non terminées.
   
   ![Capture d'écran de la vue Index avec la case Terminé cochée](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-completed-item.png)

5. Une fois que vous avez testé l'application, appuyez sur Ctrl+F5 pour arrêter le débogage de l'application. Vous êtes prêt à déployer.

## <a name="deploy-the-application-to-azure"></a>Étape 7 : Déployer l’application 
Maintenant que l’application complète fonctionne correctement avec Azure Cosmos DB, nous allons déployer cette application web vers Azure App Service.  

1. Pour publier cette application, dans l’**Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet et sélectionnez **Publier**.
   
2. Dans la boîte de dialogue **Publier**, sélectionnez **Microsoft Azure App Service**, puis **Créer** pour créer un profil App Service, ou choisissez **Sélectionner** pour utiliser un profil existant.

3. Si vous possédez un profil Azure App Service, sélectionnez un **Abonnement** dans la liste déroulante. Utilisez le filtre **Vue** pour trier par groupe de ressources ou type de ressource. Recherchez ensuite le service Azure App Service voulu et sélectionnez **OK**. 
   
   ![Boîte de dialogue App Service dans Visual Studio](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-app-service.png)

4. Pour créer un nouveau profil Azure App Service, cliquez sur **Créer** dans la boîte de dialogue **Publier** . Dans la boîte de dialogue **Créer App Service**, entrez le nom de votre application web ainsi que l’abonnement, le groupe de ressources et le plan App Service appropriés, puis sélectionnez **Créer**.

   ![Créer une boîte de dialogue App Service dans Visual Studio](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-app-service.png)

En l’espace de quelques secondes, Visual Studio publie votre application web et lance un navigateur dans lequel vous pouvez voir votre projet s’exécuter dans Azure !

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez découvert comment créer une application web ASP.NET MVC qui peut accéder aux données stockées dans Azure Cosmos DB. Vous pouvez maintenant passer à l’article suivant :

> [!div class="nextstepaction"]
> [Créer une application Java pour accéder aux données stockées dans le compte de l’API SQL Azure Cosmos DB]( sql-api-java-application.md)


[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: http://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-todo-app
