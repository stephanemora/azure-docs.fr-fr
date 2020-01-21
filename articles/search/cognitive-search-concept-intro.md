---
title: Présentation de l’enrichissement par IA
titleSuffix: Azure Cognitive Search
description: Extraction de contenu, traitement en langage naturel et traitement des images pour créer du contenu pouvant faire l’objet de recherches via l’indexation de la Recherche cognitive Azure, à l’aide de compétences cognitives et d’algorithmes d’IA.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 11/04/2019
ms.openlocfilehash: 2ef7f273d6838b1bc051c70539ef7d9da59d7148
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754582"
---
# <a name="introduction-to-ai-in-azure-cognitive-search"></a>Présentation de l’intelligence artificielle dans la Recherche cognitive Azure

L’enrichissement par IA est une fonctionnalité d’indexation de la Recherche cognitive Azure, utilisée pour extraire du texte à partir d’images, d’objets blob et d’autres sources de données non structurées. Le contenu est enrichi, ce qui facilite les recherches dans un index ou une base de connaissances. L'extraction et l'enrichissement sont implémentés par le biais de *compétences cognitives* rattachées à un pipeline d'indexation. Les compétences cognitives intégrées au service se répartissent en fonction des catégories suivantes : 

+ Les compétences de **traitement en langage naturel** incluent la [reconnaissance d'entité](cognitive-search-skill-entity-recognition.md), la [détection de la langue](cognitive-search-skill-language-detection.md), l'[extraction de phrases clés](cognitive-search-skill-keyphrases.md), la manipulation de texte et la [détection de sentiments](cognitive-search-skill-sentiment.md). Grâce à ces compétences, un texte non structuré peut adopter de nouvelles formes, mappées en tant que champs interrogeables et filtrés dans un index.

+ Les compétences de **traitement d'image** incluent la [reconnaissance optique de caractères (OCR)](cognitive-search-skill-ocr.md) et l'identification des [caractéristiques visuelles](cognitive-search-skill-image-analysis.md), comme la détection des visages, l'interprétation des images, la reconnaissance des images (monuments et personnes célèbres) ou des attributs tels que les couleurs ou l'orientation des images. Vous pouvez créer des représentations textuelles du contenu des images, dans lesquelles vous pouvez effectuer des recherches à l’aide de toutes les fonctionnalités de requête de la Recherche cognitive Azure.

![Diagramme de pipeline d’enrichissement](./media/cognitive-search-intro/cogsearch-architecture.png "vue d’ensemble du pipeline d’enrichissement")

Les compétences cognitives de la Recherche cognitive Azure sont basées sur les modèles Machine Learning préentraînés des API Cognitive Services : [Vision par ordinateur](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) et [Analyse de texte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

Le traitement en langage naturel et le traitement des images sont appliqués durant la phase d’ingestion des données. Les résultats sont intégrés à la composition d’un document au sein d’un index pouvant faire l’objet de recherches dans la Recherche cognitive Azure. Les données sont fournies en tant que jeu de données Azure, puis transmises via un pipeline d’indexation à l’aide des [compétences intégrées](cognitive-search-predefined-skills.md) dont vous avez besoin. L’architecture est extensible. Par conséquent, si les compétences intégrées ne sont pas suffisantes, vous pouvez créer et attacher des [compétences personnalisées](cognitive-search-create-custom-skill-example.md) pour intégrer un traitement personnalisé. Par exemple, il peut s’agir d’un module d’entité ou d’un classifieur de documents ciblant un domaine spécifique comme la finance, les publications scientifiques ou la médecine.

> [!NOTE]
> Si vous élargissez le champ en augmentant la fréquence des traitements, en ajoutant des documents supplémentaires ou en ajoutant plusieurs algorithmes d’IA, vous devez [attacher une ressource Cognitive Services facturable](cognitive-search-attach-cognitive-services.md). Des frais s’appliquent durant l’appel des API dans Cognitive Services ainsi que pour l’extraction d’images dans le cadre de la phase de craquage de document de la Recherche cognitive Azure. L’extraction de texte à partir des documents est gratuite.
>
> L'exécution des compétences intégrées est facturée au prix actuel du [paiement à l'utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Les prix appliqués pour l’extraction d’images sont présentés sur la [page de tarification du service Recherche cognitive Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="when-to-use-cognitive-skills"></a>Quand utiliser les compétences cognitives

Vous devez envisager d’utiliser des compétences cognitives intégrées si votre contenu brut est du texte non structuré, du contenu d’image ou du contenu qui nécessite la détection de la langue et la traduction. L’application de l’intelligence artificielle via les compétences cognitives intégrées peut déverrouiller ce contenu, en renforçant sa valeur et son utilité dans vos applications de science des données et de recherche. 

En outre, vous pouvez envisager d’ajouter une compétence personnalisée si vous avez du code open source, tiers ou interne que vous souhaitez intégrer dans le pipeline. Les modèles de classification qui identifient les caractéristiques importantes de différents types de documents appartiennent à cette catégorie, mais tout package qui ajoute de la valeur à votre contenu peut également être utilisé.

### <a name="more-about-built-in-skills"></a>En savoir plus sur les compétences intégrées

Un ensemble de compétences assemblé à l’aide de compétences prédéfinies convient parfaitement aux scénarios d’application suivants :

+ Documents numérisés (JPEG) dans lesquels effectuer une recherche en texte intégral. Vous pouvez associer une compétence de reconnaissance optique de caractères (OCR) pour identifier, extraire et ingérer du texte à partir de fichiers JPEG.

+ PDF avec image et texte combinés. Le texte des fichiers PDF peut être extrait durant l’indexation sans recourir aux étapes d’enrichissement. Toutefois, l’ajout du traitement des images et du traitement en langage naturel permet souvent de produire un résultat supérieur à celui fourni par une indexation standard.

+ Contenu multilingue sur lequel vous souhaitez appliquer la détection de langue et éventuellement la traduction de texte.

+ Documents non structurés ou semi-structurés contenant du contenu qui a une signification ou un contexte intrinsèquement masqué dans le document plus volumineux. 

  En particulier, les blobs contiennent souvent un grand corps de contenu qui est compressé dans un « champ » unique. En associant des compétences en traitement des images et en langage naturel à un indexeur, vous pouvez créer de nouvelles informations qui sont existantes dans le contenu brut, mais qui ne sont pas affichées en tant que champs distincts. Voici quelques compétences cognitives intégrées prêtes à l’emploi qui peuvent aider : l’extraction d’expressions clés, l’analyse des sentiments et la reconnaissance d’entité (personnes, organisations et lieux).

  De plus, les compétences prédéfinies peuvent également être utilisées pour restructurer du contenu via des opérations de découpage, de fusion et de mise en forme du texte.

### <a name="more-about-custom-skills"></a>En savoir plus sur les compétences personnalisées

Les compétences personnalisées peuvent prendre en charge des scénarios plus complexes, tels que la reconnaissance de formulaires ou la détection d’entité personnalisée à l’aide d’un modèle que vous fournissez et encapsulez dans l’[interface web des compétences personnalisées](cognitive-search-custom-skill-interface.md). Plusieurs exemples de compétences personnalisées incluent [Form Recognizer](/azure/cognitive-services/form-recognizer/overview), l’intégration de l’[API Recherche d’entités Bing](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example) et la [reconnaissance d’entité personnalisée](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).


## <a name="components-of-an-enrichment-pipeline"></a>Composants d’un pipeline d’enrichissement

Un pipeline d’enrichissement est basé sur des [*indexeurs*](search-indexer-overview.md) qui analysent les sources de données et fournissent un traitement d’index de bout en bout. Les compétences sont désormais jointes aux indexeurs, ce qui permet d’intercepter et d’enrichir les documents en fonction de l’ensemble de compétences que vous définissez. Une fois l’indexation effectuée, vous pouvez accéder au contenu via des requêtes de recherche ainsi qu’à l’aide de tous les [types de requête pris en charge par la Recherche cognitive Azure](search-query-overview.md).  Si vous ne connaissez pas les indexeurs, cette section vous guide tout au long des étapes.

### <a name="step-1-connection-and-document-cracking-phase"></a>Étape 1 : Phase de connexion et de décodage du document

Au début du pipeline, vous avez du texte non structuré ou du contenu non textuel (comme des images et des fichiers JPEG de documents numérisés). Les données doivent se trouver dans un service de stockage de données Azure accessible par un indexeur. Les indexeurs peuvent décoder les documents sources pour extraire le texte à partir des données sources.

![Phase de craquage de document](./media/cognitive-search-intro/document-cracking-phase-blowup.png "craquage de document")

 Les sources prises en charge comprennent le stockage Blob Azure, le stockage Table Azure, Azure SQL Database et Azure Cosmos DB. Le contenu textuel peut être extrait des types de fichier suivants : PDF, Word, PowerPoint et CSV. Pour obtenir la liste complète, consultez [Formats de document pris en charge](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Étape 2 : Phase d’enrichissement et compétences cognitives

L’enrichissement se fait via les *compétences cognitives* effectuant des opérations atomiques. Par exemple, une fois que vous avez du contenu textuel issu d’un fichier PDF, vous pouvez appliquer la détection du langage de reconnaissance d’entité ou l’extraction de phrases clés pour créer des champs (qui ne sont pas disponibles en natif dans la source) dans votre index. La collection de compétences utilisée dans votre pipeline est appelée *ensemble de compétences*.  

![Phase d’enrichissement](./media/cognitive-search-intro/enrichment-phase-blowup.png "phase d’enrichissement")

Un ensemble de compétences est basé sur des [compétences cognitives intégrées](cognitive-search-predefined-skills.md) ou des [compétences personnalisées](cognitive-search-create-custom-skill-example.md), que vous fournissez et que vous connectez à l’ensemble de compétences. Un ensemble de compétences peut être minimal ou très complexe, et détermine non seulement le type de traitement, mais également l’ordre des opérations. Un ensemble de compétences plus les mappages de champ définis dans un indexeur spécifient entièrement le pipeline d’enrichissement. Pour plus d’informations sur l’extraction de tous ces éléments ensemble, consultez [How to create a skillset in an enrichment pipeline](cognitive-search-defining-skillset.md) (Créer un ensemble de compétences dans un pipeline d’enrichissement).

En interne, le pipeline génère une collection de documents enrichis. Vous pouvez décider quelles parties des documents enrichis doivent être mappées aux champs indexables dans votre index de recherche. Par exemple, si vous avez appliqué les compétences de reconnaissance d’entité et d’extraction de phrases clés, ces nouveaux champs vont faire partie du document enrichi et peuvent donc être mappés aux champs sur votre index. Consultez [How to reference annotations in a cognitive search skillset](cognitive-search-concept-annotations-syntax.md) (Référencer des annotations dans un ensemble de compétences de recherche cognitive) pour en savoir plus sur les formations entrée/sortie.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Ajouter un élément knowledgeStore pour enregistrer des enrichissements

L’[API REST du service Recherche (api-version=2019-05-06-Preview)](search-api-preview.md) étend les ensembles de compétences avec une définition `knowledgeStore` qui fournit une connexion de stockage Azure ainsi que des projections décrivant la manière dont les enrichissements sont stockés. 

L'ajout d'une base de connaissances à un ensemble de compétences vous permet de projeter une représentation de vos enrichissements autres que la recherche en texte intégral. Pour plus d’informations, consultez [Base de connaissances (préversion)](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Étape 3 : Accès basé sur des requêtes et index de recherche

À la fin du traitement, vous disposez d’un index de recherche constitué de documents enrichis, qui peut faire l’objet de recherches textuelles dans la Recherche cognitive Azure. [L’interrogation de l’index](search-query-overview.md) est la façon dont les développeurs et les utilisateurs accèdent au contenu enrichi généré par le pipeline. 

![Index avec icône Rechercher](./media/cognitive-search-intro/search-phase-blowup.png "Index avec icône Rechercher")

L’index est semblable à tous ceux que vous pouvez créer pour la Recherche cognitive Azure : vous pouvez ajouter des analyseurs personnalisés, appeler des requêtes de recherche partielle, ajouter une recherche filtrée ou expérimenter des profils de scoring afin de remodeler les résultats de la recherche.

Les index sont générés à partir d’un schéma d’index qui définit les champs, les attributs et d’autres constructions jointes à un index spécifique, telles que les profils de score et les cartes de synonymes. Une fois qu’un index est défini et rempli, vous pouvez effectuer des indexations incrémentielles pour sélectionner de nouveaux documents sources et des documents sources mis à jour. Certaines modifications requièrent une régénération complète. Vous devez utiliser un petit jeu de données tant que la conception du schéma n’est pas stable. Pour plus d’informations, consultez [How to rebuild an Azure Search index](search-howto-reindex.md) (Régénérer un index Recherche Azure).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Principaux concepts et fonctionnalités

| Concept | Description| Liens |
|---------|------------|-------|
| Ensemble de compétences | Une ressource nommée de niveau supérieur comprenant une collection de compétences. Un ensemble de compétences correspond au pipeline d’enrichissement. Il est appelé lors de l’indexation par un indexeur. | Consulter [Définir un ensemble de compétences](cognitive-search-defining-skillset.md) |
| Compétence cognitive | Une transformation atomique dans un pipeline d’enrichissement. Souvent, il s’agit d’un composant qui effectue des extractions ou déduit une structure, renforçant ainsi notre compréhension des données d’entrée. La sortie est presque toujours textuelle et le traitement est de type traitement en langage naturel ou traitement d’images qui extrait ou génère du texte à partir d’entrées d’images. La sortie d’une compétence peut être mappée à un champ dans un index ou utilisée en tant qu’entrée pour un enrichissement en aval. Une compétence est soit prédéfinie et fournie par Microsoft, soit personnalisée, c’est-à-dire créée et déployée par vous. | [Compétences cognitives prédéfinies](cognitive-search-predefined-skills.md) |
| Extraction de données | Couvre une large gamme de traitements, mais dans le cadre de l’enrichissement par IA, la compétence de reconnaissance d’entités est le plus souvent utilisée pour extraire des données (une entité) d’une source qui ne fournit pas ces informations de manière native. | Consultez [Compétence de reconnaissance d’entités](cognitive-search-skill-entity-recognition.md) et [Compétence d’extraction de documents (préversion)](cognitive-search-skill-document-extraction.md)| 
| Traitement des images | Déduit du texte à partir d’une image, par exemple reconnaître un repère, ou extrait du texte d’une image. Parmi les exemples courants, on trouve l’OCR qui permet de copier des caractères d’un fichier de document numérisé (JPEG) ou de reconnaître un nom de rue dans une photographie sur laquelle se trouve un panneau de rue. | Consulter [Compétence d’analyse d’image](cognitive-search-skill-image-analysis.md) ou [Compétence d’OCR](cognitive-search-skill-ocr.md)
| Traitement en langage naturel | Traitement de texte pour des insights et des informations sur les entrées de texte. La détection de langue, l’analyse des sentiments et l’extraction de phrases clés sont des compétences qui se trouvent dans le traitement en langage naturel.  | Consultez [Compétence d’extraction de phrases clés](cognitive-search-skill-keyphrases.md), [Compétence de détection de langue](cognitive-search-skill-language-detection.md), [Compétence de traduction de texte (préversion)](cognitive-search-skill-text-translation.md) et [Compétence d’analyse des sentiments](cognitive-search-skill-sentiment.md) |
| Décodage du document | Le processus d’extraction ou de création du contenu textuel à partir de sources non textuelles lors de l’indexation. La reconnaissance optique des caractères (OCR) est un exemple, mais il désigne généralement les fonctionnalités principales de l’indexeur, car l’indexeur extrait le contenu des fichiers d’application. La source de données qui fournit l’emplacement du fichier source et la définition d’indexeur qui fournit des mappages de champs, sont les deux facteurs clés du décodage de document. | Consulter [Vue d’ensemble des indexeurs](search-indexer-overview.md) |
| Modélisation | Fusionner des fragments de texte dans une structure plus importante, ou à l’inverse, diviser des blocs de texte plus volumineux afin d’obtenir une taille plus facile à gérer pour davantage de traitements en aval. | Consulter [Compétence de modélisation](cognitive-search-skill-shaper.md), [Compétence de fusion de texte](cognitive-search-skill-textmerger.md), [Compétence de découpage de texte](cognitive-search-skill-textsplit.md) |
| Documents enrichis | Structure interne transitoire, générée lors du traitement, avec reflet de la sortie finale dans un index de recherche. Un ensemble de compétences détermine les enrichissements effectués. Les mappages de champs déterminent quels éléments de données sont ajoutés à l’index. Si vous le souhaitez, vous pouvez créer un magasin de connaissances pour conserver et explorer les documents enrichis en utilisant des outils tels que l’Explorateur Stockage, Power BI ou tout autre outil qui se connecte à Stockage Blob Azure. | Consulter [Base de connaissances (préversion)](knowledge-store-concept-intro.md) |
| Indexeur |  Un analyseur qui extrait les données et métadonnées pouvant faire l’objet d’une recherche d’une source de données externe et renseigne un index en fonction des mappages champ à champ entre l’index et votre source de données pour le décodage du document. Pour les enrichissements par IA, l’indexeur appelle un ensemble de compétences. Il contient les mappages de champs associant la sortie de l’enrichissement aux champs cibles de l’index. La définition de l’indexeur contient toutes les instructions et références pour les opérations du pipeline, et le pipeline est appelé lors de l’exécution de l’indexeur. Avec une configuration supplémentaire, vous pouvez réutiliser le contenu traité existant et exécuter seulement les étapes et compétences qui sont modifiées. | Consultez [Indexeurs](search-indexer-overview.md) et [Enrichissement incrémentiel (préversion)](cognitive-search-incremental-indexing-conceptual.md). |
| source de données  | Un objet utilisé par un indexeur pour se connecter à une source de données externes des types pris en charge sur Azure. | Consulter [Vue d’ensemble des indexeurs](search-indexer-overview.md) |
| Index | Index de recherche persistant de la Recherche cognitive Azure, créé à partir d’un schéma d’index qui définit la structure et l’utilisation des champs. | Consulter [Créer un index de base](search-what-is-an-index.md) | 
| Magasin de connaissances | Compte de stockage dans lequel les documents enrichis peuvent être mis en forme et projetés en plus de l’index de recherche | Consultez [Présentation de la base de connaissances](knowledge-store-concept-intro.md). | 
| Cache | Compte de stockage contenant la sortie mise en cache créée par un pipeline d’enrichissement. L’activation du cache préserve la sortie existante qui n’est pas affectée par les modifications apportées à un ensemble de compétences ou à d’autres composants du pipeline d’enrichissement. | Consultez [Enrichissement incrémentiel](cognitive-search-incremental-indexing-conceptual.md). | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Par où commencer ?

**Étape 1 : [Créer une ressource Recherche cognitive Azure](search-create-service-portal.md)** 

**Étape 2 : Essayez quelques départs rapides et des exemples pour avoir une expérience pratique**

+ [Démarrage rapide (portail)](cognitive-search-quickstart-blob.md)
+ [Didacticiel (requêtes HTTP)](cognitive-search-tutorial-blob.md)
+ [Exemple : Création d’une compétence personnalisée pour l’enrichissement par IA (C#)](cognitive-search-create-custom-skill-example.md)

Pour l’apprentissage, nous vous recommandons le service gratuit. Notez toutefois que le nombre de transactions gratuites est limité à 20 documents par jour. Pour suivre à la fois le guide de démarrage rapide et le tutoriel dans la même journée, utilisez un ensemble de fichiers plus petit (10 documents) afin de pouvoir effectuer les deux exercices. Sinon, supprimez l’indexeur que vous avez utilisé dans le guide de démarrage rapide ou le tutoriel pour remettre le compteur à zéro.

**Étape 3 : Examiner l’API**

Vous pouvez utiliser les `api-version=2019-05-06` REST sur les demandes ou le kit de développement logiciel (SDK) .NET. Si vous découvrez la base de connaissances, utilisez plutôt l’API REST en préversion (`api-version=2019-05-06-Preview`).

Cette étape permet d’utiliser les API REST pour générer une solution d’enrichissement par IA. Seules deux API sont ajoutées ou étendues pour l’enrichissement par IA. D’autres API ont la même syntaxe que les versions généralement disponibles.

| API REST | Description |
|-----|-------------|
| [Créer une source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Une ressource identifiant une source de données externes fournissant des données sources utilisées pour créer des documents enrichis.  |
| [Créer un ensemble de compétences (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Cette API est spécifique à l’enrichissement par IA. Il s’agit d’une ressource qui coordonne l’emploi des [compétences prédéfinies](cognitive-search-predefined-skills.md) et des [compétences cognitives personnalisées](cognitive-search-custom-skill-interface.md) utilisées dans un pipeline d’enrichissement au cours de l’indexation. |
| [Création d'index](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schéma exprimant un index Recherche cognitive Azure. Des champs dans l’index sont mappés à des champs dans les données sources ou à des champs créés lors de la phase d’enrichissement (par exemple, un champ pour les noms de l’organisation créé par la reconnaissance d’entité). |
| [Créer un indexeur (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Une ressource définissant les composants utilisés lors de l’indexation : notamment une source de données, un ensemble de compétences, des associations de champs à partir de structures de données sources et intermédiaires pour cibler l’index, et l’index lui-même. L’exécution de l’index est le déclencheur de l’ingestion des données et de l’enrichissement. La sortie est un index de recherche basé sur le schéma d'index, alimenté avec des données sources et enrichi par des ensembles de compétences. Cette API existante est étendue pour les scénarios de recherche cognitive avec l’inclusion d’une propriété d’ensemble de compétences. |

**Liste de vérification : workflow classique**

1. Créez un sous-ensemble de vos données sources dans un échantillon représentatif. Étant donné que l’indexation prend un certain temps, commencez par un petit ensemble de données représentatif, puis augmentez sa taille de façon incrémentielle à mesure que votre solution grandit.

1. Créez un [objet de source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source) dans la Recherche cognitive Azure afin de fournir une chaîne de connexion pour l’extraction de données.

1. Créez un [ensemble de compétences](https://docs.microsoft.com/rest/api/searchservice/create-skillset) avec les étapes d’enrichissement.

1. Définissez le [schéma d’index](https://docs.microsoft.com/rest/api/searchservice/create-index). La collection *Champs* inclut des champs issus des données sources. Vous devez également écraser les champs supplémentaires pour stocker des valeurs générées pour le contenu créé au cours de l’enrichissement.

1. Définissez [l’indexeur](https://docs.microsoft.com/rest/api/searchservice/create-skillset) faisant référence à la source de données, à l’ensemble de compétences et à l’index.

1. Dans l’indexeur, ajoutez *outputFieldMappings*. Cette section mappe la sortie de l’ensemble de compétences (à l’étape 3) aux champs d’entrées dans le schéma d’index (à l’étape 4).

1. Envoyez la requête *Créer un indexeur* que vous venez de créer (requête POST avec une définition d’indexeur dans le corps de la requête) pour exprimer l’indexeur dans la Recherche cognitive Azure. Cette étape correspond à la façon dont vous exécutez l’indexeur, en appelant le pipeline.

1. Exécutez des requêtes pour évaluer les résultats et modifiez le code pour mettre à jour les ensembles de compétences, le schéma ou la configuration de l’indexeur.

1. [Réinitialisez l’indexeur](search-howto-reindex.md) avant de régénérer le pipeline.

Pour plus d’informations sur des problèmes ou des questions spécifiques, consultez [Troubleshooting tips for cognitive search](cognitive-search-concept-troubleshooting.md) (Conseils de dépannage pour la recherche cognitive).

## <a name="next-steps"></a>Étapes suivantes

+ [Liens vers la documentation sur l’enrichissement par IA](cognitive-search-resources-documentation.md)
+ [Démarrage rapide : Procédure pas à pas d’enrichissement par IA à partir du portail](cognitive-search-quickstart-blob.md)
+ [Tutoriel : En savoir plus sur les API d’enrichissement par IA](cognitive-search-tutorial-blob.md)
+ [Base de connaissances (préversion)](knowledge-store-concept-intro.md)
+ [Créer une base de connaissances avec REST](knowledge-store-create-rest.md)
