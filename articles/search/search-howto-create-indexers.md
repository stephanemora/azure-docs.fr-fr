---
title: Créer un indexeur
titleSuffix: Azure Cognitive Search
description: Définissez des propriétés sur un indexeur pour déterminer l’origine et les destinations des données. Vous pouvez définir des paramètres pour modifier les comportements du runtime.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 5fc47599d09e5be60311dbda15868d87de4d91d2
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509382"
---
# <a name="creating-indexers-in-azure-cognitive-search"></a>Création d’indexeurs dans Recherche cognitive Azure

Un indexeur de recherche fournit un workflow automatisé pour transférer les documents et le contenu d’une source de données externe vers un index de recherche sur votre service de recherche. Comme initialement conçu, il extrait le texte et les métadonnées d’une source de données Azure, sérialise les documents au format JSON et transmet les documents obtenus à un moteur de recherche pour l’indexation. Depuis, il a été étendu pour prendre en charge l’[enrichissement par IA](cognitive-search-concept-intro.md) pour un traitement approfondi du contenu. 

L’utilisation d’indexeurs réduit considérablement la quantité et la complexité du code que vous devez écrire. Cet article se concentre sur les mécanismes de création d’un indexeur en vue d’un travail plus avancé avec des indexeurs et des [ensembles de compétences](cognitive-search-working-with-skillsets.md) spécifiques aux sources.

## <a name="whats-an-indexer-definition"></a>Qu’est-ce qu’une définition d’indexeur ?

Les indexeurs sont utilisés soit pour l’indexation textuelle qui extrait le contenu alphanumérique des champs sources vers les champs d’index, soit pour le traitement basé sur l’IA qui analyse le texte non différencié pour en déterminer la structure ou qui analyse les images pour en déterminer le texte et les informations, en ajoutant également ce contenu à un index. Les définitions d’index suivantes sont typiques de ce que vous pouvez créer pour l’un ou l’autre scénario.

### <a name="indexers-for-text-content"></a>Indexeurs pour le contenu de texte

L’objectif initial d’un indexeur était de simplifier le processus complexe de chargement d’un index en fournissant un mécanisme permettant de se connecter à une source de données et de lire le contenu textuel et numérique des champs de cette source, sérialiser ce contenu sous forme de documents JSON et transmettre ces documents au moteur de recherche pour indexation. Il s’agit toujours du principal cas d’usage et, pour cette opération, vous devez créer un indexeur avec les propriétés définies dans l’exemple suivant.

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```

Les propriétés **`name`** , **`dataSourceName`** et **`targetIndexName`** sont requises et, selon la façon dont vous créez l’indexeur, la source de données et l’index doivent déjà exister sur le service avant que vous puissiez exécuter l’indexeur. 

La propriété **`parameters`** modifie les comportements du runtime, tels que le nombre d’erreurs acceptables avant l’échec de l’ensemble du travail. Les paramètres vous expliquent également comment spécifier des comportements spécifiques à la source. Par exemple, si la source est Stockage Blob, vous pouvez définir un paramètre qui filtre selon les extensions de fichier : `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }`.

La propriété **`field mappings`** est utilisée pour mapper explicitement les champs source-destination si ces champs diffèrent par leur nom ou leur type. D’autres propriétés (non illustrées) sont utilisées pour [spécifier une planification](search-howto-schedule-indexers.md), créer l’indexeur dans un état désactivé ou spécifier une [clé de chiffrement](search-security-manage-encryption-keys.md) pour le chiffrement supplémentaire des données au repos.

### <a name="indexers-for-ai-indexing"></a>Indexeurs pour l’indexation par IA

Étant donné que les indexeurs sont le mécanisme par lequel un service de recherche effectue des requêtes sortantes, les indexeurs ont été étendus pour prendre en charge les enrichissements par IA, en ajoutant des infrastructures et des objets pour implémenter ce cas d’usage.

Toutes les propriétés et tous les paramètres ci-dessus s’appliquent aux indexeurs qui effectuent l’enrichissement par IA. Les propriétés suivantes sont spécifiques à l’enrichissement par IA : **`skillSets`** , **`outputFieldMappings`** , **`cache`** (préversion et REST uniquement). 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

L’enrichissement par IA n’entre pas dans le cadre de cet article. Pour plus d’informations, commencez par ces articles : [Enrichissement par IA](cognitive-search-concept-intro.md), [Ensembles de compétences dans Recherche cognitive Azure](cognitive-search-working-with-skillsets.md) et [Créer un ensemble de compétences (REST)](/rest/api/searchservice/create-skillset).

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>Choisir un client d’indexation et créer l’indexeur

Lorsque vous êtes prêt à créer un indexeur sur un service de recherche à distance, vous avez besoin d’un client de recherche sous la forme d’un outil, tel que Portail Azure ou Postman, ou du code qui instancie un client d’indexation. Nous vous recommandons le portail Azure ou les API REST pour les premières étapes de développement et les tests de preuve de concept.

### <a name="permissions"></a>Autorisations

Toutes les opérations liées aux indexeurs, notamment les requêtes GET d’état ou de définitions, nécessitent une [clé d’API d’administrateur](search-security-api-keys.md) sur la requête.

### <a name="limits"></a>Limites

Tous les [niveaux de service limitent](search-limits-quotas-capacity.md#indexer-limits) le nombre d’objets que vous pouvez créer. Si vous faites des expériences sur le niveau Gratuit, vous ne pouvez avoir que trois objets de chaque type et deux minutes de traitement de l’indexeur (à l’exclusion du traitement de l’ensemble de compétences).

### <a name="use-azure-portal-to-create-an-indexer"></a>Utiliser Portail Azure pour créer un indexeur

Le portail propose deux options pour créer un indexeur : [**l’Assistant Importer des données**](search-import-data-portal.md) et **Nouvel indexeur** qui fournit des champs pour spécifier une définition d’indexeur. L’Assistant est unique en ce sens qu’il crée tous les éléments requis. D’autres approches requièrent que vous ayez prédéfini une source de données et un index.

La capture d’écran suivante montre où vous pouvez trouver ces fonctionnalités dans le portail. 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="Indexeur des hôtels" border="true":::

### <a name="use-a-rest-client"></a>Utiliser un client REST

Postman et Visual Studio Code (avec une extension pour Recherche cognitive Azure) peuvent tous deux fonctionner comme client d’indexation. En utilisant l’un ou l’autre de ces outils, vous pouvez vous connecter à votre service de recherche et envoyer des requêtes [Create indexer (REST)](/rest/api/searchservice/create-indexer). Il existe de nombreux tutoriels et exemples qui illustrent le fonctionnement des clients REST pour la création d’objets. 

Commencez par l’un de ces articles pour en savoir plus sur chaque client :

+ [Créer un index de recherche à l’aide de REST et de Postman](search-get-started-rest.md)
+ [Bien démarrer avec Visual Studio Code et Recherche cognitive Azure](search-get-started-vs-code.md)

Reportez-vous aux [opérations de l’indexeur (REST)](/rest/api/searchservice/Indexer-operations) pour obtenir de l’aide dans la formulation de requêtes d’indexeur.

### <a name="use-an-sdk"></a>Utiliser un Kit de développement logiciel (SDK)

Pour Recherche cognitive, les Kits de développement logiciel (SDK) Azure implémentent des fonctionnalités généralement disponibles. Ainsi, vous pouvez utiliser n’importe lequel des Kits de développement logiciel (SDK) pour créer des objets liés à l’indexeur. Ils fournissent tous un **SearchIndexerClient** qui contient des méthodes pour créer des indexeurs et des objets connexes, y compris des ensembles de compétences.

| Azure SDK | Client | Exemples |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [DotNetHowToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [Indexeurs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>Exécuter l’indexeur

Un indexeur s’exécute automatiquement lorsque vous créez l’indexeur sur le service. C’est le moment de vérité où vous découvrirez s’il existe des erreurs de connexion aux sources de données, des problèmes de mappage des champs ou des problèmes des ensembles de compétences. 

Il existe plusieurs façons d’exécuter un indexeur :

+ Envoyez une requête HTTP pour [créer un indexeur](/rest/api/searchservice/create-indexer) ou [mettre à jour l’indexeur](/rest/api/searchservice/update-indexer) afin d’ajouter ou de modifier la définition, puis exécutez l’indexeur.

+ Envoyez une requête HTTP pour [exécuter l’indexeur](/rest/api/searchservice/run-indexer) afin d’exécuter un indexeur sans aucune modification de la définition.

+ Exécutez un programme qui appelle des méthodes SearchIndexerClient pour créer, mettre à jour ou exécuter.

> [!NOTE]
> Pour éviter d’exécuter un indexeur dès sa création, incluez **`disabled=true`** dans la définition de l’indexeur.

Vous pouvez également [planifier](search-howto-schedule-indexers.md) l’indexeur pour qu’il invoque le traitement à intervalles réguliers. 

Le traitement planifié coïncide généralement avec un besoin d’indexation incrémentielle du contenu modifié. La logique de détection des modifications est une capacité intégrée aux plateformes sources. Les modifications apportées à un conteneur de blobs sont détectées automatiquement par l’indexeur. Pour obtenir de l’aide sur la façon de tirer parti de la détection des modifications dans d’autres sources de données, consultez les documents de l’indexeur pour des sources de données spécifiques :

+ [Base de données Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Stockage de tables Azure](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="know-your-data"></a>Connaître vos données

Les indexeurs s’attendent à un ensemble de lignes tabulaires, où chaque ligne devient un document de recherche complet ou partiel dans l’index. Souvent, il y a une correspondance biunivoque entre une ligne et le document de recherche qui en résulte, où tous les champs de l’ensemble de lignes remplissent entièrement chaque document. Toutefois, vous pouvez utiliser des indexeurs pour générer une partie seulement d’un document, par exemple si vous utilisez plusieurs indexeurs ou approches pour créer l’index. 

Pour aplatir les données relationnelles dans un ensemble de lignes, vous devez créer une vue SQL ou créer une requête qui retourne des enregistrements parents et enfants dans la même ligne. Par exemple, l’exemple de jeu de données d’hôtels intégré est une base de données SQL qui comporte 50 enregistrements (un pour chaque hôtel), liés aux enregistrements des chambres dans une table connexe. La requête qui aplatit les données collectives dans un ensemble de lignes intègre toutes les informations relatives aux chambres dans les documents JSON de chaque enregistrement d’hôtel. Les informations de chambre intégrées sont générées par une requête qui utilise une clause **FOR JSON AUTO**. Vous pouvez en savoir plus sur cette technique dans [Définir une requête qui retourne une collection JSON incorporée](index-sql-relational-data.md#define-a-query-that-returns-embedded-json). Il ne s’agit là que d’un exemple ; vous pouvez trouver d’autres approches qui produisent le même effet.

En plus des données aplaties, il est important d’extraire uniquement les données interrogeables. Les données interrogeables sont alphanumériques. Recherche cognitive ne peut pas effectuer de recherche dans des données binaires dans n’importe quel format, bien que le service puisse extraire et déduire des descriptions de texte de fichiers images (voir [Enrichissement par IA](cognitive-search-concept-intro.md)) pour créer du contenu pouvant faire l’objet d’une recherche. De même, grâce à l’enrichissement par IA, les grands textes peuvent être analysés par des modèles de langage naturel pour trouver la structure ou les informations pertinentes, générant ainsi un nouveau contenu que vous pouvez ajouter à un document de recherche.

Étant donné que les indexeurs ne corrigent pas les problèmes de données, d’autres formes de nettoyage ou de manipulation des données peuvent être nécessaires. Pour plus d’informations, reportez-vous à la documentation de votre [produit de base de données Azure](/azure/?product=databases).

## <a name="know-your-index"></a>Connaître votre index

Rappelez-vous que les indexeurs transmettent les documents de recherche au moteur de recherche pour qu’il les indexe. Tout comme les indexeurs ont des propriétés qui déterminent le comportement d’exécution, un schéma d’indexation a des propriétés qui influencent profondément la façon dont les chaînes sont indexées (seules les chaînes sont analysées et transformées en jeton). En fonction des affectations de l’analyseur, les chaînes indexées peuvent être différentes de celles que vous avez transmises. Vous pouvez évaluer les effets des analyseurs à l’aide d’[Analyser le texte (REST)](/rest/api/searchservice/test-analyzer). Pour plus d’informations sur les analyseurs, consultez [Analyseurs pour le traitement de texte](search-analyzers.md).

En ce qui concerne la façon dont les indexeurs interagissent avec un index, un indexeur vérifie uniquement les noms et les types de champs. Il n’existe aucune étape de validation qui garantit que le contenu entrant est correct pour le champ de recherche correspondant dans l’index. En guise d’étape de vérification, vous pouvez exécuter des requêtes sur l’index rempli qui retournent des documents entiers ou certains champs. Pour plus d’informations sur l’interrogation du contenu d’un index, consultez [Créer une requête de base](search-query-create.md).

## <a name="next-steps"></a>Étapes suivantes

+ [Planifier des indexeurs](search-howto-schedule-indexers.md)
+ [Définir des mappages de champs](search-indexer-field-mappings.md)
+ [Superviser l’état d’un indexeur](search-howto-monitor-indexers.md)
+ [Se connecter à l’aide d’identités managées](search-howto-managed-identities-data-sources.md)