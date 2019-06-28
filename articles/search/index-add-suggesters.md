---
title: Ajouter des requêtes prédictives à un index - Recherche Azure
description: Activez les actions de requête prédictives dans Recherche Azure en créant des suggesteurs et en formulant des requêtes qui appellent des termes de requête de suggestion automatique ou d’autocomplétion.
ms.date: 05/02/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: eb6667a1429382ed566826de64ad7ffbe83183cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65521881"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Ajouter des suggesteurs à un index pour des requêtes prédictives dans Recherche Azure

Un **suggesteur** est une construction dans un [index Recherche Azure](search-what-is-an-index.md) qui prend en charge une expérience « search-as-you-type » (recherche au fur et à mesure de la frappe). Il contient une liste de champs pour lesquels vous souhaitez activer les entrées de requêtes prédictives. Dans un index, le même suggesteur prend en charge l’une ou l’ensemble de ces deux variantes prédictives : *l’autocomplétion* complète le terme ou l’expression que vous tapez, tandis que les *suggestions* fournissent une courte liste de résultats. 

La capture d’écran suivante illustre ces fonctionnalités prédictives. Dans cette page de recherche Xbox, les éléments autocomplétés vous dirigent vers une nouvelle page de résultats de recherche pour cette requête, tandis que les suggestions sont les résultats réels qui vous redirigent vers une page pour ce jeu en particulier. Vous pouvez limiter l’autocomplétion à un élément dans la barre de recherche ou fournir une liste comme celle présentée ici. Pour les suggestions, vous pouvez faire apparaître n’importe quelle partie d’un document qui décrit le mieux le résultat.

![Comparaison visuelle des requêtes autocomplétées et suggérées](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Comparaison visuelle des requêtes autocomplétées et suggérées")

Pour implémenter ces comportements dans Recherche Azure, il existe un composant de requête et d’index. 

+ Le composant d’index est un suggesteur. Vous pouvez utiliser le portail, l’API REST ou le Kit de développement logiciel (SDK) .NET pour créer un suggesteur. 

+ Le composant de requête est une action spécifiée sur la demande de requête (action d’autocomplétion ou de suggestion). 

La prise en charge de l’expérience « search-as-you-type » est activée par champ. Vous pouvez implémenter les deux comportements prédictifs dans la même solution de recherche si vous souhaitez une expérience similaire à celle indiquée dans la capture d’écran. Les deux requêtes ciblent la collection de *documents* d’un index spécifique, et les réponses sont renvoyées après qu’un utilisateur a saisi une chaîne d’entrée avec trois caractères minimum.

## <a name="create-a-suggester"></a>Créer un suggesteur

Bien qu’un suggesteur possède plusieurs propriétés, il se compose principalement d’une collection de champs pour lesquels vous activez une expérience prédictive. Par exemple, une application de voyage peut souhaiter activer la recherche prédictive sur des destinations, villes et attractions. Dans un tel scénario, les trois champs rejoindraient la collection de champs.

Pour créer un suggesteur, ajoutez-en un à un schéma d’index. Vous pouvez disposer d’un suggesteur dans un index (et plus particulièrement, un suggesteur dans la collection de suggesteurs). 

### <a name="use-the-rest-api"></a>Utiliser l’API REST

Dans l’API REST, vous pouvez ajouter des suggesteurs via [Créer un index](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [Mise à jour d’index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```
Après avoir créé un suggesteur, ajoutez [l’API Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou [l’API Autocomplétion](https://docs.microsoft.com/rest/api/searchservice/autocomplete) à votre logique de requête pour appeler la fonctionnalité.

### <a name="use-the-net-sdk"></a>Utiliser le kit de développement logiciel (SDK) .NET

Dans C#, définissez un [objet de suggesteur](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` est une collection, mais elle peut uniquement accepter un élément. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

## <a name="property-reference"></a>Informations de référence sur les propriétés

Points importants à noter concernant les suggesteurs : ils portent un nom (les suggesteurs sont référencés par nom sur une demande), ils ont un searchMode (juste un pour le moment, « analyzingInfixMatching ») et la liste des champs pour lesquels les fonctionnalités prédictives sont activées. 

Les propriétés suivantes définissent un générateur de suggestions :

|Propriété      |Description      |
|--------------|-----------------|
|`name`        |Nom du suggesteur. Vous utilisez le nom du suggesteur lors de l’appel de [l’API REST Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou de [l’API REST Autocomplétion](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |Stratégie utilisée pour rechercher des expressions candidates. Le seul mode actuellement pris en charge est `analyzingInfixMatching`, qui effectue une correspondance flexible des expressions en début ou au milieu des phrases.|
|`sourceFields`|Liste d’un ou de plusieurs champs constituant la source du contenu pour des suggestions. Seuls les champs de type `Edm.String` et `Collection(Edm.String)` peuvent être des sources pour des suggestions. Seuls les champs qui n’ont pas un analyseur de langue personnalisé défini peuvent être utilisés.<p/>Spécifiez uniquement les champs qui se prêtent à une réponse attendue et appropriée, qu’il s’agisse d’une chaîne complète dans une barre de recherche ou d’une liste déroulante.<p/>Un nom d’hôtel est un bon candidat, car il est précis. Les champs détaillés tels que des descriptions et des commentaires sont trop denses. De même, les champs répétitifs, tels que les balises et les catégories sont moins efficaces. Dans les exemples, nous incluons tout de même « catégorie » pour montrer que vous pouvez inclure plusieurs champs. |

## <a name="when-to-create-a-suggester"></a>Quand créer un suggesteur

Pour éviter une régénération d’index, un suggesteur et les champs spécifiés dans `sourceFields` doivent être créés en même temps.

Si vous ajoutez un suggesteur à un index existant, où les champs existants sont inclus dans `sourceFields`, la définition de champ change radicalement et une régénération est nécessaire. Pour plus d’informations, consultez [Comment régénérer un index Recherche Azure](search-howto-reindex.md).

## <a name="how-to-use-a-suggester"></a>Comment utiliser un suggesteur

Comme indiqué précédemment, vous pouvez utiliser un suggesteur pour les requêtes suggérées, l’autocomplétion ou les deux. 

Un suggesteur est référencé dans la demande, ainsi que l’opération. Par exemple, sur un appel GET REST, spécifiez `suggest` ou `autocomplete` pour la collection de documents. Pour REST, après avoir créé un suggesteur, utilisez [l’API Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou [l’API Autocomplétion](https://docs.microsoft.com/rest/api/searchservice/autocomplete) dans votre logique de requête.

Pour .NET, utilisez [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet) ou [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet).

Pour obtenir un exemple illustrant les deux requêtes, consultez [Add suggestions or autocomplete to your Azure Search application](search-autocomplete-tutorial.md) (Ajouter des suggestions ou l’autocomplétion à votre application Recherche Azure).

## <a name="sample-code"></a>Exemple de code

L’exemple [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) contient à la fois du code C# et Java, et illustre une construction de suggesteur, des requêtes suggérées, l’autocomplétion et la navigation à facettes. 

Il utilise un service Recherche Azure de bac à sable et un index préchargé. Vous n’avez plus qu’à appuyer sur F5 pour l’exécuter. Aucun abonnement ou connexion n’est nécessaire.

## <a name="next-steps"></a>Étapes suivantes

Nous vous recommandons l’exemple suivant pour découvrir la formulation des demandes.

> [!div class="nextstepaction"]
> [Exemples de suggestions et d’autocomplétion](search-autocomplete-tutorial.md) 
