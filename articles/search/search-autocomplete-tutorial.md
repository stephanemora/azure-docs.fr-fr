---
title: Ajouter les suggestions et l’autocomplétion dans une zone de recherche
titleSuffix: Azure Cognitive Search
description: Activez les actions de requête prédictives dans la Recherche cognitive Azure en créant des suggesteurs et en formulant des requêtes qui remplissent une zone de recherche avec des termes ou des expressions complets.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 64c4e65ca7b69c7d61c706b48591ac19be3bfcf5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "72792522"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-cognitive-search-application"></a>Ajouter les suggestions ou l’autocomplétion à votre application Recherche cognitive Azure

Dans cet article, découvrez comment utiliser les [suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) et l’[autocomplétion](https://docs.microsoft.com/rest/api/searchservice/autocomplete) pour créer une zone de recherche efficace qui prend en charge les comportements de recherche au fur et à mesure de la frappe.

+ Les *suggestions* sont des résultats suggérés au cours de la frappe, où chaque suggestion est un résultat unique provenant de l’index et qui correspond à ce que vous avez tapé jusqu’à présent. 

+ L’*autocomplétion* « termine » le mot ou l’expression que l’utilisateur est en train de taper. Au lieu de retourner des résultats, elle termine une requête, que vous pouvez ensuite exécuter pour retourner des résultats. Comme avec les suggestions, un mot ou une expression terminés dans une requête repose sur une correspondance dans l’index. Le service ne propose pas de requêtes qui ne retournent aucun résultat dans l’index.

Vous pouvez télécharger et exécuter l’exemple de code dans **DotNetHowToAutocomplete** pour évaluer ces fonctionnalités. L’exemple de code cible un index prédéfini rempli avec des [données de démonstration NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). L’index NYCJobs contient un [construction du suggesteur](index-add-suggesters.md), qui est un impératif pour utiliser les suggestions ou l’autocomplétion. Vous pouvez utiliser l’index préparé hébergé dans un service bac à sable (sandbox) ou [remplir votre propre index](#configure-app) à l’aide d’un chargeur de données dans l’exemple de solution NYCJobs. 

L’exemple **DotNetHowToAutocomplete** illustre les suggestions et l’autocomplétion, à la fois dans les versions de langage C# et JavaScript. Les développeurs en C# peuvent parcourir une application basée sur ASP.NET MVC qui utilise le [SDK .NET de la Recherche cognitive Azure](https://aka.ms/search-sdk). La logique permettant d’effectuer des appels de requêtes d’autocomplétion et suggérées est indiquée dans le fichier HomeController.cs. Les développeurs JavaScript trouveront une logique de requête équivalente dans IndexJavaScript.cshtml, qui inclut des appels directs à l’[API REST de la Recherche cognitive Azure](https://docs.microsoft.com/rest/api/searchservice/). 

Pour les deux versions de langage, l’expérience utilisateur front-end est basée sur les bibliothèques de l’[interface utilisateur jQuery](https://jqueryui.com/autocomplete/) et [XDSoft](https://xdsoft.net/jqplugins/autocomplete/). Nous utilisons ces bibliothèques pour créer la zone de recherche prenant en charge les suggestions et l’autocomplétion. Les entrées collectées dans la zone de recherche sont associées à des suggestions et des actions d’autocomplétion, comme celles définies dans HomeController.cs ou IndexJavaScript.cshtml.

Cet exercice vous permet de vous familiariser avec les tâches suivantes :

> [!div class="checklist"]
> * Implémenter une zone de recherche en JavaScript et émettre des requêtes de correspondances suggérées ou de termes complétés automatiquement
> * En C#, définir des suggestions et des actions d’autocomplétion dans HomeController.cs
> * En JavaScript, appeler l’API REST directement pour fournir les mêmes fonctionnalités

## <a name="prerequisites"></a>Conditions préalables requises

Un service Recherche cognitive Azure est facultatif pour cet exercice, car la solution utilise un service de bac à sable (sandbox) en ligne qui héberge un index de démonstration NYCJobs préparé. Si vous voulez exécuter cet exemple sur votre propre service de recherche, consultez [Configurer un index NYCJobs](#configure-app) pour obtenir des instructions.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), toute édition. L’exemple de code et les instructions ont été testés dans l’édition Communauté gratuite.

* Téléchargez l’[exemple DotNetHowToAutoComplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

L’exemple est complet. Il traite les suggestions, l’autocomplétion, la navigation par facettes et la mise en cache côté client. Examinez le fichier Lisez-moi et les commentaires pour obtenir une description complète de ce que propose l’exemple.

## <a name="run-the-sample"></a>Exécution de l'exemple

1. Ouvrez **AutocompleteTutorial.sln** dans Visual Studio. La solution contient un projet ASP.NET MVC avec une connexion à l’index de démonstration NYCJobs.

2. Appuyez sur F5 pour exécuter le projet et charger la page dans le navigateur de votre choix.

En haut, vous verrez une option qui vous donne le choix entre C# ou JavaScript. L’option C# effectue un appel dans HomeController à partir du navigateur et utilise le SDK .NET de la Recherche cognitive Azure pour récupérer les résultats. 

L’option JavaScript appelle l’API REST Recherche cognitive Azure directement depuis le navigateur. Cette option offre généralement de bien meilleures performances car elle extrait le contrôleur du flux. Vous pouvez choisir l’option qui répond à vos besoins et à vos préférences de langue. Il y a plusieurs exemples d’autocomplétion dans la page, avec des conseils pour chacun. Chaque exemple comprend des exemples de texte recommandés que vous pouvez essayer.  

Essayez de saisir quelques lettres dans chaque zone de recherche pour voir ce qu’il se passe.

## <a name="search-box"></a>Zone de recherche

L’implémentation de la zone de recherche est exactement la même pour les versions C# et JavaScript. 

Ouvrez le fichier **Index.cshtml** situé dans le dossier \Views\Home pour afficher le code :

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Cet exemple est une simple zone de texte avec une classe pour le style, un ID qui doit être référencé par JavaScript et un texte d’espace réservé.  Tout se passe dans le code JavaScript incorporé.

L’exemple de langage C# utilise JavaScript dans le fichier Index.cshtml pour exploiter la [bibliothèque d’autocomplétion de l’interface utilisateur](https://jqueryui.com/autocomplete/). Cette bibliothèque ajoute l’expérience d’autocomplétion à la zone de recherche en effectuant des appels asynchrones au contrôleur MVC afin de récupérer les suggestions. La version de langage JavaScript se trouve dans le fichier IndexJavaScript.cshtml. Il inclut le script ci-dessous pour la barre de recherche, ainsi que pour les appels de l’API REST à la Recherche cognitive Azure.

Examinons le code JavaScript du premier exemple, qui appelle la fonction d’autocomplétion de l’interface utilisateur jQuery, en passant une requête pour obtenir des suggestions :

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Le code ci-dessus s’exécute dans le navigateur au chargement de la page pour configurer l’autocomplétion de l’interface utilisateur jQuery pour la zone de texte « example1a ».  `minLength: 3` garantit que les recommandations ne sont affichées que lorsque plus de trois caractères sont saisis dans la zone de recherche.  La valeur source est importante :

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

La ligne ci-dessus indique à la fonction d’autocomplétion de l’interface utilisateur jQuery où récupérer la liste des éléments à afficher sous la zone de recherche. Dans la mesure où ce projet est un projet MVC, il appelle la fonction Suggérer dans le fichier HomeController.cs qui contient la logique permettant de retourner les suggestions de requête. (Vous trouverez plus d’informations sur la fonction Suggérer dans la section suivante.) Cette fonction transmet également quelques paramètres pour contrôler la mise en surbrillance, les correspondances approximatives et les termes. L’API JavaScript d’autocomplétion ajoute le paramètre de terme.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Développement de l’exemple pour la prise en charge des correspondances approximatives

La recherche approximative vous permet d’obtenir des résultats selon des correspondances proches, même si l’utilisateur a mal épelé un mot dans la zone de recherche. Bien que non obligatoire, elle améliore considérablement la fiabilité de l’expérience prédictive. Essayons en changeant la ligne source pour activer la correspondance approximative.

Remplacez la ligne suivante par :

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

par ceci :

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Lancez l’application en appuyant sur F5.

Essayez de saisir quelque chose comme « exécatif » et constatez que les résultats renvoient « exécutif », même s’il ne s’agit pas d’une correspondance parfaite avec ce que vous avez saisi.

### <a name="jquery-autocomplete--backed-by-azure-cognitive-search-autocomplete"></a>Autocomplétion jQuery basée sur l’autocomplétion de la Recherche cognitive Azure

Jusqu’à présent, le code de l’expérience utilisateur de recherche a été centré sur les suggestions. Le bloc de code suivant montre la fonction d’autocomplétion de l’interface utilisateur jQuery (ligne 91 dans index.cshtml) en passant une requête d’autocomplétion de la Recherche cognitive Azure :

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>Exemple en code C#

Maintenant que nous avons passé en revue le code JavaScript de la page web, examinons le code du contrôleur côté serveur C# qui récupère en fait les correspondances suggérées avec le SDK .NET de la Recherche cognitive Azure.

Ouvrez le fichier **HomeController.cs** sous le répertoire Controllers. 

La première chose que vous remarquerez est une méthode en haut de la classe appelée `InitSearch`. Cette méthode crée un client d’index HTTP authentifié dans le service Recherche cognitive Azure. Pour plus d’informations, consultez [Guide pratique pour utiliser la Recherche cognitive Azure à partir d’une application .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Sur la ligne 41, notez la fonction Suggérer. Elle est basée sur la [méthode DocumentsOperationsExtensions.Suggest](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

La fonction Suggest prend deux paramètres qui déterminent si les meilleurs résultats sont renvoyés ou si la correspondance approximative est utilisée en plus de la recherche de terme. La méthode crée un [objet SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), qui est ensuite passé à l’API de suggestion. Le résultat est ensuite converti en JSON pour être visible par le client.

Sur la ligne 69, notez la fonction d’autocomplétion. Elle est basée sur la [méthode DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

La fonction d’autocomplétion prend l’entrée du terme de recherche. La méthode crée un [objet AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Le résultat est ensuite converti en JSON pour être visible par le client.

Ajoutez un point d'arrêt au début de la fonction Suggest et examinez le code (facultatif). Notez la réponse fournie par le SDK et la façon dont elle est convertie en résultat retourné par la méthode.

Les autres exemples figurant dans la page suivent le même modèle pour ajouter les facettes et la mise en surbrillance des correspondances pour prendre en charge la mise en cache côté client des résultats de l’autocomplétion. Examinez chaque exemple pour comprendre leur fonctionnement et comment les exploiter dans votre expérience de recherche.

## <a name="javascript-example"></a>Exemple JavaScript

Une implémentation JavaScript de l’autocomplétion et des suggestions appelle l’API REST, en utilisant un URI comme source pour spécifier l’index et l’opération. 

Pour passer en revue l’implémentation JavaScript, ouvrez **IndexJavaScript.cshtml**. Notez que la fonction d’autocomplétion de l’interface utilisateur jQuery est également utilisée pour la zone de recherche, en collectant les termes de recherche entrés et en effectuant des appels asynchrones à la Recherche cognitive Azure pour récupérer des correspondances suggérées ou des termes complets. 

Nous allons examiner le code JavaScript du premier exemple :

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Si vous comparez cet exemple à l’exemple ci-dessus qui appelle le contrôleur home, vous pouvez remarquer plusieurs similarités.  La configuration de l’autocomplétion de `minLength` et `position` est exactement la même. 

La différence importante se trouve dans la source. Au lieu d’appeler la méthode Suggest dans le contrôleur home, une requête REST est créée dans une fonction JavaScript et exécutée à l’aide d’Ajax. La réponse est ensuite traitée dans « success » et utilisée comme source.

Les appels REST utilisent des URI pour spécifier si un appel d’API d’[autocomplétion](https://docs.microsoft.com/rest/api/searchservice/autocomplete) ou de [suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) est effectué. Les URI suivants se trouvent sur les lignes 9 et 10, respectivement.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Sur la ligne 148, vous trouverez un script qui appelle `autocompleteUri`. Le premier appel à `suggestUri` se trouve sur la ligne 39.

> [!Note]
> Effectuer des appels REST au service en JavaScript est proposé ici comme une démonstration pratique de l’API REST, mais cette opération ne doit pas être considérée comme une bonne pratique ou une recommandation. L’inclusion d’une clé API et d’un point de terminaison dans un script ouvre votre service aux attaques par déni de service par toute personne pouvant lire ces valeurs dans le script. Alors que vous pouvez utiliser JavaScript de manière sécurisée à des fins d’apprentissage, peut-être sur des index hébergés sur le service gratuit, nous vous recommandons d’utiliser Java ou C# pour les opérations d’indexation et de requête dans le code de production. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Configurer NYCJobs pour une exécution sur votre service

Jusqu’à présent, vous avez utilisé l’index de démonstration NYCJobs hébergé. Si vous voulez une visibilité totale de tout le code, notamment l’index, suivez ces instructions pour créer et charger l’index dans votre propre service de recherche.

1. [Créez un service Recherche cognitive Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour cet exemple. 

   > [!Note]
   > Si vous utilisez le service Recherche cognitive Azure gratuit, vous êtes limité à trois index. Le chargeur de données NYCJobs crée deux index. Assurez-vous de disposer de l’espace suffisant sur votre service pour accepter les nouveaux index.

1. Téléchargez l’exemple de code [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

1. Dans le dossier DataLoader de l’exemple de code NYCJobs, ouvrez **DataLoader.sln** dans Visual Studio.

1. Ajoutez les informations de connexion de votre service Recherche cognitive Azure. Ouvrez le fichier App.config dans le projet DataLoader et modifiez les fichiers appSettings TargetSearchServiceName et TargetSearchServiceApiKey pour qu’ils correspondent à votre service Recherche cognitive Azure et à la clé de l’API Recherche cognitive Azure. Vous trouverez ces informations dans le portail Azure.

1. Appuyez sur F5 pour lancer l’application, en créant deux index et en important l’exemple de données NYCJobs.

1. Ouvrez **AutocompleteTutorial.sln** et modifiez le fichier Web.config dans le projet **AutocompleteTutorial**. Remplacez les valeurs SearchServiceName et SearchServiceApiKey par des valeurs valides pour votre service de recherche.

1. Appuyez sur F5 pour exécuter l'application. L’exemple d’application web s’ouvre dans le navigateur par défaut. L’expérience est identique à la version de bac à sable (sandbox). Seuls les index et les données sont hébergés sur votre service.

## <a name="next-steps"></a>Étapes suivantes

Cet exemple décrit les étapes de base permettant de créer une zone de recherche qui prend en charge l’autocomplétion et les suggestions. Vous avez vu comment générer une application ASP.NET MVC et utiliser l’API REST ou le SDK .NET de la Recherche cognitive Azure pour récupérer les suggestions.

Comme prochaine étape, essayez d’intégrer les suggestions et l’autocomplétion à votre expérience de recherche. Les articles de référence suivants devraient vous aider.

> [!div class="nextstepaction"]
> [API REST de saisie semi-automatique ](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [API REST de suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Attribut d’index de facettes sur une API REST création d’index](https://docs.microsoft.com/rest/api/searchservice/create-index)

