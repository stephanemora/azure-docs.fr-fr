---
title: Ajouter des suggestions et la saisie semi-automatique dans une zone de recherche - recherche Azure
description: Activer les actions de requête prédictives dans recherche Azure en créant des générateurs de suggestions et de formuler des requêtes qui remplissent dans une zone de recherche avec terminé termes ou expressions.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: 9385ed5127b8cc1aaf84c887b652fd8970883ba6
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524023"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Ajouter des suggestions ou avec saisie semi-automatique à votre application de recherche Azure

Dans cet article, découvrez comment utiliser [suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) et [la saisie semi-automatique](https://docs.microsoft.com/rest/api/searchservice/autocomplete) pour créer une zone de recherche puissant qui prend en charge les comportements de recherche en tant que-vous-type.

+ *Suggestions* sont des suggestions de résultats générés en cours de frappe, où chaque suggestion est un résultat unique à partir de l’index qui correspond à ce que vous avez tapé jusqu'à présent. 

+ *La saisie semi-automatique* « terminée », le mot ou expression actuellement saisie d’un utilisateur. Au lieu de retourner des résultats, il termine une requête, vous pouvez ensuite exécuter pour retourner les résultats. Comme avec des suggestions, un terminé mot ou une expression dans une requête repose sur une correspondance dans l’index. Le service n’offre des requêtes qui retournent des résultats nuls dans l’index.

Vous pouvez télécharger et exécuter l’exemple de code dans **DotNetHowToAutocomplete** pour évaluer ces fonctionnalités. L’exemple de code cible d’un index prédéfini rempli avec [NYCJobs les données de démonstration](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). L’index NYCJobs contient un [construction du Générateur de suggestions](index-add-suggesters.md), qui est une condition requise pour l’utilisation des suggestions ou la saisie semi-automatique. Vous pouvez utiliser l’index préparée hébergée dans un service de bac à sable, ou [remplir votre propre index](#configure-app) à l’aide d’un chargeur de données dans l’exemple de solution NYCJobs. 

Le **DotNetHowToAutocomplete** exemple illustre des suggestions et la saisie semi-automatique, dans les deux C# et les versions de langage JavaScript. C#les développeurs peuvent parcourir une application basée sur ASP.NET MVC qui utilise le [SDK .NET Azure Search](https://aka.ms/search-sdk). Vous trouverez la logique pour effectuer la saisie semi-automatique et les appels de requête suggérés dans le fichier HomeController.cs. Les développeurs JavaScript trouverez logique de requête équivalente dans IndexJavaScript.cshtml, qui inclut des appels directs à la [API REST Azure Search](https://docs.microsoft.com/rest/api/searchservice/). 

Pour les deux versions de langue, l’expérience utilisateur frontal est basée sur le [jQuery UI](https://jqueryui.com/autocomplete/) et [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) bibliothèques. Nous utilisons ces bibliothèques pour créer la zone de recherche prenant en charge des suggestions et la saisie semi-automatique. Entrées collectées dans la zone de recherche sont associées avec des suggestions et des actions de la saisie semi-automatique, tels que ceux tel que défini dans HomeController.cs ou IndexJavaScript.cshtml.

Cet exercice vous guide dans les tâches suivantes :

> [!div class="checklist"]
> * Implémenter une zone de recherche dans JavaScript et émettre des demandes pour les correspondances suggérées ou les modalités de saisie semi-automatique
> * Dans C#, définir des suggestions et des actions de la saisie semi-automatique dans HomeController.cs
> * Dans JavaScript, appelez l’API REST directement pour fournir les mêmes fonctionnalités

## <a name="prerequisites"></a>Conditions préalables

Un service Azure Search est facultatif pour cet exercice, car la solution utilise un service de bac à sable en direct qui héberge un index de démonstration préparé NYCJobs. Si vous souhaitez exécuter cet exemple sur votre propre service de recherche, consultez [index de configurer NYC Jobs](#configure-app) pour obtenir des instructions.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), toute édition. L’exemple de code et les instructions ont été testés dans l’édition Communauté gratuite.

* Téléchargez le [DotNetHowToAutoComplete exemple](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

L’exemple est complet, des suggestions de couverture, la saisie semi-automatique, la navigation à facettes et la mise en cache côté client. Vous devez examiner le fichier Lisez-moi et les commentaires pour une description complète de ce que propose l’exemple.

## <a name="run-the-sample"></a>Exécution de l'exemple

1. Ouvrez **AutocompleteTutorial.sln** dans Visual Studio. La solution contient un projet ASP.NET MVC avec une connexion à l’index de démonstration NYC Jobs.

2. Appuyez sur F5 pour exécuter le projet et charger la page dans le navigateur de votre choix.

En haut, vous verrez une option qui vous donne le choix entre C# ou JavaScript. Le C# option appelle le HomeController à partir du navigateur et utilise le SDK .NET Azure Search pour récupérer les résultats. 

L’option JavaScript appelle l’API REST Recherche Azure directement depuis le navigateur. Cette option offre généralement de bien meilleures performances car elle extrait le contrôleur du flux. Vous pouvez choisir l’option qui répond à vos besoins et à vos préférences de langue. Il y a plusieurs exemples d’autocomplétion dans la page, avec des conseils pour chacun. Chaque exemple comprend des exemples de texte recommandés que vous pouvez essayer.  

Essayez de saisir quelques lettres dans chaque zone de recherche pour voir ce qu’il se passe.

## <a name="search-box"></a>Zone de recherche

Pour les deux C# et les versions de JavaScript, l’implémentation de zone de recherche est exactement le même. 

Ouvrez le **Index.cshtml** fichier sous le dossier \Views\Home pour afficher le code :

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Il s’agit d’une zone de texte d’entrée simple avec une classe pour le style, un ID d’être référencées par JavaScript et le texte d’espace réservé.  La magie réside dans le code JavaScript incorporé.

Le C# langage utilise JavaScript dans Index.cshtml pour tirer parti de la [bibliothèque de la saisie semi-automatique de l’interface utilisateur jQuery](https://jqueryui.com/autocomplete/). Cette bibliothèque ajoute l’expérience de la saisie semi-automatique à la zone de recherche en effectuant des appels asynchrones au contrôleur MVC pour récupérer les suggestions. La version de langage JavaScript est IndexJavaScript.cshtml. Il inclut le script ci-dessous pour la barre de recherche, ainsi que les appels d’API REST d’Azure Search.

Nous allons examiner le code JavaScript pour le premier exemple, qui appelle la fonction de la saisie semi-automatique de l’interface utilisateur, en passant une demande pour obtenir des suggestions de jQuery :

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

Le code ci-dessus s’exécute dans le navigateur lors du chargement de page pour configurer la saisie semi-automatique de l’interface utilisateur jQuery pour la zone d’entrée « example1a ».  `minLength: 3` garantit que les recommandations ne sont affichées que lorsque plus de trois caractères sont saisis dans la zone de recherche.  La valeur source est importante :

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

La ligne ci-dessus indique à la fonction de la saisie semi-automatique de l’interface utilisateur jQuery où obtenir la liste des éléments à afficher sous la zone de recherche. Dans la mesure où il s’agit d’un projet MVC, il appelle la fonction Suggérer dans HomeController.cs qui contient la logique permettant de retourner des suggestions de requête (plus sur suggérer dans la section suivante). Cette fonction transmet également les paramètres de quelques points importants du contrôle, la correspondance approximative et terme. L’API JavaScript d’autocomplétion ajoute le paramètre de terme.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Développement de l’exemple pour la prise en charge des correspondances approximatives

La recherche approximative vous permet d’obtenir des résultats selon des correspondances proches, même si l’utilisateur a mal épelé un mot dans la zone de recherche. Bien que non obligatoire, il améliore considérablement la robustesse de l’expérience prédictives. Essayons en changeant la ligne source pour activer la correspondance approximative.

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

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>jQuery la saisie semi-automatique soutenue par la saisie semi-automatique de la recherche Azure

Jusqu’ici, la code de l’expérience utilisateur de recherche a été centrée sur les suggestions. Le bloc de code suivant montre la fonction de la saisie semi-automatique de l’interface utilisateur jQuery (ligne de index.cshtml dans 91,) en passant une demande pour la saisie semi-automatique de la recherche Azure :

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

Maintenant que nous avons passé en revue le code JavaScript pour la page web, examinons le C# code côté serveur contrôleur qui récupère en fait les correspondances suggérées à l’aide du SDK .NET Azure Search.

Ouvrez le **HomeController.cs** fichier sous le répertoire de contrôleurs. 

La première chose que vous remarquerez est une méthode en haut de la classe appelée `InitSearch`. Cette méthode crée un client d’index HTTP authentifié dans le service de Recherche Azure. Pour plus d’informations, consultez [comment utiliser Azure Search à partir d’une Application .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Sur la ligne 41, notez que la fonction Suggérer. Il est basé sur le [DocumentsOperationsExtensions.Suggest méthode](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

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

La fonction Suggest prend deux paramètres qui déterminent si les meilleurs résultats sont renvoyés ou si la correspondance approximative est utilisée en plus de la recherche de terme. La méthode crée un [SuggestParameters objet](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), qui est ensuite transmis à l’API de suggestion. Le résultat est ensuite converti en JSON pour être visible par le client.

Sur la ligne 69, notez que la fonction de saisie semi-automatique. Il est basé sur le [DocumentsOperationsExtensions.Autocomplete méthode](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

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

La fonction de saisie semi-automatique prend l’entrée de terme de recherche. La méthode crée un [AutoCompleteParameters objet](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Le résultat est ensuite converti en JSON pour être visible par le client.

Ajoutez un point d'arrêt au début de la fonction Suggest et examinez le code (facultatif). Notez que la réponse retournée par le Kit de développement logiciel et comment il est converti en le résultat retourné par la méthode.

Les autres exemples sur la page suivent le même modèle pour ajouter la mise en surbrillance et les facettes pour prendre en charge côté client de mise en cache des résultats de la saisie semi-automatique. Examinez chaque exemple pour comprendre leur fonctionnement et comment les exploiter dans votre expérience de recherche.

## <a name="javascript-example"></a>Exemple JavaScript

Une implémentation de Javascript de la saisie semi-automatique et des suggestions appelle l’API REST, à l’aide d’un URI comme source pour spécifier l’index et l’opération. 

Pour vérifier l’implémentation de JavaScript, ouvrez **IndexJavaScript.cshtml**. Notez que la fonction de la saisie semi-automatique de l’interface utilisateur jQuery est également utilisé pour la zone de recherche, collecte d’entrées de terme de recherche et effectuer des appels asynchrones à Azure Search pour récupérer suggéré correspondances ou terminé les termes du contrat. 

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

En le comparant à l’exemple ci-dessus, qui appelle le fichier HomeController, vous pouvez remarquer plusieurs similarités.  La configuration de l’autocomplétion de `minLength` et `position` est exactement la même. 

La différence importante se trouve dans la source. Au lieu d’appeler la méthode suggérer dans le contrôleur home, une requête REST est créée dans une fonction JavaScript et exécutées à l’aide d’Ajax. La réponse est ensuite traitée dans « success » et utilisée comme source.

Appels REST utilisent des URI pour spécifier si un [la saisie semi-automatique](https://docs.microsoft.com/rest/api/searchservice/autocomplete) ou [Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) appel d’API est effectué. Les URI suivants se trouvent sur les lignes 9 et 10, respectivement.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Sur la ligne 148, vous trouverez un script qui appelle le `autocompleteUri`. Le premier appel à `suggestUri` se trouve sur la ligne 39.

> [!Note]
> Appels REST au service en JavaScript est disponible ici comme une démonstration pratique de l’API REST, mais ne doivent pas être considérées comme une meilleure pratique ou recommandation. L’inclusion d’une clé d’API et le point de terminaison dans un script s’ouvre votre service jusqu'à par déni de service à toute personne peut lire ces valeurs désactiver le script. Lors de sa sécurité utilisation de JavaScript à des fins pédagogiques, peut-être sur les index hébergés sur le service gratuit, nous vous recommandons d’utiliser Java ou C# pour les opérations d’indexation et de requête dans le code de production. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Configurer NYCJobs pour s’exécuter sur votre service

Jusqu'à présent, vous avez été à l’aide de l’index de démonstration NYCJobs hébergé. Si vous souhaitez une visibilité totale tout le code, y compris l’index, suivez ces instructions pour créer et charger l’index dans votre propre service de recherche.

1. [Créez un service Recherche Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour cet exemple. 

   > [!Note]
   > Si vous utilisez le service Recherche Azure gratuit, vous êtes limité à trois index. Le chargeur de données NYCJobs crée deux index. Assurez-vous de disposer de l’espace suffisant sur votre service pour accepter les nouveaux index.

1. Télécharger [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) exemple de code.

1. Dans le dossier de chargeur de données de l’exemple de code NYCJobs, ouvrez **DataLoader.sln** dans Visual Studio.

1. Ajoutez les informations de connexion pour votre service Azure Search. Ouvrez le fichier App.config dans le projet DataLoader et modifiez les fichiers appSettings TargetSearchServiceName et TargetSearchServiceApiKey pour qu’ils correspondent à votre service Recherche Azure et à la clé d’API Recherche Azure. Ces éléments sont indiqués dans le portail Azure.

1. Appuyez sur F5 pour lancer l’application, en créant des deux index et en important les données d’exemple NYCJob.

1. Ouvrez **AutocompleteTutorial.sln** et modifier le fichier Web.config dans le **AutocompleteTutorial** projet. Modifier les valeurs SearchServiceName et SearchServiceApiKey aux valeurs qui sont valides pour votre service de recherche.

1. Appuyez sur F5 pour exécuter l'application. L’exemple d’application web s’ouvre dans le navigateur par défaut. L’expérience est identique à la version de bac à sable, les index et les données sont hébergées sur votre service.

## <a name="next-steps"></a>Étapes suivantes

Cet exemple montre les étapes de base pour la création d’une zone de recherche qui prend en charge la saisie semi-automatique et des suggestions. Vous avez vu comment vous pouvez générer une application ASP.NET MVC et utiliser le SDK .NET Azure Search ou l’API REST pour récupérer les suggestions.

Comme prochaine étape, essayez de l’intégration des suggestions et la saisie semi-automatique dans votre expérience de recherche. Les articles de référence suivants devraient aider.

> [!div class="nextstepaction"]
> [API REST de saisie semi-automatique ](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [API REST de suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Attribut d’index de facettes sur une API REST création d’index](https://docs.microsoft.com/rest/api/searchservice/create-index)

