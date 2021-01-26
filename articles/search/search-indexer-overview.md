---
title: Indexateurs pour l'analyse des données lors de l'importation
titleSuffix: Azure Cognitive Search
description: Analysez Azure SQL Database, SQL Managed Instance, Azure Cosmos DB ou Stockage Azure pour extraire les données pouvant être recherchées et remplir un index Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 5861e79054bed0d9d75258dfa9cb39b198f0f93d
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216442"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexeurs dans Recherche cognitive Azure

Dans Recherche cognitive Azure, un *indexeur* est un analyseur qui extrait les données et métadonnées pouvant faire l’objet d’une recherche à partir d’une source de données Azure externe et qui renseigne un index de recherche en fonction des mappages champ à champ entre la source de données et votre index. Cette approche est parfois appelée « modèle d’extraction », car le service extrait des données sans que vous ayez à écrire un code qui ajoute des données à un index.

Les indexeurs sont spécifiques à Azure, avec des indexeurs individuels pour [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Stockage Table Azure](search-howto-indexing-azure-tables.md) et [Stockage Blob](search-howto-indexing-azure-blob-storage.md). Lors de la configuration d’un indexeur, vous spécifiez une source de données (origine), ainsi qu’un index (destination). Plusieurs sources, telles que Stockage Blob, ont des propriétés de configuration supplémentaires spécifiques à ce type de contenu.

Vous pouvez exécuter des indexeurs à la demande ou en fonction d’une planification d’actualisation des données périodique qui s’exécute jusqu’à une fois toutes les cinq minutes. Des mises à jour plus fréquentes requièrent un modèle d’émission qui met à jour simultanément les données dans Recherche cognitive Azure et dans votre source de données externe.

## <a name="usage-scenarios"></a>Scénarios d’usage

Vous pouvez utiliser un indexeur comme seul moyen d’ingestion des données ou utiliser une combinaison de techniques qui incluent le chargement de certains champs dans l’index uniquement, ou bien la transformation ou l’enrichissement des données en même temps. Le tableau suivant récapitule les scénarios principaux.

| Scénario |Stratégie |
|----------|---------|
| Source unique | Ce modèle est le plus simple : une source de données est le seul fournisseur de contenu pour un index de recherche. À partir de la source, vous identifiez un champ contenant des valeurs uniques qui sert de clé de document dans l’index de recherche. La valeur unique sera utilisée comme identificateur. Tous les autres champs sources sont mappés implicitement ou explicitement aux champs correspondants dans un index. </br></br>Un point important à retenir est que la valeur d’une clé de document provient des données sources. Un service de recherche ne génère pas de valeurs de clés. Lors des exécutions suivantes, les documents entrants avec de nouvelles clés sont ajoutés, tandis que les documents entrants avec des clés existantes sont fusionnés ou écrasés, selon que les champs d’index ont la valeur null ou sont remplis. |
| Sources multiples| Un index peut accepter du contenu provenant de plusieurs sources, chaque exécution apportant un nouveau contenu à partir d’une source différente. </br></br>Un résultat peut être un index qui obtient des documents après chaque exécution de l’indexeur, avec des documents entiers créés dans leur intégralité à partir de chaque source. Par exemple, les documents 1 à 100 proviennent du Stockage Blob, les documents 101 à 200 proviennent d’Azure SQL, etc. Le défi de ce scénario consiste à concevoir un schéma d’index qui fonctionne pour toutes les données entrantes et une structure de clé de document uniforme dans l’index de recherche. En mode natif, les valeurs qui identifient de façon unique un document sont metadata_storage_path dans un conteneur d’objets blob et une clé primaire dans une table SQL. Vous pouvez imaginer qu’une ou les deux sources doivent être modifiées pour fournir des valeurs de clés dans un format commun, indépendamment de l’origine du contenu. Pour ce scénario, attendez-vous à effectuer un certain niveau de prétraitement pour homogénéiser les données afin qu’elles puissent être extraites dans un index unique.</br></br>Un autre résultat peut être la recherche de documents partiellement remplis lors de la première exécution, puis complétés lors des exécutions suivantes avec des valeurs provenant d’autres sources. Par exemple, les champs 1 à 10 proviennent du Stockage blob, les objets 11 à 20 proviennent d’Azure SQL, etc. Le défi de ce modèle est de s’assurer que chaque exécution d’indexation cible le même document. La fusion de champs dans un document existant requiert une correspondance sur la clé de document. Pour obtenir un exemple de ce scénario, consultez le [Tutoriel : Indexer à partir de plusieurs sources de données](tutorial-multiple-data-sources.md). |
| Transformation du contenu | Recherche cognitive prend en charge les comportements [d’enrichissement par IA](cognitive-search-concept-intro.md) facultatifs qui ajoutent l’analyse d’images et le traitement en langage naturel pour créer des contenus et structures nouveaux pouvant faire l’objet d’une recherche. L’enrichissement par IA est piloté par l’indexeur, par le biais d’un [ensemble de compétences](cognitive-search-working-with-skillsets.md) joint. Pour effectuer l’enrichissement par IA, l’indexeur a toujours besoin d’un index et d’une source de données, mais dans ce scénario, il ajoute le traitement par l’ensemble de compétences à l’exécution de l’indexeur. |

## <a name="approaches-for-creating-and-managing-indexers"></a>Méthodes de création et de gestion des indexeurs

Vous pouvez créer et gérer des indexeurs en suivant l’une de ces approches :

+ [Portail > Assistant Importer des données](search-import-data-portal.md)
+ [API REST du service](/rest/api/searchservice/Indexer-operations)
+ [Kit de développement logiciel (SDK) .NET](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

Si vous utilisez un kit de développement logiciel (SDK), créez un [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) pour utiliser des indexeurs, des sources de données et des ensembles de compétences. Le lien ci-dessus concerne le kit de développement logiciel (SDK) .NET, mais tous les kits de développement logiciel fournissent un SearchIndexerClient et des API similaires.

Au départ, les nouvelles sources de données sont annoncées en tant que fonctionnalités d’évaluation et sont en mode REST uniquement. Après la mise à la disposition générale, la prise en charge complète est intégrée au portail et aux différents kits de développement logiciel (SDK), chacun étant associé à ses propres calendriers de publication.

## <a name="permissions"></a>Autorisations

Toutes les opérations liées aux indexeurs, notamment les requêtes GET d’état ou de définitions, nécessitent une [admin api-key](search-security-api-keys.md).

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Sources de données prises en charge

Les indexeurs analysent les magasins de données sur Azure.

+ [Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (en préversion)
+ [Stockage de tables Azure](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SQL Managed Instance](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [SQL Server sur les machines virtuelles Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

## <a name="stages-of-indexing"></a>Étapes de l’indexation

Lors d’une exécution initiale, lorsque l’index est vide, un indexeur lit toutes les données fournies dans la table ou le conteneur. Lors des exécutions suivantes, l’indexeur peut généralement détecter et récupérer uniquement les données qui ont été modifiées. Pour les données blob, la détection des modifications est automatique. Pour d’autres sources de données comme Azure SQL ou Cosmos DB, la détection des modifications doit être activée.

Pour chacun des documents qu’il reçoit, un indexeur implémente ou coordonne plusieurs étapes, de la récupération du document à un « transfert » vers un moteur de recherche final pour indexation. Si vous le souhaitez, un indexeur peut également opérer de manière instrumentale pour gérer l’exécution d’un ensemble de compétences et des sorties, en supposant qu’un ensemble de compétences est défini.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Étapes de l'indexeur" border="false":::

### <a name="stage-1-document-cracking"></a>Étape 1 : Décodage du document

Le décodage de document est le processus d’ouverture de fichiers et d’extraction du contenu. Selon le type de source de données, l’indexeur essaiera d’effectuer différentes opérations pour extraire le contenu potentiellement indexable.  

Exemples :  

+ Si le document est un enregistrement d’une [source de données Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), l’indexeur extrait chacun des champs de l’enregistrement.
+ Si le document est un fichier PDF d’une [source de données Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md), l’indexeur extrait le texte, les images et les métadonnées.
+ Si le document est un enregistrement d’une [source de données Cosmos DB](search-howto-index-cosmosdb.md), l’indexeur extrait les champs et les sous-champs du document Cosmos DB.

### <a name="stage-2-field-mappings"></a>Étape 2 : Mappages de champs 

Un indexeur extrait le texte d’un champ source et l’envoie à un champ de destination dans un index ou une base de connaissances. Si les noms et les types de champs coïncident, le chemin d’accès est validé. Toutefois, vous pouvez utiliser des noms ou des types différents dans la sortie, auquel cas vous devez indiquer à l’indexeur comment mapper le champ. Cette étape survient après le décodage du document, mais avant les transformations, lorsque l’indexeur lit les données des documents sources. Lorsque vous définissez un [mappage de champs](search-indexer-field-mappings.md), la valeur du champ source est envoyée telle quelle au champ de destination, sans aucune modification. Les mappages de champs sont facultatifs.

### <a name="stage-3-skillset-execution"></a>Étape 3 : Exécution d’un ensemble de compétences

L’exécution d’un ensemble de compétences est une étape facultative qui appelle un traitement IA intégré ou personnalisé. Vous pouvez en avoir besoin pour la reconnaissance optique de caractères (OCR) sous la forme d’une analyse d’images, ou dans le cas d’une traduction linguistique. Quelle que soit la transformation, l’exécution d’un ensemble de compétences est l’endroit où l’enrichissement se produit. Si un indexeur est un pipeline, vous pouvez considérer un [ensemble de compétences](cognitive-search-defining-skillset.md) comme un « pipeline dans le pipeline ». Un ensemble de compétences possède sa propre séquence d’étapes appelée « compétences ».

### <a name="stage-4-output-field-mappings"></a>Étape 4 : Mappages de champs de sortie

Si vous incluez un ensemble de compétences, vous devrez probablement inclure des mappages de champs de sortie. La sortie d’un ensemble de compétences est en fait une arborescence d’informations appelée « document enrichi ». Les mappages de champs de sortie vous permettent de sélectionner les parties de cette arborescence à mapper dans les champs de votre index. Découvrez comment [définir des mappages de champs de sortie](cognitive-search-output-field-mapping.md).

Alors que les mappages de champs associent les valeurs verbatim de la source de données aux champs de destination, les mappages de champs de sortie indiquent à l’indexeur comment associer les valeurs transformées dans le document enrichi aux champs de destination dans l’index. Contrairement aux mappages de champs, qui sont considérés comme facultatifs, vous devrez toujours définir un mappage de champs de sortie pour tout contenu transformé qui figurera dans un index.

L’image suivante montre un exemple de [session de débogage](cognitive-search-debug-session.md) représentant les étapes de l’indexeur : le décodage de document, les mappages de champs, l’exécution d’un ensemble de compétences, et les mappages de champs de sortie.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="exemple de session de débogage" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-configuration-steps"></a>Étapes de configuration de base

Les indexeurs peuvent offrir des fonctionnalités propres à la source de données. À cet égard, certains aspects de la configuration de l’indexeur ou de la source de données varient en fonction du type d’indexeur. Cependant, tous les indexeurs présentent une composition et des exigences de base identiques. Les étapes communes à tous les indexeurs sont décrites ci-dessous.

### <a name="step-1-create-a-data-source"></a>Étape 1 : Création d'une source de données

Un indexeur obtient une connexion de source de données à partir d’un objet *source de données*. La définition de source de données fournit une chaîne de connexion et éventuellement des informations d’identification. Appelez l’API REST de [création de source de données](/rest/api/searchservice/create-data-source) ou la [classe SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) pour créer la ressource.

Les sources de données sont configurées et gérées indépendamment des indexeurs qui les utilisent. Autrement dit, une source de données peut être utilisée par plusieurs indexeurs pour charger plusieurs index à la fois.

### <a name="step-2-create-an-index"></a>Étape 2 : Création d'un index

Un indexeur automatise certaines tâches liées à l’ingestion des données, mais la création d’un index n’en fait généralement pas partie. Au préalable, vous devez disposer d’un index prédéfini présentant des champs qui correspondent à ceux de votre source de données externe. Les champs doivent correspondre par nom et type de données. Pour plus d’informations sur la structuration d’un index, consultez l’article [Create an Index (Azure Search REST API)](/rest/api/searchservice/Create-Index)(Création d’un index (API REST Recherche cognitive Azure)) ou [SearchIndex class](/dotnet/api/azure.search.documents.indexes.models.searchindex) (Classe SearchIndex). Pour plus d’informations sur les associations de champ, consultez [Mappages de champs dans les indexeurs de Recherche cognitive Azure](search-indexer-field-mappings.md).

> [!Tip]
> Bien que les indexeurs ne puissent pas générer d’index pour vous, l’Assistant **Importation des données** du portail peut vous aider. Dans la plupart des cas, l’Assistant peut déduire un schéma d’index à partir des métadonnées existantes dans la source, en présentant un schéma d’index préliminaire que vous pouvez modifier en ligne pendant que l’Assistant est actif. Une fois que l’index est créé sur le service, les modifications supplémentaires dans le portail sont principalement limitées à l’ajout de nouveaux champs. Pensez à utiliser l’Assistant pour créer un index (mais pas pour le réviser). Pour mettre vos connaissances en pratique, parcourez la [procédure pas à pas dans le portail](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Étape 3 : Créer et planifier l’indexeur

La définition de l’indexeur est une construction qui rassemble tous les éléments liés à l’ingestion des données. Les éléments obligatoires incluent une source de données et un index. Les éléments facultatifs incluent une planification et des mappages de champs. Les mappages de champs ne sont facultatifs que si les champs source et les champs d’index correspondent parfaitement. Pour plus d’informations sur la structuration d’un indexeur, consultez l’article [Create Indexer (Azure Search REST API)](/rest/api/searchservice/Create-Indexer)(Création d’un indexeur (API REST Recherche cognitive Azure)).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Exécuter des indexeurs à la demande

Bien qu’il soit courant de planifier l’indexation, un indexeur peut également être appelé à la demande à l’aide de la [commande Exécuter](/rest/api/searchservice/run-indexer) :

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> Quand l’API d’exécution (Run) retourne un code de réussite, l’appel de l’indexeur a été planifié, mais le traitement réel se produit de façon asynchrone. 

Vous pouvez surveiller l’état de l’indexeur dans le portail ou à l’aide de [l’API Get Indexer Status](/rest/api/searchservice/get-indexer-status). 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Get indexer status

Vous pouvez récupérer l’état et l’historique d’exécution d’un indexeur à l’aide de la [commande Get Indexer Status](/rest/api/searchservice/get-indexer-status) :

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

La réponse contient l'état d'intégrité global de l'indexeur, le dernier appel de l'indexeur (ou celui en cours), ainsi que l'historique des appels récents de l'indexeur.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

L'historique d'exécution contient les 50 exécutions les plus récentes, classées par ordre chronologique inverse (la dernière exécution est répertoriée en premier dans la réponse).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez la structure de base, l’étape suivante consiste à passer en revue les exigences et les tâches propres à chaque type de source de données.

+ [Azure SQL Database, SQL Managed Instance ou SQL Server sur une machine virtuelle Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)
+ [Stockage de tables Azure](search-howto-indexing-azure-tables.md)
+ [Indexation d’objets blob CSV avec l’indexeur d’objets blob Recherche cognitive Azure](search-howto-index-csv-blobs.md)
+ [Indexation d’objets blob JSON avec l’indexeur d’objets blob Recherche cognitive Azure](search-howto-index-json-blobs.md)