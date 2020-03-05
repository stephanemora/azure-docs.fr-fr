---
title: Annonces de nouvelles fonctionnalités
titleSuffix: Azure Cognitive Search
description: Annonces de fonctionnalités nouvelles et améliorées, notamment le changement de Recherche Azure en Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/03/2020
ms.openlocfilehash: 27dae07328af125c25512ab9f1eb81d0f4eda99b
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271320"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Nouveauté dans Recherche cognitive Azure

Découvrir les nouveautés du service. Marquez cette page pour rester au fait des nouveautés du service.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nom du nouveau service

Recherche Azure est maintenant renommé  **Recherche cognitive Azure** pour refléter l’utilisation étendue (mais facultative) de compétences cognitives et du traitement par IA dans les opérations de base. Les versions d’API, les packages NuGet, les espaces de noms et les points de terminaison ne sont pas modifiés. Les solutions de recherche, nouvelles et existantes, ne sont pas affectées par le changement de nom du service.

## <a name="feature-announcements"></a>Annonces de fonctionnalités

### <a name="february-2020"></a>Février 2020

+ La fonctionnalité [Détection PII (préversion) ](cognitive-search-skill-pii-detection.md) est une compétence cognitive utilisée lors de l’indexation, qui extrait des informations d’identification personnelle d’un texte en entrée, et vous donne la possibilité de les masquer dans ce texte de différentes façons.

+ La fonctionnalité [Recherche d’entité personnalisée (préversion)](cognitive-search-skill-custom-entity-lookup.md ) recherche du texte dans une liste personnalisée définie par l’utilisateur de mots et d’expressions. À l’aide de cette liste, elle étiquète tous les documents contenant des entités correspondantes. La compétence prend également en charge un degré de correspondance approximative qui peut être appliqué pour rechercher des correspondances similaires sans être rigoureusement exactes. 

### <a name="january-2020"></a>Janvier 2020

+ [Les clés de chiffrement gérées par le client](search-security-manage-encryption-keys.md) sont désormais mises à la disposition générale. Si vous utilisez REST, vous pouvez accéder à la fonctionnalité à l’aide de `api-version=2019-05-06`. Pour le code managé, le bon package est toujours la [version préliminaire 8.0 SDK .NET](search-dotnet-sdk-migration-version-9.md) même si la fonctionnalité n’est plus en préversion. 

+ Un accès privé à un service de recherche est disponible par le biais de deux mécanismes actuellement en préversion :

  + Vous pouvez restreindre l’accès à des adresses IP spécifiques à l’aide de l’API REST de gestion `api-version=2019-10-01-Preview` pour créer le service. L’API en préversion comprend de nouvelles propriétés **IpRule** et **NetworkRuleSet** dans [API CreateOrUpdate](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Cette fonctionnalité d’évaluation est disponible dans les régions sélectionnées. Pour plus d’informations, consultez la section [Utilisation de l’API REST de gestion](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

  + Actuellement disponible par le biais d’une version préliminaire à accès limité, vous pouvez configurer un service Search Azure qui prend en charge le point de terminaison privé Azure pour les connexions des clients sur le même réseau virtuel. Pour plus d’informations, consultez la section [Créer un point de terminaison privé pour une connexion sécurisée](service-create-private-endpoint.md).

### <a name="december-2019"></a>Décembre 2019

+ Dans le portail, [Créer une application (préversion)](search-create-app-portal.md) est un nouvel Assistant qui génère un fichier HTML téléchargeable. Le fichier est fourni avec un script incorporé qui restitue une application web opérationnelle de type « localhost », qui est liée à un index de votre service de recherche. Les pages sont configurables dans l’Assistant et peuvent contenir une barre de recherche, une zone de résultats, une navigation dans la barre latérale et la prise en charge des requêtes TypeAhead. Vous pouvez modifier le code HTML hors connexion pour étendre ou personnaliser le workflow ou l’apparence.

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

## <a name="service-updates"></a>Mises à jour de service

[Les annonces de mise à jour de service](https://azure.microsoft.com/updates/?product=search&status=all) pour Recherche cognitive Azure sont disponibles sur le site web Azure.