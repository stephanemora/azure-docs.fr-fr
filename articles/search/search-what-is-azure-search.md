---
title: Présentation de la Recherche cognitive Azure
titleSuffix: Azure Cognitive Search
description: La Recherche cognitive Azure est un service de recherche cloud complètement managé de Microsoft. Découvrez les cas d’usage, le workflow de développement, les comparaisons avec d’autres produits de recherche Microsoft et le guide de démarrage rapide.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/09/2021
ms.custom: contperf-fy21q1
ms.openlocfilehash: e17d08d09814c135af3e0b4fc299b6e6f42326d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549887"
---
# <a name="what-is-azure-cognitive-search"></a>Qu’est-ce que la Recherche cognitive Azure ?

La Recherche cognitive Azure ([anciennement la « Recherche Azure »](whats-new.md#new-service-name)) est un service de recherche cloud qui offre aux développeurs des API et des outils permettant d’élaborer une expérience de recherche riche, sur du contenu privé et hétérogène, dans les applications web, mobiles et d’entreprise. 

La recherche est fondamentale pour toute application exposant du contenu aux utilisateurs, avec des scénarios courants comme la recherche dans un catalogue ou dans des documents, la recherche sur un site de e-commerce ou l’exploration de connaissances pour la science des données. Les API et l’architecture de Recherche cognitive simplifient l’ajout d’une récupération d’informations sophistiquée à n’importe quelle solution.

Un service de recherche comprend les composants suivants :

+ Moteur de recherche pour recherche en texte intégral
+ Stockage persistant du contenu indexé appartenant à l’utilisateur
+ API pour l’indexation et l’interrogation de contenu
+ [Enrichissements basés sur l’IA](cognitive-search-concept-intro.md) facultatifs, créant du contenu avec possibilité de recherche à partir d’images, de texte brut non structuré et de fichiers d’application
+ Intégration facultative à d’autres services Azure pour les données, le machine learning et l’IA, la supervision et la sécurité
+ Implémentation facultative de la [recherche sémantique ](semantic-search-overview.md) (préversion) pour une meilleure pertinence

D’un point de vue architectural, un service de recherche se trouve entre les magasins de données externes qui contiennent vos données non indexées et votre application cliente qui envoie des demandes de requête à un index de recherche et gère la réponse.

![Architecture de la Recherche cognitive Azure](media/search-what-is-azure-search/azure-search-diagram.svg "Architecture de Recherche cognitive Azure")

En externe, la recherche peut s’intégrer à d’autres services Azure. Elle le fait sous la forme d’*indexeurs* qui automatisent l’ingestion et la récupération des données à partir des sources de données Azure, et sous la forme d’*ensembles de compétences* qui incorporent l’intelligence artificielle consommable à partir de Cognitive Services (par exemple l’analyse des images et du texte), ou l’intelligence artificielle personnalisée que vous créez dans Azure Machine Learning ou wrappez dans Azure Functions.

## <a name="inside-a-search-service"></a>À l’intérieur d’un service de recherche

Sur le service de recherche proprement dit, les deux charges de travail principales sont l’*indexation* et l’*interrogation*. 

+ L’[indexation](search-what-is-an-index.md) est un processus d’entrée qui charge du contenu dans votre service de recherche et le rend consultable. En interne, le texte entrant est traité sous forme de jetons et stocké dans des index inversés qui permettent des recherches rapides. Vous pouvez charger tout texte qui se présente sous la forme de documents JSON.

  En outre, si votre contenu comprend des fichiers mixtes, vous avez la possibilité d’ajouter l’*enrichissement par IA* par le biais de [compétences cognitives](cognitive-search-working-with-skillsets.md). L’enrichissement par IA peut extraire du texte incorporé dans des fichiers d’application, et également déduire le texte et la structure des fichiers non-texte en analysant le contenu. 

  Les compétences fournissant l’analyse sont des compétences prédéfinies par Microsoft ou des compétences personnalisées que vous créez. L’analyse et les transformations ultérieures peuvent déboucher sur de nouvelles informations et structures qui n’existaient pas avant, fournissant ainsi un utilitaire de valeur supérieure pour de nombreux scénarios de recherche et d’exploration de connaissances.

+ L’[interrogation](search-query-overview.md) peut avoir lieu une fois qu’un index est rempli avec du texte pouvant faire l’objet d’une recherche, quand votre application cliente envoie des demandes de requête à un service de recherche et gère les réponses. Toutes les exécutions de requête s’effectuent sur un index de recherche que vous créez, détenez et stockez dans votre service. Dans votre application cliente, l’expérience de recherche est définie au moyen d’API provenant de la Recherche cognitive Azure ; elle peut inclure le paramétrage de la pertinence, la saisie semi-automatique, la correspondance des synonymes, la correspondance approximative, les critères spéciaux, le filtrage et le tri.

Cette fonctionnalité est exposée par le biais d’une [API REST](/rest/api/searchservice/) ou d’un [SDK.NET](search-howto-dotnet-sdk.md) simple, qui masque la complexité inhérente de la récupération d’informations. Vous pouvez également utiliser le portail Azure pour l’administration de service et la gestion de contenu, avec des outils de prototypage et d’interrogation de vos index et ensembles de compétences. Étant donné que le service s’exécute dans le cloud, infrastructure et la disponibilité sont gérées par Microsoft.

## <a name="why-use-cognitive-search"></a>Pourquoi utiliser la Recherche cognitive ?

La Recherche cognitive Azure est adaptée aux scénarios d’application suivants :

+ Regrouper du contenu hétérogène dans un index de recherche privé, défini par l’utilisateur.

+ Implémentez facilement les fonctionnalités de recherche : le réglage de la pertinence, la navigation par facettes, les filtres (y compris la recherche géospatiale), le mappage de synonymes et la saisie semi-automatique.

+ Transformez en documents JSON recherchables de volumineux fichiers image ou texte indifférenciés ou des fichiers d’application stockés dans le stockage Blob Azure ou Cosmos DB. Cette opération est effectuée au cours de l’indexation par le biais de [compétences cognitives](cognitive-search-concept-intro.md) qui ajoutent un traitement externe.

+ Ajoutez une analyse de texte linguistique ou personnalisée. Si vous disposez d’un contenu autre que l’anglais, la Recherche cognitive Azure prend en charge les analyseurs Lucene et les processeurs de langage naturel de Microsoft. Vous pouvez également configurer des analyseurs pour obtenir un traitement spécialisé de contenu brut, tel que le filtrage des signes diacritiques, ou la reconnaissance et la conservation de modèles dans les chaînes.

Pour plus d’informations sur les fonctionnalités spécifiques, consultez [Fonctionnalités de la Recherche cognitive Azure](search-features-list.md)

## <a name="how-to-get-started"></a>Pour commencer

Une exploration de bout en bout des fonctionnalités de recherche de base peut être effectuée en quatre étapes :

1. [**Créez un service de recherche**](search-create-service-portal.md) avec le niveau Gratuit partagé ou un [niveau facturable](https://azure.microsoft.com/pricing/details/search/) pour les ressources dédiées qui sont utilisées uniquement par votre service. Tous les guides de démarrage rapide et les tutoriels peuvent être suivis dans un service gratuit.

1. [**Créez un index de recherche**](search-what-is-an-index.md) à l’aide du portail, de l’[API REST](/rest/api/searchservice/create-index), du [SDK .NET](search-howto-dotnet-sdk.md) ou d’un autre SDK. Le schéma d’index définit la structure du contenu pouvant être recherché.

1. [**Chargez du contenu**](search-what-is-data-import.md) à l’aide du [modèle « Push »](tutorial-optimize-indexing-push-api.md) pour envoyer (push) des documents JSON à partir de n’importe quelle source, ou utilisez le [modèle « Pull » (indexeurs)](search-indexer-overview.md) si vos données sources se trouvent dans Azure.

1. [**Interrogez un index**](search-query-overview.md) avec l’[Explorateur de recherche](search-explorer.md) dans le portail, l’[API REST](search-get-started-rest.md), le [SDK .NET](/dotnet/api/azure.search.documents.searchclient.search) ou un autre SDK.

Pour une première exploration, commencez avec l’[**Assistant Importation de données**](search-get-started-portal.md) et une source de données Azure intégrée pour créer, charger et interroger un index en quelques minutes.

Pour obtenir de l’aide sur les solutions complexes ou personnalisées, [**contactez un partenaire**](resource-partners-knowledge-mining.md) disposant d’une expertise approfondie de la technologie Recherche cognitive.

## <a name="compare-search-options"></a>Comparer les options de recherche

Les clients souhaitent souvent comparer les performances de la Recherche cognitive Azure par rapport à d’autres solutions de recherche. Le tableau suivant récapitule ces différences clés.

| Par rapport à | Différences clés |
|-------------|-----------------|
| Recherche Microsoft | [Recherche Microsoft](/microsoftsearch/overview-microsoft-search) est destinée aux utilisateurs authentifiés Microsoft 365 qui doivent interroger du contenu dans SharePoint. Elle est proposée en tant qu’expérience de recherche prête à l’emploi, activée et configurée par des administrateurs, avec la possibilité d’accepter du contenu externe via des connecteurs de Microsoft et d’autres sources. Si cela décrit votre scénario, Recherche Microsoft avec Microsoft 365 est une option intéressante à explorer.<br/><br/>Par contre, Recherche cognitive Azure exécute des requêtes sur un index que vous définissez, rempli avec des données et des documents que vous possédez, provenant souvent de différentes sources. Recherche cognitive Azure dispose de capacités de collecte pour certaines sources de données via des [indexeurs](search-indexer-overview.md), mais vous pouvez envoyer n’importe quel document JSON conforme à votre schéma d’index dans une ressource unique consolidée avec possibilité de recherche. Vous pouvez également personnaliser le pipeline d’indexation pour inclure du machine learning et des analyseurs lexicaux. Comme Recherche cognitive est conçu pour être un composant de plug-in dans des solutions plus grandes, vous pouvez intégrer la recherche à pratiquement n’importe quelle application, sur n’importe quelle plateforme.|
|Bing | [API Recherche Web Bing](../cognitive-services/bing-web-search/index.yml) recherche dans les index de Bing.com les termes correspondant à votre requête. Les index sont créés à partir de contenus HTML, XML et tout autre contenu web disponible sur les sites publics. Conçu autour des mêmes principes, la [Recherche personnalisée Bing](/azure/cognitive-services/bing-custom-search/) offre la même technologie robot pour les types de contenu web, élargie aux sites web individuels.<br/><br/>Dans Recherche cognitive, vous pouvez définir et remplir l’index. Vous pouvez utiliser des [indexeurs](search-indexer-overview.md) pour collecter des données sur des sources de données Azure ou envoyer un document JSON conforme aux index à votre service de recherche. |
|Recherche de base de données | De nombreuses plateformes de base de données incluent une expérience de recherche intégrée. SQL Server dispose d’une [recherche de texte intégral](/sql/relational-databases/search/full-text-search). Cosmos DB et autres technologies similaires disposent d’index requêtables. Lorsque vous évaluez des produits qui combinent recherche et stockage, il peut être difficile de déterminer quelle fonction utiliser. De nombreuses solutions utilisent les deux : SGBD pour le stockage et la recherche cognitive Azure pour des fonctionnalités de recherche spécialisées.<br/><br/>Par rapport à la recherche SGBD, la Recherche cognitive Azure stocke du contenu de sources variées et offre des fonctionnalités de traitement de texte spécialisées telles que du traitement de texte orienté linguistique (recherche de radical, lemmatisation, formes de mots) dans [56 langues](/rest/api/searchservice/language-support). Elle prend également en charge la correction automatique de mots, [synonymes](/rest/api/searchservice/synonym-map-operations), [suggestions](/rest/api/searchservice/suggestions), [contrôles de scoring](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facettes](./search-filters-facets.md) et [segmentation du texte en unités lexicales personnalisée](/rest/api/searchservice/custom-analyzers-in-azure-search). Le [moteur de recherche en texte intégral](search-lucene-query-architecture.md) dans la Recherche cognitive Azure est basé sur Apache Lucene, une norme du secteur en matière de récupération d’informations. Toutefois, bien que la Recherche cognitive Azure conserve les données sous la forme d’index inversé, il ne s’agit pas d’un remplacement de véritable stockage de données et nous vous déconseillons de l’utiliser dans cette capacité. Pour en savoir plus, consultez ce [sujet du forum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>L’utilisation des ressources est un autre point d’inflexion dans cette catégorie. L’indexation et quelques opérations de requête sont souvent intenses en termes de calcul. La recherche de déchargement de DBMS vers une solution dédiée dans le cloud conserve les ressources système pour le traitement transactionnel. De plus, en externalisant la recherche, vous pouvez facilement ajuster l’échelle afin de correspondre au volume de la requête.|
|Solution de recherche dédiée | En partant du postulat que vous avez choisi la recherche dédiée avec une fonctionnalité complète, une dernière comparaison catégorielle se fera entre des solutions locales et un service cloud. De nombreuses technologies de recherche offrent un contrôle sur l’indexation et les pipelines de requêtes, l’accès à une syntaxe de filtrage et de requête plus riche, un contrôle sur le classement et la pertinence, et des fonctionnalités de recherche intelligente et autonome. <br/><br/>Choisissez un service cloud si vous cherchez une solution clé en main ajustable avec une surcharge et une maintenance minimes. <br/><br/>Dans le paradigme du cloud, plusieurs fournisseurs proposent des fonctionnalités de base comparables, notamment une recherche en texte intégral, une recherche en fonction de la localisation et une capacité à gérer un certain niveau d’ambiguïté dans les entrées de recherche. En général, c’est une [fonctionnalité spécialisée](search-features-list.md) ou l’ergonomie et la simplicité globales des API, des outils et de la gestion qui déterminent la meilleure option. |

Parmi les fournisseurs de services cloud, la Recherche cognitive Azure s’avère plus efficace pour ce qui est des charges de travail de recherche en texte intégral sur les bases de données et les magasins de contenu sur Azure, dans le cas des applications qui s’appuient principalement sur la recherche pour récupérer les informations et parcourir le contenu. 

Voici les principaux atouts :

+ Intégration de données Azure (robots) au niveau de la couche d’indexation
+ Intégration d’Azure Private Link pour prendre en charge les exigences de sécurité en dehors d’Internet
+ Intégration au traitement de l’IA pour rendre possible la recherche de texte dans des types de contenu ne pouvant pas être recherché.
+ Analyse linguistique et personnalisée, incluant des analyseurs de recherche en texte intégral solide en 56 langues
+ [Fonctionnalités critiques](search-features-list.md) : langage de requête riche, paramétrage de la pertinence, recherche par facettes, autocomplétion, synonymes, recherche basée sur la localisation et composition des résultats.
+ Mise à l’échelle Azure, fiabilité et disponibilité de premier ordre

Parmi nos clients, ceux capables d’exploiter le plus large éventail de fonctionnalités de la Recherche cognitive Azure sont les catalogues en ligne, les programmes métier et les applications de découverte de documents.

## <a name="watch-this-video"></a>Regardez cette vidéo

Dans cette vidéo de 15 minutes, le Chef de programme Luis Cabrera présente Recherche cognitive Azure.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]