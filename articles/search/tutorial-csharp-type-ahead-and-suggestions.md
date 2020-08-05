---
title: Tutoriel C# sur l’autocomplétion et les suggestions
titleSuffix: Azure Cognitive Search
description: Ajoutez l’autocomplétion et les suggestions pour collecter les entrées de termes de recherche des utilisateurs à l’aide d’une liste déroulante. Ce tutoriel s’appuie sur un projet existant basé sur des hôtels.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 07/15/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 244809db8efac00124a11ab5c163bfefc9ea7dd2
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420598"
---
# <a name="tutorial-add-autocomplete-and-suggestions-using-the-net-sdk"></a>Tutoriel : Ajouter l’autocomplétion et les suggestions à l’aide du SDK .NET

Découvrez comment implémenter l’autocomplétion (requêtes en saisie semi-automatique et suggestions de documents) quand un utilisateur commence à taper dans une zone de recherche. Dans ce tutoriel, nous allons montrer les requêtes autocomplétées et les résultats de suggestions séparément, puis ensemble. Il suffit à l’utilisateur de taper deux ou trois caractères pour obtenir tous les résultats disponibles.

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
> * Ajouter des suggestions
> * Mettre en évidence les suggestions
> * Ajouter l’autocomplétion
> * Combiner l’auto-complétion et les suggestions

## <a name="prerequisites"></a>Prérequis

Ce tutoriel fait partie d’une série et repose sur le projet de pagination créé dans le [Tutoriel C# : Pagination des résultats de la recherche - Recherche cognitive Azure](tutorial-csharp-paging.md).

Vous pouvez également télécharger et exécuter la solution pour ce tutoriel spécifique : [3-add-typeahead](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead).

## <a name="add-suggestions"></a>Ajouter des suggestions

Nous allons commencer par le cas le plus simple de proposition de choix à l’utilisateur : une liste déroulante de suggestions.

1. Dans le fichier index.cshtml, remplacez l’`@id` de l’instruction **TextBoxFor** par **azureautosuggest**.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

2. Après cette instruction, après la balise **&lt;/div&gt;** fermante, entrez ce script. Ce script tire parti du [widget d’autocomplétion](https://api.jqueryui.com/autocomplete/) de la bibliothèque de l’interface utilisateur open source jQuery pour présenter la liste déroulante des résultats suggérés. 

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/Suggest?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    L’ID « azureautosuggest » connecte le script ci-dessus à la zone de recherche. L’option source du widget est définie sur une méthode Suggest qui appelle l’API Suggest avec deux paramètres de requête : **highlights** et **fuzzy**, tous deux ayant la valeur false dans cette instance. De plus, un minimum de deux caractères sont nécessaires pour déclencher la recherche.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Ajouter des références aux scripts jQuery à la vue

1. Pour accéder à la bibliothèque jQuery, remplacez la section &lt;head&gt; du fichier de la vue par le code suivant :

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Étant donné que nous introduisons une nouvelle référence jQuery, nous devons également supprimer (ou commenter) la référence jQuery par défaut dans le fichier _Layout.cshtml (dans le dossier **Views/Shared**). Recherchez les lignes suivantes et commentez la première ligne de script comme indiqué. Cette modification permet d’éviter les conflits de références à jQuery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Maintenant, nous pouvons utiliser les fonctions jQuery d’autocomplétion prédéfinies.

### <a name="add-the-suggest-action-to-the-controller"></a>Ajouter l’action Suggest au contrôleur

1. Dans le contrôleur Home, ajoutez l’action **Suggest** (par exemple, après l’action **Page**).

    ```cs
        public async Task<ActionResult> Suggest(bool highlights, bool fuzzy, string term)
        {
            InitSearch();

            // Setup the suggest parameters.
            var parameters = new SuggestParameters()
            {
                UseFuzzyMatching = fuzzy,
                Top = 8,
            };

            if (highlights)
            {
                parameters.HighlightPreTag = "<b>";
                parameters.HighlightPostTag = "</b>";
            }

            // Only one suggester can be specified per index. It is defined in the index schema.
            // The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    Le paramètre **Top** spécifie le nombre de résultats à retourner (s’il n’est pas spécifié, le nombre par défaut est 5). Un _suggesteur_ est spécifié sur l’index Azure, au moment où les données sont configurées et non par une application cliente, comme ce tutoriel. Dans ce cas, le suggesteur est appelé « sg », et il recherche uniquement dans le champ **HotelName**. 

    La correspondance approximative permet d’inclure les « correspondances proches » dans la sortie, à une distance maximale d’une modification. Si le paramètre **highlights** est défini sur true, des balises HTML en gras sont ajoutées à la sortie. Nous allons définir ces deux paramètres sur true dans la section suivante.

2. Vous pouvez obtenir des erreurs de syntaxe. Si c’est le cas, ajoutez les deux instructions **using** suivantes au début du fichier.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Exécutez l'application. Obtenez-vous un éventail d’options quand vous entrez « po », par exemple ? Essayez à présent « pa ».

    ![La saisie de « po » révèle deux suggestions](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Notez que les lettres que vous entrez _doivent_ commencer un mot et pas simplement être incluses dans le mot.

4. Dans le script de la vue, définissez **&fuzzy** sur true et réexécutez l’application. À présent, entrez « po ». Comme vous pouvez le constater, la recherche part du principe qu’une des lettres tapées peut être incorrecte.
 
    ![Saisie de « pa » avec fuzzy défini sur true](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Si cela vous intéresse, la [Syntaxe des requêtes Lucene dans Recherche cognitive Azure](https://docs.microsoft.com/azure/search/query-lucene-syntax) décrit la logique utilisée dans les recherches approximatives.

## <a name="add-highlighting-to-the-suggestions"></a>Mettre en évidence les suggestions

Nous pouvons améliorer l’apparence des suggestions proposées à l’utilisateur en affectant la valeur true au paramètre **highlights**. Toutefois, nous devons d’abord ajouter du code à la vue pour afficher le texte en gras.

1. Dans la vue (index.cshtml), ajoutez le script suivant après le script **azureautosuggest** que vous avez entré plus haut.

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

2. À présent, modifiez l’ID de la zone de texte comme suit.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Réexécutez l’application ; le texte que vous avez entré doit apparaître en gras dans les suggestions. Par exemple, essayez de taper « pa ».
 
    ![Saisie de « pa » avec mise en évidence](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. La logique utilisée dans le script de mise en évidence ci-dessus n’est pas infaillible. Si vous entrez un terme qui apparaît deux fois dans le même nom, les résultats en gras ne sont pas tout à fait ce que vous souhaiteriez. Essayez de taper « mo ».

    Il appartient au développeur de déterminer si un script fonctionne « suffisamment bien » ou s’il doit faire l’objet d’ajustements. Nous n’allons pas approfondir la mise en évidence dans ce tutoriel, mais il peut être important de trouver un algorithme précis si la mise en évidence n’est pas efficace pour vos données. Pour plus d’informations, consultez [Mise en surbrillance des correspondances](search-pagination-page-layout.md#hit-highlighting).

## <a name="add-autocomplete"></a>Ajouter l’autocomplétion

Une autre variante, qui diffère légèrement des suggestions, est l’autocomplétion, qui permet d’entrer automatiquement un terme de requête Là encore, nous allons commencer par l’implémentation la plus simple, avant d’améliorer l’expérience utilisateur.

1. Entrez le script suivant dans la vue, après vos scripts précédents.

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

2. À présent, changez l’ID de la zone de texte comme suit.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Dans le contrôleur Home, nous devons entrer l’action **Autocomplete**, par exemple, sous l’action **Suggest**.

    ```cs
        public async Task<ActionResult> AutoComplete(string term)
        {
            InitSearch();

            // Setup the autocomplete parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 6
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Convert the results to a list that can be displayed in the client.
            List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();

            // Return the list.
            return new JsonResult(autocomplete);
        }
    ```

    Notez que nous utilisons la même fonction de *suggesteur*, appelée « sg », dans la recherche avec auto-complétion, que pour les suggestions (l’auto-complétion ne porte donc que sur les noms d’hôtel).

    Parmi les paramètres **AutocompleteMode** disponibles, nous utilisons **OneTermWithContext**. Pour obtenir une description des options supplémentaires, consultez [API Autocomplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete).

4. Exécutez l'application. Notez que la plage d’options affichées dans la liste déroulante est constituée de mots uniques. Essayez de taper des mots commençant par « re ». Comme vous pouvez le constater, le nombre d’options diminue à mesure que vous tapez des lettres.

    ![Entrée avec auto-complétion de base](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    Tel quel, le script de suggestions que vous avez exécuté est probablement plus utile que ce script d’auto-complétion. Pour rendre l’auto-complétion plus conviviale, il convient de l’ajouter à la recherche de suggestions.

## <a name="combine-autocompletion-and-suggestions"></a>Combiner l’auto-complétion et les suggestions

La combinaison de l’auto-complétion et des suggestions est l’option la plus complexe et fournit probablement la meilleure expérience utilisateur. Nous voulons afficher, conjointement avec le texte en cours de frappe, le premier choix de Recherche cognitive Azure pour compléter le texte automatiquement. De plus, nous voulons proposer une plage de suggestions sous forme de liste déroulante.

Il existe des bibliothèques qui offrent cette fonctionnalité, souvent appelée « auto-complétion inline » ou nom similaire. Toutefois, nous allons implémenter cette fonctionnalité en mode natif afin que vous puissiez voir ce qui se passe. Dans cet exemple, nous allons d’abord nous pencher sur le contrôleur.

1. Nous devons ajouter au contrôleur une action qui retourne simplement un résultat d’auto-complétion ainsi qu’un nombre spécifié de suggestions. Nous appellerons cette action **AutocompleteAndSuggest**. Dans le contrôleur Home, ajoutez l’action suivante, après vos autres nouvelles actions.

    ```cs
        public async Task<ActionResult> AutocompleteAndSuggest(string term)
        {
            InitSearch();

            // Setup the type-ahead search parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 1,
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Setup the suggest search parameters.
            var sp = new SuggestParameters()
            {
                Top = 8,
            };

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and it searches only the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", sp);

            // Create an empty list.
            var results = new List<string>();

            if (autocompleteResult.Results.Count > 0)
            {
                // Add the top result for type-ahead.
                results.Add(autocompleteResult.Results[0].Text);
            }
            else
            {
                // There were no type-ahead suggestions, so add an empty string.
                results.Add("");
            }
            for (int n = 0; n < suggestResult.Results.Count; n++)
            {
                // Now add the suggestions.
                results.Add(suggestResult.Results[n].Text);
            }

            // Return the list.
            return new JsonResult(results);
        }
    ```

    Une option d’auto-complétion est retournée en haut de la liste **results**, suivie de toutes les suggestions.

2. Dans la vue, nous implémentons d’abord une astuce qui permet d’afficher un mot d’auto-complétion en gris clair juste au-dessous du texte plus foncé entré par l’utilisateur. HTML inclut un positionnement relatif à cet effet. Modifiez comme suit l’instruction **TextBoxFor** (et les instructions &lt;div&gt; qui l’entourent) ; l’opération consiste à placer une deuxième zone de recherche nommée **underneath** juste en dessous de notre zone de recherche normale, en décalant celle-ci vers le haut de 39 pixels par rapport à son emplacement par défaut !

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Notez que nous rechangeons l’ID, en **azureautocomplete** dans ce cas.

3. Également dans la vue, entrez le script suivant, après tous les scripts que vous avez entrés jusqu’à présent. Il est assez fourni.

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggest?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    Remarquez l’utilisation intelligente de la fonction **interval** pour effacer le texte sous-jacent quand il ne correspond plus à ce que tape l’utilisateur et définir la même casse (lettres majuscules ou lettres minuscules) que ce que tape l’utilisateur (« pa » pouvant correspondre à « PA », « pA » et « Pa » pendant la recherche), afin que le texte superposé soit net.

    Lisez les commentaires dans le script pour mieux comprendre ce dernier.

4. Enfin, nous devons apporter un ajustement mineur à deux classes HTML pour les rendre transparentes. Ajoutez la ligne suivante aux classes **searchBoxForm** et **searchBox** dans le fichier hotels.css.

    ```html
        background: rgba(0,0,0,0);
    ```

5. À présent, exécutez l’application. Entrez « pa » dans la zone de recherche. Obtenez-vous « palace » en tant que suggestion d’auto-complétion ainsi que deux noms d’hôtels contenant « pa » ?

    ![Entrée avec auto-complétion et suggestions](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Essayez d’accepter la suggestion d’auto-complétion à l’aide de la touche Tab et de sélectionner des suggestions à l’aide des touches de direction et de la touche Tab. Renouvelez l’opération à l’aide de la souris et d’un simple clic. Vérifiez que le script gère parfaitement toutes ces situations.

    Il peut vous sembler plus simple de charger une bibliothèque qui offre cette fonctionnalité, mais vous connaissez désormais au moins une façon de faire fonctionner l’auto-complétion inline !

## <a name="takeaways"></a>Éléments importants à retenir

Retenez les points importants suivants de ce projet :

* L’auto-complétion et les suggestions peuvent permettre à l’utilisateur de trouver exactement ce qu’il souhaite au moyen de quelques frappes de clavier.
* Combinées, l’auto-complétion et les suggestions peuvent offrir une expérience utilisateur complète.
* Testez toujours les fonctions d’auto-complétion avec toutes les formes d’entrée.
* L’utilisation de la fonction **setInterval** peut être utile pour vérifier et corriger les éléments d’interface utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Dans le tutoriel suivant, nous abordons une autre façon d’améliorer l’expérience utilisateur, consistant à utiliser des facettes pour affiner les recherches au moyen d’un simple clic.

> [!div class="nextstepaction"]
> [Tutoriel C# : Utiliser des facettes pour faciliter la navigation - Recherche cognitive Azure](tutorial-csharp-facets.md)


