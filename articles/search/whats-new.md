---
title: Nouveauté dans Recherche cognitive Azure
description: Annonces de fonctionnalités nouvelles et améliorées, notamment le changement de Recherche Azure en Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 06/30/2020
ms.openlocfilehash: 078892691bfaec62f71f9d601a42de3f80221149
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958155"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Nouveauté dans Recherche cognitive Azure

Découvrir les nouveautés du service. Marquez cette page pour rester au fait des nouveautés du service.

## <a name="feature-announcements"></a>Annonces de fonctionnalités

### <a name="june-2020"></a>Juin 2020

+ La [base de connaissances](knowledge-store-concept-intro.md) est désormais mise à la disposition générale.

+ L’[API REST du service Recherche 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) est la nouvelle version stable des API REST. Outre la base de connaissances, cette version en disponibilité générale comprend des améliorations de la pertinence et du scoring des recherches.

+ Le nouvel algorithme de classement de pertinence est désormais [BM25](https://en.wikipedia.org/wiki/Okapi_BM25) pour tout service que vous créez. Pour les services existants, vous pouvez choisir de définir la propriété `similarity` sur les champs d’index. Cette propriété est en disponibilité générale.

+ Le nouvel indexeur `executionEnvironment` peut être défini explicitement sur `private`. Cette fonctionnalité prend en charge l’accès de l’indexeur aux données externes sur des points de terminaison privés et est en disponibilité générale.

+ [Azure Machine Learning (AML)](cognitive-search-aml-skill.md) est un nouveau type de compétence permettant d’intégrer un point de terminaison d’inférence à partir d’Azure Machine Learning. L’expérience du portail prend en charge la découverte et l’intégration de votre point de terminaison Azure Machine Learning dans un ensemble de compétences Recherche cognitive. La découverte exige que vos services Recherche cognitive et Azure ML soient déployés dans le même abonnement. Cette compétence est en disponibilité générale. Pour bien démarrer, suivez [ce tutoriel](cognitive-search-tutorial-aml-custom-skill.md).

### <a name="may-2020-microsoft-build"></a>Mai 2020 (Microsoft Build)

+ La fonctionnalité [Sessions de débogage](cognitive-search-debug-session.md) est maintenant en préversion. Les sessions de débogage offrent une interface sur portail pour examiner et résoudre les problèmes liés à un ensemble de compétences. Les correctifs créés dans la session de débogage peuvent être enregistrés dans des ensembles de compétences de production. Pour bien démarrer, suivez [ce tutoriel](cognitive-search-tutorial-debug-sessions.md).

+ Protégez un point de terminaison de service de recherche de l’Internet public en [configurant des règles IP pour la prise en charge des pare-feu entrants](service-configure-firewall.md) ou en tirant parti d’[Azure Private Link pour un point de terminaison de recherche privé](service-create-private-endpoint.md). Les deux fonctionnalités sont en disponibilité générale.

+ Utilisez une [identité managée par le système](search-howto-managed-identities-data-sources.md) (préversion) pour configurer une connexion à une source de données Azure à des fins d’indexation. S’applique aux [indexeurs](search-indexer-overview.md) qui ingèrent le contenu de sources de données Azure comme Azure SQL Database, Azure Cosmos DB et le Stockage Azure.

+ Changez la base de calcul des scores de recherche pour passer d’un calcul par partition à un calcul sur toutes les partitions avec les paramètres de requête [sessionId](index-similarity-and-scoring.md) et [scoringStatistics=global](index-similarity-and-scoring.md#scoring-statistics). Ces paramètres sont en disponibilité générale.

+ Ajoutez un paramètre de requête [featuresMode (préversion)](index-similarity-and-scoring.md#featuresMode-param) pour développer un score de pertinence afin d’afficher plus de détails : score de similarité par champ, fréquence des termes par champ et nombre de jetons uniques correspondants par champ. Vous pouvez consommer ces points de données dans des algorithmes de scoring personnalisés. Pour obtenir un exemple qui illustre cette fonctionnalité, consultez [Ajouter le machine learning (LearnToRank) pour améliorer la pertinence de la recherche](https://github.com/Azure-Samples/search-ranking-tutorial).

### <a name="march-2020"></a>Mars 2020

+ Avec la [suppression réversible d’objets blob natifs (préversion)](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), l’indexeur du Stockage Blob Azure dans Recherche cognitive Azure reconnaît les objets blob qui sont dans un état de suppression réversible, et supprime le document de recherche correspondant durant l’indexation.

+ Une nouvelle [API REST de gestion (13/03/2020)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) stable est désormais en disponibilité générale. 

### <a name="february-2020"></a>Février 2020

+ La fonctionnalité [Détection PII (préversion) ](cognitive-search-skill-pii-detection.md) est une compétence cognitive utilisée lors de l’indexation, qui extrait des informations d’identification personnelle d’un texte en entrée, et vous donne la possibilité de les masquer dans ce texte de différentes façons.

+ La fonctionnalité [Recherche d’entité personnalisée (préversion)](cognitive-search-skill-custom-entity-lookup.md ) recherche du texte dans une liste personnalisée définie par l’utilisateur de mots et d’expressions. À l’aide de cette liste, elle étiquète tous les documents contenant des entités correspondantes. La compétence prend également en charge un degré de correspondance approximative qui peut être appliqué pour rechercher des correspondances similaires sans être rigoureusement exactes. 

### <a name="january-2020"></a>Janvier 2020

+ [Les clés de chiffrement gérées par le client](search-security-manage-encryption-keys.md) sont désormais mises à la disposition générale. Si vous utilisez REST, vous pouvez accéder à la fonctionnalité à l’aide de `api-version=2019-05-06` ou version ultérieure. Pour le code managé, le bon package est toujours la [version préliminaire 8.0 SDK .NET](search-dotnet-sdk-migration-version-9.md) même si la fonctionnalité n’est plus en préversion. 

+ Un accès privé à un service de recherche est disponible par le biais de deux mécanismes actuellement en préversion :

  + Vous pouvez restreindre l’accès à des adresses IP spécifiques à l’aide de l’API REST de gestion `api-version=2019-10-01-Preview` pour créer le service. L’API en préversion comprend de nouvelles propriétés **IpRule** et **NetworkRuleSet** dans [API CreateOrUpdate](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Cette fonctionnalité d’évaluation est disponible dans les régions sélectionnées. Pour plus d’informations, consultez la section [Utilisation de l’API REST de gestion](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

### <a name="december-2019"></a>Décembre 2019

+ Dans le portail, [Créer une application de démonstration (préversion)](search-create-app-portal.md) est un nouvel Assistant qui génère un fichier HTML téléchargeable avec accès par requête (lecture seule) à un index. Le fichier est fourni avec un script incorporé qui restitue une application web opérationnelle de type « localhost », qui est liée à un index de votre service de recherche. Les pages sont configurables dans l’Assistant et peuvent contenir une barre de recherche, une zone de résultats, une navigation dans la barre latérale et la prise en charge des requêtes TypeAhead. Vous pouvez modifier le code HTML hors connexion pour étendre ou personnaliser le workflow ou l’apparence. Il est difficile d’étendre une application de démonstration pour qu’elle inclue les couches de sécurité et d’hébergement qui sont généralement nécessaires dans les scénarios de production. Vous devez la considérer comme un outil de validation et de test plutôt que comme un raccourci vers une application cliente complète.

+ La section [Créer un point de terminaison privé pour les connexions sécurisées (version préliminaire)](service-create-private-endpoint.md) explique comment configurer une liaison privée pour sécuriser les connexions de votre service Search. Cette fonctionnalité d’évaluation est disponible à la demande et utilise [Azure Private Link](../private-link/private-link-overview.md) et le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) dans le cadre de la solution.

### <a name="november-2019---ignite-conference"></a>Novembre 2019 - Conférence Ignite

+ [L’enrichissement incrémentiel (version préliminaire)](cognitive-search-incremental-indexing-conceptual.md) ajoute la mise en cache et l’état à un pipeline d’enrichissement afin que vous puissiez travailler sur des étapes ou des phases spécifiques sans perdre le contenu déjà traité. Auparavant, toute modification apportée à un pipeline d’enrichissement nécessitait une regénération complète. Grâce à l’enrichissement incrémentiel, la sortie de l’analyse coûteuse, en particulier l’analyse des images, est préservée.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ L’[Extraction de document (préversion)](cognitive-search-skill-document-extraction.md) est une compétence cognitive utilisée lors de l’indexation, qui vous permet d’extraire le contenu d’un fichier à partir d’un ensemble de compétences. Auparavant, le craquage de document avait exclusivement lieu avant l’exécution de l’ensemble de compétences. Avec l’ajout de cette compétence, vous pouvez également effectuer cette opération dans le cadre de l’exécution de l’ensemble de compétences.

+ La [Traduction de texte](cognitive-search-skill-text-translation.md) est une compétence cognitive utilisée pendant l’indexation, qui évalue le texte et, pour chaque enregistrement, retourne le texte traduit dans la langue cible spécifiée.

+ Les [Modèles Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) peuvent lancer rapidement vos visualisations et l’analyse de contenu enrichi dans un magasin de connaissances dans Power BI Desktop. Ce modèle est conçu pour les projections de tables Azure créées à l’aide de l’[Assistant Importer des données](knowledge-store-create-portal.md).

+ [Azure Data Lake Storage Gen2 (préversion)](search-howto-index-azure-data-lake-storage.md), l’[API Gremlin de Cosmos DB (préversion)](search-howto-index-cosmosdb.md) et l’[API Cassandra de Cosmos DB (préversion)](search-howto-index-cosmosdb.md) sont désormais pris en charge dans les indexeurs. Vous pouvez vous inscrire avec [ce formulaire](https://aka.ms/azure-cognitive-search/indexer-preview). Vous recevez un e-mail de confirmation une fois que vous avez été accepté dans le programme de préversion.

### <a name="july-2019"></a>Juillet 2019

+ Mise à la disposition générale dans le [Cloud Azure Government](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nom du nouveau service

Recherche Azure est maintenant renommé  **Recherche cognitive Azure** pour refléter l’utilisation étendue (mais facultative) de compétences cognitives et du traitement par IA dans les opérations de base. Les versions d’API, les packages NuGet, les espaces de noms et les points de terminaison ne sont pas modifiés. Les solutions de recherche, nouvelles et existantes, ne sont pas affectées par le changement de nom du service.

## <a name="service-updates"></a>Mises à jour de service

[Les annonces de mise à jour de service](https://azure.microsoft.com/updates/?product=search&status=all) pour Recherche cognitive Azure sont disponibles sur le site web Azure.