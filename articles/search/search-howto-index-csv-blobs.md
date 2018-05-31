---
title: Indexation d’objets blob CSV avec l’indexeur d’objets blob Recherche Azure | Microsoft Docs
description: Apprendre à indexer les objets blob CSV avec Azure Search
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 12/28/2017
ms.author: eugenesh
ms.openlocfilehash: bf65ab7858ba792418e325e7a025ee1bd88bbb27
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363034"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexation d’objets blob CSV avec l’indexeur d’objets blob Azure Search
Par défaut, l’ [indexeur d’objets blob Azure Search](search-howto-indexing-azure-blob-storage.md) analyse les objets blob de texte délimité comme un bloc de texte unique. Toutefois, avec des objets blob contenant des données CSV, vous souhaitez généralement traiter chaque ligne dans l’objet blob comme un document distinct. Par exemple, vous pouvez analyser le texte délimité suivant dans deux documents contenant chacun les champs « id », « datePublished » et « tags » : 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Dans cet article, vous découvrirez comment analyser les objets blob CSV avec un indexeur d’objets blob Recherche Azure. 

> [!IMPORTANT]
> Cette fonctionnalité est actuellement en préversion publique et ne doit pas être utilisée dans les environnements de production. Pour plus d’informations, consultez [REST api-version=2017-11-11-Preview](search-api-2017-11-11-preview.md). 
> 

## <a name="setting-up-csv-indexing"></a>Configuration de l’indexation CSV
Pour indexer des objets blob CSV, créer ou mettre à jour une définition d’indexeur le mode d’analyse `delimitedText` :  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Pour plus d’informations sur l’API Créer un indexeur, consultez [Créer un indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

`firstLineContainsHeaders` Indique que la première ligne (non vide) de chaque objet blob contient des en-têtes.
Si les objets blob ne contiennent pas de ligne d’en-tête initiale, les en-têtes doivent être spécifiés dans la configuration de l’indexeur : 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Vous pouvez personnaliser le caractère délimiteur à l’aide du paramètre de configuration `delimitedTextDelimiter`. Par exemple : 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Actuellement, seul le format UTF-8 est pris en charge. Si vous devez prendre en charge d’autres encodages, faites-le-nous savoir sur [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Lorsque vous utilisez le mode d’analyse de texte délimité, Azure Search suppose que tous les objets blob dans votre source de données seront de type CSV. Si vous devez prendre en charge une combinaison d’objets blob CSV et autres dans la même source de données, faites-le nous savoir sur [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Exemples de requête
En résumé, voici des exemples complets de charges utiles. 

Source de données : 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexeur :

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer Azure Search
Si vous avez des suggestions de fonctionnalités ou des idées d’amélioration, faites-le-nous savoir [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

