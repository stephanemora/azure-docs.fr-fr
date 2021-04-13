---
title: Nouveauté dans Recherche cognitive Azure
description: Annonces de fonctionnalités nouvelles et améliorées, notamment le changement de Recherche Azure en Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/12/2021
ms.custom: references_regions
ms.openlocfilehash: b9ae576a2d0ab8aee00be5b755c348806b412bef
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108487"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Nouveauté dans Recherche cognitive Azure

Découvrir les nouveautés du service. Marquez cette page pour rester au fait des nouveautés du service. Consultez la [liste des fonctionnalités d’évaluation](search-api-preview.md) pour voir la liste complète des fonctionnalités qui ne sont pas encore en disponibilité générale.

## <a name="march-2021"></a>Mars 2021

|Fonctionnalité&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Description | Disponibilité  |
|------------------------------|---------------|---------------|
| [Recherche sémantique](semantic-search-overview.md) | Collection de fonctionnalités liées aux requêtes qui améliorent considérablement la pertinence des résultats de recherche par des ajustements minimaux apportés à une demande de requête. </br></br>Le [classement sémantique](semantic-ranking.md) calcule les scores de pertinence en utilisant la signification sémantique des mots et du contenu. </br></br>Les [légendes sémantiques](semantic-how-to-query-request.md) retournent des passages pertinents du document qui résument le mieux le document, avec mise en évidence des termes ou expressions les plus importants. </br></br>Les [réponses sémantiques](semantic-answers.md) retournent des passages clés, extraits d’un document de recherche, qui sont formulés comme une réponse directe à une requête qui ressemble à une question. | Préversion publique ([sur demande](https://aka.ms/SemanticSearchPreviewSignup)). </br></br>Utilisez [Rechercher dans des documents (REST)](/rest/api/searchservice/preview-api/search-documents) (api-version=2020-06-30-Preview) ou l’[Explorateur de recherche](search-explorer.md) dans le portail Azure. </br></br>Des restrictions de région et de niveau s’appliquent. |
| [Vérification orthographique des termes de la requête](speller-how-to-add.md) | Vous pouvez appliquer la vérification orthographique aux termes de la requête avant que ceux-ci ne parviennent au moteur de recherche. L’option `speller` fonctionne avec n’importe quel type de requête (simple, complète ou sémantique). |  Préversion publique, REST uniquement, api-version=2020-06-30-Preview|
| [Indexeur SharePoint Online](search-howto-index-sharepoint-online.md) | Cet indexeur vous connecte à un site SharePoint Online, ce qui vous permet d’indexer le contenu d’une bibliothèque de documents. | Préversion publique, REST uniquement, api-version=2020-06-30-Preview |
| [Normaliseurs](search-normalizers.md) | Les normaliseurs fournissent un prétraitement de texte simple comme la gestion de la casse, la suppression des accents, la mise en correspondance du code ASCII (asciifolding), etc., sans passer par l’ensemble de la chaîne d’analyse.| Préversion publique, REST uniquement, api-version=2020-06-30-Preview |
[**Compétence de recherche d’entité personnalisée**](cognitive-search-skill-custom-entity-lookup.md ) |  Compétence cognitive qui recherche du texte dans une liste personnalisée de mots et d'expressions définie par l'utilisateur. À l’aide de cette liste, elle étiquète tous les documents contenant des entités correspondantes. La compétence prend également en charge un degré de correspondance approximative qui peut être appliqué pour rechercher des correspondances similaires sans être rigoureusement exactes. | En disponibilité générale. |
|

## <a name="february-2021"></a>Février 2021

|Fonctionnalité&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Description | Disponibilité  |
|------------------------------|---------------|---------------|
| [Réinitialiser des documents (préversion)](search-howto-run-reset-indexers.md) |  Retraite des documents de recherche sélectionnés individuellement dans les charges de travail d’indexeur. | [API REST Recherche 2020-06-30-Preview](/rest/api/searchservice/index-preview) |
| [Zones de disponibilité](search-performance-optimization.md#availability-zones)| Les services de recherche avec deux réplicas ou plus dans certaines régions, comme indiqué dans [Mettre à l’échelle pour les performances](search-performance-optimization.md#availability-zones) gagnent en résilience car disposant de réplicas à deux endroits physiques distincts ou plus.  | La région et la date de création du service de recherche déterminent la disponibilité. Pour plus d’informations, consultez l’article Mettre à l’échelle pour les performances. |
| [Azure CLI](/cli/azure/search) </br>[Azure PowerShell](/powershell/module/az.search/) | Les nouvelles révisions fournissent désormais la gamme complète des opérations dans l’API REST de gestion du 01-08-2020, y compris la prise en charge des règles de pare-feu IP et du point de terminaison privé. | En disponibilité générale. |

## <a name="january-2021"></a>Janvier 2021

|Fonctionnalité&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Description | Disponibilité  |
|------------------------------|-------------|---------------|
| [Accélérateur de solution pour Recherche cognitive Azure et QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | Extrait les questions et les réponses du document et suggère les réponses les plus pertinentes. Une application de démonstration en direct est disponible sur [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo).  | Projet open source (sans contrat de niveau de service) |

## <a name="2020-archive"></a>Archive 2020

| Month | Fonctionnalité | Description |
|-------|---------|-------------|
| Novembre | [Chiffrement à clé gérée par le client (étendu)](search-security-manage-encryption-keys.md) | Étend le chiffrement géré par le client sur l’ensemble des ressources créées et gérées par un service de recherche. En disponibilité générale.|
| Septembre | [Extension Visual Studio Code pour Recherche cognitive Azure](search-get-started-vs-code.md) | Ajoute un espace de travail, la navigation, IntelliSense et des modèles pour la création d’index, d’indexeurs, de sources de données et d’ensembles de compétences. Cette fonctionnalité est actuellement disponible en préversion publique.| 
| Septembre | [Identité de service managée (indexeurs)](search-howto-managed-identities-data-sources.md) | En disponibilité générale.  |
| Septembre | [Requêtes sortantes utilisant une liaison privée](search-indexer-howto-access-private.md) | En disponibilité générale.  |
| Septembre | [API REST Gestion (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | En disponibilité générale. |
| Septembre | [API REST Gestion (2020-08-01-Preview)](/rest/api/searchmanagement/management-api-versions) | Ajoute une ressource de liaison privée partagée pour Azure Functions et les bases de données Azure SQL pour MySQL. |
| Septembre | [SDK .NET de gestion 4.0](/dotnet/api/overview/azure/search/management) |  Mise à jour du SDK Azure pour le SDK de gestion, ciblant la version 2020-08-01 de l’API REST. En disponibilité générale.|
| Août | [double chiffrement](search-security-overview.md#encryption) | En disponibilité générale sur tous les services de recherche créés après le 1er août 2020 dans les régions suivantes : USA Ouest 2, USA Est, USA Centre Sud, US Gov Virginie, US Gov Arizona. |
| Juillet | [Bibliothèque cliente Azure.Search.Documents](/dotnet/api/overview/azure/search.documents-readme) | SDK Azure pour .NET en disponibilité générale. |
| Juillet | [Bibliothèque cliente azure.search.documents](/python/api/overview/azure/search-documents-readme)  | SDK Azure pour Python en disponibilité générale. |
| Juillet | [Bibliothèque cliente @azure/search-documents](/javascript/api/overview/azure/search-documents-readme)  | SDK Azure pour JavaScript en disponibilité générale. |
| June | [Base de connaissances](knowledge-store-concept-intro.md) | En disponibilité générale. |
| June | [API REST Recherche 2020-06-30](/rest/api/searchservice/) | En disponibilité générale. |
| June | [API REST Recherche 2020-06-30-Preview](/rest/api/searchservice/) | Ajoute Réinitialiser l’ensemble de compétences pour retraiter certaines compétences et l’enrichissement incrémentiel. |
| June | [Algorithme de pertinence Okapi BM25](index-ranking-similarity.md) | En disponibilité générale. |
| June |  **executionEnvironment** (s’applique aux services de recherche utilisant Azure Private Link.) | En disponibilité générale. |
| June | [Compétence AML (préversion)](cognitive-search-aml-skill.md) | Compétence cognitive qui étend l’enrichissement par IA à l’aide d’un modèle AML (Azure Machine Learning) personnalisé. |
| Mai | [Sessions de débogage (préversion)](cognitive-search-debug-session.md) | Débogueur d’ensemble de compétences dans le portail.  |
| Mai | [Règles IP pour la prise en charge de pare-feu entrant](service-configure-firewall.md) | En disponibilité générale.  |
| Mai | [Azure Private Link pour un point de terminaison de recherche privé](service-create-private-endpoint.md) | En disponibilité générale.  |
| Mai | [Identité de service managée (indexeurs) - (préversion)](search-howto-managed-identities-data-sources.md) | Connexion aux sources de données Azure à l’aide d’une identité managée.  |
| Mai | [Paramètre de requête sessionId](index-similarity-and-scoring.md), [Paramètre scoringStatistics=global](index-similarity-and-scoring.md#scoring-statistics)  | Statistiques de recherche mondiales utiles pour les [modèles Machine Learning (LearnToRank) pour la pertinence de la recherche](https://github.com/Azure-Samples/search-ranking-tutorial).  |
| Mai | [Expansion du score de pertinence featuresMode (préversion)](index-similarity-and-scoring.md#featuresMode-param)  |   |
|Mars  | [Suppression réversible native de blobs (préversion)](search-howto-index-changed-deleted-blobs.md) | Supprime les documents de recherche si le blob source est supprimé de manière réversible dans le stockage blob. |
|Mars  | [API REST Gestion (2020-03-13)](/rest/api/searchmanagement/management-api-versions) | En disponibilité générale. |
|February | [Compétence Détection PII (préversion)](cognitive-search-skill-pii-detection.md)  | Compétence cognitive qui extrait et masque des informations personnelles. |
|February | [Compétence Recherche d'entité personnalisée (préversion)](cognitive-search-skill-custom-entity-lookup.md) | Compétence cognitive qui recherche les mots et expressions d’une liste et étiquette tous les documents contenant des entités correspondantes.  |
|Janvier | [Chiffrement à clé gérée par le client](search-security-manage-encryption-keys.md) | Mise à la disposition générale  |
|Janvier | [Règles IP pour la prise en charge de pare-feu entrant (préversion)](service-configure-firewall.md) | Nouvelles propriétés **IpRule** et **NetworkRuleSet** dans l’[API CreateOrUpdate](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service).  |
|Janvier | [Créer un point de terminaison privé (préversion)](service-create-private-endpoint.md) | Configurez une liaison privée pour les connexions sécurisées à votre service de recherche. Cette fonctionnalité en préversion a une dépendance avec [Azure Private Link](../private-link/private-link-overview.md) et le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) dans le cadre de la solution. |

## <a name="2019-archive"></a>Archive 2019

| Month | Fonctionnalité | Description |
|-------|---------|-------------|
|Décembre | [Créer une application de démonstration (préversion)](search-create-app-portal.md) | Assistant qui génère un fichier HTML téléchargeable avec accès par requête (en lecture seule) à un index, conçu comme outil de validation et de test et non comme raccourci vers une application cliente complète.|
|Novembre | [Enrichissement incrémentiel (préversion)](cognitive-search-incremental-indexing-conceptual.md) | Met en cache le traitement des ensembles de compétences pour les réutiliser ultérieurement.  |
|Novembre | [Compétence Extraction de document (préversion)](cognitive-search-skill-document-extraction.md) | Compétence cognitive pour extraire le contenu d’un fichier à partir d’un ensemble de compétences.|
|Novembre | [Compétence Traduction de texte](cognitive-search-skill-text-translation.md) | Compétence cognitive utilisée pendant l’indexation, qui évalue et traduit le texte. En disponibilité générale.|
|Novembre | [Modèles Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | Modèle pour la visualisation de contenu dans une base de connaissances |
|Novembre | [Azure Data Lake Storage Gen2 (préversion)](search-howto-index-azure-data-lake-storage.md), [API Gremlin de Cosmos DB (préversion)](search-howto-index-cosmosdb.md) et [API Cassandra de Cosmos DB (préversion)](search-howto-index-cosmosdb.md) | Nouvelles sources de données d’indexeur en préversion publique. |
|Juillet | [Prise en charge du cloud Azure Government](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia&products=search) | En disponibilité générale.|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nom du nouveau service

Recherche Azure a été renommé **Recherche cognitive Azure** en octobre 2019 pour refléter l’utilisation étendue (mais facultative) de compétences cognitives et du traitement par IA dans les opérations de base. Les versions d’API, les packages NuGet, les espaces de noms et les points de terminaison ne sont pas modifiés. Les solutions de recherche, nouvelles et existantes, ne sont pas affectées par le changement de nom du service.

## <a name="service-updates"></a>Mises à jour de service

[Les annonces de mise à jour de service](https://azure.microsoft.com/updates/?product=search&status=all) pour Recherche cognitive Azure sont disponibles sur le site web Azure.