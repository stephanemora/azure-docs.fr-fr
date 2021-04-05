---
title: Effectuer des recherches sur les objets blob CSV
titleSuffix: Azure Cognitive Search
description: Extrayez et importez un fichier CSV à partir de Stockage Blob Azure en utilisant le mode d’analyse delimitedText.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: d9633031ca8358ab0498c2e806b22e6c4ddd3eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99430477"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Comment indexer des objets blob CSV en utilisant le mode d’analyse delimitedText et des indexeurs d’objets blob dans Recherche cognitive Azure

L’[indexeur d’objets blob](search-howto-indexing-azure-blob-storage.md) dans Recherche cognitive fournit un mode d’analyse `delimitedText` pour les fichiers CSV qui traite chaque ligne du fichier CSV en tant que document de recherche distinct. Par exemple, dans le cas du texte délimité par des virgules suivant, `delimitedText` produirait deux documents dans l’index de recherche : 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

Sans le mode d’analyse `delimitedText`, le contenu entier du fichier CSV serait traité comme un seul document de recherche.

Chaque fois que vous créez plusieurs documents de recherche à partir d’un seul objet blob, veillez à consulter [Indexation d’objets blob pour produire plusieurs documents de recherche](search-howto-index-one-to-many-blobs.md) afin de comprendre le fonctionnement des attributions de clés de document. L’indexeur d’objets blob est capable de rechercher ou de générer des valeurs qui définissent de façon unique chaque nouveau document. Plus précisément, il peut créer un `AzureSearch_DocumentKey` transitoire qui est généré quand un objet blob est analysé en parties plus petites, la valeur étant ensuite utilisée comme clé du document de recherche dans l’index.

## <a name="setting-up-csv-indexing"></a>Configuration de l’indexation CSV

Pour indexer des objets blob CSV, créez ou mettez à jour une définition d’indexeur avec le mode d’analyse `delimitedText` sur une demande [Créer un indexeur](/rest/api/searchservice/create-indexer) :

```http
{
  "name" : "my-csv-indexer",
  ... other indexer properties
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
}
```

`firstLineContainsHeaders` Indique que la première ligne (non vide) de chaque objet blob contient des en-têtes.
Si les objets blob ne contiennent pas de ligne d’en-tête initiale, les en-têtes doivent être spécifiés dans la configuration de l’indexeur : 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

Vous pouvez personnaliser le caractère délimiteur à l’aide du paramètre de configuration `delimitedTextDelimiter`. Par exemple :

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> Actuellement, seul le format UTF-8 est pris en charge. Si vous devez prendre en charge d’autres encodages, faites-le-nous savoir sur [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Lorsque vous utilisez le mode d’analyse de texte délimité, Recherche cognitive Azure suppose que tous les objets blob dans votre source de données seront de type CSV. Si vous devez prendre en charge une combinaison d’objets blob CSV et autres dans la même source de données, faites-le nous savoir sur [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
>

## <a name="request-examples"></a>Exemples de demande

En résumé, voici des exemples complets de charges utiles. 

Source de données : 

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
}   
```

Indexeur :

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-csv-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```


