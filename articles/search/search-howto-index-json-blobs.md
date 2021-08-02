---
title: Effectuer des recherches sur les objets blob JSON
titleSuffix: Azure Cognitive Search
description: Analyser les objets blob Azure JSON pour le contenu de texte à l’aide de l’indexeur d’objets blob Recherche cognitive Azure. Les indexeurs automatisent l’ingestion des données pour les sources de données sélectionnées, comme le stockage Blob Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: c0abbf8dc928dc20778d748e16eea5ae8b775282
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111557087"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Guide pratique pour indexer des objets blob JSON avec un indexeur d’objets blob dans Recherche cognitive Azure

Cet article explique comment [configurer un indexeur d’objets blob](search-howto-indexing-azure-blob-storage.md) pour les objets blob qui se composent de documents JSON. Les objets blob JSON dans Stockage Blob Azure prennent généralement l’une des formes suivantes :

+ Document JSON unique
+ Document JSON contenant un tableau d’éléments JSON bien formés
+ Document JSON contenant plusieurs entités, séparées par un saut de ligne

L’indexeur d’objets blob fournit un paramètre **`parsingMode`** pour optimiser la sortie du document de recherche en fonction de la structure. Les modes d’analyse comprennent les options suivantes :

| parsingMode | Document JSON | Description |
|--------------|-------------|--------------|
| **`json`** | Un seul par objet blob | (par défaut) Analyse les objets blob JSON comme un bloc de texte unique. Chaque objet blob JSON devient un document de recherche unique. |
| **`jsonArray`** | Plusieurs par objet blob | Analyse un tableau JSON dans l’objet blob, où chaque élément du tableau devient un document de recherche distinct.  |
| **`jsonLines`** | Plusieurs par objet blob | Analyse un objet blob qui contient plusieurs entités JSON (également un tableau), les différents éléments étant séparés par un saut de ligne. L’indexeur démarre un nouveau document de recherche après chaque nouvelle ligne. |

Pour **`jsonArray`** et **`jsonLines`** , vous devez consulter [Indexation d’un objet blob pour générer de nombreux documents de recherche](search-howto-index-one-to-many-blobs.md) afin de comprendre comment l’indexeur d’objets blob gère la désambiguïsation de la clé de document pour plusieurs documents de recherche générés à partir du même objet blob.

Dans la définition de l’indexeur, vous pouvez définir des [mappages de champs](search-indexer-field-mappings.md) pour choisir les propriétés du document JSON source à utiliser pour remplir votre index de recherche cible. Par exemple, lors de l’utilisation du mode d’analyse **`jsonArray`** , si le tableau existe en tant que propriété de plus bas niveau, vous pouvez définir une propriété **`document root`** qui indique l’emplacement du tableau dans l’objet blob.

Les sections suivantes décrivent plus en détail chacun de ces modes. Si vous n’êtes pas familiarisé avec les clients et les concepts d’indexeur, consultez [Créer un indexeur de recherche](search-howto-create-indexers.md). Vous devez également connaître les détails de la [configuration d’un indexeur d’objets blob simple](search-howto-indexing-azure-blob-storage.md), qui n’est pas reprise ici.

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>Indexer des documents JSON uniques (un par objet blob)

Par défaut, les indexeurs d’objets blob analysent les objets blob JSON en tant que bloc de texte unique, à raison d’un document de recherche pour chaque objet blob dans un conteneur. Si le JSON est structuré, le document de recherche peut refléter cette structure, avec les différents éléments représentés en tant que champs individuels. Par exemple, prenons le document JSON suivant dans Stockage Blob Azure :

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

L’indexeur d’objets blob analyse le document JSON dans un document de recherche unique ; pour ce faire, il charge un index en mettant en correspondance les champs « text », « datePublished » et « tags » de la source avec les champs d’index cibles de même nom et de même type. Étant donné un index avec les champs « text », « datePublished » et « tags », l’indexeur d’objets blob peut déduire le mappage correct sans qu’un mappage de champs ne soit présent dans la requête.

Même si le comportement par défaut consiste à avoir un document de recherche par objet blob JSON, la définition du mode d’analyse « json » change les mappages de champs internes pour le contenu, en promouvant les champs dans `content` en champs réels dans l’index de recherche. Un exemple de définition d’indexeur pour le mode d’analyse **`json`** peut se présenter comme suit :

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> Comme avec tous les indexeurs, si les champs ne correspondent pas clairement, vous devez envisager de spécifier explicitement des [mappages de champs](search-indexer-field-mappings.md) individuels à moins que vous n’utilisiez les mappages de champs implicites disponibles pour le contenu et les métadonnées d’objets blob, comme décrit dans la [configuration d’un indexeur d’objets blob simple](search-howto-indexing-azure-blob-storage.md).

### <a name="json-example-single-hotel-json-files"></a>Exemple json (fichiers JSON d’hôtel unique)

Le [jeu de données de documents JSON sur les hôtels](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotel-json-documents) disponible sur GitHub est utile pour tester l’analyse JSON, où chaque objet blob représente un fichier JSON structuré. Vous pouvez charger les fichiers de données sur le stockage Blob et utiliser l’Assistant **Importation de données** pour évaluer rapidement comment ce contenu est analysé dans des documents de recherche individuels. 

Le jeu de données est constitué de cinq objets blob, chacun contenant un document d’hôtels avec une collection d’adresses et une collection de chambres. L’indexeur d’objets blob détecte les deux collections et reflète la structure des documents d’entrée dans le schéma d’index.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analyser des tableaux JSON

Vous pouvez également utiliser l’option de tableau JSON. Cette option est utile quand les objets blob contiennent un tableau d’objets JSON bien formés et que vous souhaitez que chaque élément devienne un document de recherche distinct. À l’aide de **`jsonArrays`** , l’objet blob JSON suivant produit trois documents distincts, chacun avec des champs `"id"` et `"text"`.  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

La propriété **`parameters`** de l’indexeur contient des valeurs de mode d’analyse. Pour un tableau JSON, la définition de l’indexeur doit être similaire à l’exemple suivant.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>Exemple jsonArrays (échantillon de données d’essais cliniques)

Le [jeu de données JSON sur les essais cliniques](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-json) disponible sur GitHub est utile pour tester l’analyse d’un tableau JSON. Vous pouvez charger les fichiers de données sur le stockage Blob et utiliser l’Assistant **Importation de données** pour évaluer rapidement comment ce contenu est analysé dans des documents de recherche individuels. 

Le jeu de données est constitué de huit objets blob, chacun contenant un tableau JSON d’entités, pour un total de 100 entités. Les entités varient en fonction des champs remplis, mais le résultat final est un document de recherche par entité, à partir de tous les tableaux, dans tous les objets blob.

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>Analyse de tableaux JSON imbriqués

Pour les tableaux JSON comprenant des éléments imbriqués, vous pouvez spécifier un **`documentRoot`** afin d’indiquer une structure multiniveau. Par exemple, si vos objets blob ressemblent à ceci :

```http
{
    "level1" : {
        "level2" : [
            { "id" : "1", "text" : "Use the documentRoot property" },
            { "id" : "2", "text" : "to pluck the array you want to index" },
            { "id" : "3", "text" : "even if it's nested inside the document" }  
        ]
    }
}
```

Utilisez cette configuration pour indexer le tableau contenu dans la propriété `level2` :

```http
{
    "name" : "my-json-array-indexer",
    ... other indexer properties
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
}
```

## <a name="parse-json-entities-separated-by-newlines"></a>Analyser les entités JSON séparées par des sauts de lignes

Si votre objet blob contient plusieurs entités JSON séparées par un saut de ligne et que vous souhaitez que chaque élément devienne un document de recherche distinct, utilisez **`jsonLines`** .

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

Pour les lignes JSON, la définition de l’indexeur doit être similaire à l’exemple suivant.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

### <a name="jsonlines-example-caselaw-sample-data"></a>Exemple jsonLines (échantillon de données de jurisprudence)

Le [jeu de données JSON de jurisprudence](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) disponible sur GitHub est utile pour tester l’analyse de données comportant des sauts de ligne JSON. Comme avec les autres exemples, vous pouvez charger ces données sur le stockage Blob et utiliser l’Assistant **importation de données** pour évaluer rapidement l’impact du mode d’analyse sur les objets blob individuels.

Le jeu de données se compose d’un objet blob contenant 10 entités JSON séparées par un saut de ligne, où chaque entité décrit un dossier juridique unique. Le résultat final est un document de recherche par entité.

## <a name="map-json-fields-to-search-fields"></a>Mapper des champs JSON sur des champs de recherche

Les mappages de champs sont utilisés pour associer un champ source à un champ de destination dans les situations où les noms et les types de champs ne sont pas identiques. Toutefois, les mappages de champs peuvent également être utilisés pour mettre en correspondance des parties d’un document JSON et les intégrer aux champs de niveau supérieur du document de recherche.

L’exemple suivant illustre ce scénario. Pour en savoir plus sur les mappages de champs en général, consultez [Mappages de champs](search-indexer-field-mappings.md).

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2016-04-13"
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Prenons un index de recherche avec les champs suivants : `text` de type `Edm.String`, `date` de type `Edm.DateTimeOffset` et `tags` de type `Collection(Edm.String)`. Notez la différence entre « datePublished » dans la source et le champ `date` dans l’index. Pour mapper votre document JSON à la forme souhaitée, utilisez les mappages de champ suivants :

```http
"fieldMappings" : [
    { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
    { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
    { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]
```

Les champs sources sont spécifiés selon la notation de [pointeur JSON](https://tools.ietf.org/html/rfc6901). Vous débutez par une barre oblique pour faire référence à la racine de votre document JSON, puis sélectionnez la propriété souhaitée (au niveau arbitraire de l’imbrication) en utilisant un chemin d’accès séparé par des barres obliques avant.

Vous pouvez également faire référence à des éléments de tableau en utilisant un index de base zéro. Par exemple, pour sélectionner le premier élément du tableau « tags » dans l’exemple ci-dessus, utilisez un mappage de champ similaire au suivant :

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Si **`sourceFieldName`** fait référence à une propriété qui n’existe pas dans l’objet blob JSON, ce mappage est ignoré sans erreur. Ce comportement permet à l’indexation de continuer pour les objets blob JSON qui ont un schéma différent (ce qui est un cas d’usage courant). Comme il n’y a pas de contrôle de validation, vérifiez attentivement que les mappages ne comportent pas de fautes de frappe afin de ne pas perdre de documents pour une mauvaise raison.
>

## <a name="next-steps"></a>Étapes suivantes

+ [Configurer des indexeurs d’objets blob](search-howto-indexing-azure-blob-storage.md)
+ [Définir des mappages de champs](search-indexer-field-mappings.md)
+ [Présentation des indexeurs](search-indexer-overview.md)
+ [Guide pratique pour indexer des objets blob CSV avec un indexeur d’objets blob](search-howto-index-csv-blobs.md)
+ [Tutoriel : Rechercher des données semi-structurées à partir de Stockage Blob Azure](search-semi-structured-data.md)