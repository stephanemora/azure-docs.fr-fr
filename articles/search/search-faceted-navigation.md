---
title: Ajouter une hiérarchie de catégorie de navigation à facettes
titleSuffix: Azure Cognitive Search
description: Ajoutez la navigation à facettes pour le filtrage autonome dans les applications qui s’intègrent à Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/30/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ebd61d57e79864c1af8a583de349eb5a6706fce8
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273929"
---
# <a name="add-faceted-navigation-to-a-search-app"></a>Ajouter la navigation à facettes à une application de recherche

La navigation à facettes est utilisée pour le filtrage autonome par exploration hiérarchique sur les résultats d’une requête dans une application de recherche, dans le cadre de laquelle votre application propose des contrôles de formulaire permettant de limiter une recherche à des groupes de documents (par exemple, des catégories ou des marques) ; Recherche cognitive Azure fournit la structure de données et des filtres sur lesquels reposera l’opération. 

Dans cet article, découvrez les étapes de base de la création d’une structure de navigation à facettes dans Recherche cognitive Azure.

> [!div class="checklist"]
> * Définir des attributs de champ dans l’index
> * Structurer la demande et la réponse
> * Ajouter des contrôles et des filtres de navigation dans la couche de présentation

Le code dans la couche de présentation fait le gros du travail dans une expérience de navigation à facettes. Les démonstrations et les exemples listés à la fin de cet article fournissent un code de travail qui vous montre comment tout réunir.

## <a name="faceted-navigation-in-a-search-page"></a>Navigation à facettes dans une page de recherche

Les facettes sont dynamiques et renvoyées sur une requête. Une réponse de recherche s’affiche avec toutes les catégories de facettes utilisées pour parcourir les documents dans le résultat. La requête s’exécute en premier, puis les facettes sont extraites des résultats actuels et assemblées en une structure de navigation à facettes.

Dans Recherche cognitive, les facettes traversent une couche et ne peuvent pas être hiérarchiques. Si vous ne connaissez pas la structure de navigation à facettes, l’exemple suivant en affiche une sur la gauche. Les nombres indiquent le nombre de correspondances pour chaque facette. Le même document peut être représenté dans plusieurs facettes.

:::image source="media/tutorial-csharp-create-first-app/azure-search-facet-nav.png" alt-text="résultats de la recherche à facettes":::

Les facettes peuvent vous aider à trouver ce que vous recherchez, tout en vous assurant d’obtenir au moins un résultat. En tant que développeur, les facettes vous permettent d’exposer les critères de recherche les plus utiles pour naviguer dans votre index de recherche.

## <a name="enable-facets-in-the-index"></a>Activer les facettes dans l’index

Les facettes sont activées sur une base champ par champ dans une définition d’index lorsque vous affectez à l’attribut « facette » la valeur true.

Bien qu’il ne soit pas strictement obligatoire, vous devez également définir l’attribut « filtrable » afin de pouvoir créer les filtres nécessaires pour sauvegarder l’expérience de navigation à facettes dans votre application de recherche.

L’exemple suivant de l’exemple d’index « hôtels » affiche « à choix multiples » et « filtrable » sur les champs à faible cardinalité qui contiennent des valeurs uniques ou des expressions courtes : « Catégorie », « Étiquettes », « Évaluation ».

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "Description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "HotelName", "type": "Edm.String", "facetable": false },
    { "name": "Category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint" }
  ]
}
```

### <a name="choosing-fields"></a>Choix des champs

Vous pouvez calculer des facettes sur la base de champs à une seule valeur ou de collections. Les champs qui fonctionnent le mieux dans la navigation à facettes présentent les caractéristiques suivantes :

* Cardinalité faible (un petit nombre de valeurs distinctes qui se répètent dans l’ensemble des documents dans votre corpus de recherche)

* Valeurs descriptives courtes (un ou deux mots) qui s’affichent correctement dans une arborescence de navigation

Le contenu d’un champ, et non le champ lui-même, produit les facettes dans une structure de navigation à facettes. Si la facette est une *couleur* de champ de chaîne, les facettes sont bleues, vertes et toute autre valeur pour ce champ.

Il est recommandé de vérifier les champs et d’y rechercher des valeurs null, des fautes d’orthographe ou des différences de casse, ainsi que les versions singulier et pluriel du même mot. Les filtres et les facettes ne subissent aucune analyse lexicale ni [vérification orthographique](speller-how-to-add.md), ce qui signifie que toutes les valeurs d’un champ « à facettes » sont des facettes potentielles, même si les mots diffèrent d’un caractère.

### <a name="defaults-in-rest-and-azure-sdks"></a>Valeurs par défaut dans REST et les kits de développement logiciel (SDK) Azure

Si vous utilisez l’un des kits de développement logiciel (SDK) Azure, votre code doit spécifier tous les attributs de champ. En revanche, l’API REST contient des valeurs par défaut pour les attributs de champ basés sur le [type de données](/rest/api/searchservice/supported-data-types). Les types de données suivants sont « filtrables » et « à choix multiple » par défaut :

* `Edm.String`
* `Edm.DateTimeOffset`
* `Edm.Boolean`
* `Edm.Int32`, `Edm.Int64`, `Edm.Double`
* Collections des types ci-dessus (par exemple, `Collection(Edm.String)` ou `Collection(Edm.Double)`)

Vous ne pouvez pas utiliser les champs `Edm.GeographyPoint` ou `Collection(Edm.GeographyPoint)` dans la navigation par facettes. Les facettes fonctionnent mieux sur les champs présentant une faible cardinalité. En raison de la résolution des coordonnées géographiques, il est rare que deux jeux de coordonnées soient égaux dans un jeu de données. Par conséquent, elles ne sont pas prises en charge pour les coordonnées géographiques. Vous aurez besoin d’un champ de ville ou de région pour les facettes créées par lieu.

> [!Tip]
> Pour optimiser le stockage et les performances, une meilleure pratique consiste à désactiver la création de facettes pour les champs qui ne doivent pas être utilisés en tant que facettes. En particulier, les champs de chaîne pour les valeurs uniques, comme un ID ou un nom de produit, doivent être définis sur `"facetable": false` pour empêcher leur utilisation accidentelle (et inefficace) dans la navigation à facettes. Cela est particulièrement vrai pour l’API REST qui active les filtres et les facettes par défaut.

## <a name="facet-request-and-response"></a>Requête et réponse à facettes

Les facettes sont spécifiées dans la requête, et la structure de navigation à facettes est retournée en haut de la réponse. La structure d’une demande et d’une réponse est relativement simple. En fait, le véritable travail derrière la navigation à facettes repose sur la couche de présentation, traitée dans une section ultérieure. 

L’exemple REST suivant est une requête non qualifiée (`"search": "*"`) étendue à la totalité de l’index (voir l'[exemple intégré des hôtels](search-get-started-portal.md)). Les facettes sont généralement une liste de champs, mais cette requête n’en affiche qu’une pour une réponse plus lisible ci-dessous.

```http
POST https://{{service_name}}.search.windows.net/indexes/hotels/docs/search?api-version={{api_version}}
{
    "search": "*",
    "queryType": "simple",
    "select": "",
    "searchFields": "",
    "filter": "",
    "facets": [ "Category"], 
    "orderby": "",
    "count": true
}
```

Il est utile d’initialiser une page de recherche avec une requête ouverte pour remplir complètement la structure de navigation à facettes. Dès que vous transmettez des termes de requête dans la demande, la structure de navigation à facettes est limitée aux correspondances dans les résultats, plutôt qu’à la totalité de l’index.

La réponse à l’exemple ci-dessus comprend la structure de navigation à facettes en haut. La structure se compose de valeurs « Catégorie » et d’un nombre d’hôtels pour chacune d’elles. Elle est suivie du reste des résultats de la recherche, tronqués ici par souci de concision. Cet exemple fonctionne bien pour plusieurs raisons. Le nombre de facettes pour ce champ est inférieur à la limite (la valeur par défaut est 10), de sorte que chaque hôtel de l’index de 50 hôtels est représenté dans exactement une de ces catégories.

```json
{
    "@odata.context": "https://demo-search-svc.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "@odata.count": 50,
    "@search.facets": {
        "Category": [
            {
                "count": 13,
                "value": "Budget"
            },
            {
                "count": 12,
                "value": "Resort and Spa"
            },
            {
                "count": 9,
                "value": "Luxury"
            },
            {
                "count": 7,
                "value": "Boutique"
            },
            {
                "count": 5,
                "value": "Suite"
            },
            {
                "count": 4,
                "value": "Extended-Stay"
            }
        ]
    },
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Category": "Boutique",
            "Tags": [
                "pool",
                "air conditioning",
                "concierge"
            ],
            "ParkingIncluded": false,
  . . .
```

## <a name="facets-syntax"></a>Syntaxe des facettes

Un paramètre de requête de facette est défini sur une liste délimitée par des virgules de champs « à facettes » et, selon le type de données, peut être davantage paramétré pour définir les nombres, les ordres de tri et les plages : `count:<integer>`,`sort:<>`, `interval:<integer>` et `values:<list>`. Pour plus d’informations sur les paramètres de facettes, consultez [« Paramètres de requête » dans l’API REST](/rest/api/searchservice/search-documents#query-parameters).

```http
POST https://{{service_name}}.search.windows.net/indexes/hotels/docs/search?api-version={{api_version}}
{
    "search": "*",
    "facets": [ "Category", "Tags,count:5", "Rating,values:1|2|3|4|5"],
    "count": true
}
```

Pour chaque arborescence de navigation à facettes, il existe une limite par défaut de 10 facettes. Cette valeur par défaut est judicieuse pour les structures de navigation, car elle permet de conserver une taille gérable pour la liste des valeurs. Vous pouvez remplacer la valeur par défaut en affectant une valeur à « nombre ». Par exemple, `"Tags,count:5"` réduit le nombre d’étiquettes sous la section Étiquettes aux cinq premières.

Pour les valeurs numériques et DateTime uniquement, vous pouvez définir explicitement des valeurs sur le champ de facette (par exemple,`facet=Rating,values:1|2|3|4|5`) pour séparer les résultats en plages contiguës (des plages basées sur des valeurs numériques ou des périodes de temps). Vous pouvez également ajouter « Intervalle :, comme dans `facet=Rating,interval:1`. 

Chaque plage est créée avec 0 comme point de départ, une valeur de la liste comme point de terminaison, puis la plage précédente en moins pour créer des intervalles discrets.

### <a name="discrepancies-in-facet-counts"></a>Différences dans les nombres de facettes

Dans certaines circonstances, il est possible que les décomptes de facettes ne correspondent pas aux jeux de résultats (consultez [Navigation par facettes dans la Recherche cognitive Azure (page de questions Microsoft Q&A)](/answers/topics/azure-cognitive-search.html)).

Les décomptes de facettes peuvent être erronés en raison de l'architecture de partitionnement. Chaque index de recherche a plusieurs partitions et chacune d’elles indique les N premières facettes par décompte de document, qui est ensuite combiné en un résultat unique. Si certaines partitions ont beaucoup de valeurs correspondantes, tandis que d’autres en ont moins, il est possible que certaines valeurs de facettes soient manquantes ou sous-comptabilisées dans les résultats.

Ce comportement peut changer à tout moment mais si vous rencontrez ce problème aujourd’hui, vous pouvez le contourner en gonflant artificiellement le décompte :\<number> sur un nombre élevé pour appliquer la déclaration complète à partir de chaque partition. Si la valeur de décompte : est supérieure ou égale au nombre de valeurs uniques dans le champ, vous êtes sûr d'obtenir des résultats précis. Toutefois, lorsque les décomptes de documents sont élevés, les performances baissent, alors utilisez cette option judicieusement.

## <a name="presentation-layer"></a>Couche de présentation

Dans le code d’application, le modèle consiste à utiliser les paramètres de requête de facette pour renvoyer la structure de navigation à facettes, ainsi que les résultats de la facette, mais aussi une expression $filter.  L’expression de filtre gère l’événement de clic et affine le résultat de la recherche en fonction de la sélection des facettes.

### <a name="facet-and-filter-combination"></a>Combinaison de facettes et de filtre

L’extrait de code suivant tiré du fichier `JobsSearch.cs` de la démonstration NYCJobs ajoute la Fonction sélectionnée au filtre si vous sélectionnez une valeur de la facette Fonction.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

Voici un autre exemple provenant de l’exemple des hôtels. L’extrait de code suivant ajoute categorFacet au filtre si un utilisateur sélectionne une valeur à partir de la facette de catégorie.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

### <a name="html-for-faceted-navigation"></a>HTML pour la navigation à facettes

L’exemple suivant, extrait du fichier `index.cshtml` de l’exemple d’application NYCJobs, montre la structure HTML statique correspondant à l’affichage de la navigation à facettes sur la page des résultats de la recherche. La liste de facettes est créée ou recréée dynamiquement lorsque vous envoyez un terme de recherche, ou lorsque vous activez ou désactivez une facette.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

### <a name="build-html-dynamically"></a>Générer dynamiquement du code HTML

L’extrait de code suivant, tiré de la page `index.cshtml` (provenant aussi de la démonstration NYCJobs), crée dynamiquement le code HTML pour afficher la première facette, Fonction. Des fonctions similaires génèrent dynamiquement le code HTML pour les autres facettes. Chaque facette est associée à une étiquette et à un nombre indiquant combien d’éléments ont été trouvés pour ce résultat de facette.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

## <a name="tips-for-working-with-facets"></a>Conseils relatifs à l’utilisation des facettes

Cette section est un ensemble de conseils et de solutions de contournement qui peuvent être utiles.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Conserver une structure de navigation par facettes de manière asynchrone par rapport aux résultats filtrés

L’une des difficultés liées à la navigation à facettes dans Recherche cognitive Azure est le fait que les facettes n’existent que pour les résultats actuels. Dans la pratique, il est courant de conserver un ensemble statique de facettes, afin que l’utilisateur puisse naviguer dans l’ordre inverse, en suivant les étapes déjà parcourues pour explorer les autres chemins d’accès via le contenu de la recherche. 

Il s’agit d’un cas d’usage courant, mais que la structure de navigation à facettes ne propose pas par défaut à l’heure actuelle. Les développeurs qui veulent utiliser des facettes statiques contournent cette limitation en émettant deux requêtes filtrées, l’une dont la portée se limite aux résultats et l’autre, qui permet de créer une liste statique de facettes pour la navigation.

### <a name="clear-facets"></a>Effacer les facettes

Lorsque vous concevez la page de résultats, pensez à ajouter un mécanisme de suppression des facettes. Si vous ajoutez des cases à cocher, il est facile de savoir comment effacer les filtres. Pour les autres dispositions, vous devrez peut-être utiliser un modèle de navigation ou une autre approche créative. Dans l’exemple C# des hôtels, vous pouvez envoyer une recherche vide pour réinitialiser la page. En revanche, l’exemple d’application NYCJobs fournit un `[X]` pouvant faire l’objet d’un clic après une facette sélectionnée pour effacer la facette, ce qui représente une file d’attente visuelle plus forte pour l’utilisateur.

### <a name="trim-facet-results-with-more-filters"></a>Tronquer les résultats de facettes avec d’autres filtres

Les résultats de la facette sont des documents trouvés dans les résultats de la recherche qui correspondent à un terme de la facette. Dans l’exemple suivant, dans les résultats de la recherche pour *cloud computing*, 254 éléments ont également la *spécification interne* comme type de contenu. Les éléments ne sont pas nécessairement mutuellement exclusifs. Si un élément répond aux critères des deux filtres, il est compté dans chacun d'eux. La duplication est possible lors de l’utilisation des facettes sur les champs `Collection(Edm.String)`, souvent utilisés pour implémenter le balisage de document.

```output
Search term: "cloud computing"
Content type
   Internal specification (254)
   Video (10)
```

En général, si vous trouvez que les résultats de la facette sont toujours trop volumineux, nous vous recommandons d’ajouter des filtres afin d’apporter aux utilisateurs de votre application plus d’options pour affiner la recherche.

### <a name="a-facet-only-search-experience"></a>Expérience de recherche à facettes uniquement

Si votre application utilise exclusivement la navigation à facettes (autrement dit, aucune zone de recherche), vous pouvez marquer le champ en tant que `searchable=false`, `filterable=true`, `facetable=true` pour produire un index plus compact. Votre index n’inclut pas les index inversés et il n’y a pas d’analyse de texte ni de création de jetons. Les filtres sont effectués sur des correspondances exactes au niveau du caractère.

### <a name="validate-inputs-at-query-time"></a>Vérifier les entrées au moment de la requête

Si vous générez dynamiquement la liste de facettes en fonction d’une entrée utilisateur non fiable, vérifiez la validité des noms des champs à facettes. Vous pouvez également contourner les noms lors de la génération d’URL à l’aide de `Uri.EscapeDataString()` dans .NET, ou son équivalent dans la plateforme de votre choix.

## <a name="demos-and-samples"></a>Démonstrations et exemples

Plusieurs exemples incluent la navigation à facettes. Cette section contient des liens vers les exemples et des remarques sur la bibliothèque cliente et la langue utilisées pour chacune d’elles.

### <a name="create-your-first-app-in-c-razor"></a>Créer votre première application en C# (Razor)

Ce didacticiel et cet exemple de série en C# incluent une [leçon axée sur la navigation à facettes](tutorial-csharp-facets.md). La solution est une application MVC ASP.NET et la couche de présentation utilise les bibliothèques clientes Razor.

### <a name="add-search-to-web-apps-react"></a>Ajouter une recherche à des applications web (React)

Les didacticiels et les exemples en [C#](tutorial-csharp-overview.md), [Python](tutorial-python-overview.md) et [JavaScript](tutorial-javascript-overview.md) incluent la navigation à facettes, ainsi que des filtres, des suggestions et la saisie semi-automatique. Ces exemples utilisent React pour la couche de présentation.

### <a name="nycjobs-sample-code-and-demo-ajax"></a>Exemple de code et démonstration NYCJobs (Ajax)

L’exemple NYCJobs est une application MVC ASP.NET qui utilise Ajax dans la couche de présentation. Elle est disponible en tant qu' [application de démonstration en direct](https://aka.ms/azjobsdemo) et en tant que code source dans le [référentiel Azure-Samples sur GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

### <a name="video-demonstration"></a>Démonstration vidéo

À 45:25 dans [Présentation approfondie de Recherche cognitive Azure](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410) se trouve une démonstration de la façon d’implémenter des facettes.
