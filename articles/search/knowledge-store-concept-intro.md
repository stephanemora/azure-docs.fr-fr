---
title: Présentation générale de la Base de connaissances (préversion) – Recherche Azure
description: Envoyez des documents enrichis vers Stockage Azure afin de pouvoir consulter, remodeler et utiliser des documents enrichis dans Recherche Azure et dans d'autres applications.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: overview
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 4a27e4d8f2fbaafe6d27a3e3cabd31aa715b9d80
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540750"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>Qu'est-ce que la base de connaissances de Recherche Azure ?

> [!Note]
> La base de connaissances est en préversion et n’a pas été conçue pour la production. L’[API REST version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’y a pas de prise en charge de .NET SDK pour l’instant.
>

La base de connaissances est une fonctionnalité facultative de Recherche Azure qui enregistre les métadonnées et documents enrichis créés par un pipeline d'indexation basé sur l'IA [(recherche cognitive)](cognitive-search-concept-intro.md). La base de connaissances repose sur un compte de stockage Azure que vous configurez dans le cadre du pipeline. Lorsqu'il est activé, le service de recherche utilise ce compte de stockage pour mettre en cache une représentation de chaque document enrichi. 

Si vous avez déjà eu recours à la recherche cognitive par le passé, vous savez que des ensembles de compétences peuvent être utilisés pour déplacer un document dans le cadre d'une séquence d'enrichissements. Le résultat peut être un index Recherche Azure ou (nouveauté de cette préversion) des projections d'une base de connaissances.

Les projections vous permettent de structurer les données à utiliser dans une application située en aval. Vous pouvez utiliser l'[Explorateur Stockage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) conçu pour Stockage Azure, ou n'importe quelle application capable de se connecter à Stockage Azure, ce qui ouvre de nouvelles possibilités d'utilisation de documents enrichis. Les pipelines de science des données et les analyses personnalisées en sont des exemples.

![Diagramme de base de connaissances au sein d'un pipeline](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Diagramme de base de connaissances au sein d'un pipeline")

Pour utiliser la base de connaissances, ajoutez un élément `knowledgeStore` à un ensemble de compétences qui définit des opérations par étapes dans un pipeline d'indexation. Lors de l'exécution, Recherche Azure crée un espace sur votre compte de stockage Azure et l'alimente avec les définitions et le contenu créés par le pipeline.

## <a name="benefits-of-knowledge-store"></a>Avantages de la base de connaissances

Une base de connaissances vous fournit une structure, un contexte et un contenu réel, glanés à partir de fichiers de données non structurés et semi-structurés, tels que des objets blob, des fichiers image ayant fait l'objet d'une analyse, ou même des données structurées transformées en nouveaux formulaires. Une [procédure pas à pas](knowledge-store-howto.md) conçue pour cette préversion montre comment un document JSON dense est partitionné en sous-structures, reconstitué en nouvelles structures et mis à disposition pour des processus situés en aval, comme des charges de travail liées au Machine Learning et à la science des données.

Bien qu'il soit utile de voir ce qu'un pipeline d'indexation basé sur l'IA peut produire, le vrai pouvoir de la base de connaissances réside dans sa capacité à remodeler les données. Vous pouvez commencer avec un ensemble de compétences de base, puis procéder à une itération sur celui-ci afin d'ajouter des niveaux de structure croissants, que vous pouvez ensuite combiner en nouvelles structures, utilisables dans des applications autres que Recherche Azure.

La base de connaissances vous offre notamment les avantages suivants :

+ Utilisez des documents enrichis dans des [outils d'analyse et de génération d'états](#tools-and-apps) autres que des outils de recherche. Power BI avec Power Query est un excellent choix, mais n'importe quel outil ou application capable de se connecter à Stockage Azure peut puiser dans une base de connaissances que vous avez créée.

+ Affinez un pipeline d'indexation IA lors des étapes de débogage et de la définition des ensembles de compétences. Une base de connaissances vous présente le produit de la définition d'un ensemble de compétences dans un pipeline d'indexation IA. Vous pouvez utiliser ces résultats afin de concevoir un ensemble de compétences plus performant car vous voyez exactement à quoi ressemblent les enrichissements. Vous pouvez utiliser l'[Explorateur Stockage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) de Stockage Azure pour afficher le contenu d'une base de connaissances.

+ Modelez les données pour les transformer en nouveaux formulaires. Le remodelage est codifié en ensembles de compétences, mais le fait est qu'un ensemble de compétences peut désormais fournir cette capacité. La [compétence Modélisateur](cognitive-search-skill-shaper.md) de Recherche Azure a été étendue pour s'adapter à cette tâche. Le remodelage vous permet de définir une projection alignée sur l'utilisation que vous prévoyez de faire des données tout en préservant les relations.

> [!Note]
> L'indexation basée sur l'IA à l'aide de Cognitive Services n'est pas un domaine familier pour vous ? Recherche Azure s'intègre aux fonctionnalités de vision et de langage de Cognitive Services pour extraire et enrichir les données sources à l'aide de la reconnaissance optique de caractères sur des fichiers image, de la reconnaissance d'entités et de l'extraction d'expressions clés à partir de fichiers texte et autres. Pour plus d'informations, consultez [Qu'est-ce que la recherche cognitive ?](cognitive-search-concept-intro.md).

## <a name="create-a-knowledge-store"></a>Créer une base de connaissances

Une base de connaissances fait partie de la définition d'un ensemble de compétences. Dans cette préversion, sa création requiert l'API REST, avec `api-version=2019-05-06-Preview` ou l'Assistant **Importation de données** du portail.

Le document JSON suivant spécifie une `knowledgeStore`, qui fait partie d'un ensemble de compétences appelé par un indexeur (non affiché). La spécification des projections dans la `knowledgeStore` détermine si des tables ou des objets sont créés dans Stockage Azure.

Si vous avez déjà eu recours à l'indexation basée sur l'IA, la définition de l'ensemble de compétences détermine la création, l'organisation et la substance de chaque document enrichi.

```json
{
  "name": "my-new-skillset",
  "description": 
  "Example showing knowledgeStore placement, supported in api-version=2019-05-06-Preview. You need at least one skill, most likely a Shaper skill if you are modulating data structures.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }    
    ]     
    } 
}
```

## <a name="components-backing-a-knowledge-store"></a>Composants soutenant une base de connaissances

Pour créer une base de connaissances, vous avez besoin des services et artefacts suivants.

### <a name="1---source-data"></a>1 - Source de données

Les données ou documents que vous souhaitez enrichir doivent être disponibles dans une source de données Azure prise en charge par les indexeurs Recherche Azure : 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [stockage d’objets blob Azure](search-howto-indexing-azure-blob-storage.md)

[Stockage Table Azure](search-howto-indexing-azure-tables.md) peut être utilisé pour les données sortantes d'une base de connaissances, mais pas en tant que ressource pour les données entrantes vers un pipeline d'indexation basé sur l'IA.

### <a name="2---azure-search-service"></a>2 - Service Recherche Azure

Vous avez également besoin d'un service Recherche Azure et de l'API REST afin de créer et de configurer les objets utilisés pour l'enrichissement des données. L'API REST à utiliser pour créer une base de connaissances est `api-version=2019-05-06-Preview`.

Recherche Azure fournit la fonctionnalité d'indexation, et des indexeurs sont utilisés pour piloter l'ensemble du processus de bout en bout, ce qui permet de conserver et d'enrichir les documents dans Stockage Azure. Les indexeurs utilisent une source de données, un index et un ensemble de compétences, qui sont tous nécessaires à la création et à l'alimentation d'une base de connaissances.

| Object | API REST | Description |
|--------|----------|-------------|
| source de données | [Créer une source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Ressource identifiant une source de données Azure externe qui fournit les données sources utilisées pour créer des documents enrichis.  |
| ensemble de compétences | [Créer un ensemble de compétences (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Ressource coordonnant l'utilisation de [compétences intégrées](cognitive-search-predefined-skills.md) et de [compétences cognitives personnalisées](cognitive-search-custom-skill-interface.md) utilisées dans un pipeline d'enrichissement lors de l'indexation. |
| index | [Création d'index](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Un schéma exprimant un index Recherche Azure. Des champs dans l’index sont mappés à des champs dans les données sources ou à des champs créés lors de la phase d’enrichissement (par exemple, un champ pour les noms de l’organisation créé par la reconnaissance d’entité). |
| indexeur | [Créer un indexeur (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Une ressource définissant les composants utilisés lors de l’indexation : notamment une source de données, un ensemble de compétences, des associations de champs à partir de structures de données sources et intermédiaires pour cibler l’index, et l’index lui-même. L’exécution de l’index est le déclencheur de l’ingestion des données et de l’enrichissement. La sortie est un index de recherche basé sur le schéma d'index, alimenté avec des données sources et enrichi par des ensembles de compétences.  |

### <a name="3---cognitive-services"></a>3 - Cognitive Services

Les enrichissements spécifiés dans un ensemble de compétences reposent sur les fonctionnalités de langage et de vision par ordinateur de Cognitive Services. La fonctionnalité Cognitive Services est utilisée lors de l'indexation, via votre ensemble de compétences. Un ensemble de compétences est une composition de compétences, qui sont liées à des fonctionnalités spécifiques de langage et de vision par ordinateur. Pour intégrer Cognitive Services, vous devez [joindre une ressource Cognitive Services](cognitive-search-attach-cognitive-services.md) à un ensemble de compétences.

### <a name="4---storage-account"></a>4 - Compte de stockage

Sous votre compte de stockage Azure, Recherche Azure crée un conteneur d'objets blob ou des tables, en fonction de la manière dont vous configurez un ensemble de compétences. Si vos données proviennent de Stockage Blob ou Table Azure, vous êtes déjà configuré. Sinon, vous devez créer un compte de stockage Azure. Les tables et les objets de Stockage Azure contiennent les documents enrichis créés par le pipeline d'indexation basé sur l'IA.

Le compte de stockage est spécifié dans l'ensemble de compétences. Dans `api-version=2019-05-06-Preview`, la définition d'un ensemble de compétences comprend une définition de la base de connaissances pour vous permettre de fournir les informations de compte.

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5 - Accès et utilisation

Une fois les enrichissements disponibles dans le stockage, n'importe quel outil ou technologie capable de se connecter à Stockage Blob ou Table Azure peut être utilisé pour explorer, analyser ou utiliser le contenu. La liste suivante est un début :

+ L'[Explorateur Stockage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) permet d'afficher la structure et le contenu des documents enrichis. Considérez-le comme votre outil de référence pour afficher le contenu de la base de connaissances.

+ [Power BI avec Power Query](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e) permet de formuler des requêtes en langage naturel. Vous pouvez aussi utiliser les outils de génération d'états et d'analyse si vous disposez de données numériques.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) permet d'effectuer d'autres manipulations.

+ L'index Recherche Azure permet d'effectuer des recherches en texte intégral dans le contenu que vous avez indexé à l'aide de la [recherche cognitive](cognitive-search-concept-intro.md).

## <a name="document-persistence"></a>Persistance de document

Sur le compte de stockage, les enrichissements peuvent être exprimés sous forme de tables dans Stockage Table Azure ou sous forme d'objets dans Stockage Blob Azure. Rappelons qu'une fois stockés, les enrichissements peuvent être utilisés comme source pour charger des données dans d'autres bases de données et outils.

+ Le stockage Table permet d'obtenir une représentation tabulaire des données, en tenant compte des schémas. Si vous souhaitez remodeler ou recombiner différemment des éléments, le stockage Table vous confère la granularité nécessaire.

+ Le stockage Blob crée une représentation JSON complète de chaque document. Vous pouvez utiliser les deux options de stockage au sein d'un même ensemble de compétences pour bénéficier d'une gamme complète d'expressions.

+ Recherche Azure conserve le contenu d'un index. Si votre scénario n'est pas lié à une recherche, par exemple si votre objectif est d'effectuer une analyse dans un autre outil, vous pouvez supprimer l'index créé par le pipeline. Mais vous pouvez également conserver l'index et utiliser un outil intégré tel que l'[Explorateur de recherche](search-explorer.md) en troisième position (après l'Explorateur Stockage et votre application d'analyse) pour interagir avec votre contenu.

Outre le contenu des documents, les documents enrichis comprennent les métadonnées de la version de l'ensemble de compétences qui a produit les enrichissements.  

## <a name="inside-a-knowledge-store"></a>À l'intérieur d'une base de connaissances

La base de connaissances comprend un cache d'annotations et des projections. Le *cache* est utilisé en interne par le service pour mettre en cache les résultats des compétences et suivre les modifications. Une *projection* définit le schéma et la structure des enrichissements correspondant à l'utilisation prévue. Il n'y a qu'un seul cache par base de connaissances, mais plusieurs projections. 

Le cache est toujours un conteneur d'objets blob, mais les projections peuvent être articulées sous forme de tables ou d'objets :

+ En tant qu'objet, la projection est mappée avec Stockage Blob, où la projection est enregistrée dans un conteneur, dans lequel se trouvent les objets ou les représentations hiérarchiques au format JSON pour les scénarios tels qu'un pipeline de science des données.

+ En tant que table, la projection est mappée avec Stockage Table. Une représentation tabulaire préserve les relations dans les scénarios tels que l'analyse de données ou l'exportation sous forme de dataframes pour le Machine Learning. Les projections enrichies peuvent ensuite être facilement importées dans d'autres magasins de données. 

Vous pouvez créer différentes projections dans une base de connaissances afin de répondre aux besoins de divers groupes de votre organisation. Un développeur peut avoir besoin d'accéder à l'intégralité de la représentation JSON d'un document enrichi, tandis que les scientifiques ou les analystes des données peuvent préférer les structures de données granulaires ou modulaires modelées par votre ensemble de compétences.

Par exemple, si l'un des objectifs du processus d'enrichissement est également de créer un jeu de données pour former un modèle, la projection des données dans le magasin d'objets peut permettre d'utiliser les données de vos pipelines de science des données. Sinon, si vous souhaitez créer un tableau de bord Power BI rapide basé sur les documents enrichis, la projection tabulaire peut être efficace.

<!---
## Data lifecycle and billing

Each time you run the indexer, the cache in Azure storage is updated if the skillset definition or underlying source data has changed. As input documents are edited or deleted, changes are propagated through the annotation cache to the projections, ensuring that your projected data is a current representation of your inputs at the end of the indexer run. 

Generally speaking, pipeline processing can be an all-or-nothing operation, but Azure Search can process incremental changes, which saves you time and money.

If a document is new or updated, all skills are run. If only the skillset changes, reprocessing is scoped to just those skills and documents affected by your edit.

### Changes to a skillset
Suppose that you have a pipeline composed of multiple skills, operating over a large body of static data (for example, scanned documents), that takes 8 hours and costs $200 to create the knowledge store. Now suppose you need to tweak one of the skills in the skillset. Rather than starting over, Azure Search can determine which skill is affected, and reprocess only that skill. Cached data and projections that are unaffected by the change remain intact in the knowledge store.

### Changes in the data
Scenarios can vary considerably, but let's suppose instead of static data, you have volatile data that changes between indexer invocations. Given no changes to the skillset, you are charged for processing the delta of new and modified document. The timestamp information varies by data source, but for illustration, in a Blob container, Azure Search looks at the `lastmodified` date to determine which blobs need to be ingested.

> [!Note]
> While you can edit the data in the projections, any edits will be overwritten on the next pipeline invocation, assuming the document in source data is updated. 

### Deletions

Although Azure Search creates and updates structures and content in Azure storage, it does not delete them. Projections and cached documents continue to exist even when the skillset is deleted. As the owner of the storage account, you should delete a projection if it is no longer needed. 

### Tips for development

+ Start small with a representative sample of your data as you make significant changes to skillset composition. As your design finalizes, you can slowly add more data during later-stage development, and then roll in the entire data set when you are comfortable with the pipeline composition.

+ Retain control over indexer invocation. Indexers can run on a schedule, which is helpful for solutions that are rolled into production, but less helpful if you are actively developing your pipeline. During development, avoid schedules so that you don’t lose track of cache or projection state. Once your solution is in production and skillset composition is static, you can put the indexer on a schedule to pick up routine changes in the external source data. 

-->

## <a name="where-do-i-start"></a>Par où commencer ?

Pour l'apprentissage, nous vous recommandons le service gratuit. Notez toutefois que le nombre de transactions gratuites est limité à 20 documents par jour et par abonnement.

Lorsque vous utilisez plusieurs services, créez tous vos services dans la même région pour optimiser les performances et réduire les coûts. Vous n'êtes pas facturé pour la bande passante des données entrantes ou sortantes qui vont vers un autre service de la même région.

**Étape 1 : [Créer une ressource Recherche Azure](search-create-service-portal.md)** 

**Étape 2 : [Créer un compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Étape 3 : [Créer une ressource Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Étape 4 : [Prise en main du portail](cognitive-search-quickstart-blob.md) - ou - [Bien démarrer avec les exemples de données en utilisant REST et Postman](knowledge-store-howto.md)** 

Vous pouvez utiliser REST `api-version=2019-05-06-Preview` pour créer un pipeline basé sur l'IA qui inclut la base de connaissances. Dans la dernière API de la préversion, l'objet Ensemble de compétences fournit la définition `knowledgeStore`.

## <a name="takeaways"></a>Éléments importants à retenir

La base de connaissances offre toutes sortes d'avantages, notamment la possibilité d'utiliser les documents enrichis dans des scénarios autres que la recherche, le contrôle des coûts et la gestion de la dérive dans le cadre de votre processus d'enrichissement. Ces fonctionnalités peuvent toutes être utilisées en ajoutant un compte de stockage à votre ensemble de compétences et en utilisant le langage d'expression mis à jour, comme décrit dans [Prise en main de la base de connaissances](knowledge-store-howto.md). 

## <a name="next-steps"></a>Étapes suivantes

L'approche la plus simple pour créer des documents enrichis consiste à utiliser l'Assistant **Importation de données**.

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer un pipeline de recherche cognitive à l’aide de compétences et d’exemples de données](cognitive-search-quickstart-blob.md)
