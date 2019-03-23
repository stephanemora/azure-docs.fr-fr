---
title: Ajouter des requêtes prédictives à un index - recherche Azure
description: Activer les actions de requête type-ahead dans recherche Azure en créant des générateurs de suggestions et de formuler des requêtes qui appellent la saisie semi-automatique ou d’autosuggested termes de requête.
ms.date: 03/22/2019
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
ms.openlocfilehash: 877294e80d655ab75be78a5aa57854a03a5f267a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370650"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Ajouter des générateurs de suggestions à un index pour prédictives dans Azure Search

Un **Générateur de suggestions** est une construction dans un [index Azure Search](search-what-is-an-index.md) qui prend en charge une expérience de « recherche-sous-vous-type ». Il contient une liste de champs pour lesquels vous souhaitez activer les entrées de requête prédictives. Il existe deux variantes prédictives : [ *la saisie semi-automatique* ](search-autocomplete-tutorial.md) se termine le terme ou l’expression que vous tapez, [ *suggestion automatique* ](search-autosuggest-example.md) fournit une courte liste des termes ou expressions que vous pouvez sélectionner en tant que requête d’entrée. Vous avez sans aucun doute vu ces comportements avant dans les moteurs de recherche commercial.

![Comparaison visuelle de la saisie semi-automatique et suggestion automatique](./media/index-add-suggesters/visual-comparison-suggest-complete.png "comparaison visuelle de la saisie semi-automatique et suggestion automatique")

Pour implémenter ces comportements dans recherche Azure, il existe un composant de requête et d’index. 

+ Dans un index, ajouter un générateur de suggestions. Vous pouvez utiliser le portail, l’API REST ou le SDK .NET pour créer un générateur de suggestions. 

+ Sur une requête, spécifiez l’action une suggestion automatique ou la saisie semi-automatique. 

> [!Important]
> La saisie semi-automatique est actuellement en version préliminaire, disponible en version préliminaire API REST et du SDK .NET et non pris en charge pour les applications de production. 

Prise en charge de tampon clavier est activée sur une base par champ. Vous pouvez implémenter les deux comportements prédictives dans la même solution de recherche si vous souhaitez une expérience similaire à celui indiqué dans la capture d’écran. Les deux cibles de demandes le *documents* collection d’index spécifique et les réponses sont retournés après un utilisateur a fourni au moins une chaîne d’entrée de trois caractères.

## <a name="create-a-suggester"></a>Créer un générateur de suggestions

Bien qu’un générateur de suggestions possède plusieurs propriétés, il est principalement une collection de champs pour lesquels vous activez une expérience prédictives. Par exemple, une application de voyage peut souhaiter activer la recherche prédictive sur des destinations, villes et attractions. Par conséquent, les trois champs seraient trouve dans la collection de champs.

Pour créer un générateur de suggestions, ajoutez un à un schéma d’index. Vous pouvez avoir un générateur de suggestions dans un index (plus précisément, un générateur de suggestions dans la collection de générateurs de suggestions). 

Dans l’API REST, vous pouvez ajouter des générateurs de suggestions via [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [Index de mise à jour](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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

Dans le SDK .NET, utilisez un [classe de générateur de suggestions](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Générateur de suggestions est une collection, mais elle peut uniquement accepter un seul élément.

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

Points importants à noter concernant les générateurs de suggestions est qu’il existe un nom (générateurs de suggestions sont référencées par nom sur une demande), un searchMode (actuellement qu’une seule, « analyzingInfixMatching ») et la liste des champs pour lesquels prédictives est activé. 

Après avoir créé un suggesteur, ajoutez l'[API Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) à votre logique de requête pour appeler la fonctionnalité.

### <a name="property-reference"></a>Informations de référence sur les propriétés

Les propriétés suivantes définissent un générateur de suggestions :

|Propriété      |Description      |
|--------------|-----------------|
|`name`        |Le nom du Générateur de suggestions. Vous utilisez le nom du Générateur de suggestions lors de l’appel le [API REST de Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou [API REST de la saisie semi-automatique (version préliminaire)](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |Stratégie utilisée pour rechercher des expressions candidates. Le seul mode actuellement pris en charge est `analyzingInfixMatching`, qui effectue une correspondance flexible des expressions en début ou au milieu des phrases.|
|`sourceFields`|Liste d’un ou de plusieurs champs constituant la source du contenu pour des suggestions. Seuls les champs de type `Edm.String` et `Collection(Edm.String)` peuvent être des sources pour des suggestions. Seuls les champs qui n’ont pas un analyseur de langue personnalisé défini peuvent être utilisés.<p/>Spécifiez uniquement les champs qui se prêtent à une réponse attendue et appropriée, qu’il s’agisse d’une chaîne complète dans une barre de recherche ou une liste déroulante.<p/>Un nom d’hôtel est un bon candidat, car il comporte la précision. Les champs détaillés tels que des descriptions et les commentaires sont trop denses. De même, les champs répétitifs, tels que les balises et les catégories sont moins efficaces. Dans les exemples, nous incluons « catégorie » quand même afin de démontrer que vous pouvez inclure plusieurs champs. |

### <a name="index-rebuilds-for-existing-fields"></a>Reconstructions d’index pour les champs existants

Générateurs de suggestions contiennent des champs, et si vous ajoutez un générateur de suggestions à un index existant ou modifier sa composition de champ, vous devrez très probablement reconstruire l’index.

| Action | Impact |
|--------|--------|
| Créer de nouveaux champs et créer un nouveau générateur de suggestions simultanément dans la même mise à jour | Moins d’impact. Si un index contient des champs précédemment ajoutés, l’ajout de nouveaux champs et un nouveau générateur de suggestions n’a aucun impact sur les champs existants. |
| Ajouter des champs existants à un générateur de suggestions | Impact élevé. Ajout d’un champ modifie la définition de champ, ce qui nécessite un [régénération complète](search-howto-reindex.md).|

## <a name="use-a-suggester"></a>Utiliser un générateur de suggestions

Comme indiqué précédemment, vous pouvez utiliser un générateur de suggestions de suggestion automatique, la saisie semi-automatique ou les deux. 

Un générateur de suggestions est référencé dans la demande, ainsi que l’opération. Par exemple, sur un appel GET REST, spécifiez `suggest` ou `autocomplete` sur la collection de documents. Pour REST, après la création d’un générateur de suggestions, utilisez le [API Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou [la saisie semi-automatique API (version préliminaire)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) dans votre logique de requête.

Pour .NET, utilisez [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) ou [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet).

Exemples décrivant chaque demande :

+ [Ajouter suggestion automatique pour les sélections de requête de liste déroulante](search-autosuggest-example.md)

+ [Ajouter la saisie semi-automatique pour les entrées de terme partielle dans Azure Search](search-autocomplete-tutorial.md) (en version préliminaire) 

## <a name="sample-code"></a>Exemple de code

Le [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) exemple contient à la fois C# et le code Java et illustre une construction de générateur de suggestions, suggestion automatique, la saisie semi-automatique et la navigation à facettes. 

Elle utilise un bac à sable service recherche Azure et un index préchargé donc tout ce que vous avez à faire est appuyez sur F5 pour l’exécuter. Aucun abonnement ou la connexion si nécessaire.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les exemples suivants pour voir comment les demandes sont formulées :

+ [Exemple de requête autosuggested](search-autosuggest-example.md) 
+ [Exemple de requête de saisie semi-automatique (version préliminaire)](search-autocomplete-tutorial.md) 
