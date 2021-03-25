---
title: Rechercher sur des blobs de texte brut
titleSuffix: Azure Cognitive Search
description: Configurez un indexeur de recherche pour extraire du texte brut de blobs Azure à des fins de recherche en texte intégral dans le service Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: b8881d3fa7ade08da103c5af4b828a12e74cc355
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99509450"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Comment indexer des objets en texte brut dans le service Recherche cognitive Azure

Quand vous utilisez un [indexeur d’objets blob](search-howto-indexing-azure-blob-storage.md) pour extraire du texte d’objet blob pouvant faire l’objet d’une recherche en texte intégral, vous pouvez attribuer un mode d’analyse pour obtenir de meilleurs résultats d’indexation. Par défaut, l’indexeur analyse le contenu des blobs comme un bloc de texte unique. Cependant, si tous les blobs contiennent du texte brut dans le même encodage, vous pouvez améliorer considérablement les performances d’indexation à l’aide du mode d’analyse `text`.

Recommandations pour l’utilisation de l’analyse `text` :

+ Le type de fichier est .txt.
+ Les fichiers sont de n’importe quel type, mais le contenu lui-même est du texte (par exemple, code source d’un programme, HTML, XML, etc.). Pour les fichiers dans une langue de balisage, tous les caractères de syntaxe sont fournis sous forme de texte statique.

Rappelez-vous que tous les indexeurs sérialisent en JSON. Par défaut, le contenu de la totalité du fichier texte est indexé dans un seul grand champ en tant que `"content": "<file-contents>"`. Toutes les instructions de nouvelle ligne et de retour sont incorporées dans le champ de contenu et exprimées sous la forme `\r\n\`.

Si vous souhaitez obtenir un résultat plus précis et que le type de fichier est compatible, envisagez les solutions suivantes :

+ Mode d’analyse [`delimitedText`](search-howto-index-csv-blobs.md), si la source est au format CSV
+ [`jsonArray` ou `jsonLines`](search-howto-index-json-blobs.md), si la source est au format JSON

Une troisième option pour subdiviser le contenu nécessite des fonctionnalités avancées faisant appel à l’[enrichissement par IA](cognitive-search-concept-intro.md). Elle ajoute une analyse qui identifie et assigne des segments du fichier à différents champs de recherche. Il se peut que vous trouviez une solution complète ou partielle par le biais de [compétences intégrées](cognitive-search-predefined-skills.md), mais une solution plus probable serait un modèle d’apprentissage qui comprenne votre contenu, articulé dans un modèle d’apprentissage personnalisé et encapsulé dans une [compétence personnalisée](cognitive-search-custom-skill-interface.md).

## <a name="set-up-plain-text-indexing"></a>Configurer l’indexation de texte brut

Pour indexer des blobs de texte brut, créez ou mettez à jour une définition d’indexeur avec la propriété de configuration `parsingMode` pour `text` sur une demande [Créer un indexeur](/rest/api/searchservice/create-indexer) :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text" } }
}
```

Par défaut, le `UTF-8` encodage est possible. Pour spécifier un encodage différent, utilisez la `encoding` propriété de configuration : 

```http
{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
}
```

## <a name="request-example"></a>Exemple de requête

Les modes d’analyse sont spécifiés dans la définition de l’indexeur.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-plaintext-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```

## <a name="next-steps"></a>Étapes suivantes

+ [Indexeurs dans Recherche cognitive Azure](search-indexer-overview.md)
+ [Comment configurer un indexeur de blobs](search-howto-indexing-azure-blob-storage.md)
+ [Vue d’ensemble de l’indexation de blobs](search-blob-storage-integration.md)