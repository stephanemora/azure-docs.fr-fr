---
title: Indexation de recherche pour requêtes de recherche effectuées dans une langue autre que l’anglais
titleSuffix: Azure Cognitive Search
description: Créez un index qui prend en charge le contenu multilingue, puis créez des requêtes étendues à ce contenu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 627ec77af4e492b4f22404972729cecdb1c40f06
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801602"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Comment créer un index dans plusieurs langues dans Recherche cognitive Azure

Une condition requise dans une application de recherche multilingue est la possibilité d’effectuer une recherche et de récupérer les résultats dans la langue de l’utilisateur. Dans la Recherche cognitive Azure, une manière de remplir les conditions de langue d’une application multilingue consiste à créer des champs dédiés au stockage de chaînes dans une langue spécifique, puis à limiter la recherche en texte intégral à ces seuls champs au moment de la requête.

+ Dans les définitions de champ, définissez un analyseur de langue qui appelle les règles linguistiques de la langue cible. Pour afficher la liste complète des analyseurs pris en charge, consultez [Ajouter des analyseurs linguistiques](index-add-language-analyzers.md).

+ À la demande de requête, définissez les paramètres pour étendre la recherche de texte aux champs spécifiques, puis écarter les résultats de tous les champs qui ne fournissent pas de contenu compatible avec l’expérience de recherche que vous souhaitez offrir.

Le succès de cette technique dépend de l’intégrité du contenu des champs. La Recherche cognitive Azure ne convertit pas les chaînes et n’effectue pas de détection de la langue dans le cadre de l’exécution de la requête. Il vous appartient de vous assurer que les champs contiennent les chaînes que vous attendez.

## <a name="define-fields-for-content-in-different-languages"></a>Définir des champs de contenu dans différentes langues

Dans la Recherche cognitive Azure, les requêtes ciblent un index unique. Les développeurs qui souhaitent fournir des chaînes spécifiques d’une langue dans une expérience de recherche unique définissent généralement des champs dédiés pour stocker les valeurs : un champ pour les chaînes en français, un autre pour les chaînes en anglais, et ainsi de suite.

La propriété « Analyzer » sur une définition de champ est utilisée pour définir l'[Analyseur de langage](index-add-language-analyzers.md). Il sera utilisé pour l’indexation et l’exécution des requêtes.

```JSON
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft"
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft"
    },
```

## <a name="build-and-load-an-index"></a>Créer et charger un index

La [création et le remplissage de l’index](search-get-started-dotnet.md) sont une étape intermédiaire (et peut-être évidente) avant la formulation d’une requête. Nous signalons cette étape ici par souci d’exhaustivité. Une manière de déterminer si la disponibilité de l’index consiste à vérifier la liste des index dans le [portail](https://portal.azure.com).

> [!TIP]
> La détection de la langue et la traduction du texte sont prises en charge lors de l’ingestion des données via l'[enrichissement par IA](cognitive-search-concept-intro.md) et les [compétences](cognitive-search-working-with-skillsets.md). Si vous disposez d’une source de données Azure avec un contenu en langue mixte, vous pouvez essayer les fonctionnalités de détection et de traduction de la langue à l’aide de l'[Assistant Importer des données](cognitive-search-quickstart-blob.md).

## <a name="constrain-the-query-and-trim-results"></a>Limiter la requête et réduire les résultats

Les paramètres de la requête sont utilisés pour limiter la recherche à des champs spécifiques, puis écarter les résultats des champs dépourvus d’utilité pour votre scénario. 

| Paramètres | Objectif |
|-----------|--------------|
| **searchFields** | Limite la recherche en texte intégral à la liste des champs nommés. |
| **$select** | Réduit la réponse pour inclure uniquement les champs que vous spécifiez. Par défaut, tous les champs récupérables sont retournés. Le paramètre **$select** vous permet de choisir les champs à retourner. |

Si votre objectif est de limiter la recherche aux champs contenant des chaînes en français, utilisez **searchFields** pour cibler la requête sur les champs contenant des chaînes dans cette langue.

La spécification de l’analyseur sur une demande de requête n’est pas nécessaire. Un analyseur de langue sur la définition de champ sera toujours utilisé lors du traitement de la requête. Pour les requêtes qui spécifient plusieurs champs appelant différents analyseurs de langue, les termes ou expressions sont traités indépendamment par les analyseurs attribués pour chaque champ.

Par défaut, une recherche retourne tous les champs marqués comme récupérables. Par conséquent, il peut être utile d’exclure les champs non conformes à l’expérience de recherche spécifique de la langue que vous souhaitez offrir. Plus précisément, si vous limitez la recherche à un champ contenant des chaînes en français, vous voulez probablement exclure des résultats les champs contenant des chaînes en anglais. Le paramètre de requête **$select** vous permet de contrôler les champs qui sont retournés à l’application appelante.

#### <a name="example-in-rest"></a>Exemple dans REST

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>Exemple en C#

```csharp
private static void RunQueries(SearchClient srchclient)
{
    SearchOptions options;
    SearchResults<Hotel> response;

    options = new SearchOptions()
    {
        IncludeTotalCount = true,
        Filter = "",
        OrderBy = { "" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description_fr");
    options.SearchFields.Add("Tags");
    options.SearchFields.Add("Description_fr");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
}
```

## <a name="boost-language-specific-fields"></a>Améliorer les champs spécifiques à la langue

Parfois, la langue de l'agent d'émission d'une requête n'est pas connue, auquel cas la requête peut être exécutée simultanément sur tous les champs. Une préférence de résultats dans une langue donnée peut être définie à l'aide des [profils de score](index-add-scoring-profiles.md). Dans l'exemple ci-dessous, les correspondances trouvées dans la description en anglais auront un score supérieur par rapport aux correspondances dans d’autres langues :

```JSON
  "scoringProfiles": [
    {
      "name": "englishFirst",
      "text": {
        "weights": { "description": 2 }
      }
    }
  ]
```

Vous incluez ensuite le profil de score dans la demande de recherche :

```http
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "pets allowed",
  "searchFields": "Tags, Description",
  "select": "HotelName, Tags, Description",
  "scoringProfile": "englishFirst",
  "count": "true"
}
```

## <a name="next-steps"></a>Étapes suivantes

+ [Analyseurs de langage](index-add-language-analyzers.md)
+ [Fonctionnement de la recherche en texte intégral dans la Recherche cognitive Azure](search-lucene-query-architecture.md)
+ [API REST de recherche de documents](/rest/api/searchservice/search-documents)
+ [Vue d’ensemble de l’enrichissement de l’IA](cognitive-search-concept-intro.md)
+ [Présentation de compétences](cognitive-search-working-with-skillsets.md)