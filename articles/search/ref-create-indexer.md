---
title: Créer un indexeur (API REST du service Recherche Azure, api-version=2017-11-11-Preview)
description: Dans l’API en préversion, les indexeurs sont étendus pour inclure des paramètres outputFieldMapping permettant de mapper la sortie d’enrichissement sur un champ dans un index Recherche Azure.
services: search
manager: pablocas
author: luiscabrer
ms.author: luisca
ms.date: 05/01/2018
ms.prod: azure
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.openlocfilehash: 595502ecf0a78491c73800e8077de65707c7a486
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365183"
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>Créer un indexeur (API REST du service Recherche Azure, api-version=2017-11-11-Preview)

Cette référence d’API est une version de la documentation propre à la préversion, qui ajoute des éléments de [recherche cognitive](cognitive-search-concept-intro.md) à l’API Créer un indexeur. Comme dans le cas de la version [en disponibilité générale](https://docs.microsoft.com/rest/api/searchservice/create-indexer), vous pouvez créer un indexeur au sein d’un service Recherche Azure en utilisant une demande HTTP POST. 

```http
POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json  
    api-key: [admin key]  
```  
La clé **api-key** doit être une clé d’administration (par opposition à une clé de requête). Pour plus d’informations sur les clés, consultez la section relative à l’authentification dans [Sécurité dans Recherche Azure](search-security-overview.md). [Création d’un service Recherche Azure dans le portail](search-create-service-portal.md) indique comment obtenir l’URL du service et les propriétés de clé utilisées dans la demande.

Vous pouvez également utiliser une requête PUT en spécifiant le nom de source de données sur l'URI. Si la source de données n'existe pas, elle est créée.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
Le paramètre **api-version** est obligatoire. La version actuelle mise à la disposition générale est `api-version=2017-11-11`, mais vous avez besoin de la préversion pour la recherche cognitive : `api-version=2017-11-11-Preview`.  Consultez [Versions d’API dans Recherche Azure](search-api-versions.md) pour plus d’informations.

Pour obtenir des conseils sur la création d’indexeurs propres à la plateforme de données, commencez par consulter la [vue d’ensemble des indexeurs](search-indexer-overview.md), qui inclut la liste complète des [articles connexes](search-indexer-overview.md#next-steps).

> [!NOTE]  
>  Le nombre maximal d'indexeurs que vous pouvez créer varie en fonction du niveau de tarification. Le service gratuit autorise jusqu'à 3 indexeurs. Le service standard autorise 50 indexeurs. Les services haute définition standard ne gèrent pas du tout les indexeurs. Pour plus d’informations, consultez [Limites de service](search-limits-quotas-capacity.md) .    

## <a name="request"></a>Requête  

Les composants [source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source), [index](https://docs.microsoft.com/rest/api/searchservice/create-index) et [ensemble de compétences](ref-create-skillset.md) font partie de la définition d’un [indexeur](search-indexer-overview.md), mais chacun est indépendant et peut être utilisé dans différentes combinaisons. Par exemple, vous pouvez utiliser une même source de données avec plusieurs indexeurs ou un même index avec plusieurs indexeurs ou plusieurs indexeurs écrivant dans un seul index.

 Le corps de la requête contient une définition d’indexeur, avec les éléments suivants :

+ [dataSourceName](#dataSourceName)
+ [targetIndexName](#targetIndexName)
+ [skillsetName](#skillset)
+ [schedule](#indexer-schedule)
+ [parameters](#indexer-parameters)
+ [fieldMappings](#field-mappings)
+ [outputFieldMappings](#output-fieldmappings)

## <a name="request-syntax"></a>Syntaxe de la requête

Vous trouverez ci-dessous la syntaxe de structuration de la charge utile de la requête. Vous trouverez un exemple de requête plus loin dans cet article.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "skillsetName" : "Required for cognitive search enrichment",
    "schedule" : { Optional, but immediately runs once if unspecified. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```
<a name="dataSourceName"></a>

### <a name="datasourcename"></a>« dataSourceName »

Une [définition de source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source) inclut souvent des propriétés qui peuvent être utilisées par un indexeur pour exploiter les caractéristiques d’une plateforme source. Par conséquent, la source de données que vous transmettez à l’indexeur détermine la disponibilité de certaines propriétés et de certains paramètres, par exemple le filtrage de type de contenu dans les objets blob Azure ou le délai d’expiration de requête d’Azure SQL Database. 

<a name="targetIndexName"></a>

### <a name="targetindexname"></a>« targetIndexName »

Un [schéma d’index](https://docs.microsoft.com/rest/api/searchservice/create-index) définit la collection de champs contenant des attributions interrogeables, filtrables, récupérables entre autres qui déterminent l’utilisation du champ concerné. Lors de l’indexation, l’indexeur analyse la source de données, décode éventuellement des documents pour en extraire des informations, sérialise les résultats au format JSON et indexe la charge utile en fonction du schéma défini pour votre index.

<a name="skillset"></a>

### <a name="skillsetname"></a>« skillsetName »

La [recherche cognitive (préversion)](cognitive-search-concept-intro.md) désigne les fonctionnalités de traitement du langage naturel et des images dans Recherche Azure, appliquées au cours de l’ingestion des données pour extraire des entités, des expressions clés, un langage, des informations provenant d’images, etc. Des transformations sont appliquées au contenu via des *compétences*, que vous combinez en un seul et même [*ensemble de compétences*](ref-create-skillset.md), un par indexeur. À l’instar des sources de données et des index, un ensemble de compétences est un composant indépendant que vous attachez à un indexeur. Vous pouvez réaffecter un ensemble de compétences à d’autres indexeurs, mais chaque indexeur ne peut utiliser qu’un ensemble de compétences à la fois.
 
<a name="indexer-schedule"></a>

### <a name="schedule"></a>« schedule »  
Un indexeur peut éventuellement spécifier une planification. Sans planification, l’indexeur s’exécute immédiatement lorsque vous envoyez la requête en vous connectant à la source de données, en l’analysant et en l’indexant. Dans certains cas, notamment les travaux d’indexation de longue durée, les planifications permettent [d’étendre la fenêtre de traitement](search-howto-reindex.md#scale-out-indexing) au-delà du maximum de 24 heures. Si une planification est présente, l’indexeur est exécuté périodiquement, conformément à la planification. Le planificateur est intégré ; vous ne pouvez pas utiliser un planificateur externe. Une **planification** dispose des attributs suivants : 

-   **interval**: obligatoire. Valeur de durée qui spécifie un intervalle ou une période d'exécution pour l'indexeur. L’intervalle minimal autorisé est de cinq minutes ; l’intervalle maximal autorisé est d’une journée. Il doit être formaté en tant que valeur « dayTimeDuration » XSD (un sous-ensemble limité d'une valeur de [durée ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Le modèle pour cela est : `"P[nD][T[nH][nM]]".` Exemples : `PT15M` toutes les 15 minutes, `PT2H` toutes les deux heures.  

-   **startTime** : facultatif. Date/heure UTC (temps universel coordonné) à laquelle l’exécution de l’indexeur doit commencer.  

<a name="indexer-parameters"></a>

### <a name="parameters"></a>« parameters »

Un indexeur peut éventuellement utiliser des paramètres de configuration qui modifient les comportements d’exécution. Les paramètres de configuration sont séparés par des virgules dans la requête de l’indexeur. 

```json
    {
      "name" : "my-blob-indexer-for-cognitive-search",
      ... other indexer properties
      "parameters" : { "maxFailedItems" : "15", "batchSize" : "100", "configuration" : { "parsingMode" : "json", "indexedFileNameExtensions" : ".json, .jpg, .png", "imageAction" : "generateNormalizedImages", "dataToExtract" : "contentAndMetadata" } }
    }
```

#### <a name="general-parameters-for-all-indexers"></a>Paramètres généraux pour tous les indexeurs

| Paramètre | Type et valeurs autorisées   | Usage  |
|-----------|------------|--------------------------|--------|
| `"batchSize"` | Entier <br/>La valeur par défaut est propre à la source (1 000 pour Azure SQL Database et Azure Cosmos DB, 10 pour Stockage Blob Azure). | Spécifie le nombre d’éléments lus à partir de la source de données et indexés comme un lot unique afin d’améliorer les performances. |
| `"maxFailedItems"` | Entier <br/>La valeur par défaut est 0 | Nombre d’erreurs tolérées avant que l’exécution d’un indexeur ne soit considérée comme un échec. Définissez ce paramètre sur -1 si vous ne souhaitez pas que des erreurs arrêtent le processus d’indexation. Vous pouvez récupérer des informations sur les éléments en échec à l’aide de l’opération [Get Indexer Status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).  |
| `"maxFailedItemsPerBatch"` | Entier <br/>La valeur par défaut est 0 | Nombre d’erreurs tolérées dans chaque lot avant que l’exécution d’un indexeur ne soit considérée comme un échec. Définissez ce paramètre sur -1 si vous ne souhaitez pas que des erreurs arrêtent le processus d’indexation. |

#### <a name="blob-configuration-parameters"></a>Paramètres de configuration des objets blob

Plusieurs paramètres sont propres à un indexeur particulier, par exemple [l’indexation d’objets blob Azure](search-howto-indexing-azure-blob-storage.md).

| Paramètre | Type et valeurs autorisées   | Usage  |
|-----------|---------------------------|--------|
| `"parsingMode"` | Chaîne<br/>`"text"`<br/>`"delimitedText"`<br/>`"json"`<br/>`"jsonArray"`  | Pour les [objets blob Azure](search-howto-indexing-azure-blob-storage.md), définissez ce paramètre sur `text` pour améliorer les performances de l’indexation des fichiers de texte brut dans le stockage d’objets blob. <br/>Pour les [objets blob au format CSV](search-howto-index-csv-blobs.md), définissez ce paramètre sur `delimitedText` lorsque les objets blob sont des fichiers CSV bruts. <br/>Pour les [objets blob au format JSON](search-howto-index-json-blobs.md), définissez ce paramètre sur `json` pour extraire un contenu structuré ou sur `jsonArray` (préversion) pour extraire des éléments individuels d’un tableau sous forme de documents distincts dans Recherche Azure. |
| `"excludedFileNameExtensions"` | Chaîne<br/>liste de valeurs séparées par des virgules | Pour les [objets blob Azure](search-howto-indexing-azure-blob-storage.md), ignore les types de fichier contenus dans la liste. Par exemple, vous pouvez inclure les extensions « .png, .mp4 » pour ignorer ces fichiers lors de l’indexation. | 
| `"indexedFileNameExtensions"` | Chaîne<br/>liste de valeurs séparées par des virgules | Pour les [objets blob Azure](search-howto-indexing-azure-blob-storage.md), sélectionne les objets blob si l’extension de fichier associée se trouve dans la liste. Par exemple, vous pouvez axer l’indexation sur les fichiers d’applications « .docx, .pptx, .msg » pour inclure spécifiquement ces types de fichier. | 
| `"failOnUnsupportedContentType"` | true <br/>false (valeur par défaut) | Pour les [objets blob Azure](search-howto-indexing-azure-blob-storage.md), définissez ce paramètre sur `false` si vous souhaitez poursuivre l’indexation lorsqu’un type de contenu non pris en charge est rencontré et que vous ne connaissez pas tous les types de contenu (extensions de fichier) à l’avance. |
| `"failOnUnprocessableDocument"` | true <br/>false (valeur par défaut)| Pour les [objets blob Azure](search-howto-indexing-azure-blob-storage.md), définissez ce paramètre sur `false` si vous souhaitez poursuivre l’indexation d’un document en cas d’échec. |
| `"indexStorageMetadataOnlyForOversizedDocuments"` | true <br/>false (valeur par défaut)| Pour les [objets blob Azure](search-howto-indexing-azure-blob-storage.md), définissez cette propriété sur `true` pour indexer toujours les métadonnées de stockage d’un contenu d’objet blob trop volumineux à traiter.  Par défaut, les objets blob surdimensionnés sont traités comme des erreurs. Pour connaître les limites de taille des objets blob, consultez [Limites du service](search-limits-quotas-capacity.md). |
| `"delimitedTextHeaders"` | Chaîne<br/>liste de valeurs séparées par des virgules| Pour les [objets blob au format CSV (préversion)](search-howto-index-csv-blobs.md), spécifie une liste d’en-têtes de colonne séparés par des virgules, ce qui est utile pour le mappage des champs sources aux champs de destination dans un index. |
| `"delimitedTextDelimiter"` | Chaîne<br/>définie par l’utilisateur | Pour les [objets blob au format CSV (préversion)](search-howto-index-csv-blobs.md), spécifie le délimiteur de fin de ligne des fichiers CSV dans lesquels chaque ligne commence un nouveau document (par exemple, « "|" »).  |
| `"firstLineContainsHeaders"` | true (valeur par défaut) <br/>false | Pour les [objets blob au format CSV (préversion)](search-howto-index-csv-blobs.md), indique que la première ligne (non vide) de chaque objet blob contient des en-têtes.|
| `"documentRoot"`  | Chaîne<br/>définie par l’utilisateur | Pour les [tableaux au format JSON (préversion)](search-howto-index-json-blobs.md#nested-json-arrays), étant donné un document structuré ou semi-structuré, vous pouvez spécifier un chemin d’accès au tableau à l’aide de cette propriété. |
| `"dataToExtract"` | Chaîne<br/>`"storageMetadata"`<br/>`"allMetadata"`<br/>`"contentAndMetadata"` (par défaut) | Pour les [objets blob Azure](search-howto-indexing-azure-blob-storage.md) :<br/>Définissez ce paramètre sur `"storageMetadata"` pour indexer seulement les [propriétés d’objets blob standard et les métadonnées spécifiées par l’utilisateur](../storage/blobs/storage-properties-metadata.md). <br/>Définissez ce paramètre sur `"allMetadata"` pour extraire les métadonnées fournies par le sous-système de stockage d’objets blob Azure et indexer les [métadonnées propres au type de contenu](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (par exemple, les métadonnées propres aux seuls fichiers .png). <br/>Définissez ce paramètre sur `"contentAndMetadata"` pour extraire toutes les métadonnées et le contenu textuel de chaque objet blob. <br/><br/>Pour [l’analyse d’images dans la recherche cognitive (préversion)](cognitive-search-concept-image-scenarios.md), lorsque le paramètre `"imageAction"` a la valeur `"generateNormalizedImages"`, le paramètre `"dataToExtract"` indique à l’indexeur les données à extraire du contenu d’images. S’applique au contenu d’images incorporé dans une application . PDF ou autre, ou à des fichiers image tels que .jpg et .png dans des objets blob Azure.  |
| `"imageAction"` |  Chaîne<br/>`"none"`<br/>`"generateNormalizedImages"` | Pour les [objets blob Azure](search-howto-indexing-azure-blob-storage.md), définissez ce paramètre sur `"none"` pour ignorer les images incorporées ou les fichiers image du jeu de données. Il s’agit de la valeur par défaut. <br/><br/>Pour [l’analyse d’images dans la recherche cognitive](cognitive-search-concept-image-scenarios.md), définissez ce paramètre sur `"generateNormalizedImages"` pour extraire le texte des images (par exemple, le mot « stop » d’un panneau de signalisation Stop) et l’incorporer dans le champ de contenu. Lors de l’analyse d’images, l’indexeur crée un tableau d’images normalisées dans le cadre du décodage du document et incorpore les informations générées dans le champ de contenu. Cette action nécessite de définir le paramètre `"dataToExtract"` sur `"contentAndMetadata"`. Une image normalisée fait référence à un traitement supplémentaire qui entraîne une sortie d’image uniforme, dimensionnée et pivotée pour promouvoir un rendu cohérent lorsque vous incluez des images dans les résultats d’une recherche visuelle (par exemple, des photographies de même taille dans un contrôle de graphique comme illustré dans la [démonstration JFK](https://github.com/Microsoft/AzureSearch_JFK_Files)). Ces informations sont générées pour chaque image utilisée. |


#### <a name="other-configuration-parameters"></a>Autres paramètres de configuration

Les paramètres ci-dessous sont propres à Azure SQL Database.

| Paramètre | Type et valeurs autorisées   | Usage  |
|-----------|---------------------------|--------|
| `"queryTimeout"` | Chaîne<br/>« hh:mm:ss »<br/>« 00:05:00 »| Pour [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), définissez ce paramètre pour augmenter le délai d’expiration au-delà de la valeur par défaut de 5 minutes.|

<a name="field-mappings"></a>

### <a name="fieldmappings"></a>« fieldMappings »

Les définitions d’indexeur contiennent des associations de champs pour le mappage d’un champ source sur un champ de destination dans un index Recherche Azure. Il existe deux types d’associations selon que le transfert de contenu suit un itinéraire direct ou enrichi :

+ **fieldMappings** : facultatif ; appliqué quand les noms de champs source et de destination ne correspondent pas, ou quand vous souhaitez spécifier une fonction.
+ **outputFieldMappings** : requis si vous générez [un pipeline d’enrichissement](cognitive-search-concept-intro.md). Dans un pipeline d’enrichissement, le champ de sortie est une construction définie au cours du processus d’enrichissement. Par exemple, le champ de sortie peut être une structure composée générée durant un enrichissement à partir de deux champs distincts dans le document source. 

Dans l’exemple suivant, considérez une table source avec un champ `_id`. Azure Search n'autorise pas un nom de champ commençant par un trait de soulignement. Le champ doit être renommé. Pour cela, utilisez la propriété `fieldMappings` de l'indexeur comme suit :

```json
"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
```

Vous pouvez spécifier plusieurs mappages de champs.

```json
"fieldMappings" : [
    { "sourceFieldName" : "_id", "targetFieldName" : "id" },
    { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" }
]
```

Les noms de champs sources et cibles sont sensibles à la casse.

Pour en savoir plus sur les scénarios où les mappages de champs sont utiles, consultez [Mappages de champs dans les indexeurs Recherche Azure](https://docs.microsoft.com/azure/search/search-indexer-field-mappings).

<a name="output-fieldmappings"></a>

### <a name="outputfieldmappings"></a>« outputFieldMappings »

Dans les scénarios de [recherche cognitive](cognitive-search-concept-intro.md) dans lesquels un ensemble de compétences est lié à un indexeur, vous devez ajouter `outputFieldMappings` pour associer toute sortie d’une étape d’enrichissement qui fournit du contenu à un champ pouvant faire l’objet d’une recherche dans l’index.

```json
  "outputFieldMappings" : [
        {
          "sourceFieldName" : "/document/organizations", 
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*", 
          "targetFieldName" : "keyphrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "language",
            "mappingFunction": null
        }      
   ],
```

<a name="FieldMappingFunctions"></a>

### <a name="field-mapping-functions"></a>Fonctions de mappage de champs

Les mappages de champs peuvent également être utilisés pour transformer des valeurs de champs source à l’aide de *fonctions de mappage de champs*. Par exemple, une valeur de chaîne arbitraire peut être encodée en base64 afin qu’elle puisse être utilisée pour remplir un champ de clé de document.

Pour plus d’informations sur quand et comment utiliser les fonctions de mappage de champs, consultez [Fonctions de mappage de champs](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions).

## <a name="request-examples"></a>Exemples de requête  
 Le premier exemple illustre la création d’un indexeur qui copie les données de la table référencée par la source de données `ordersds` vers l’index `orders` selon une planification qui commence le 1er janvier 2015 UTC et s’exécute toutes les heures. Chaque appel de l'indexeur est réussi si l'indexation n'échoue pas pour plus de 5 éléments par lot, et pour plus de 10 éléments au total.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2018-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

Le second exemple illustre une opération de recherche cognitive, indiquée par la référence à un ensemble de compétences et [outputFieldMappings](#output-fieldmappings). Les [ensembles de compétences](ref-create-skillset.md) sont des ressources de niveau supérieur, définies séparément. Cet exemple est une abréviation de la définition de l’indexeur présentée dans le [didacticiel relatif à la recherche cognitive](cognitive-search-tutorial-blob.md).

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
        "sourceFieldName" : "content",
        "targetFieldName" : "content"
    }
   ],
  "outputFieldMappings" : 
  [
    {
        "sourceFieldName" : "/document/organizations", 
        "targetFieldName" : "organizations"
    },
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "configuration": 
    {
    "dataToExtract": "contentAndMetadata",
    "imageAction": "generateNormalizedImages"
    }
  }
}
```

## <a name="response"></a>response  
 Pour une requête réussie : « 201 Créé ».  

## <a name="see-also"></a>Voir aussi

+ [Présentation de l’indexeur](search-indexer-overview.md)
+ [Vue d’ensemble de la recherche cognitive](cognitive-search-concept-intro.md)
+ [Démarrage rapide : Essayer la recherche cognitive](cognitive-search-quickstart-blob.md)
+ [Guide pratique pour mapper des champs](cognitive-search-output-field-mapping.md)
