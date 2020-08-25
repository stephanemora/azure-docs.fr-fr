---
title: Tutoriel C# pour créer votre première application
titleSuffix: Azure Cognitive Search
description: Découvrez comment générer votre première application de recherche en C#, étape par étape. Il fournit un lien vers une application opérationnelle sur GitHub et le processus complet pour générer l’application à partir de zéro. Découvrez les composants essentiels de Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: 4c96dfa078ec12e0681574629276c48af2c1760d
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85261458"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Tutoriel : Créer votre première application de recherche à l’aide du SDK .NET

Découvrez comment créer une interface web pour interroger un index et présenter les résultats de la recherche en utilisant Recherche cognitive Azure. Ce tutoriel s’appuyant sur un index hébergé existant, vous pouvez vous concentrer sur la création d’une page de recherche. L’index contient des données d’hôtels fictives. Une fois que vous avez une page de base, vous pourrez l’améliorer dans les leçons suivantes pour inclure une pagination, des facettes et une expérience d’auto-complétion.

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
> * Configurer un environnement de développement
> * Modéliser des structures de données
> * Créer une page web
> * Définir des méthodes
> * Test de l'application

Vous allez également découvrir à quel point il est simple d’effectuer un appel de recherche. Les instructions essentielles du code que vous allez développer sont encapsulées dans les quelques lignes suivantes.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Cet appel lance une recherche de données Azure et retourne les résultats.

![Recherche portant sur le terme « pool »](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

[Installez Visual Studio](https://visualstudio.microsoft.com/) à utiliser comme IDE.

### <a name="install-and-run-the-project-from-github"></a>Installer et exécuter le projet à partir de GitHub

1. Recherchez l’exemple sur GitHub : [Créer votre première application](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Sélectionnez **Clone or download** (Cloner ou télécharger) et créez votre copie privée locale du projet.
1. À l’aide de Visual Studio, accédez à la solution pour la page de recherche de base et ouvrez-la, puis sélectionnez **Démarrer sans débogage** (ou appuyez sur F5).
1. Tapez des mots (par exemple « wifi », « view », « bar », « parking ») et examiner les résultats !

    ![Recherche portant sur le terme « wifi »](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Nous espérons que ce projet se déroule normalement et que l’application Azure est opérationnelle. De nombreux composants essentiels pour des recherches plus sophistiquées étant inclus dans cette application, il est judicieux de la suivre et de la recréer étape par étape.

Pour créer ce projet à partir de zéro et ainsi mieux connaître les composants de Recherche cognitive Azure, effectuez les étapes suivantes.

## <a name="set-up-a-development-environment"></a>Configurer un environnement de développement

1. Dans Visual Studio 2017 ou version ultérieure, sélectionnez **Nouveau/Projet** puis **Application web ASP.NET Core**. Attribuez un nom au projet, par exemple « FirstAzureSearchApp ».

    ![Création d’un projet cloud](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Une fois que vous avez cliqué sur **OK** pour ce type de projet, un second ensemble d’options s’appliquant à ce projet vous est présenté. Sélectionnez **Application web (Model-View-Controller)** .

    ![Création d’un projet MVC](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Ensuite, dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet**, puis **Gérer les packages NuGet pour la solution...** . Nous devons installer un seul package. Sélectionnez l’onglet **Parcourir** puis, dans la zone de recherche, tapez « Recherche cognitive Azure ». Installez **Microsoft.Azure.Search** quand il apparaît dans la liste (version 9.0.1 ou ultérieure). Vous devrez valider quelques boîtes de dialogue supplémentaires pour terminer l’installation.

    ![Utilisation de NuGet pour ajouter des bibliothèques Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-cognitive-search"></a>Initialiser Recherche cognitive Azure

Pour cet exemple, nous utilisons des données d’hôtels disponibles publiquement. Ces données sont une collection arbitraire de 50 noms d’hôtels fictifs et de descriptions, créée uniquement à des fins de démonstration. Pour accéder à ces données, vous devez spécifier un nom et la clé correspondante.

1. Ouvrez le fichier appsettings.json dans votre nouveau projet et remplacez les lignes par défaut par les nom et clé suivants. La clé d’API indiquée ici n’est pas un exemple de clé ; c’est _exactement_ la clé dont vous avez besoin pour accéder aux données d’hôtels. Votre fichier appsettings.json doit maintenant ressembler à ceci.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Nous n’en avons pas encore terminé avec ce fichier ; accédez à ses propriétés et définissez le paramètre **Copier dans le répertoire de sortie** sur **Copier si plus récent**.

    ![Copie des paramètres d’application dans la sortie](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Modéliser des structures de données

Les modèles (classes C#) permettent de communiquer des données entre le client (vue), le serveur (contrôleur) et également le cloud Azure à l’aide de l’architecture MVC (modèle, vue, contrôleur). En règle générale, ces modèles reflètent la structure des données sollicitées. De plus, nous avons besoin d’un modèle pour gérer les communications entre la vue et le contrôleur.

1. Ouvrez le dossier **Modèles** de votre projet à l’aide de l’Explorateur de solutions ; vous y verrez un modèle par défaut : **ErrorViewModel.cs**.

2. Cliquez avec le bouton droit sur le dossier **Modèles** et sélectionnez **Ajouter**, puis **Nouvel élément**. Ensuite, dans la boîte de dialogue qui s’affiche, sélectionnez **ASP.NET Core**, puis la première option **Classe**. Renommez le fichier .cs en Hotel.cs, puis cliquez sur **Ajouter**. Remplacez tout le contenu de Hotel.cs par le code suivant. Notez les membres **Address** et **Room** de la classe ; ces champs étant eux-mêmes des classes, nous aurons également besoin de modèles pour eux.

    ```cs
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnLucene)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }

            [IsFilterable, IsSortable]
            public GeographyPoint Location { get; set; }

            public Room[] Rooms { get; set; }
        }
    }
    ```

3. Suivez la même procédure de création de modèle pour la classe **Address**, mais nommez le fichier Address.cs. Remplacez le contenu par ce qui suit.

    ```cs
    using Microsoft.Azure.Search;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

4. Là encore, suivez le même processus pour créer la classe **Room**, mais nommez le fichier Room.cs. Remplacez également le contenu par ce qui suit.

    ```cs
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string Type { get; set; }

            [IsFilterable, IsFacetable]
            public double? BaseRate { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string BedOptions { get; set; }

            [IsFilterable, IsFacetable]

            public int SleepsCount { get; set; }

            [IsFilterable, IsFacetable]
            public bool? SmokingAllowed { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }
        }
    }
    ```

5. L’ensemble de classes **Hotel**, **Address** et **Room** constitue ce qui dans Azure est connu sous le terme de [_type complexe_](search-howto-complex-data-types.md), une fonctionnalité importante de Recherche cognitive Azure. Les types complexes peuvent avoir de nombreux niveaux de classes et de sous-classes et permettent de représenter des structures de données beaucoup plus complexes qu’au moyen de _types simples_ (classe contenant uniquement des membres primitifs). Comme nous avons besoin d’un modèle supplémentaire, réeffectuez le processus de création de classe de modèle, mais cette fois appelez la classe SearchData.cs et remplacez le code par défaut par le code suivant.

    ```cs
    using Microsoft.Azure.Search.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public DocumentSearchResult<Hotel> resultList;
        }
    }
    ```

    Cette classe contient l’entrée de l’utilisateur (**searchText**), et la sortie de la recherche (**resultList**). Le type de la sortie, **DocumentSearchResult&lt;Hotel&gt;** , est critique ; en effet, il correspond exactement aux résultats de la recherche et nous devons passer cette référence à la vue.



## <a name="create-a-web-page"></a>Créer une page web

Le projet que vous avez créé génère par défaut une série de vues clientes. Les vues exactes dépendent de la version de .NET Core que vous utilisez (nous utilisons la version 2.1 dans cet exemple). Elles sont regroupées dans le dossier **Vues** du projet. Il vous suffira de modifier le fichier Index.cshtml (dans le dossier **Vues/Accueil**).

Supprimez le contenu d’Index.cshtml dans son intégralité et regénérez le fichier dans les étapes suivantes.

1. Nous utilisons deux petites images dans la vue. Vous pouvez utiliser vos propres images ou copier les images à partir du projet GitHub : azure-logo.png et search.png. Ces deux images doivent être placées dans le dossier **wwwroot/images**.

2. La première ligne d’Index.cshtml doit référencer le modèle que nous allons utiliser pour communiquer des données entre le client (vue) et le serveur (contrôleur), qui correspond au modèle **SearchData** que nous avons créé. Ajoutez cette ligne au fichier Index.cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Comme il est courant d’entrer un titre pour la vue, les lignes suivantes doivent être :

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Après le titre, entrez une référence à une feuille de style HTML, que nous allons créer sous peu.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Attaquons-nous à présent au cœur de la vue. Un point clé à retenir est que la vue doit gérer deux situations. Tout d’abord, elle doit gérer l’affichage quand l’application est lancée et que l’utilisateur n’a pas encore entré de texte de recherche. Deuxièmement, elle doit gérer l’affichage des résultats, en plus de la zone de texte de recherche, à chaque utilisation par l’utilisateur. Pour gérer ces deux situations, nous devons vérifier si le modèle fourni à la vue est Null ou non. Un modèle Null indique que nous nous trouvons dans la première des deux situations (exécution initiale de l’application). Ajoutez le code suivant au fichier Index.cshtml et lisez les commentaires.

    ```cs
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. Enfin, nous ajoutons la feuille de style. Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau/Fichier** puis **Feuille de style** (avec **Général** en surbrillance). Remplacez le code par défaut par ce qui suit. Nous n’allons pas nous attarder davantage sur ce fichier ; les styles correspondent à une syntaxe HTML standard.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

7. Enregistrez le fichier de feuille de style sous le nom hotels.css, dans le dossier wwwroot/css, avec le fichier site.css par défaut.

Notre vue est à présent complète. Nous sommes en bonne voie. Les modèles et les vues sont terminés ; seul reste à définir le contrôleur, qui relie tout cela.

## <a name="define-methods"></a>Définir des méthodes

Nous devons modifier le contenu d’un contrôleur (**contrôleur Home**), qui est créé par défaut.

1. Ouvrez le fichier HomeController.cs et remplacez les instructions **using** par ce qui suit.

    ```cs
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using FirstAzureSearchApp.Models;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    ```

### <a name="add-index-methods"></a>Ajouter des méthodes Index

Nous avons besoin de deux méthodes **Index**, l’une ne prenant aucun paramètre (quand l’application est ouverte) et l’autre prenant un modèle en guise de paramètre (quand l’utilisateur a entré du texte de recherche). La première de ces méthodes est créée par défaut. 

1. Ajoutez la méthode suivante, après la méthode **Index()** par défaut.

    ```cs
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Notez la déclaration **async** de la méthode et l’appel **await** à **RunQueryAsync**. Ces mots clés ont pour rôle de rendre nos appels asynchrones et d’éviter, par là même, le blocage de threads sur le serveur.

    Le bloc **catch** utilise le modèle d’erreur qui a été créé pour nous par défaut.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Noter la gestion des erreurs et les autres méthodes et vues par défaut

L’ensemble de vues créées par défaut diffère légèrement suivant la version de .NET Core que vous utilisez. Pour .NET Core 2.1, les vues par défaut sont Index, About, Contact, Privacy et Error. Pour .NET Core 2.2, par exemple, les vues par défaut sont Index, Privacy et Error. Dans les deux cas, vous pouvez afficher ces pages par défaut lors de l’exécution de l’application et examiner la façon dont elles sont gérées dans le contrôleur.

Nous testerons la vue Error plus loin dans ce tutoriel.

Dans l’exemple GitHub, nous avons supprimé les vues non utilisées et leurs actions associées.

### <a name="add-the-runqueryasync-method"></a>Ajouter la méthode RunQueryAsync

L’appel de Recherche cognitive Azure est encapsulé dans notre méthode **RunQueryAsync**.

1. Tout d’abord, ajoutez des variables statiques pour configurer le service Azure et un appel pour les lancer.

    ```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
    ```

2. Maintenant, ajoutez la méthode **RunQueryAsync** proprement dite.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
    ```

    Dans cette méthode, nous nous assurons d’abord que notre configuration Azure est lancée, puis nous définissons certains paramètres de recherche. Les noms des champs dans le paramètre **Select** correspondent exactement aux noms de propriété dans la classe **hotel**. Il est possible d’omettre le paramètre **Select** ; dans ce cas, toutes les propriétés sont retournées. Toutefois, l’affectation d’aucun paramètre **Select** est inefficace si seul un sous-ensemble des données nous intéresse. En spécifiant les propriétés qui nous intéressent, seules ces propriétés sont retournées.

    L’appel asynchrone à la fonctionnalité de recherche (**model.resultList = await _indexClient.Documents.SearchAsync&lt;Hotel&gt;(model.searchText, parameters);** ) constitue le cœur de ce tutoriel et de cette application. La classe **DocumentSearchResult** est particulièrement intéressante ; une bonne idée (quand l’application est en cours d’exécution) consiste à y définir un point d’arrêt et à utiliser un débogueur pour examiner le contenu de **model.resultList**. Vous devriez trouver cela intuitif, les résultats contenant pour l’essentiel les données demandées.

Le moment de vérité est arrivé.

### <a name="test-the-app"></a>Test de l'application

À présent, nous allons vérifier que l’application s’exécute correctement.

1. Sélectionnez **Déboguer/Démarrer sans débogage** ou appuyez sur la touche F5. Si vous avez tout codé correctement, vous obtenez la vue Index initiale.

     ![Ouverture de l’application](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Entrez du texte tel que « beach » (ou n’importe quel texte qui vous vient à l’esprit), puis cliquez sur l’icône de recherche. Vous devriez obtenir des résultats.

     ![Recherche portant sur le terme « beach »](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Essayez d’entrer « five star ». Comme vous pouvez le constater, vous n’obtenez aucun résultat. Une recherche plus sophistiquée traiterait « five star » (cinq étoiles) comme un synonyme de « luxury » (luxe) et retournerait les résultats correspondants. L’utilisation de synonymes est disponible dans Recherche cognitive Azure, mais nous n’allons pas l’aborder dans les premiers tutoriels.
 
4. Essayez d’entrer « hot » comme texte de recherche. Vous n’obtenez _aucune_ entrée contenant le mot « hotel ». Notre recherche porte uniquement sur les mots entiers, même si quelques résultats sont retournés.

5. Essayez d’autres mots, tels que « pool », « sunshine » ou « view ». Vous verrez Recherche cognitive Azure fonctionner à son niveau le plus simple, mais néanmoins convaincant.

## <a name="test-edge-conditions-and-errors"></a>Tester des erreurs et conditions limites

Il est important de vérifier que nos fonctionnalités de gestion des erreurs fonctionnent comme prévu, même quand les choses fonctionnent parfaitement. 

1. Dans la méthode **Index**, après l’appel **try {** , entrez la ligne **Throw new Exception()** . Cette exception force une erreur quand nous effectuons une recherche sur du texte.

2. Exécutez l’application, entrez « bar » comme texte de recherche, puis cliquez sur l’icône de recherche. L’exception doit aboutir à la vue d’erreur.

     ![Forcer une erreur](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Il est considéré comme risqué du point de vue de la sécurité de retourner des numéros d’erreur interne dans les pages d’erreur. Si votre application est prévue pour une utilisation générale, effectuez quelques recherches concernant la sécurité et les bonnes pratiques relatives à ce qu’il faut retourner en cas d’erreur.

3. Supprimez **Throw new Exception()** quand vous jugez que la gestion des erreurs fonctionne correctement.

## <a name="takeaways"></a>Éléments importants à retenir

Retenez les points importants suivants de ce projet :

* Un appel de Recherche cognitive Azure est concis et il est facile d’interpréter les résultats.
* Les appels asynchrones ajoutent une petite dose de complexité au contrôleur, mais constituent la meilleure approche si vous avez l’intention de développer des applications de qualité.
* Cette application a effectué une recherche de texte simple, définie par ce qui est configuré dans **searchParameters**. Toutefois, cette classe peut être remplie avec de nombreux membres qui rendent une recherche plus sophistiquée. Il n’est pas nécessaire d’effectuer un travail supplémentaire considérable pour rendre cette application beaucoup plus puissante.

## <a name="next-steps"></a>Étapes suivantes

Pour offrir à l’utilisateur la meilleure expérience de Recherche cognitive Azure, nous devons ajouter des fonctionnalités, notamment la pagination (au moyen de numéros de page ou d’un défilement infini) ainsi que l’auto-complétion et les suggestions. Nous devons aussi envisager des paramètres de recherche plus complexes (par exemple, des recherches géographiques permettant de trouver des hôtels dans un rayon spécifique autour d’un point donné et le tri des résultats de la recherche).

Ces étapes suivantes sont traitées dans une série de tutoriels. Commençons par la pagination.

> [!div class="nextstepaction"]
> [Tutoriel C# : Pagination des résultats de la recherche - Recherche cognitive Azure](tutorial-csharp-paging.md)


