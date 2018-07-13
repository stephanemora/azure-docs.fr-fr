---
title: 'Didacticiel : Ajouter la saisie semi-automatique à votre zone de recherche avec Recherche Azure | Microsoft Docs'
description: Exemples d’améliorer de l’expérience utilisateur vis-à-vis de vos applications centrées sur les données avec la saisie automatique de Recherche Azure et les suggestions d’API.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: mcarter
ms.openlocfilehash: 90e99e7d44183d70f4e348c7b9070001fa3c6329
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100981"
---
# <a name="tutorial-add-auto-complete-to-your-search-box-using-azure-search"></a>Didacticiel : Ajouter la saisie automatique à votre zone de recherche avec Recherche Azure

Dans ce didacticiel, vous allez apprendre à utiliser les [suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions), la [saisie semi-automatique](https://docs.microsoft.com/en-us/rest/api/searchservice/autocomplete) et les [facettes](search-faceted-navigation.md) dans l’[API REST Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/) et le [kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions?view=azure-dotnet) afin de créer une zone de recherche puissante. Les *suggestions* fournissent des recommandations de résultats réels, selon ce que l’utilisateur a saisi jusqu'à présent. La *saisie semi-automatique*, [nouvelle fonctionnalité en préversion](search-api-preview.md) dans Recherche Azure, fournit des termes de l’index afin de compléter la saisie de l’utilisateur. Nous allons comparer plusieurs techniques pour améliorer la productivité de l’utilisateur pour qu’ils trouvent rapidement et facilement ce qu’ils cherchent, en leur offrant la richesse d’une recherche lors de la saisie.

Ce didacticiel vous aide à gérer une application ASP.NET MVC qui utilise C# pour appeler les [bibliothèques client .NET Recherche Azure](https://aka.ms/search-sdk) et JavaScript pour appeler l’API REST Recherche Azure directement. L’application dans ce didacticiel cible un index rempli via l’exemple de données [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). Vous pouvez utiliser l’index déjà configuré dans la démonstration NYCJobs, ou remplir votre propre index à l’aide d’un chargeur de données dans l’exemple de solution NYCJobs. L’exemple utilise les bibliothèques client JavaScript de l’[interface utilisateur jQuery](https://jqueryui.com/autocomplete/) et [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) pour créer une zone de recherche qui prend en charge la saisie semi-automatique. Avec ces composants, ainsi que Recherche Azure, vous allez voir plusieurs exemples de prise en charge de la saisie semi-automatique avec la saisie en avance dans votre zone de recherche.

Vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Télécharger et configurer la solution
> * Ajouter des informations de service de recherche aux paramètres d’application
> * Implémenter une zone de recherche
> * Ajouter la prise en charge d’une liste de saisie semi-automatique qui extrait des informations depuis une source distante 
> * Récupérer des suggestions et la saisie semi-automatique à l’aide du kit de développement logiciel (SDK) .NET et de l’API REST
> * Prise en charge de mise en cache client pour améliorer les performances 

## <a name="prerequisites"></a>Prérequis

* Visual Studio 2017. Vous pouvez utiliser gratuitement [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). 

* Téléchargez l’exemple de [code source](https://github.com/azure-samples/search-dotnet-getting-started) pour ce didacticiel.

* Un compte Azure et un service Recherche Azure actifs (facultatif). Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/free/). Pour obtenir des instructions sur l’approvisionnement du service, voir [Créer un service de recherche](search-create-service-portal.md). Le compte et le service sont facultatifs car ce didacticiel peut être effectué avec un index NYCJobs hébergé déjà en place pour d’autres démonstrations.

* Téléchargez l’exemple de code [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) pour importer les données NYCJobs dans un index sur votre propre service Recherche Azure (facultatif).

> [!Note]
> Si vous utilisez le service Recherche Azure gratuit, vous êtes limité à trois index. Le chargeur de données NYCJobs crée deux index. Assurez-vous de disposer de l’espace suffisant sur votre service pour accepter les nouveaux index.

### <a name="set-up-azure-search-optional"></a>Configurer Recherche Azure (facultatif)

Suivez les étapes dans cette section si vous souhaitez importer les données de l’exemple d'application NYCJobs dans votre propre index. Cette étape est facultative.  Si vous souhaitez utiliser l’index exemple fourni, passez à la prochaine section pour l’exécution de l’exemple.

1. Dans le dossier DataLoader, de l’exemple de code NYCJobs, ouvrez le fichier de solution DataLoader.sln dans Visual Studio.

1. Mettez à jour les informations de connexion de votre service Recherche Azure.  Ouvrez le fichier App.config dans le projet DataLoader et modifiez les fichiers appSettings TargetSearchServiceName et TargetSearchServiceApiKey pour qu’ils correspondent à votre service Recherche Azure et à la clé d’API Recherche Azure.  Ces éléments sont indiqués dans le portail Azure.

1. Appuyez sur F5 pour lancer l’application.  Deux index sont ainsi créés et l’exemple de données NYCJob est importé.

1. Dans l’exemple de code du didacticiel, ouvrez le fichier de solution AutocompleteTutorial.sln dans Visual Studio.  Ouvrez le fichier Web.config dans le projet AutocompleteTutorial et modifiez les valeurs SearchServiceName et SearchServiceApiKey par les mêmes valeurs ci-dessus.

### <a name="running-the-sample"></a>Exécution de l’exemple

Vous êtes maintenant prêt à exécuter l’exemple d'application du didacticiel.  Ouvrez le fichier de solution AutocompleteTutorial.sln dans Visual Studio pour exécuter le didacticiel.  La solution contient un projet ASP.NET MVC.  Appuyez sur F5 pour exécuter le projet et charger la page dans le navigateur de votre choix.  En haut, vous verrez une option qui vous donne le choix entre C# ou JavaScript.  L’option C# effectue un appel dans HomeController à partir du navigateur et utilise le kit de développement logiciel (SDK) .NET Recherche Azure pour récupérer les résultats.  L’option JavaScript appelle l’API REST Recherche Azure directement depuis le navigateur.  Cette option offrira généralement de bien meilleures performances car elle extrait le contrôleur du flux.  Vous pouvez choisir l’option qui répond à vos besoins et à vos préférences de langue.  Il y a plusieurs exemples de saisie semi-automatique sur la page, avec des conseils pour chacun.  Chaque exemple comprend des exemples de texte recommandés que vous pouvez essayer.  Essayez de saisir quelques lettres dans chaque zone de recherche pour voir ce qu’il se passe.

## <a name="how-this-works-in-code"></a>Fonctionnement du code

Maintenant que vous avez vu les exemples dans le navigateur, examinez l’exemple de plus près pour analyser ses composants et leur fonctionnement.

### <a name="search-box"></a>Zone de recherche

Pour ces deux options de langue, la zone de texte est la même.  Ouvrez le fichier Index.cshtml, situé dans le dossier \Views\Home. La zone de recherche est simple :

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Il s’agit d’une zone de texte banale avec une classe pour le style, un ID à faire référencer par JavaScript et un texte d’espace réservé.  Tout se passe dans Javascript.

### <a name="javascript-code-c"></a>Code JavaScript (C#)

L’exemple de langage C# utilise JavaScript dans le fichier Index.cshtml pour exploiter la bibliothèque de saisie semi-automatique de l’interface utilisateur.  Cette bibliothèque ajoute l’expérience de saisie semi-automatique à la zone de recherche en effectuant des appels asynchrones vers le contrôleur MVC afin de récupérer les recommandations.  Nous allons examiner le code JavaScript du premier exemple :

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

Ce code s’exécute dans le navigateur au chargement d’une page pour configurer la saisie semi-automatique de la zone de texte « example1a ».  `minLength: 3` garantit que les recommandations ne sont affichées que lorsque plus de trois caractères sont saisis dans la zone de recherche.  La valeur source est importante :

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Cette ligne indique à l’API de saisie semi-automatique où récupérer la liste des éléments à afficher sous la zone de recherche.  Comme il s’agit d’un projet MVC, la fonction Suggest appelée dans le fichier HomeController.cs.  Nous examinerons cela de plus près dans la section suivante.  Elle transmet également quelques paramètres pour contrôler la mise en surbrillance, les correspondances approximatives et les termes.  L’API JavaScript de saisie semi-automatique ajoute le paramètre de terme.

#### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Développement de l’exemple pour la prise en charge des correspondances approximatives

La recherche approximative vous permet d’obtenir des résultats selon des correspondances proches, même si l’utilisateur a mal épelé un mot dans la zone de recherche.  Essayons en changeant la ligne source pour activer la correspondance approximative.

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

### <a name="homecontrollercs-c"></a>HomeController.cs (C#)

Maintenant que nous avons passé en revue le code JavaScript de l’exemple, jetons un œil au code du contrôleur C# qui récupère les recommandations avec le kit de développement logiciel (SDK) .NET Recherche Azure.

1. Ouvrez le fichier HomeController.cs sous le répertoire Controllers. 

1. La première chose que vous remarquerez est une méthode en haut de la classe nommée InitSearch.  Cette méthode crée un client d’index HTTP authentifié dans le service de Recherche Azure.  Si vous souhaitez en savoir plus sur son fonctionnement, consultez le didacticiel suivant : [Utilisation de la recherche Azure depuis une application .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

1. Passez à la fonction Suggest.

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

La fonction Suggest prend deux paramètres qui déterminent si les meilleurs résultats sont renvoyés ou si la correspondance approximative est utilisée en plus de la recherche de terme.  La méthode crée un objet SuggestParameters, qui est ensuite envoyé à l’API Suggest. Le résultat est ensuite converti en JSON pour être visible par le client.
Ajoutez un point d'arrêt au début de la fonction Suggest et examinez le code (facultatif).  Observez la réponse renvoyée par le kit de développement logiciel (SDK) et comment elle est convertie en résultat renvoyé par la méthode.

Les autres exemples sur la page suivent le même schéma pour ajouter la mise en surbrillance, la saisie en avance pour les recommandations de saisie semi-automatique et les facettes pour prendre en charge la mise en cache client des résultats de la saisie semi-automatique.  Examinez chaque exemple pour comprendre leur fonctionnement et comment les exploiter dans votre expérience de recherche.

### <a name="javascript-language-example"></a>Exemple de langage JavaScript

Pour l’exemple de langage JavaScript, le code JavaScript sur la page IndexJavaScript.cshtml exploite la saisie semi-automatique de l’interface utilisateur jQuery.  Il s’agit d’une bibliothèque qui fait le gros du travail en présentant une belle zone de recherche et en facilitant les appels asynchrones vers Recherche Azure pour récupérer des recommandations.  Nous allons examiner le code JavaScript du premier exemple :

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

En le comparant à l’exemple ci-dessus, qui appelle le fichier HomeController, vous pouvez remarquer plusieurs similarités.  La configuration de la saisie semi-automatique de `minLength` et `position` est exactement la même.  La différence importante se trouve dans la source.  Au lieu d’appeler la méthode Suggest dans le fichier HomeController, une requête REST est créée dans une fonction JavaScript et exécutée via ajax.  La réponse est ensuite traitée dans « success » et utilisée comme source.

## <a name="takeaways"></a>Éléments importants à retenir

Ce didacticiel décrit les étapes de base pour créer une zone de recherche qui prend en charge la saisie semi-automatique et les suggestions.  Vous avez vu comment créer une application ASP.NET MVC et utiliser le kit de développement logiciel (SDK) .NET ou l’API REST Recherche Azure pour récupérer les suggestions.

## <a name="next-steps"></a>Étapes suivantes

Intégrez des suggestions et la saisie semi-automatique à votre expérience de recherche.  Pensez à l’aide offerte aux utilisateurs par l’utilisation directe du kit de développement logiciel (SDK) .NET ou de l’API REST, pour qu’ils profitent pleinement de Recherche Azure lors de leurs saisies et ainsi améliorer leur productivité.

> [!div class="nextstepaction"]
> [API REST de saisie semi-automatique ](https://docs.microsoft.com/en-us/rest/api/searchservice/autocomplete)
> [API REST de suggestions](https://docs.microsoft.com/en-us/rest/api/searchservice/suggestions)
> [Attribut d’index de facettes sur une API REST création d’index](https://docs.microsoft.com/en-us/rest/api/searchservice/create-index)

