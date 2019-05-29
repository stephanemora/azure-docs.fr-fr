---
title: Présentation du service Recherche Azure - Recherche Azure
description: Recherche Azure est un service de recherche cloud hébergé et complètement managé de Microsoft. Passez en revue les descriptions des fonctionnalités, un workflow de développement, une comparaison de Recherche Azure à d’autres produits de recherche Microsoft et la façon de le prendre en main.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 23754a1184f89d1fb563b2d73109f3a10c48920c
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65539317"
---
# <a name="what-is-azure-search"></a>Présentation de Recherche Azure
La Recherche Azure est une solution cloud de recherche en tant que service, qui offre aux développeurs des API et des outils permettant d’ajouter une expérience de recherche riche concernant du contenu privé et hétérogène dans les applications web, mobiles et d’entreprise. L’exécution d’une requête est effectué sur un index défini par l’utilisateur.

+ Générez un index de recherche contenant uniquement les données issues de plusieurs plateformes et types de contenu. 

+ Tirez parti des enrichissements IA pour extraire du texte et des fonctionnalités à partir de fichiers image, ou des entités et expressions clés à partir de texte brut.

+ Créez des expériences de recherche intuitives avec la navigation par facettes, les filtres, les synonymes, l'autocomplétion et l'analyse de texte pour les termes de recherche corrigés automatiquement par « Vous voulez dire ». Améliorez la pertinence grâce à des fonctions et à une logique d'optimisation.

+ Créez des applications de recherche pour des cas d'utilisation spécifiques. La géo-recherche prend en charge l'expérience « rechercher à proximité ». La recherche multilingue est prise en charge par l'intermédiaire d'analyseurs de langue pour la recherche en texte intégral dans une langue autre que l'anglais.

Cette fonctionnalité est exposée par le biais d’une [API REST](/rest/api/searchservice/) ou d’un [SDK.NET](search-howto-dotnet-sdk.md) simple, qui masque la complexité inhérente de la récupération d’informations. En plus des API, le portail Azure offre la prise en charge de l’administration et de la gestion de contenu, avec des outils de prototypage et d’interrogation de vos index. Étant donné que le service s’exécute dans le cloud, infrastructure et la disponibilité sont gérées par Microsoft.

<a name="feature-drilldown"></a>

## <a name="feature-descriptions"></a>Description des fonctionnalités

| Recherche &nbsp;de base&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Caractéristiques |
|-------------------|----------|
|Recherche de texte de forme libre | La [**recherche en texte intégral**](search-lucene-query-architecture.md) est le principal cas d'utilisation de la plupart des applications basées sur la recherche. Vous pouvez formuler des requêtes à l’aide d’une syntaxe prise en charge. <br/><br/>La [**syntaxe de requête simple**](query-simple-syntax.md) offre des opérateurs logiques, de recherche d’expression, de suffixe et de précédence.<br/><br/>La [**syntaxe de requête Lucene**](query-lucene-syntax.md) inclut toutes les opérations de la syntaxe simple, avec en plus des extensions pour la recherche partielle, la recherche de proximité, la promotion de termes et les expressions régulières.|
| Pertinence | [**Notation simple**](index-add-scoring-profiles.md) est l’un des principaux avantages de Recherche Azure. Les profils de score permettent de modéliser la pertinence en fonction des valeurs dans les documents eux-mêmes. Par exemple, vous souhaiterez peut-être que les nouveaux produits ou les produits en promotion apparaissent en priorité dans les résultats de la recherche. Vous pouvez également créer des profils de score à l'aide de balises pour obtenir un score personnalisé en fonction de préférences de recherche de client que vous avez suivies et stockées séparément. |
| Recherche basée sur la localisation | Recherche Azure traite, filtre et affiche les emplacements géographiques. Les utilisateurs peuvent ainsi explorer les données selon la proximité d’un résultat de recherche par rapport à un emplacement physique. [Regardez cette vidéo](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) ou [étudiez cet exemple](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) pour en savoir plus. |
| Filtres et facettes | La [**navigation par facettes**](search-faceted-navigation.md) est activée par le biais d’un seul paramètre de requête. La Recherche Azure renvoie une structure de navigation par facettes que vous pouvez utiliser en tant que code de liste de catégories pour le filtrage autonome (par exemple, pour filtrer les éléments de catalogue par plage de prix ou par marque). <br/><br/> L’option [**Filtres**](query-odata-filter-orderby-syntax.md) permet d’intégrer la navigation par facettes dans l’interface utilisateur de votre application, d’améliorer la formulation des requêtes et d’appliquer un filtre en fonction de critères spécifiés par les utilisateurs ou les développeurs. Créez des filtres à l’aide de la syntaxe OData. |
| Fonctionnalités de l’expérience utilisateur | L'[**Autocomplétion**](search-autocomplete-tutorial.md) peut être activée sur une barre de recherche pour les requêtes prédictives. <br/><br/>[**Suggestions de recherche** ](https://docs.microsoft.com/rest/api/searchservice/suggesters) fonctionne sur les entrées de texte partielle dans une barre de recherche, mais les résultats est également les documents dans vos conditions d’index plutôt que de requête. <br/><br/>[**Synonymes**](search-synonyms.md) Dans les moteurs de recherche, les synonymes associent des termes équivalents qui élargissent implicitement l’étendue d’une requête, sans que l’utilisateur ait à fournir le terme. <br/><br/>Le [**surligneur d’éléments**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) applique la mise en forme de texte à un mot clé correspondant dans les résultats de la recherche. Vous pouvez choisir quels champs renvoient les extraits de texte en surbrillance.<br/><br/>[**Tri**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) est proposée pour plusieurs champs par le biais du schéma d’index, et elle est activée ou désactivée au moment de la requête avec un paramètre de recherche unique.<br/><br/> [**Pagination**](search-pagination-page-layout.md) et la limitation des résultats de la recherche sont d’un fonctionnement très simple avec le contrôle finement ajusté qu’offre Recherche Azure sur vos résultats de recherche.  <br/><br/>|

| Enrichissement&nbsp;IA&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;       | Caractéristiques |
|-------------------|----------|
|Documents enrichis par l'IA | Une [**Recherche cognitive**](cognitive-search-concept-intro.md) à des fins d'analyse de texte et d'images peut être appliquée à un pipeline d'indexation afin d'extraire des informations textuelles à partir de contenus bruts. Parmi les [compétences intégrées](cognitive-search-predefined-skills.md), on peut citer la reconnaissance de caractères optiques (ce qui rend possible la recherche de JPEG), la reconnaissance d’entité (identifiant une organisation, un nom ou un emplacement), et la reconnaissance de phrase clé. Vous pouvez aussi [coder des compétences personnalisées](cognitive-search-create-custom-skill-example.md) à attacher au pipeline. |
| Enrichissements stockés à des fins d'analyse et d'utilisation| La [**Base de connaissances (préversion)** ](knowledge-store-concept-intro.md) est une extension de l’indexation basée sur l’IA. Avec Stockage Azure en tant que principal, vous pouvez enregistrer les enrichissements créés lors de l'indexation. Ces artefacts peuvent vous aider à concevoir des ensembles de compétences plus performants ou à créer une forme et une structure à partir de données amorphes ou ambiguës. Vous pouvez créer des projections de ces structures afin de cibler des charges de travail ou des utilisateurs spécifiques. Vous pouvez également analyser directement les données extraites ou les charger dans d'autres applications.<br/><br/> |

| Importation/indexation&nbsp;de données | Caractéristiques |
|----------------------------------|----------|
| Sources de données | Les index Recherche Azure acceptent les données de n’importe quelle source, sous réserve qu’elles soient soumises en tant que structure de données JSON. <br/><br/> Les [**indexeurs**](search-indexer-overview.md) automatisent l'ingestion des données pour les sources de données Azure prises en charge et gèrent la sérialisation JSON. Connectez-vous à [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md) ou [Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md) pour extraire le contenu pouvant faire l'objet d'une recherche dans les magasins de données primaires. Les indexeurs d’objets blob Azure peuvent effectuer une *recherche de document* pour [extraire le texte présentant la plupart des formats de fichier](search-howto-indexing-azure-blob-storage.md), y compris Microsoft Office, ainsi que les documents PDF et HTML. |
| Structures de données hiérarchiques et imbriquées | Les [**types complexes**](search-howto-complex-data-types.md) et les collections vous permettent de modéliser pratiquement tout type de structure JSON en tant qu'index Recherche Azure. La cardinalité « un à plusieurs » et « plusieurs à plusieurs » peut être exprimée de manière native à travers des collections, des types complexes et des collections de types complexes.|
| Analyse linguistique | Les analyseurs sont des composants utilisés pour le traitement au cours des opérations d’indexation et de recherche de texte. Il existe deux types de clé API. <br/><br/>Les [**analyseurs lexicaux personnalisés**](index-add-custom-analyzers.md) servent pour des requêtes de recherche complexes en utilisant la mise en correspondance phonétique et des expressions régulières. <br/><br/>Les [**analyseurs de langage**](index-add-language-analyzers.md) de Lucene ou de Microsoft sont utilisés pour gérer intelligemment les caractéristiques linguistiques propres à la langue, notamment les temps des verbes, le masculin et le féminin, les noms au pluriel irrégulier (par exemple, « cheval » et « chevaux »), la décomposition des mots, la césure des mots (pour les langues sans espaces), etc. <br/><br/>|


| Niveau&nbsp;de la plate-forme&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Caractéristiques |
|-------------------|----------|
| Outils de prototypage et d’inspection | Dans le portail, vous pouvez utiliser l’[**Assistant Importation de données**](search-import-data-portal.md) pour configurer des indexeurs, le concepteur d’index pour créer un index, et l’[**Explorateur de recherche**](search-explorer.md) pour tester les requêtes et affiner les profils de score. Vous pouvez également ouvrir un index pour consulter son schéma. |
| Surveillance et diagnostics | [**Activez les fonctionnalités de surveillance**](search-monitor-usage.md) pour aller au-delà des métriques visibles en un coup d'œil sur le portail. Des mesures sur les requêtes par seconde, la latence et la limitation sont capturées et affichées sur les pages du portail sans aucune configuration supplémentaire. <br/><br/>L'[**Analytique du trafic des recherches**](search-traffic-analytics.md) constitue une autre alternative de surveillance. Les données côté serveur et côté client sont collectées et analysées pour dévoiler des insights sur ce que les utilisateurs saisissent dans la zone de recherche. |
| Chiffrement côté serveur | Le [**chiffrement au repos géré par Microsoft**](search-security-overview.md#encrypted-transmission-and-storage) est intégré dans la couche de stockage interne et est irrévocable. Vous pouvez également compléter le chiffrement par défaut avec des [**clés de chiffrement gérées par le client (préversion)** ](search-security-manage-encryption-keys.md). Les clés que vous créez et gérez dans Azure Key Vault permettent de chiffrer les index et les mappages de synonymes dans Recherche Azure. |
| Infrastructure | La **plateforme haute disponibilité** garantit une expérience de service de recherche extrêmement fiable. Lorsqu’il est correctement mis à l'échelle, [Azure Search offre un SLA de 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> En tant que solution complète **entièrement gérée et extensible**, Recherche Azure n’exige absolument aucune gestion d’infrastructure. Votre service peut être adapté à vos besoins avec la mise à l’échelle en deux dimensions pour gérer plus de stockage de documents, plus de charge de requêtes, ou les deux.<br/><br/>|

## <a name="how-to-use-azure-search"></a>Utilisation de Recherche Azure
### <a name="step-1-provision-service"></a>Étape 1 : Configuration du service
Vous pouvez provisionner un service Recherche Azure dans le [portail Azure](https://portal.azure.com/) ou via [l’API Azure Resource Manager](/rest/api/searchmanagement/). Vous pouvez opter pour le service gratuit partagé avec d’autres abonnés ou pour un [niveau payant](https://azure.microsoft.com/pricing/details/search/) qui dédie les ressources que seul votre service utilise. Pour les niveaux payants, vous pouvez mettre à l’échelle un service dans deux dimensions : 

- Ajoutez des réplicas pour accroître votre capacité à traiter de lourdes charges de requêtes.   
- Ajoutez des partitions pour accroître votre capacité à stocker plus de documents. 

En gérant le stockage de documents et le débit de requêtes séparément, vous pouvez étalonner l’allocation de ressources en fonction des besoins de production.

### <a name="step-2-create-index"></a>Étape 2 : Création d’index
Avant de pouvoir charger du contenu qui peut faire l’objet de recherches, vous devez d’abord définir un index Recherche Azure. Un index est comparable à une table de base de données qui conserve vos données et peut accepter des requêtes de recherche. Vous devez définir le schéma d’index à mapper pour refléter la structure des documents dans lesquels vous voulez effectuer des recherches, de la même façon que les champs d’une base de données.

Il est possible de créer un schéma dans le portail Azure ou par programmation à l’aide du [Kit de développement logiciel (SDK) .NET](search-howto-dotnet-sdk.md) ou de [l’API REST](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Étape 3 : Charger des données
Une fois que vous avez défini un index, vous êtes prêt à charger du contenu. Vous pouvez utiliser un modèle push ou pull.

Le modèle push extrait des données auprès de sources de données externes. Ce modèle est pris en charge grâce aux *indexeurs* qui simplifient et automatisent certains aspects de l’ingestion de données, comme la connexion aux données, leur lecture et leur sérialisation. Des [indexeurs](/rest/api/searchservice/Indexer-operations) sont disponibles pour Azure Cosmos DB, Azure SQL Database, Stockage Blob Azure et SQL Server hébergé dans une machine virtuelle Azure. Vous pouvez configurer un indexeur pour une actualisation de données à la demande ou planifiée.

Le modèle d’émission est fourni via le Kit de développement logiciel (SDK) ou les API REST permettant d’envoyer les documents mis à jour à un index. Vous pouvez émettre des données à partir de n'importe quel groupe de données à l’aide du format JSON. Pour obtenir des conseils sur le chargement des données, consultez [Ajout, mise à jour ou suppression de documents](/rest/api/searchservice/addupdate-or-delete-documents) ou [Utilisation du Kit de développement logiciel (SDK) .NET](search-howto-dotnet-sdk.md).

### <a name="step-4-search"></a>Étape 4 : Recherche
Après avoir rempli une index, vous pouvez [émettre des requêtes de recherche](/rest/api/searchservice/Search-Documents) à destination du point de terminaison du service en utilisant des requêtes HTTP simples avec l’API REST ou le SDK .NET.

## <a name="how-it-compares"></a>Comparaison

Les clients souhaitent souvent comparer les performances de Recherche Azure par rapport à d’autres solutions de recherche. Le tableau suivant récapitule ces différences clés.

| Par rapport à | Différences clés |
|-------------|-----------------|
|Bing | [API Recherche Web Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) recherche dans les index de Bing.com les termes correspondant à votre requête. Les index sont créés à partir de contenus HTML, XML et tout autre contenu web disponible sur les sites publics. Conçu autour des mêmes principes, la [Recherche personnalisée Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) offre la même technologie robot pour les types de contenu web, élargie aux sites web individuels.<br/><br/>Recherche Azure recherche dans un index que vous définissez, alimenté par les données et documents que vous possédez, provenant souvent de sources diverses. Recherche Azure dispose de capacités robot pour certaines sources de données à travers des [indexeurs](search-indexer-overview.md), mais vous pouvez envoyer tout document JSON conforme à votre schéma d’index vers une ressource unique consolidée avec possibilité de recherche. |
|Recherche de base de données | De nombreuses plateformes de base de données incluent une expérience de recherche intégrée. SQL Server dispose d’une [recherche de texte intégral](https://docs.microsoft.com/sql/relational-databases/search/full-text-search). Cosmos DB et autres technologies similaires disposent d’index requêtables. Lorsque vous évaluez des produits qui combinent recherche et stockage, il peut être difficile de déterminer quelle fonction utiliser. De nombreuses solutions utilisent les deux : SGBD pour le stockage et Recherche Azure pour des fonctionnalités de recherche spécialisées.<br/><br/>Par rapport à la recherche DBMS, la Recherche Azure stocke du contenu de sources variées et offre des fonctionnalités de traitement de texte spécialisées telles que du traitement de texte orienté linguistique (recherche de radical, lemmatisation, formes de mots) dans [56 langues](https://docs.microsoft.com/rest/api/searchservice/language-support). Elle prend également en charge la correction automatique de mots, [synonymes](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions), [contrôles de scoring](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facettes](https://docs.microsoft.com/azure/search/search-filters-facets) et [segmentation du texte en unités lexicales personnalisée](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). Le [moteur de recherche en texte intégral](search-lucene-query-architecture.md) dans Recherche Azure est basé sur Apache Lucene, une norme du secteur en matière de récupération d’informations. Bien que la Recherche Azure conserve des données sous la forme d’index inversé, il s’agit rarement d’un remplacement de véritable stockage de données. Pour en savoir plus, consultez ce [sujet du forum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>L’utilisation des ressources est un autre point d’inflexion dans cette catégorie. L’indexation et quelques opérations de requête sont souvent intenses en termes de calcul. La recherche de déchargement de DBMS vers une solution dédiée dans le cloud conserve les ressources système pour le traitement transactionnel. De plus, en externalisant la recherche, vous pouvez facilement ajuster l’échelle afin de correspondre au volume de la requête.|
|Solution de recherche dédiée | En partant du postulat que vous avez choisi la recherche dédiée avec une fonctionnalité complète, une dernière comparaison catégorielle se fera entre des solutions locales et un service cloud. De nombreuses technologies de recherche offrent un contrôle sur l’indexation et les pipelines de requêtes, l’accès à une syntaxe de filtrage et de requête plus riche, un contrôle sur le classement et la pertinence, et des fonctionnalités de recherche intelligente et autonome. <br/><br/>Choisissez un service cloud si vous cherchez une solution clé en main ajustable avec une surcharge et une maintenance minimes. <br/><br/>Dans le paradigme du cloud, plusieurs fournisseurs proposent des fonctionnalités de base comparables, notamment une recherche en texte intégral, une recherche en fonction de la localisation et une capacité à gérer un certain niveau d’ambiguïté dans les entrées de recherche. En général, c’est une [fonctionnalité spécialisée](#feature-drilldown) ou l’ergonomie et la simplicité globales des API, des outils et de la gestion qui déterminent la meilleure option. |

Parmi les fournisseurs de services cloud, Recherche Azure s’avère plus efficace pour ce qui est des charges de travail de recherche en texte intégral sur les bases de données et les magasins de contenu sur Azure, dans le cas des applications qui s’appuient principalement sur la recherche pour récupérer les informations et parcourir le contenu. 

Voici les principaux atouts :

+ Intégration de données Azure (robots) au niveau de la couche d’indexation
+ Portail Azure de gestion centralisée
+ Mise à l’échelle Azure, fiabilité et disponibilité de premier ordre
+ Analyse linguistique et personnalisée, incluant des analyseurs de recherche en texte intégral solide en 56 langues
+ [Principales fonctionnalités communes aux applications centrées sur les recherches](#feature-drilldown) : notation, recherche par facettes, suggestions, synonymes, recherche basée sur la localisation, etc.

> [!Note]
> Les sources de données non Azure sont entièrement prises en charge, mais elles s’appuient non pas sur des indexeurs mais sur une méthodologie Push qui fait largement appel au code. En utilisant les API, vous pouvez diriger n’importe quelle collection de documents JSON vers un index Recherche Azure.

Parmi nos clients, ceux capables d’exploiter le plus large éventail de fonctionnalités Recherche Azure sont les catalogues en ligne, les programmes métier et les applications de découverte de documents.

## <a name="rest-api--net-sdk"></a>API REST | SDK .NET

Bien qu’il soit possible d’effectuer de nombreuses tâches dans le portail, Recherche Azure s’adresse avant tout aux développeurs désireux d’intégrer la fonctionnalité de recherche dans des applications existantes. Les interfaces de programmation suivantes sont disponibles.

|Plateforme |Description |
|-----|------------|
|[REST](/rest/api/searchservice/) | Commandes HTTP prises en charge par tous les langages et toutes les plateformes de programmation, y compris Xamarin, Java et JavaScript|
|[Kit de développement logiciel (SDK) .NET](search-howto-dotnet-sdk.md) | Le wrapper .NET pour l’API REST offre un codage efficace en C# et d’autres langages de code géré ciblant .NET Framework |

## <a name="free-trial"></a>Essai gratuit
Les abonnés Azure peuvent [configurer un service dans le niveau Gratuit](search-create-service-portal.md).

Si vous n’êtes pas abonné, vous pouvez [ouvrir gratuitement un compte Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Vous obtenez alors des crédits pour tester les services Azure payants. Une fois ceux-ci épuisés, vous pouvez conserver le compte et utiliser les [services Azure gratuits](https://azure.microsoft.com/free/). Votre carte de crédit n’est pas débitée tant que vous n’avez pas explicitement modifié vos paramètres pour demander à l’être.

Vous pouvez également [activer les avantages d’abonnement MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) : Votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants. 

## <a name="how-to-get-started"></a>Pour commencer

1. Créez un [service gratuit](search-create-service-portal.md). Tous les démarrages rapides et les didacticiels peuvent être effectués sur le service gratuit.

2. Passez au travers du [didacticiel sur l’utilisation d’outils intégrés pour l’indexation et les requêtes](search-get-started-portal.md). Découvrez les concepts importants et familiarisez-vous avec les informations fournies par le portail.

3. Allez plus loin avec le code en utilisant l’API REST ou .NET :

   + [Utilisation du kit de développement logiciel (SDK) .NET](search-howto-dotnet-sdk.md) illustre le flux de travail lié au code managé.  
   + [Getting started with Azure Search using the REST API ](https://github.com/Azure-Samples/search-rest-api-getting-started) (Prise en main de Recherche Azure à l’aide de l’API REST) présente les mêmes étapes avec l’API REST. Vous pouvez également utiliser ce démarrage rapide pour appeler des API REST à partir de Postman ou Fiddler : [Explorer les API REST Recherche Azure](search-fiddler.md).

## <a name="watch-this-video"></a>Regardez cette vidéo

Les moteurs de recherche servent généralement à récupérer les informations sur les applications mobiles, sur le web et dans les magasins de données d’entreprise. Avec la Recherche Azure, vous disposez d’outils permettant de créer une expérience de recherche semblable à celle des grands sites web commerciaux.

Dans cette vidéo de 9 minutes du responsable de programme Liam Cavanagh, découvrez en quoi l’intégration d’un moteur de recherche peut profiter à votre application. Les principales fonctionnalités de Recherche Azure vous sont présentées dans de courtes démonstrations, et vous découvrirez ce qu’est un flux de travail type. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ Entre 0 et 3 minutes : présentation des principales fonctionnalités et cas d’emploi.
+ Entre 3 et 4 minutes : approvisionnement du service. 
+ Entre 4 et 6 minutes : utilisation de l’Assistant Importation de données pour créer un index à l’aide du jeu de données intégrées sur l’immobilier.
+ Entre 6 et 9 minutes : présentation de l’Explorateur de recherche et de différentes requêtes.
