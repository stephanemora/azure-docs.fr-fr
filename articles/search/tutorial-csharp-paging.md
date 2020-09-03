---
title: Tutoriel C# sur la pagination des résultats de la recherche
titleSuffix: Azure Cognitive Search
description: Ajoutez des boutons de pagination et de navigation aux résultats de la recherche, en vous appuyant sur un projet existant basé sur des hôtels pour ajouter les premier et dernier boutons, les boutons Suivant et Précédent, et les boutons numérotés. Son deuxième système de pagination repose sur un défilement infini, déclenché par le déplacement d’une barre de défilement verticale jusqu’à sa limite inférieure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 3223bf3e2b413940fd99404453b7f958655e6fff
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998473"
---
# <a name="tutorial-add-paging-to-search-results-using-the-net-sdk"></a>Tutoriel : Ajouter la pagination aux résultats de recherche à l’aide du SDK .NET

Découvrez comment implémenter deux systèmes de pagination différents, le premier basé sur des numéros de page et le second sur un défilement infini. Les deux systèmes de pagination sont largement utilisés, et le choix de l’un ou de l’autre dépend de l’expérience utilisateur que vous souhaitez associer aux résultats. Ce tutoriel intègre les systèmes de pagination au projet créé dans le [Tutoriel C#  : Créer votre première application - Recherche cognitive Azure](tutorial-csharp-create-first-app.md).

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
> * Étendre votre application avec une pagination numérotée
> * Étendre votre application avec un défilement infini

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

Disposer pleinement du projet [Tutoriel C# : Créer votre première application - Recherche cognitive Azure](tutorial-csharp-create-first-app.md). Ce projet peut être votre propre version ou vous pouvez l’installer à partir de GitHub : [Créer votre première application](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Étendre votre application avec une pagination numérotée

La pagination numérotée est le système de pagination de choix pour les principaux moteurs de recherche Internet et la plupart des autres sites web de recherche. La pagination numérotée inclut généralement des options « page suivante » et « page précédente » en plus d’une plage de numéros de pages réels. De plus, des options « première page » et « dernière page » peuvent également être disponibles. Ces options donnent certainement à l’utilisateur la possibilité de contrôler l’exploration des résultats basés sur des pages.

Nous allons ajouter un système qui inclut les options de première page, de page précédente, de page suivante et de dernière page ainsi que des numéros de page qui, au lieu de commencer par 1, entourent la page sur laquelle se trouve actuellement l’utilisateur (ainsi, par exemple, si l’utilisateur consulte la page 10, les numéros de page 8, 9, 10, 11 et 12 peuvent être affichés).

Le système est suffisamment flexible pour permettre de définir le nombre de numéros de page visibles dans une variable globale.

Le système traite les boutons de numéro de page les plus à gauche et à droite comme des boutons spéciaux, en ce sens qu’ils déclenchent le changement de la plage des numéros de page affichés. Par exemple, si les numéros de page 8, 9, 10, 11 et 12 sont affichés et que l’utilisateur clique sur 8, c’est la plage de numéros de page 6, 7, 8, 9 et 10 qui est alors affichée. Un décalage similaire vers la droite a lieu s’il sélectionne 12.

### <a name="add-paging-fields-to-the-model"></a>Ajouter des champs de pagination au modèle

Si ce n’est déjà fait, ouvrez la solution de page de recherche de base.

1. Ouvrez le fichier de modèle SearchData.cs.

2. Tout d’abord, ajoutez des variables globales. Dans MVC, les variables globales sont déclarées dans leur propre classe statique. **ResultsPerPage** définit le nombre de résultats par page. **MaxPageRange** détermine le nombre de numéros de page visibles sur la vue. **PageRangeDelta** détermine de quel nombre de pages à gauche ou à droite la plage de pages doit être décalée quand le numéro de page le plus à gauche ou à droite est sélectionné. En général, ce dernier nombre est égal à environ la moitié de **MaxPageRange**. Ajoutez le code suivant à l’espace de noms.

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >Si vous exécutez ce projet sur un appareil doté d’un écran plus petit, par exemple un ordinateur portable, envisagez de définir **ResultsPerPage** sur 2.

3. Ajoutez des propriétés de pagination à la classe **SearchData**, par exemple, après la propriété **searchText**.

    ```cs
        // The current page being displayed.
        public int currentPage { get; set; }

        // The total number of pages of results.
        public int pageCount { get; set; }

        // The left-most page number to display.
        public int leftMostPage { get; set; }

        // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
        public int pageRange { get; set; }

        // Used when page numbers, or next or prev buttons, have been selected.
        public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>Ajouter un tableau d’options de pagination à la vue

1. Ouvrez le fichier index.cshtml et ajoutez le code suivant juste avant la balise &lt;/body&gt; fermante. Ce nouveau code présente un tableau d’options de pagination : première page, page précédente, pages 1, 2, 3, 4, 5, page suivante, dernière page.

    ```cs
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
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

            <td>
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
                <td>
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

            <td>
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

            <td>
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
    ```

    Nous utilisons un tableau HTML pour aligner parfaitement les informations. Toutefois, l’action provient en totalité des instructions @Html.ActionLink, chacune appelant le contrôleur avec un **nouveau** modèle créé avec des entrées différentes pour la propriété **paging** que nous avons ajoutée.

    Les options relatives aux première et dernière pages n’envoient pas de chaînes telles que « first » (première) et « last » (dernière), mais les numéros de page corrects.

2. Ajoutez des classes de pagination à la liste des styles HTML dans le fichier hotels.css. La classe **pageSelected** identifie la page que l’utilisateur est en train de consulter (en affichant le numéro en gras) dans la liste des numéros de page.

    ```html
        .pageButton {
            border: none;
            color: darkblue;
            font-weight: normal;
            width: 50px;
        }

        .pageSelected {
            border: none;
            color: black;
            font-weight: bold;
            width: 50px;
        }

        .pageButtonDisabled {
            border: none;
            color: lightgray;
            font-weight: bold;
            width: 50px;
        }
    ```

### <a name="add-a-page-action-to-the-controller"></a>Ajouter une action Page au contrôleur

1. Ouvrez le fichier HomeController.cs, puis ajoutez l’action **Page**. Cette action répond aux options de page sélectionnées.

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

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

                // Recover the search text and search for the data for the new page.
                model.searchText = TempData["searchfor"].ToString();

                await RunQueryAsync(model, page, leftMostPage);

                // Ensure Temp data is stored for next call, as TempData only stored for one call.
                TempData["page"] = (object)page;
                TempData["searchfor"] = model.searchText;
                TempData["leftMostPage"] = model.leftMostPage;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

    La méthode **RunQueryAsync** affiche une erreur de syntaxe, en raison du troisième paramètre ; nous y reviendrons un peu plus tard.

    > [!Note]
    > Les appels **TempData** stockent une valeur (un **objet**) dans un stockage temporaire, bien que ce stockage persiste pendant _uniquement_ un seul appel. Si nous stockons une information dans des données temporaires, elle est disponible pour le prochain appel d’une action de contrôleur, mais certainement pas pour l’appel suivant ! En raison de cette courte durée de vie, nous stockons le texte de recherche et les propriétés de pagination dans un stockage temporaire à chaque appel de **Page**.

2. L’action **Index(model)** doit être mise à jour pour stocker les variables temporaires et pour ajouter le paramètre de page la plus à gauche à l’appel **RunQueryAsync**.

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
                await RunQueryAsync(model, 0, 0);

                // Ensure temporary data is stored for the next call.
                TempData["page"] = 0;
                TempData["leftMostPage"] = 0;
                TempData["searchfor"] = model.searchText;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. La méthode **RunQueryAsync** doit être mise à jour de manière significative. Nous utilisons les champs **Skip**, **Top** et **IncludeTotalResultCount** de la classe **SearchParameters** pour ne demander que l’équivalent d’une page de résultats, à partir du paramètre **Skip**. Nous devons également calculer les variables de pagination pour notre vue. Remplacez la méthode entière par le code suivant.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                   // Enter Hotel property names into this list so only these values will be returned.
                   // If Select is empty, all values will be returned, which can be inefficient.
                   Select = new[] { "HotelName", "Description" },
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
            model.pageCount = ( (int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

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

            return View("Index", model);
        }
    ```

4. Enfin, nous devons apporter une petite modification à la vue. La variable **resultsList.Results.Count** contient maintenant le nombre de résultats retournés dans une page (3 dans notre exemple), pas le nombre total. Comme nous avons défini **IncludeTotalResultCount** sur true, la variable **resultsList.Count** contient à présent le nombre total de résultats. Recherchez l’emplacement auquel est affiché le nombre de résultats dans la vue et remplacez-le par le code suivant.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > La définition d’**IncludeTotalResultCount** sur true entraîne une légère diminution des performances, car ce total doit être calculé par Recherche cognitive Azure. Avec des jeux de données complexes, un avertissement s’affiche, indiquant que la valeur retournée est une _approximation_. Dans le cas de nos données d’hôtel, la valeur sera précise.

### <a name="compile-and-run-the-app"></a>Compiler et exécuter l’application

Sélectionnez à présent **Démarrer sans débogage** (ou appuyez sur la touche F5).

1. Effectuez une recherche portant sur un texte qui donne beaucoup de résultats (par exemple, « wifi »). Pouvez-vous parcourir les résultats de façon claire ?

    ![Pagination numérotée de résultats comportant le terme « wifi »](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Essayez de cliquer sur le numéro de page le plus à droite, puis sur celui le plus à gauche. Les numéros de page s’ajustent-ils en conséquence afin que le numéro de la page actuelle se trouve au centre ?

3. Les options de première et dernière pages sont-elles utiles ? Certaines recherches web populaires utilisent ces options, d’autres pas.

4. Accédez à la dernière page de résultats. La dernière page est la seule page qui peut contenir moins de **ResultsPerPage** résultats.

    ![Examen de la dernière page de résultats comportant le terme « wifi »](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Tapez « town » et cliquez sur l’icône de recherche. Aucune option de pagination n’est affichée s’il y a moins de l’équivalent d’une page de résultats.

    ![Recherche du terme « town »](./media/tutorial-csharp-create-first-app/azure-search-town.png)

À présent, enregistrez ce projet et essayons une autre forme de pagination.

## <a name="extend-your-app-with-infinite-scrolling"></a>Étendre votre application avec un défilement infini

Le défilement infini est déclenché quand l’utilisateur fait défiler une barre de défilement verticale jusqu’au dernier des résultats affichés. Quand cela se produit, un appel au serveur est effectué pour obtenir la page de résultats suivante. S’il n’y a plus aucun résultat, rien n’est retourné et la barre de défilement verticale ne change pas. S’il existe plus de résultats, ils sont ajoutés à la page actuelle et la barre de défilement change pour indiquer que des résultats supplémentaires sont disponibles.

Le point important ici est que la page en cours d’affichage n’est pas remplacée, mais enrichie des nouveaux résultats. Un utilisateur peut toujours faire défiler l’affichage jusqu’aux premiers résultats de la recherche.

Pour implémenter un défilement infini, nous allons partir du projet tel qu’il existait avant que ne soient ajoutés des éléments de défilement de numéro de page. Ainsi, le cas échéant, créez une autre copie de la page de recherche de base à partir de GitHub : [Créer votre première application](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Ajouter des champs de pagination au modèle

1. Tout d’abord, ajoutez une propriété **paging** à la classe **SearchData** (dans le fichier de modèle SearchData.cs).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Cette variable est une chaîne qui conserve « next » si la page de résultats suivante doit être envoyée, ou Null pour la première page d’une recherche.

2. Dans le même fichier et dans l’espace de noms, ajoutez une classe de variable globale avec une seule propriété. Dans MVC, les variables globales sont déclarées dans leur propre classe statique. **ResultsPerPage** définit le nombre de résultats par page. 

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Ajouter une barre de défilement verticale à la vue

1. Dans le fichier index.cshtml, recherchez la section qui affiche les résultats (elle commence par **@if (Model != null)** ).

2. Remplacez la section par le code ci-dessous. La nouvelle section **&lt;div&gt;** englobe la zone que l’utilisateur doit être en mesure de faire défiler et ajoute un attribut **overflow-y** et un appel à une fonction **onscroll** appelée « scrolled() », comme suit.

    ```cs
        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>

            <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    // Display the hotel name and description.
                    @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. Juste sous la boucle, après la balise &lt;/div&gt;, ajoutez la fonction **scrolled**.

    ```javascript
        <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 2) {
                                div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                            }
                        });
                    }
                }
        </script>
    ```

    L’instruction **if** dans le script ci-dessus vérifie si l’utilisateur a fait défiler jusqu’au bas de la barre de défilement verticale. Si tel le cas, un appel est effectué à destination d’une action appelée **Next** par le biais du contrôleur **Home**. Aucune autre information n’est nécessaire pour le contrôleur, qui retourne la page de données suivante. Ces données sont ensuite mises en forme à l’aide des mêmes styles HTML que la page d’origine. Si aucun résultat n’est retourné, rien n’est ajouté et les choses en restent là.

### <a name="handle-the-next-action"></a>Gérer l’action Next

Seules trois actions doivent être envoyées au contrôleur : la première exécution de l’application, qui appelle **Index()** , la première recherche par l’utilisateur, qui appelle **Index(model)** , puis les appels suivants qui permettent d’obtenir d’autres résultats, par le biais de **Next(model)** .

1. Ouvrez le fichier du contrôleur Home et supprimez la méthode **RunQueryAsync** du tutoriel d’origine.

2. Remplacez l’action **Index(model)** par le code suivant. Il gère désormais le champ **paging** quand il est Null ou défini sur « next », et gère l’appel à Recherche cognitive Azure.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First call. Check for valid text input.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    page = 0;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Enter Hotel property names into this list so only these values will be returned.
                    // If Select is empty, all values will be returned, which can be inefficient.
                    Select = new[] { "HotelName", "Description" },
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

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Comme dans le cas de la méthode de pagination numérotée, nous utilisons les paramètres de recherche **Skip** et **Top** pour demander uniquement les données qui doivent être retournées.

3. Ajoutez l’action **Next** au contrôleur Home. Comme vous pouvez le constater, il retourne une liste, avec pour chaque hôtel deux éléments : son nom et sa description. Ce format est conçu pour être compatible avec l’utilisation de la fonction **scrolled** appliquée aux données retournées dans la vue.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel name, then description, to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Si vous obtenez une erreur de syntaxe sur **List&lt;string&gt;** , ajoutez la directive **using** suivante au début du fichier du contrôleur.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Compiler et exécuter votre projet

Sélectionnez à présent **Démarrer sans débogage** (ou appuyez sur la touche F5).

1. Entrez un terme qui donne beaucoup de résultats (par exemple, « pool »), puis testez la barre de défilement verticale. Une nouvelle page de résultats est-elle déclenchée ?

    ![Défilement infini de résultats comportant le terme « pool »](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Pour qu’une barre de défilement s’affiche dans la première page de résultats, la hauteur de cette page doit être légèrement supérieure à la hauteur de la zone dans laquelle les résultats sont affichés. Dans notre exemple **.box1** a une hauteur de 30 pixels, **.box2** une hauteur de 100 pixels _et_ une marge inférieure de 24 pixels. Ainsi, chaque entrée utilise 154 pixels. Trois entrées occupent 3 x 154 = 462 pixels. Pour qu’une barre de défilement verticale s’affiche, la hauteur de la zone d’affichage doit être définie sur une valeur inférieure à 462 pixels ; même 461 peut faire l’affaire. Ce problème ne concerne que la première page ; pour les pages suivantes, une barre de défilement apparaît systématiquement. La ligne à mettre à jour est : **&lt;div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()"&gt;** .

2. Faites défiler jusqu’au bas des résultats. Comme vous pouvez le constater, toutes les informations se trouvent désormais sur la page de la vue. Vous pouvez faire défiler jusqu’en haut sans déclencher d’appels de serveur.

Des systèmes de défilement infini plus sophistiqués peuvent utiliser la roulette de la souris, ou un autre mécanisme similaire, pour déclencher le chargement d’une nouvelle page de résultats. Le défilement infini, qui évite des clics de souris supplémentaires, présente un certain charme, mais nous n’allons pas l’approfondir dans ces tutoriels, laissant la voie libre à l’examen d’autres options !

## <a name="takeaways"></a>Éléments importants à retenir

Retenez les points importants suivants de ce projet :

* La pagination numérotée est appropriée pour les recherches où l’ordre des résultats est quelque peu arbitraire, ce qui signifie que ce qui intéresse l’utilisateur peut se trouver dans les dernières pages.
* Le défilement infini convient quand l’ordre des résultats est particulièrement important. C’est, par exemple, le cas si les résultats sont triés en fonction de la distance par rapport au centre d’une ville de destination.
* Dans une certaine mesure, la pagination numérotée permet une meilleure navigation. Par exemple, l’utilisateur peut se souvenir qu’un résultat intéressant se trouvait dans la page 6, facilité de référence que n’offre pas le défilement infini.
* Le défilement infini, dépourvu de numéros de page sur lesquels il est nécessaire de cliquer, présente un certain charme.
* Une caractéristique du défilement infini, et qui fait son efficacité, est que les résultats sont ajoutés à une page existante ; ils ne remplacent pas cette page.
* Le stockage temporaire persiste le temps d’un appel et doit être réinitialisé pour être conservé d’un appel à l’autre.


## <a name="next-steps"></a>Étapes suivantes

La pagination est essentielle pour les recherches sur Internet. La pagination ayant été bien couverte, l’étape suivante consiste à améliorer l’expérience utilisateur en ajoutant des recherches avec auto-complétion.

> [!div class="nextstepaction"]
> [Tutoriel C# : Ajouter l’auto-complétion et des suggestions - Recherche cognitive Azure](tutorial-csharp-type-ahead-and-suggestions.md)
