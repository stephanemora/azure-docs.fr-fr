---
title: Présentation de la Recherche cognitive Azure
titleSuffix: Azure Cognitive Search
description: La Recherche cognitive Azure est un service de recherche cloud complètement managé de Microsoft. Découvrez les cas d’usage, le workflow de développement, les comparaisons avec d’autres produits de recherche Microsoft et le guide de démarrage rapide.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 10/22/2020
ms.custom: contperfq1
ms.openlocfilehash: f9a5197b982958fe0a0ff21c4b442142beb38882
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422465"
---
# <a name="what-is-azure-cognitive-search"></a>Qu’est-ce que la Recherche cognitive Azure ?

La Recherche cognitive Azure ([anciennement la « Recherche Azure »](whats-new.md#new-service-name)) est un service de recherche cloud qui offre aux développeurs des API et des outils permettant d’élaborer une expérience de recherche riche, sur du contenu privé et hétérogène, dans les applications web, mobiles et d’entreprise.

Lorsque vous créez un service Recherche cognitive, vous bénéficiez d’un moteur de recherche effectuant l’indexation et l’exécution des requêtes, d’un stockage persistant des index que vous créez et gérez, ainsi que d’un langage de requête permettant de composer des requêtes simples ou complexes. Le cas échéant, un service de recherche s’intègre à d’autres services Azure. Il le fait sous la forme d’ *indexeurs* qui automatisent l’ingestion/la récupération des données à partir des sources de données Azure, et sous la forme d’ *ensembles de compétences* qui incorporent l’intelligence artificielle consommable à partir de Cognitive Services (par exemple, l’analyse des images et du texte) ou l’intelligence artificielle personnalisée que vous créez dans Azure Machine Learning ou wrappez dans Azure Functions.

![Architecture de la Recherche cognitive Azure](media/search-what-is-azure-search/azure-search-diagram.svg "Architecture de Recherche cognitive Azure")

D’un point de vue architectural, un service de recherche se trouve entre les magasins de données externes qui contiennent vos données non indexées, et une application cliente qui envoie des demandes de requête à un index de recherche et gère la réponse.  Un schéma d’index détermine la structure d’un contenu recherchable. 

Les deux charges de travail principales d’un service de recherche sont l’ *indexation* et l’ *interrogation*.

+ L’indexation apporte du texte à votre service de recherche et le rend recherchable. En interne, le texte entrant est traité sous forme de jetons et stocké dans des index inversés qui permettent des recherches rapides. Pendant l’indexation, vous avez la possibilité d’ajouter des *compétences cognitives* prédéfinies par Microsoft ou des compétences personnalisées que vous créez. L’analyse et les transformations ultérieures peuvent déboucher sur de nouvelles informations et structures qui n’existaient pas avant, fournissant ainsi un utilitaire de valeur supérieure pour de nombreux scénarios de recherche et d’exploration de connaissances.

+ Lorsqu’un index est rempli avec des données recherchables, votre application cliente envoie des demandes de requête à un service de recherche et gère les réponses. Toutes les exécutions de requête s’effectuent sur un index de recherche que vous créez, détenez et stockez dans votre service. Dans votre application cliente, l’expérience de recherche est définie au moyen d’API provenant de la Recherche cognitive Azure ; elle peut inclure le paramétrage de la pertinence, la saisie semi-automatique, la correspondance des synonymes, la correspondance approximative, les critères spéciaux, le filtrage et le tri.

Cette fonctionnalité est exposée par le biais d’une [API REST](/rest/api/searchservice/) ou d’un [SDK.NET](search-howto-dotnet-sdk.md) simple, qui masque la complexité inhérente de la récupération d’informations. Vous pouvez également utiliser le portail Azure pour l’administration de service et la gestion de contenu, avec des outils de prototypage et d’interrogation de vos index et ensembles de compétences. Étant donné que le service s’exécute dans le cloud, infrastructure et la disponibilité sont gérées par Microsoft.

## <a name="when-to-use-cognitive-search"></a>Quand utiliser la Recherche cognitive

La Recherche cognitive Azure est adaptée aux scénarios d’application suivants :

+ Regroupement de types de contenu hétérogènes dans un index de recherche privé, défini par l’utilisateur. Vous pouvez remplir un index de recherche avec des flux de documents JSON à partir de n’importe quelle source. Pour les sources prises en charge sur Azure, utilisez un *indexeur* qui automatise l’indexation. Le contrôle du schéma d’index et de la planification de l’actualisation constitue une raison essentielle pour laquelle utiliser la Recherche cognitive.

+ Implémentation facile des fonctionnalités de recherche. Les API Recherche simplifient la construction des requêtes, la navigation par facettes, les filtres (y compris la recherche géospatiale), le mappage des synonymes, la saisie semi-automatique et le paramétrage de la pertinence. À l’aide des fonctionnalités intégrées, vous pouvez répondre aux attentes des utilisateurs finaux en matière de recherche, de la même façon que les moteurs de recherche Web commerciaux.

+ Le contenu brut est constitué de volumineux fichiers image ou texte indifférencié, ou de fichiers d’application stockés dans le stockage Blob Azure ou Cosmos DB. Vous pouvez appliquer des [compétences cognitives](cognitive-search-concept-intro.md) pendant l’indexation pour identifier et extraire du texte, créer une structure ou créer des informations, telles que des entités ou du texte traduit.

+ Le contenu nécessite une analyse de texte linguistique ou personnalisée. Si vous disposez d’un contenu autre que l’anglais, la Recherche cognitive Azure prend en charge les analyseurs Lucene et les processeurs de langage naturel de Microsoft. Vous pouvez également configurer des analyseurs pour obtenir un traitement spécialisé de contenu brut, tel que le filtrage des signes diacritiques, ou la reconnaissance et la conservation de modèles dans les chaînes.

Pour plus d’informations sur les fonctionnalités spécifiques, consultez [Fonctionnalités de la Recherche cognitive Azure](search-features-list.md)

## <a name="how-to-use-cognitive-search"></a>Guide pratique pour utiliser la Recherche cognitive

### <a name="step-1-provision-service"></a>Étape 1 : Configuration du service

Vous pouvez [créer un service gratuit](search-create-service-portal.md) partagé avec d’autres abonnés ou un [niveau payant](https://azure.microsoft.com/pricing/details/search/) qui dédie les ressources que seul votre service utilise. Tous les démarrages rapides et les didacticiels peuvent être effectués sur le service gratuit.

Concernant les niveaux payants, vous pouvez mettre à l’échelle un service en deux dimensions pour étalonner l’allocation de ressources en fonction des besoins de production :

+ Ajoutez des réplicas pour accroître votre capacité à traiter de lourdes charges de requêtes
+ Ajoutez des partitions pour accroître votre capacité à stocker plus de documents

### <a name="step-2-create-an-index"></a>Étape 2 : Création d'un index

Définissez un schéma d’index à mapper, pour refléter la structure des documents dans lesquels vous voulez effectuer des recherches, de la même façon que les champs d’une base de données. Un index de recherche est une structure de données spécialisée, qui est optimisée pour l’exécution rapide des requêtes.

Il est courant de [créer le schéma d’index dans le portail Azure](search-what-is-an-index.md), ou par programme à l’aide du [kit SDK .NET](search-howto-dotnet-sdk.md) ou d’une [API REST](/rest/api/searchservice/).

> [!TIP]
> Commencez par le guide de [Démarrage rapide : Assistant Importation de données](search-get-started-portal.md) pour créer, charger et interroger un index en quelques minutes.

### <a name="step-3-load-data"></a>Étape 3 : Charger les données

Une fois que vous avez défini un index, vous êtes prêt à charger du contenu. Vous pouvez utiliser un modèle push ou pull.

Le modèle d’envoi (push) « pousse » les documents JSON dans un index au moyen d’API à partir d’un [kit SDK](search-howto-dotnet-sdk.md) ou de [REST](/rest/api/searchservice/addupdate-or-delete-documents). Le jeu de données externe peut être pratiquement n’importe quelle source de données, à condition que les documents soient au format JSON.

Le modèle de tirage (pull) « tire » les données à partir de sources sur Azure, et les envoie à un index de recherche. Le modèle de tirage est implémenté par l’intermédiaire d’ [*indexeurs*](/rest/api/searchservice/Indexer-operations) qui simplifient et automatisent certains aspects de l’ingestion de données, comme la connexion aux données, leur lecture et leur sérialisation. Les sources de données prises en charge comprennent Azure Cosmos DB, Azure SQL et Stockage Azure.

### <a name="step-4-send-queries-and-handle-responses"></a>Étape 4 : Envoyer des requêtes et gérer les réponses

Après avoir rempli une index, vous pouvez [émettre des requêtes de recherche](search-query-overview.md) à destination du point de terminaison du service en utilisant des requêtes HTTP simples avec l’[API REST](/rest/api/searchservice/Search-Documents) ou le [SDK .NET](/dotnet/api/azure.search.documents.searchclient.search).

Parcourez la page [Créer votre première application de recherche](tutorial-csharp-create-first-app.md) pour générer et étendre une page web qui collecte les entrées d’utilisateur et gère les résultats. Vous pouvez également utiliser [Postman pour les appels REST interactifs](search-get-started-postman.md) ou l’[Explorateur de recherche](search-explorer.md) intégré dans le portail Azure pour interroger un index existant.

## <a name="how-it-compares"></a>Comparaison

Les clients souhaitent souvent comparer les performances de la Recherche cognitive Azure par rapport à d’autres solutions de recherche. Le tableau suivant récapitule ces différences clés.

| Par rapport à | Différences clés |
|-------------|-----------------|
| Recherche Microsoft | [Recherche Microsoft](https://docs.microsoft.com/microsoftsearch/overview-microsoft-search) est destinée aux utilisateurs authentifiés Microsoft 365 qui doivent interroger du contenu dans SharePoint. Elle est proposée en tant qu’expérience de recherche prête à l’emploi, activée et configurée par des administrateurs, avec la possibilité d’accepter du contenu externe via des connecteurs de Microsoft et d’autres sources. Si cela décrit votre scénario, Recherche Microsoft avec Microsoft 365 est une option intéressante à explorer.<br/><br/>Par contre, Recherche cognitive Azure exécute des requêtes sur un index que vous définissez, rempli avec des données et des documents que vous possédez, provenant souvent de différentes sources. Recherche cognitive Azure dispose de capacités de collecte pour certaines sources de données via des [indexeurs](search-indexer-overview.md), mais vous pouvez envoyer n’importe quel document JSON conforme à votre schéma d’index dans une ressource unique consolidée avec possibilité de recherche. Vous pouvez également personnaliser le pipeline d’indexation pour inclure du machine learning et des analyseurs lexicaux. Comme Recherche cognitive est conçu pour être un composant de plug-in dans des solutions plus grandes, vous pouvez intégrer la recherche à pratiquement n’importe quelle application, sur n’importe quelle plateforme.|
|Bing | [API Recherche Web Bing](../cognitive-services/bing-web-search/index.yml) recherche dans les index de Bing.com les termes correspondant à votre requête. Les index sont créés à partir de contenus HTML, XML et tout autre contenu web disponible sur les sites publics. Conçu autour des mêmes principes, la [Recherche personnalisée Bing](/azure/cognitive-services/bing-custom-search/) offre la même technologie robot pour les types de contenu web, élargie aux sites web individuels.<br/><br/>Dans Recherche cognitive, vous pouvez définir et remplir l’index. Vous pouvez utiliser des [indexeurs](search-indexer-overview.md) pour collecter des données sur des sources de données Azure ou envoyer un document JSON conforme aux index à votre service de recherche. |
|Recherche de base de données | De nombreuses plateformes de base de données incluent une expérience de recherche intégrée. SQL Server dispose d’une [recherche de texte intégral](/sql/relational-databases/search/full-text-search). Cosmos DB et autres technologies similaires disposent d’index requêtables. Lorsque vous évaluez des produits qui combinent recherche et stockage, il peut être difficile de déterminer quelle fonction utiliser. De nombreuses solutions utilisent les deux : SGBD pour le stockage et la recherche cognitive Azure pour des fonctionnalités de recherche spécialisées.<br/><br/>Par rapport à la recherche SGBD, la Recherche cognitive Azure stocke du contenu de sources variées et offre des fonctionnalités de traitement de texte spécialisées telles que du traitement de texte orienté linguistique (recherche de radical, lemmatisation, formes de mots) dans [56 langues](/rest/api/searchservice/language-support). Elle prend également en charge la correction automatique de mots, [synonymes](/rest/api/searchservice/synonym-map-operations), [suggestions](/rest/api/searchservice/suggestions), [contrôles de scoring](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facettes](./search-filters-facets.md) et [segmentation du texte en unités lexicales personnalisée](/rest/api/searchservice/custom-analyzers-in-azure-search). Le [moteur de recherche en texte intégral](search-lucene-query-architecture.md) dans la Recherche cognitive Azure est basé sur Apache Lucene, une norme du secteur en matière de récupération d’informations. Toutefois, bien que la Recherche cognitive Azure conserve les données sous la forme d’index inversé, il ne s’agit pas d’un remplacement de véritable stockage de données et nous vous déconseillons de l’utiliser dans cette capacité. Pour en savoir plus, consultez ce [sujet du forum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>L’utilisation des ressources est un autre point d’inflexion dans cette catégorie. L’indexation et quelques opérations de requête sont souvent intenses en termes de calcul. La recherche de déchargement de DBMS vers une solution dédiée dans le cloud conserve les ressources système pour le traitement transactionnel. De plus, en externalisant la recherche, vous pouvez facilement ajuster l’échelle afin de correspondre au volume de la requête.|
|Solution de recherche dédiée | En partant du postulat que vous avez choisi la recherche dédiée avec une fonctionnalité complète, une dernière comparaison catégorielle se fera entre des solutions locales et un service cloud. De nombreuses technologies de recherche offrent un contrôle sur l’indexation et les pipelines de requêtes, l’accès à une syntaxe de filtrage et de requête plus riche, un contrôle sur le classement et la pertinence, et des fonctionnalités de recherche intelligente et autonome. <br/><br/>Choisissez un service cloud si vous cherchez une solution clé en main ajustable avec une surcharge et une maintenance minimes. <br/><br/>Dans le paradigme du cloud, plusieurs fournisseurs proposent des fonctionnalités de base comparables, notamment une recherche en texte intégral, une recherche en fonction de la localisation et une capacité à gérer un certain niveau d’ambiguïté dans les entrées de recherche. En général, c’est une [fonctionnalité spécialisée](search-features-list.md) ou l’ergonomie et la simplicité globales des API, des outils et de la gestion qui déterminent la meilleure option. |

Parmi les fournisseurs de services cloud, la Recherche cognitive Azure s’avère plus efficace pour ce qui est des charges de travail de recherche en texte intégral sur les bases de données et les magasins de contenu sur Azure, dans le cas des applications qui s’appuient principalement sur la recherche pour récupérer les informations et parcourir le contenu. 

Voici les principaux atouts :

+ Intégration de données Azure (robots) au niveau de la couche d’indexation
+ Portail Azure de gestion centralisée
+ Mise à l’échelle Azure, fiabilité et disponibilité de premier ordre
+ Traitement IA de données brutes pour faciliter la recherche, y compris le texte dans des images ou la recherche de séquences dans un contenu non structuré.
+ Analyse linguistique et personnalisée, incluant des analyseurs de recherche en texte intégral solide en 56 langues
+ [Principales fonctionnalités communes aux applications centrées sur les recherches](search-features-list.md) : notation, recherche par facettes, suggestions, synonymes, recherche basée sur la localisation, etc.

Parmi nos clients, ceux capables d’exploiter le plus large éventail de fonctionnalités de la Recherche cognitive Azure sont les catalogues en ligne, les programmes métier et les applications de découverte de documents.

## <a name="watch-this-video"></a>Regardez cette vidéo

Dans cette vidéo de 15 minutes, le Chef de programme Luis Cabrera présente Recherche cognitive Azure.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]