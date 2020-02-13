---
title: Tutoriel C# sur l’utilisation de facettes pour faciliter la navigation
titleSuffix: Azure Cognitive Search
description: Ce tutoriel s’appuie sur le projet « Pagination des résultats de la recherche - Recherche cognitive Azure » pour ajouter la navigation à facettes. Découvrez dans quelle mesure les facettes permettent d’affiner facilement une recherche.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: d88a9d7efdabd493fd31b961748bb6ad3bd8d738
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121572"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-cognitive-search"></a>Tutoriel C# : Utiliser des facettes pour faciliter la navigation - Recherche cognitive Azure

Les facettes facilitent la navigation, en fournissant à l’utilisateur un ensemble de liens qui l’aident à cibler sa recherche. Les facettes sont des attributs des données (par exemple, la catégorie, ou une fonctionnalité spécifique, d’un hôtel dans nos exemples de données).

Ce tutoriel repose sur le projet de pagination créé dans le [Tutoriel C#  : Pagination des résultats de la recherche - Recherche cognitive Azure](tutorial-csharp-paging.md).

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
> * Définir des propriétés de modèle en tant que _IsFacetable_
> * Ajouter la navigation à facettes à votre application

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

Disposer pleinement du projet [Tutoriel C# : Pagination des résultats de la recherche - Recherche cognitive Azure](tutorial-csharp-paging.md). Ce projet peut être votre propre version ou vous pouvez l’installer à partir de GitHub : [Créer votre première application](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Définir les propriétés de modèle en tant que IsFacetable

Pour qu’une propriété de modèle puisse être trouvée dans une recherche à facettes, elle doit être étiquetée avec **IsFacetable**.

1. Examinez la classe **Hotel**. **Category** et **Tags**, par exemple, sont marqués comme **IsFacetable**, contrairement à **HotelName** et **Description**. 

    ```cs
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
    ```

2. Comme nous n’allons pas changer d’étiquettes dans le cadre de ce tutoriel, fermez le fichier hotel.cs sans le modifier.

    > [!Note]
    > Une recherche à facettes génère une erreur si un champ demandé dans la recherche n’est pas correctement étiqueté.


## <a name="add-facet-navigation-to-your-app"></a>Ajouter la navigation à facettes à votre application

Pour cet exemple, nous allons permettre à l’utilisateur de sélectionner une catégorie d’hôtels, ou une commodité, dans des listes de liens affichés à gauche des résultats. L’utilisateur commence par entrer du texte de recherche, puis peut affiner les résultats de la recherche en sélectionnant successivement une catégorie et une commodité (il peut aussi sélectionner la commodité en premier, l’ordre n’étant pas important).

Le contrôleur doit passer les listes de facettes à la vue. Nous devons conserver les sélections de l’utilisateur à mesure que la recherche progresse ; là encore, nous utilisons un stockage temporaire comme mécanisme de conservation des données.

![Utilisation de la navigation à facettes pour affiner une recherche portant sur le terme « pool »](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Ajouter des chaînes de filtre au modèle SearchData

1. Ouvrez le fichier SearchData.cs et ajoutez des propriétés de chaîne à la classe **SearchData** destinées à contenir les chaînes de filtre des facettes.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Ajouter la méthode d’action Facet

Le contrôleur Home a besoin d’une nouvelle action, **Facet**, et de mises à jour pour ses actions **Index** et **Page** existantes ainsi que pour la méthode **RunQueryAsync**.

1. Ouvrez le fichier du contrôleur Home et ajoutez l’instruction **using** suivante pour activer la construction **List&lt;string&gt;** .

    ```cs
    using System.Collections.Generic;
    ```

2. Remplacez la méthode d’action **Index(SearchData model)** .

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the search call for the first page.
                await RunQueryAsync(model, 0, 0, "", "");
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. Remplacez la méthode d’action **Page(SearchData model)** .

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

                // Calculate the page that should be displayed.
                switch (model.paging)
                {
                    case "prev":
                        page = (int)TempData["page"] - 1;
                        break;

                    case "next":
                        page = (int)TempData["page"] + 1;
                        break;

                    default:
                        page = int.Parse(model.paging);
                        break;
                }

                // Recover the leftMostPage.
                int leftMostPage = (int)TempData["leftMostPage"];

                // Recover the filters.
                string catFilter = TempData["categoryFilter"].ToString();
                string ameFilter = TempData["amenityFilter"].ToString();

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Search for the new page.
                await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

4. Ajoutez un méthode d’action **Facet(SearchData model)** à activer quand l’utilisateur clique sur un lien à facette. Le modèle contient un filtre de recherche de catégorie ou un filtre de recherche de commodité. Ajoutez-le éventuellement après l’action **Page**.

    ```cs
        public async Task<ActionResult> Facet(SearchData model)
        {
            try
            {
                // Filters set by the model override those stored in temporary data.
                string catFilter;
                string ameFilter;
                if (model.categoryFilter != null)
                {
                    catFilter = model.categoryFilter;
                } else
                {
                    catFilter = TempData["categoryFilter"].ToString();
                }

                if (model.amenityFilter != null)
                {
                    ameFilter = model.amenityFilter;
                } else
                {
                    ameFilter = TempData["amenityFilter"].ToString();
                }

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Initiate a new search.
                await RunQueryAsync(model, 0, 0, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

### <a name="set-up-the-search-filter"></a>Configurer le filtre de recherche

Quand un utilisateur sélectionne une facette, il peut, par exemple, cliquer sur la catégorie **Resort and Spa** ; dans ce cas, seuls des hôtels spécifiés comme appartenant à cette catégorie doivent être retournés dans les résultats. Pour affiner une recherche de cette façon, nous devons configurer un _filtre_.

1. Remplacez la méthode **RunQueryAsync** par le code suivant. En gros, elle prend une chaîne de filtre de catégories et une chaîne de filtre de commodités et définit le paramètre **Filter** de **SearchParameters**.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
        {
            InitSearch();

            string facetFilter = "";

            if (catFilter.Length > 0 && ameFilter.Length > 0)
            {
                // Both facets apply.
                facetFilter = $"{catFilter} and {ameFilter}"; 
            } else
            {
                // One, or zero, facets apply.
                facetFilter = $"{catFilter}{ameFilter}";
            }

            var parameters = new SearchParameters
            {
                Filter = facetFilter,

                // Return information on the text, and number, of facets in the data.
                Facets = new List<string> { "Category,count:20", "Tags,count:20" },

                // Enter Hotel property names into this list, so only these values will be returned.
                Select = new[] { "HotelName", "Description", "Category", "Tags" },

                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Top = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalResultCount = true,
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ((int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
            {
                // Trigger a switch to a higher page range.
                leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
            }
            model.leftMostPage = leftMostPage;

            // Calculate the number of page numbers to display.
            model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

            // Ensure Temp data is stored for the next call.
            TempData["page"] = page;
            TempData["leftMostPage"] = model.leftMostPage;
            TempData["searchfor"] = model.searchText;
            TempData["categoryFilter"] = catFilter;
            TempData["amenityFilter"] = ameFilter;

            // Return the new view.
            return View("Index", model);
        }
    ```

    Nous avons ajouté les propriétés **Category** et **Tags** à la liste des éléments **Select** à retourner. Cet ajout n’est pas obligatoire pour que la navigation à facettes fonctionne, mais nous utilisons ces informations pour vérifier que le filtrage est correct.

### <a name="add-lists-of-facet-links-to-the-view"></a>Ajouter des listes de liens à facette à la vue

La vue nécessite certaines modifications significatives. 

1. Commencez par ouvrir le fichier hotels.css (dans le dossier wwwroot/css) et ajoutez les classes suivantes.

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

2. Pour la vue, nous organisons la sortie sous la forme d’un tableau, afin d’aligner correctement les listes de facettes sur la gauche et les résultats sur la droite. Ouvrez le fichier index.cshtml. Remplacez tout le contenu des balises HTML &lt;body&gt; par le code suivant.

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Facet Navigation
                    </h1>
                </td>
            </tr>

            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it.-->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.resultList != null)
                        {
                            List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                            if (categories.Count > 0)
                            {
                                <h5 class="facetheader">Category:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < categories.Count; c++)
                                    {
                                        var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                        </li>
                                    }
                                </ul>
                            }

                            List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                            if (tags.Count > 0)
                            {
                                <h5 class="facetheader">Amenities:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < tags.Count; c++)
                                    {
                                        var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                        </li>
                                    }
                                </ul>
                            }
                        }
                    </div>
                </td>
                <td valign="top">
                    <div id="resultsplace">
                        @if (Model != null && Model.resultList != null)
                        {
                            // Show the result count.
                            <p class="sampleText">
                                @Html.DisplayFor(m => m.resultList.Count) Results
                            </p>

                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);

                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nCategory: {Model.resultList.Results[i].Document.Category}";
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                            }
                        }
                    </div>
                </td>
            </tr>

            <tr>
                <td></td>
                <td valign="top">
                    @if (Model != null && Model.pageCount > 1)
                    {
                        // If there is more than one page of results, show the paging buttons.
                        <table>
                            <tr>
                                <td class="tdPage">
                                    @if (Model.currentPage > 0)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">|&lt;</p>
                                    }
                                </td>

                                <td class="tdPage">
                                    @if (Model.currentPage > 0)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">&lt;</p>
                                    }
                                </td>

                                @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
                                {
                                    <td class="tdPage">
                                        @if (Model.currentPage == pn)
                                        {
                                            // Convert displayed page numbers to 1-based and not 0-based.
                                            <p class="pageSelected">@(pn + 1)</p>
                                        }
                                        else
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                                            </p>
                                        }
                                    </td>
                                }

                                <td class="tdPage">
                                    @if (Model.currentPage < Model.pageCount - 1)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">&gt;</p>
                                    }
                                </td>

                                <td class="tdPage">
                                    @if (Model.currentPage < Model.pageCount - 1)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">&gt;|</p>
                                    }
                                </td>
                            </tr>
                        </table>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

    Remarquez l’utilisation de l’appel **Html.ActionLink**. Cet appel communique les chaînes de filtre valides au contrôleur quand l’utilisateur clique sur un lien à facette. 

### <a name="run-and-test-the-app"></a>Exécuter et tester l’application

La navigation à facettes permet à l’utilisateur d’affiner les recherches d’un simple clic, comme l’illustre la séquence suivante.

1. Exécutez l’application, puis tapez « airport » comme texte de recherche. Vérifiez que la liste de facettes apparaît clairement à gauche. Ces facettes sont toutes celles qui s’appliquent aux hôtels dont le descriptif comporte le terme « airport », et chacune est accompagnée de son nombre d’occurrences.

    ![Utilisation de la navigation à facettes pour affiner une recherche portant sur le terme « airport »](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Cliquez sur la catégorie **Resort and Spa**. Vérifiez que tous les résultats appartiennent à cette catégorie.

    ![Réduction de la recherche à « Resort and Spa »](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Cliquez sur la commodité **continental breakfast**. Vérifiez que tous les résultats appartiennent toujours à la catégorie « Resort and Spa » et correspondent à la commodité sélectionnée.

    ![Réduction de la recherche à « continental breakfast »](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Essayez de sélectionner une autre catégorie, puis une commodité, et observez la façon dont les résultats sont affinés. Ensuite, faites l’opération inverse, en choisissant d’abord une commodité, puis une catégorie.

    >[!Note]
    > Quand une sélection est effectuée dans une liste de facettes (par exemple, une catégorie), elle remplace toute sélection antérieure dans la liste des catégories.

## <a name="takeaways"></a>Éléments importants à retenir

Retenez les points importants suivants de ce projet :

* Il est impératif de marquer chaque propriété comme **IsFacetable** si elle doit être incluse dans la navigation à facettes.
* La navigation à facettes fournit à un utilisateur un moyen facile et intuitif d’affiner une recherche.
* Il convient d’organiser la navigation à facettes en sections (catégories d’hôtel, commodités d’un hôtel, gammes de prix, plages d’évaluation, etc.) portant chacune un titre approprié.

## <a name="next-steps"></a>Étapes suivantes

Dans le tutoriel suivant, nous examinons le classement des résultats. À ce stade, les résultats sont simplement triés dans l’ordre dans lequel ils sont stockés dans la base de données.

> [!div class="nextstepaction"]
> [Tutoriel C# : Classer les résultats - Recherche cognitive Azure](tutorial-csharp-orders.md)
