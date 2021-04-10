---
title: Tutoriel C# sur le classement des résultats
titleSuffix: Azure Cognitive Search
description: Ce tutoriel C# montre comment trier les résultats de la recherche. Il s’appuie sur un projet existant basé sur des hôtels. Il comporte un tri par propriété principale et par propriété secondaire, et inclut un profil de scoring pour ajouter des critères de boosting.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1f8100dd6340383eadec5d10b7f23db59ba0ebdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786383"
---
# <a name="tutorial-order-search-results-using-the-net-sdk"></a>Tutoriel : Trier des résultats de recherche à l’aide du SDK .NET

Tout au long de cette série de tutoriels, les résultats ont été retournés et affichés dans un [ordre par défaut](index-add-scoring-profiles.md#what-is-default-scoring). Dans ce tutoriel, vous allez ajouter des critères de tri principal et secondaire. Comme alternative au classement sur la base de valeurs numériques, le dernier exemple montre comment classer les résultats selon un profil de score personnalisé. Nous allons également explorer plus avant l’affichage de _types complexes_.

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
> * Classer des résultats en fonction d’une seule propriété
> * Classer des résultats en fonction de plusieurs propriétés
> * Classer des résultats en fonction d’un profil de score

## <a name="overview"></a>Vue d’ensemble

Ce tutoriel étend le projet de défilement infini créé dans le tutoriel [Ajouter la pagination aux résultats de recherche](tutorial-csharp-paging.md).

Une version terminée du code de ce tutoriel se trouve dans le projet suivant :

* [5-order-results (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/5-order-results)

## <a name="prerequisites"></a>Prérequis

* Solution [2b-add-infinite-scroll (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll). Ce projet peut être votre propre version reposant sur le tutoriel précédent ou une copie issue de GitHub.

Ce tutoriel a été mis à jour pour utiliser le package [Azure.Search.Documents (version 11)](https://www.nuget.org/packages/Azure.Search.Documents/). Pour obtenir une version antérieure du SDK .NET, consultez [Exemple de code Microsoft.Azure.Search (version 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="order-results-based-on-one-property"></a>Classer des résultats en fonction d’une seule propriété

Lors du classement de résultats en fonction d’une seule propriété, par exemple l’évaluation des hôtels, nous voulons non seulement obtenir les résultats classés, mais aussi la confirmation que l’ordre est bien correct. L’ajout du champ Rating aux résultats nous permet de vérifier que les résultats sont triés correctement.

Dans cet exercice, nous allons également ajouter un peu plus d’informations à l’affichage des résultats : le prix de la chambre la moins chère et celui de la chambre la plus chère pour chaque hôtel.

Il n’est pas nécessaire de modifier les modèles pour activer le classement. Seuls la vue et le contrôleur nécessitent des mises à jour. Commencez par ouvrir le contrôleur home.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Ajouter la propriété OrderBy aux paramètres de recherche

1. Dans HomeController.cs, ajoutez l’option **OrderBy** et incluez la propriété Rating, avec un ordre de tri décroissant. Dans la méthode **Index(SearchData model)** , ajoutez la ligne suivante aux paramètres de recherche.

    ```cs
    options.OrderBy.Add("Rating desc");
    ```

    >[!Note]
    > L’ordre par défaut est croissant, même si vous pouvez ajouter `asc` à la propriété pour rendre cela explicite. Vous spécifiez un ordre décroissant en ajoutant `desc`.

1. Maintenant, exécutez l’application et entrez un terme de recherche courant. Les résultats peuvent ou non être dans l’ordre correct, car ni vous en tant que développeur ni l’utilisateur n’avez un moyen simple de vérifier les résultats !

1. Nous allons faire apparaître que les résultats sont classés sur l’évaluation. Remplacez d’abord les classes **box1** et **box2** dans le fichier hotels.css par les classes suivantes (ces classes sont les seules nouvelles classes dont nous avons besoin pour ce tutoriel).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    > [!Tip]
    > Les navigateurs mettent généralement en cache les fichiers css : ceci peut faire qu’un ancien fichier css est utilisé et que vos modifications sont ignorées. Un bon moyen de contourner ceci est d’ajouter au lien une chaîne de requête avec un paramètre de version. Par exemple :
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    > Mettez à jour le numéro de version si vous pensez qu’un ancien fichier css est utilisé par votre navigateur.

1. Ajoutez la propriété **Rating** au paramètre **Select** dans la méthode **Index(SearchData model)** afin que les résultats incluent les trois champs suivants :

    ```cs
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Rating");
    ```

1. Ouvrez la vue (index.cshtml) et remplacez la boucle de rendu ( **&lt;!-- Show the hotel data. --&gt;** ) par le code suivant.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var ratingText = $"Rating: {result[i].Document.Rating}";

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. L’évaluation doit être disponible dans la première page affichée et dans les pages suivantes qui sont appelées via le défilement infini. Pour cette dernière situation, nous devons mettre à jour à la fois l’action **Next** dans le contrôleur et la fonction **scrolled** dans la vue. En commençant par le contrôleur, remplacez la méthode **Next** par le code suivant. Ce code crée et communique le texte de l’évaluation.

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
    
            string fullDescription = result.Document.Description;
    
            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Maintenant, mettez à jour la fonction **scrolled** dans la vue, de façon à afficher le texte de l’évaluation.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 3) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Vous pouvez maintenant réexécuter l’application. Recherchez un terme courant, comme « wifi », et vérifiez que les résultats sont classés par ordre décroissant de l’évaluation des hôtels.

    ![Classement basé sur l’évaluation](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Vous remarquerez que plusieurs hôtels ont une évaluation identique : leur apparition dans l’affichage correspond donc à nouveau à l’ordre dans lequel les données sont trouvées, ce qui est arbitraire.

    Avant de passer à l’ajout d’un deuxième niveau de classement, ajoutons du code pour afficher la plage de prix des chambres. Nous ajoutons ce code pour deux raisons : montrer l’extraction de données à partir d’un _type complexe_ et présenter le classement des résultats en fonction des prix (peut-être le moins cher en premier).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Ajouter la plage de prix des chambres à la vue

1. Ajoutez les propriétés qui contiennent le prix de la chambre la moins chère et la plus chère au modèle Hotel.cs.

    ```cs
    // Room rate range
    public double cheapest { get; set; }
    public double expensive { get; set; }
    ```

1. Calculez les prix des chambres à la fin de l’action **Index(SearchData model)** dans le contrôleur home. Ajoutez les calculs après le stockage des données temporaires.

    ```cs
    // Ensure TempData is stored for the next call.
    TempData["page"] = page;
    TempData["searchfor"] = model.searchText;

    // Calculate the room rate ranges.
    await foreach (var result in model.resultList.GetResultsAsync())
    {
        var cheapest = 0d;
        var expensive = 0d;

        foreach (var room in result.Document.Rooms)
        {
            var rate = room.BaseRate;
            if (rate < cheapest || cheapest == 0)
            {
                cheapest = (double)rate;
            }
            if (rate > expensive)
            {
                expensive = (double)rate;
            }
        }
        model.resultList.Results[n].Document.cheapest = cheapest;
        model.resultList.Results[n].Document.expensive = expensive;
    }
    ```

1. Ajoutez la propriété **Rooms** au paramètre **Select** dans la méthode d’action **Index(SearchData model)** du contrôleur.

    ```cs
    options.Select.Add("Rooms");
    ```

1. Modifiez la boucle de rendu de la vue pour afficher la plage des prix pour la première page de résultats.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Changez la méthode **Next** du contrôleur home de façon à communiquer la plage de prix pour les pages de résultats suivantes.

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";

            string fullDescription = result.Document.Description;

            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(rateText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Mettez à jour la fonction **scrolled** dans la vue, de façon à gérer le texte des prix des chambres.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 4) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Exécutez l’application et vérifiez que les plages de prix des chambres sont affichés.

    ![Affichage des plages de prix des chambres](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

La propriété **OrderBy** des paramètres de recherche n’accepte pas d’entrée telle que **Rooms.BaseRate** pour fournir le prix de la chambre la moins chère, même si les chambres sont déjà triées en fonction du prix. Dans ce cas, les chambres ne sont pas triées en fonction du prix. Pour afficher les hôtels de l’exemple de jeu de données, classés en fonction du prix des chambres, il faudrait de trier les résultats dans votre contrôleur home et envoyer ces résultats à la vue dans l’ordre souhaité.

## <a name="order-results-based-on-multiple-values"></a>Classer des résultats en fonction de plusieurs valeurs

La question est maintenant de savoir comment distinguer entre les hôtels ayant la même évaluation. Une approche peut être un classement secondaire basé sur la dernière date à laquelle l’hôtel a été rénové, afin que les hôtels les plus récemment rénovés apparaissent plus haut dans les résultats.

1. Pour ajouter un deuxième niveau de tri, ajoutez **LastRenovationDate** aux résultats de la recherche et à **OrderBy** dans la méthode **Index(SearchData model)** .

    ```cs
    options.Select.Add("LastRenovationDate");

    options.OrderBy.Add("LastRenovationDate desc");
    ```

    >[!Tip]
    > Vous pouvez entrer un nombre quelconque de propriétés dans la liste **OrderBy**. Si des hôtels avaient le même classement et la même date de rénovation, une troisième propriété pourrait ainsi être entrée pour les distinguer.

1. Là encore, nous avons besoin de voir la date de rénovation dans la vue, juste pour être certain que le classement est correct. Pour quelque chose comme une rénovation, il est probable que seule l’année suffise. Remplacez la boucle de rendu de la vue par le code suivant.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var lastRenovatedText = $"Last renovated: { result[i].Document.LastRenovationDate.Value.Year}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Modifiez la méthode **Next** du contrôleur home pour transférer le composant « année » de la dernière date de rénovation.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var ratingText = $"Rating: {result.Document.Rating}";
                var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
                var lastRenovatedText = $"Last renovated: {result.Document.LastRenovationDate.Value.Year}";

                string fullDescription = result.Document.Description;

                // Add strings to the list.
                nextHotels.Add(result.Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(fullDescription);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. Changez la fonction **scrolled** dans la vue de façon à afficher le texte de la rénovation.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 5) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Exécutez l'application. Effectuez une recherche sur un terme courant, comme « pool » ou « view », et vérifiez que les hôtels avec la même évaluation sont maintenant affichés dans l’ordre décroissant de la date de rénovation.

    ![Classement sur la date de rénovation](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="order-results-based-on-a-scoring-profile"></a>Classer des résultats en fonction d’un profil de score

Les exemples donnés jusqu’à présent dans le tutoriel montrent comment classer sur des valeurs numériques (évaluation et date de rénovation), fournissant une séquence de classement _exacte_. Cependant, certaines recherches et certaines données ne se prêtent pas à cette comparaison facile entre deux éléments de données. Pour les requêtes de recherche en texte intégral, Recherche cognitive comprend le concept de _classement_. Des _profils de scoring_ peuvent être spécifiés pour influer sur le classement des résultats et fournir ainsi des comparaisons plus complexes et qualitatives.

Les [profils de scoring](index-add-scoring-profiles.md) sont définis dans le schéma d’index. Plusieurs profils de score ont été définis sur les données des hôtels. Voyons comment un profil de score est défini, puis essayons d’écrire du code pour effectuer des recherches sur ces profils.

### <a name="how-scoring-profiles-are-defined"></a>Comment les profils de score sont définis

Les profils de scoring sont définis dans un index de recherche au moment de la conception. L’index des hôtels en lecture seule hébergé par Microsoft a trois profils de scoring. Cette section explore les profils de scoring et vous montre comment les utiliser dans le code.

1. Voici le profil de scoring par défaut pour le jeu de données des hôtels, utilisé quand vous ne spécifiez pas de paramètre **OrderBy** ou **ScoringProfile**. Ce profil améliore le _score_ pour un hôtel si le texte recherché est présent dans le nom, la description ou la liste d’étiquettes (équipements) de l’hôtel. Notez comment les pondérations du score favorisent certains champs. Si le texte de recherche apparaît dans un autre champ, non listé ci-dessous, il aura une pondération de 1. Bien sûr, plus le score est élevé, plus un résultat apparait haut dans la vue.

     ```cs
    {
        "name": "boostByField",
        "text": {
            "weights": {
                "Tags": 3,
                "HotelName": 2,
                "Description": 1.5,
                "Description_fr": 1.5,
            }
        }
    }
    ```

1. Le profil de scoring alternatif suivant améliore le score de façon significative si un paramètre fourni contient un ou plusieurs des éléments de la liste d’étiquettes (que nous appelons « équipements »). Le point clé de ce profil est qu’un paramètre _doit_ être fourni, contenant du texte. Si le paramètre est vide ou n’est pas fourni, une erreur est levée.

    ```cs
    {
        "name":"boostAmenities",
        "functions":[
            {
            "fieldName":"Tags",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":null,
            "distance":null,
            "tag":{
                "tagsParameter":"amenities"
            },
            "type":"tag",
            "boost":5
            }
        ],
        "functionAggregation":0
    },
    ```

1. Dans ce troisième profil, l’évaluation des hôtels donne une amélioration significative au score. La date de la dernière rénovation augmente également le score, mais seulement si cette donnée n’est pas antérieure de plus de 730 jours (2 ans) à la date actuelle.

    ```cs
    {
        "name":"renovatedAndHighlyRated",
        "functions":[
            {
            "fieldName":"Rating",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":{
                "boostingRangeStart":0,
                "boostingRangeEnd":5,
                "constantBoostBeyondRange":false
            },
            "distance":null,
            "tag":null,
            "type":"magnitude",
            "boost":20
            },
            {
            "fieldName":"LastRenovationDate",
            "freshness":{
                "boostingDuration":"P730D"
            },
            "interpolation":"quadratic",
            "magnitude":null,
            "distance":null,
            "tag":null,
            "type":"freshness",
            "boost":10
            }
        ],
        "functionAggregation":0
    }
    ```

    Maintenant, voyons si ces profils fonctionnent comme nous le pensons.

### <a name="add-code-to-the-view-to-compare-profiles"></a>Ajouter du code à la vue pour comparer les profils

1. Ouvrez le fichier index.cshtml et remplacez la section &lt;body&gt; par le code suivant.

    ```html
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

1. Ouvrez le fichier SearchData.cs et remplacez la classe **SearchData** par le code suivant.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

1. Ouvrez le fichier hotels.css et ajoutez les classes HTML suivantes.

    ```html
    .facetlist {
        list-style: none;
    }
    
    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Ajouter du code au contrôleur pour spécifier un profil de score

1. Ouvrez le fichier du contrôleur home. Ajoutez l’instruction **using** suivante (pour faciliter la création de listes).

    ```cs
    using System.Linq;
    ```

1. Pour cet exemple, nous avons besoin de l’appel initial à **Index** pour en faire un peu plus que retourner seulement la vue initiale. La méthode recherche maintenant jusqu’à 20 équipements à afficher dans la vue.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchOptions options = new SearchOptions();
            // Search for up to 20 amenities.
            options.Facets.Add("Tags,count:20");

            SearchResults<Hotel> searchResult = await _searchClient.SearchAsync<Hotel>("*", options);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

1. Nous avons besoin de deux méthodes privées pour enregistrer les facettes dans un stockage temporaire, et pour les récupérer auprès d’un stockage temporaire et remplir un modèle.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

1. Nous devons définir les paramètres **OrderBy** et **ScoringProfile** en fonction des besoins. Remplacez la méthode **Index(SearchData model)** existante par ce qui suit.

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Recover the facet text, and the facet check box settings.
                RecoverFacets(model, true);

                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First search with text. 
                // Recover the facet text, but ignore the check box settings, and use the current model settings.
                RecoverFacets(model, false);

                // First call. Check for valid text input, and valid scoring profile.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                if (model.scoring == null)
                {
                    model.scoring = "Default";
                }
                page = 0;
            }

            // Setup the search parameters.
            var options = new SearchOptions
            {
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Size = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalCount = true,
            };
            // Select the data properties to be returned.
            options.Select.Add("HotelName");
            options.Select.Add("Description");
            options.Select.Add("Tags");
            options.Select.Add("Rooms");
            options.Select.Add("Rating");
            options.Select.Add("LastRenovationDate");

            List<string> parameters = new List<string>();
            // Set the ordering based on the user's radio button selection.
            switch (model.scoring)
            {
                case "RatingRenovation":
                    // Set the ordering/scoring parameters.
                    options.OrderBy.Add("Rating desc");
                    options.OrderBy.Add("LastRenovationDate desc");
                    break;

                case "boostAmenities":
                    {
                        options.ScoringProfile = model.scoring;

                        // Create a string list of amenities that have been clicked.
                        for (int a = 0; a < model.facetOn.Length; a++)
                        {
                            if (model.facetOn[a])
                            {
                                parameters.Add(model.facetText[a]);
                            }
                        }

                        if (parameters.Count > 0)
                        {
                            options.ScoringParameters.Add($"amenities-{ string.Join(',', parameters)}");
                        }
                        else
                        {
                            // No amenities selected, so set profile back to default.
                            options.ScoringProfile = "";
                        }
                    }
                    break;

                case "renovatedAndHighlyRated":
                    options.ScoringProfile = model.scoring;
                    break;

                default:
                    break;
            }

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options);

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
            TempData["scoring"] = model.scoring;
            SaveFacets(model, true);

            // Calculate the room rate ranges.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var cheapest = 0d;
                var expensive = 0d;

                foreach (var room in result.Document.Rooms)
                {
                    var rate = room.BaseRate;
                    if (rate < cheapest || cheapest == 0)
                    {
                        cheapest = (double)rate;
                    }
                    if (rate > expensive)
                    {
                        expensive = (double)rate;
                    }
                }

                result.Document.cheapest = cheapest;
                result.Document.expensive = expensive;
            }
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    Lisez les commentaires pour chacune des sélections de **switch**.

1. Nous n’avons pas besoin d’apporter des modifications à l’action **Next** si vous avez ajouté le code supplémentaire pour la section précédente sur le classement basé sur plusieurs propriétés.

### <a name="run-and-test-the-app"></a>Exécuter et tester l’application

1. Exécutez l'application. Vous devez voir un ensemble complet d’équipements dans la vue.

1. Pour le classement, sélectionner « By numerical Rating » (Par évaluation numérique) vous donnera le classement numérique que vous avez déjà implémenté dans ce tutoriel, avec la date de rénovation déterminant le classement des hôtels ayant la même évaluation.

   ![Classement de « beach » (plage) basé sur l’évaluation](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

1. Essayez maintenant le profil « By amenities » (Par équipements). Effectuez différentes sélections d’équipements et vérifiez que les hôtels avec ces équipements sont promus en haut de la liste des résultats.

   ![Classement de « beach » (plage) basé sur le profil](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

1. Essayez « By Renovated date/Rating profile » (Par profil de Date de rénovation/Évaluation) pour voir si vous obtenez ce que vous attendez. Seuls les hôtels rénovés récemment doivent obtenir une amélioration _freshness_ (fraîcheur).

### <a name="resources"></a>Ressources

Pour plus d’informations, consultez [Ajouter des profils de score à un index Recherche cognitive Azure](./index-add-scoring-profiles.md).

## <a name="takeaways"></a>Éléments importants à retenir

Retenez les points importants suivants de ce projet :

* Les utilisateurs s’attendent à ce que les résultats de la recherche soient classés, les plus pertinents apparaissant en premier.
* Les données doivent être structurées pour que le classement soit facile. Nous n’avons pas pu effectuer facilement un tri sur « cheapest » (le moins cher) pour commencer, car les données ne sont pas structurées pour permettre un classement sans du code supplémentaire.
* Il peut y avoir de nombreux niveaux de classement pour faire la distinction entre les résultats qui ont la même valeur à un niveau plus élevé du classement.
* Il est naturel que certains résultats soient classés par ordre croissant (par exemple la distance séparant d’un point) et certains autres par ordre décroissant (par exemple l’évaluation des invités).
* Des profils de score peuvent être définis quand des comparaisons numériques ne sont pas disponibles ou pas assez pertinentes pour un jeu de données. Attribuer un score à chaque résultat aide à classer et à afficher les résultats de façon intelligente.

## <a name="next-steps"></a>Étapes suivantes

Vous avez terminé cette série de tutoriels C# et vous avez normalement acquis une bonne connaissance des API de Recherche cognitive Azure.

Pour plus d’informations de référence et des tutoriels, parcourez [Microsoft Learn](/learn/browse/?products=azure) ou les autres tutoriels de la [Documentation de Recherche cognitive Azure](./index.yml).