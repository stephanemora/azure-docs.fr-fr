---
title: Indexer des objets BLOB contenant plusieurs rechercher indexer des documents à partir de l’indexeur d’objets Blob Azure pour la recherche en texte intégral - recherche Azure
description: Analyser les objets BLOB Azure pour le contenu de texte à l’aide de l’indexeur d’objets Blob Azure Search. Chaque objet blob peut contenir un ou plusieurs documents d’index de recherche Azure.
ms.date: 02/12/2019
author: arv100kri
manager: briansmi
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: e95eff015340659b642dff800a03f615e22c1577
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57220837"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>Indexation d’objets BLOB de la production de plusieurs documents de recherche
Par défaut, un indexeur d’objets blob traite le contenu d’un objet blob comme un document de recherche unique. Certains **parsingMode** valeurs prennent en charge les scénarios où un objet blob individuel peut entraîner plusieurs documents de recherche. Les différents types de **parsingMode** qui permettent à un indexeur pour extraire plus de document d’une recherche à partir d’un objet blob est :
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

> [!IMPORTANT]
> `jsonLines` mode d’analyse est en version préliminaire publique et ne doit pas être utilisé dans les environnements de production. Pour plus d’informations, consultez [REST api-version=2017-11-11-Preview](search-api-2017-11-11-preview.md). 

## <a name="one-to-many-document-key"></a>Clé de document de type un-à-plusieurs
Chaque document qui s’affiche dans un index Azure Search est identifiée par une clé de document. 

Si aucun mode d’analyse n’est spécifié, et s’il en existe aucun mappage explicite pour le champ de clé dans l’index Azure Search automatiquement [mappe](search-indexer-field-mappings.md) le `metadata_storage_path` comme clé la propriété. Ce mappage garantit que chaque objet blob apparaît sous la forme d’un document de recherche distinct.

Lorsque vous utilisez un des modes d’analyse répertoriées ci-dessus, un seul objet blob correspond à « many » rechercher des documents, effectuer une clé de document basée uniquement sur les métadonnées d’objets blob ne sont pas adaptées. Pour contourner cette contrainte, recherche Azure est capable de générer une clé de document de « un-à-plusieurs » pour chaque entité extraite à partir d’un objet blob. Cette propriété est nommée `AzureSearch_DocumentKey` et est ajoutée à chaque entité extraite à partir de l’objet blob. La valeur de cette propriété est garantie être unique pour chaque entité _entre les objets BLOB_ et les entités seront affichera sous forme de documents de recherche distinctes.

Par défaut, lorsqu’aucun mappage de champs explicites pour le champ d’index de clé n’est spécifiés, le `AzureSearch_DocumentKey` est mappé à celui-ci, à l’aide de la `base64Encode` fonction de mappage de champs.

## <a name="example"></a>Exemples
Supposons que vous avez une définition d’index avec les champs suivants :
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Et votre conteneur d’objets blob a des objets BLOB avec la structure suivante :

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Lorsque vous créez un indexeur et définissez le **parsingMode** à `jsonLines` - sans spécifier n’importe quel champ explicite mappages pour le champ de clé, le mappage suivant être implicitement appliquées
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Cette configuration entraîne l’index de recherche Azure contenant les informations suivantes (id codé en base64 raccourci par souci de concision)

| id | température | pression | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mappage de champ personnalisé pour le champ de clé d’index

En supposant que la même définition d’index en tant que l’exemple précédent, supposons que votre conteneur d’objets blob a des objets BLOB avec la structure suivante :

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Lorsque vous créez un indexeur avec `delimitedText` **parsingMode**, il se sentent naturelle pour configurer une fonction de mappage de champs pour le champ de clé comme suit :

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Toutefois, ce mappage est _pas_ résulte 4 documents s’affiche dans l’index, car le `recordid` champ n’est pas unique _entre les objets BLOB_. Par conséquent, nous vous recommandons d’apporter utilisent le mappage de champs implicite appliqué à partir de la `AzureSearch_DocumentKey` propriété pour le champ d’index de clé pour les modes d’analyse « un-à-plusieurs ».

Si vous ne souhaitez pas configurer un mappage de champs explicites, assurez-vous que le _sourceField_ est distincte pour chaque entité **sur tous les objets BLOB**.

> [!NOTE]
> L’approche utilisée par `AzureSearch_DocumentKey` visant à assurer l’unicité par entité extraite est susceptible de changer, et par conséquent vous fiez pas à sa valeur pour les besoins de votre application.

## <a name="see-also"></a>Voir aussi

+ [Indexeurs dans Recherche Azure](search-indexer-overview.md)
+ [Indexation de Stockage Blob Azure avec Recherche Azure](search-howto-index-json-blobs.md)
+ [Indexation d’objets blob CSV avec l’indexeur d’objets blob Recherche Azure](search-howto-index-csv-blobs.md)
+ [Indexation d’objets BLOB JSON avec l’indexeur d’objets blob Azure Search](search-howto-index-csv-blobs.md)

## <a name="NextSteps"></a>Étapes suivantes
* Pour en savoir plus sur la Recherche Azure, consultez la [page du service Recherche](https://azure.microsoft.com/services/search/).