---
title: Guide pratique pour utiliser les résultats de la recherche
titleSuffix: Azure Cognitive Search
description: Structurez et triez les résultats de recherche, récupérez le nombre de documents et ajoutez la navigation du contenu aux résultats de recherche dans Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: c32e58a43b5409fd9f8ede536167d185270c6a22
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721572"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Guide pratique pour utiliser les résultats de recherche dans Recherche cognitive Azure
Cet article explique comment implémenter les éléments standard d’une page de résultats de recherche, comme les totaux, l’extraction de documents, les ordres de tri et la navigation. Les options de page qui fournissent des données ou des informations aux résultats de recherche sont spécifiées par le biais des demandes [Recherche de documents](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) envoyées au service Recherche cognitive Azure. 

Dans l’API REST, les demandes incluent une commande GET, un chemin d’accès et des paramètres de requête informant le service de la nature de la demande et de la formulation de la réponse. Dans le kit SDK .NET, l’API équivalente est la classe [DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Pour générer rapidement une page de recherche pour votre client, explorez les options suivantes :

+ Utilisez le [générateur d’applications](search-create-app-portal.md) du portail pour créer une page HTML avec barre de recherche, navigation par facettes et zone de résultats.
+ Pour créer un client fonctionnel, suivez le tutoriel [Créer votre première application en C#](tutorial-csharp-create-first-app.md).

Plusieurs exemples de code comportent une interface frontale web qui se trouve ici : [Application de démonstration New York City Jobs](https://azjobsdemo.azurewebsites.net/), [exemple de code JavaScript avec un site de démonstration en direct](https://github.com/liamca/azure-search-javascript-samples) et [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Une demande valide inclut plusieurs éléments, parmi lesquels une URL de service et un chemin d’accès, un verbe HTTP, `api-version`, etc. Par souci de concision, nous avons tronqué les exemples afin de mettre en évidence la syntaxe se rapportant à la pagination uniquement. Pour plus d’informations sur la syntaxe des demandes, consultez [API REST de Recherche cognitive Azure](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Nombre total de résultats et nombre de pages

L’affichage du nombre total des résultats d’une requête et la présentation de ces résultats en blocs plus petits sont des éléments de base sur presque toutes les pages de recherche.

![][1]

Dans Recherche cognitive Azure, vous utilisez les paramètres `$count`, `$top` et `$skip` pour renvoyer ces valeurs. L’exemple suivant illustre un exemple de demande du nombre total de correspondances sur un index nommé « online-catalog », retourné sous la forme `@odata.count` :

    GET /indexes/online-catalog/docs?$count=true

Récupérez des documents par groupes de 15 et affichez le nombre total de résultats à partir de la première page :

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

La pagination des résultats nécessite `$top` et `$skip`, où `$top` indique le nombre d’éléments à renvoyer dans un lot, et `$skip` spécifie le nombre d’éléments à ignorer. Dans l’exemple suivant, chaque page affiche les 15 éléments suivants, conformément aux sauts incrémentiels indiqués dans le paramètre `$skip` .

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Disposition

Vous souhaiterez peut-être afficher une miniature, un sous-ensemble de champs et un lien vers la fiche produit complète sur vos pages de résultats de recherche.

 ![][2]

Dans Recherche cognitive Azure, vous pouvez utiliser `$select` et une [requête d’API Recherche](https://docs.microsoft.com/rest/api/searchservice/search-documents) pour implémenter cette expérience.

Pour renvoyer un sous-ensemble de champs relatif à une disposition en mosaïque :

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Les images et les fichiers multimédias ne peuvent pas faire l’objet de recherches directes et doivent être stockés sur une autre plateforme de stockage, comme l’espace de stockage Azure Blob, afin de limiter les coûts. Dans les index et les documents, définissez un champ destiné à stocker l’adresse URL du contenu externe. Vous pourrez utiliser ce champ comme référence d’image. L’URL de l’image doit se trouver dans le document.

Pour récupérer une page de description de produit relative à un événement **onClick** , utilisez la [recherche de document](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) afin d’indiquer la clé du document à récupérer. Le type de données de la clé est `Edm.String`. Dans cet exemple, il s’agit de *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Tri par pertinence, évaluation ou prix

Les données sont souvent triées par défaut en fonction de la pertinence, mais il est courant de proposer d’autres ordres de tri afin que les clients puissent rapidement réorganiser les résultats existants.

 ![][3]

Dans Recherche cognitive Azure, le tri repose sur l’expression `$orderby` pour tous les champs indexés comme étant `"Sortable": true.`. Une clause `$orderby` est une expression OData. Pour plus d’informations sur la syntaxe, voir [Syntaxe des expressions OData pour les filtres et les clauses order by](query-odata-filter-orderby-syntax.md).

La pertinence est clairement liée aux profils de score. Vous pouvez utiliser le score par défaut, qui repose sur l’analyse de texte et les statistiques pour ordonner tous les résultats : dans ce cas, les documents présentant des correspondances plus nombreuses ou plus fortes affichent un score plus élevé.

D’autres ordres de tri sont souvent associés aux événements **onClick** qui renvoient à une méthode permettant de générer l’ordre de tri. Par exemple, avec cet élément de page :

 ![][4]

Vous pouvez créer la méthode qui accepte l’option de tri sélectionnée comme entrée, et renvoie une liste ordonnée conforme aux critères associés à cette option.

 ![][5]

> [!NOTE]
> Si le score par défaut suffit dans de nombreuses situations, nous vous recommandons tout de même de baser la pertinence sur un profil de score personnalisé. Un profil de score personnalisé accorde plus d’importance aux éléments qui avantagent votre entreprise. Pour plus d’informations, voir [Ajout de profils de score](index-add-scoring-profiles.md) .
>

## <a name="hit-highlighting"></a>Mise en surbrillance des correspondances

Vous pouvez appliquer une mise en forme aux termes correspondants dans les résultats de recherche, ce qui facilite la recherche de correspondance. Des instructions pour la mise en surbrillance des correspondances sont fournies dans la [demande de requête](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

La mise en forme est appliquée aux requêtes de termes entières. Les requêtes portant sur des termes partiels, telles que des recherches approximatives ou des recherches par caractères génériques entraînant une extension de requête dans le moteur, ne peuvent pas utiliser la mise en surbrillance des correspondances.

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```



## <a name="faceted-navigation"></a>Navigation à facettes

Les options de navigation de recherche sont communes à toutes les pages de résultats et se trouvent souvent sur le côté ou en haut de la page. Dans Recherche cognitive Azure, la navigation à facettes permet une recherche autonome en fonction de filtres prédéfinis. Pour en savoir plus, consultez [Navigation à facettes dans Recherche cognitive Azure](search-faceted-navigation.md).

## <a name="filters-at-the-page-level"></a>Filtres au niveau de la page

Si votre solution comporte dans sa conception des pages de recherche dédiées à certains types de contenu (par exemple, une application de vente au détail en ligne avec des départements figurant en haut de la page), vous pouvez associer une [expression de filtre](search-filters.md) à un événement **onClick** pour ouvrir une page à l’état préfiltré.

Vous pouvez envoyer un filtre avec ou sans expression de recherche. Par exemple, la demande suivante applique un filtre en fonction du nom de la marque et ne renvoie que les documents correspondants.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Pour en savoir plus sur les expressions `$filter`, consultez [Recherche de documents (API Recherche cognitive Azure)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

## <a name="see-also"></a>Voir aussi

- [API REST Recherche cognitive Azure](https://docs.microsoft.com/rest/api/searchservice)
- [Opérations d’index](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Opérations de document](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Navigation par facettes dans Recherche cognitive Azure](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
