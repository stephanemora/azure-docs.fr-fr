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
ms.openlocfilehash: d47b14342caf0312e052584d20f1a9c86ca29cad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786918"
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>Créer un indexeur (API REST du service Recherche Azure, api-version=2017-11-11-Preview)

Cette référence d’API est une version de la documentation propre à la préversion, décrivant les améliorations de recherche cognitive apportées à l’indexation.

Comme dans le cas de la version [en disponibilité générale](https://docs.microsoft.com/rest/api/searchservice/create-indexer), vous pouvez créer un indexeur au sein d’un service Recherche Azure en utilisant une demande HTTP POST. 

```http
POST https://[service name].search.windows.net/indexers?api-version=[api-version]  
    Content-Type: application/json  
    api-key: [admin key]  
```  
La clé **api-key** doit être une clé d’administration (par opposition à une clé de requête). Pour plus d’informations sur les clés, consultez la section relative à l’authentification dans [Sécurité dans Recherche Azure](search-security-overview.md). [Création d’un service Recherche Azure dans le portail](search-create-service-portal.md) indique comment obtenir l’URL du service et les propriétés de clé utilisées dans la demande.

Vous pouvez également utiliser une requête PUT en spécifiant le nom de source de données sur l'URI. Si la source de données n'existe pas, elle est créée.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
Le paramètre **api-version** est obligatoire. La version actuelle est `2016-09-01`. Consultez [Versions d’API dans Recherche Azure](search-api-versions.md) pour plus d’informations.

Pour obtenir des conseils sur la création d’indexeurs propres à la plateforme de données, commencez par consulter la [vue d’ensemble des indexeurs](search-indexer-overview.md), qui inclut la liste complète des [articles connexes](search-indexer-overview.md#next-steps).

> [!NOTE]  
>  Le nombre maximal d'indexeurs que vous pouvez créer varie en fonction du niveau de tarification. Le service gratuit autorise jusqu'à 3 indexeurs. Le service standard autorise 50 indexeurs. Pour plus d’informations, consultez [Limites de service](search-limits-quotas-capacity.md) .    

## <a name="request"></a>Requête  
 Le corps de la requête contient une définition de l'indexeur, qui spécifie la source de données et l'index cible pour l'indexation, ainsi qu'une planification d'indexation et des paramètres facultatifs.  

 Vous trouverez ci-dessous la syntaxe de structuration de la charge utile de la requête. Vous trouverez un exemple de demande plus loin dans cette rubrique.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "schedule" : { Optional. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```

### <a name="indexer-schedule"></a>Planification de l’indexeur  
Un indexeur peut éventuellement spécifier une planification. Si une planification est présente, l’indexeur est exécuté périodiquement, conformément à la planification. Le planificateur est intégré ; vous ne pouvez pas utiliser un planificateur externe. La **planification** dispose des attributs suivants : 

-   **interval**: obligatoire. Valeur de durée qui spécifie un intervalle ou une période d'exécution pour l'indexeur. L'intervalle minimal autorisé est de 5 minutes, l'intervalle maximal autorisé est d'une journée. Il doit être formaté en tant que valeur « dayTimeDuration » XSD (un sous-ensemble limité d'une valeur de [durée ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Le modèle pour cela est : `"P[nD][T[nH][nM]]".` Exemples : `PT15M` toutes les 15 minutes, `PT2H` toutes les deux heures.  

-   **startTime** : facultatif. Date/heure UTC (temps universel coordonné) à laquelle l’exécution de l’indexeur doit commencer.  

### <a name="indexer-parameters"></a>Paramètres d’indexeur  
 Un indexeur peut éventuellement spécifier plusieurs paramètres qui affectent son comportement. Tous les paramètres répertoriés ci-dessous sont facultatifs.  

-   **maxFailedItems** : nombre d’éléments dont l’indexation peut échouer avant que l’exécution de l’indexeur soit considérée comme un échec. La valeur par défaut est 0. Des informations sur les éléments qui ont échoué sont retournées par l’opération [Obtenir l’état d’un indexeur&#40;API REST du service Recherche Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).  

-   **maxFailedItemsPerBatch** : nombre d’éléments dont l’indexation peut échouer dans chaque lot avant que l’exécution de l’indexeur soit considérée comme un échec. La valeur par défaut est 0.  

-   **batchSize** : spécifie le nombre d’éléments lus à partir de la source de données et indexés comme un lot unique afin d’améliorer les performances. La valeur par défaut varie selon le type de source de données : 1000 pour Azure SQL et Azure Cosmos DB, et 10 pour le Stockage Blob Azure.

### <a name="field-mapping-parameters"></a>Paramètres de mappage de champs

Les définitions d’indexeur contiennent des associations de champs pour le mappage d’un champ source sur un champ de destination dans un index Recherche Azure. Il existe deux types d’associations selon que le transfert de contenu suit un itinéraire direct ou enrichi :

+ **fieldMappings** : facultatif ; appliqué quand les noms de champs source et de destination ne correspondent pas, ou quand vous souhaitez spécifier une fonction.
+ **outputFieldMappings** : requis si vous générez [un pipeline d’enrichissement](cognitive-search-concept-intro.md). Dans un pipeline d’enrichissement, le champ de sortie est une construction définie au cours du processus d’enrichissement. Par exemple, le champ de sortie peut être une structure composée générée durant un enrichissement à partir de deux champs distincts dans le document source. 

#### <a name="fieldmappings"></a>fieldMappings

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

#### <a name="outputfieldmappings"></a>outputFieldMappings

Dans les scénarios de recherche cognitive dans lesquels un ensemble de compétences est lié à un indexeur, vous devez ajouter `outputFieldMappings` pour associer toute sortie d’une étape d’enrichissement qui fournit du contenu à un champ pouvant faire l’objet d’une recherche dans l’index.

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

#### <a name="field-mapping-functions"></a>Fonctions de mappage de champs

Les mappages de champs peuvent également être utilisés pour transformer des valeurs de champs source à l’aide de *fonctions de mappage de champs*. Par exemple, une valeur de chaîne arbitraire peut être encodée en base64 afin qu’elle puisse être utilisée pour remplir un champ de clé de document.

Pour plus d’informations sur quand et comment utiliser les fonctions de mappage de champs, consultez [Fonctions de mappage de champs](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions).

### <a name="request-body-examples"></a>Exemples de corps de demande  
 L'exemple suivant crée un indexeur qui copie les données de la table référencée par la source de données `ordersds` vers l'index `orders` selon une planification qui commence le 1er janvier 2015 UTC et s'exécute toutes les heures. Chaque appel de l'indexeur est réussi si l'indexation n'échoue pas pour plus de 5 éléments par lot, et pour plus de 10 éléments au total.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

## <a name="response"></a>response  
 Pour une requête réussie : « 201 Créé ».  

## <a name="see-also"></a>Voir aussi

+ [Vue d’ensemble de la recherche cognitive](cognitive-search-concept-intro.md)
+ [Démarrage rapide : Essayer la recherche cognitive](cognitive-search-quickstart-blob.md)
+ [Guide pratique pour mapper des champs](cognitive-search-output-field-mapping.md)
