---
title: Annonces de nouvelles fonctionnalités
titleSuffix: Azure Cognitive Search
description: Annonces de fonctionnalités nouvelles et améliorées, notamment le changement de Recherche Azure en Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 247378461a99d42f21a67a2c0aebc7019727f3a0
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555143"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Nouveauté dans Recherche cognitive Azure

Découvrir les nouveautés du service. Marquez cette page pour rester au fait des nouveautés du service.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Nouveau nom du service Recherche Azure

Recherche Azure est maintenant renommé  **Recherche cognitive Azure** pour refléter l’utilisation étendue de compétences cognitives et du traitement par IA dans les opérations de base. Alors que les compétences cognitives ajoutent de nouvelles fonctionnalités, l’utilisation de l’IA est strictement facultative. Vous pouvez continuer à utiliser la Recherche cognitive Azure sans l’IA pour générer des solutions de recherche en texte intégral riches sur du contenu basé sur du texte, privé et hétérogène, dans un index que vous créez et gérez dans le cloud. 

Les versions d’API, les packages NuGet, les espaces de noms et les points de terminaison ne sont pas modifiés. Vos solutions de recherche existantes ne sont pas affectées par le changement de nom du service.

## <a name="feature-announcements"></a>Annonces de fonctionnalités

4 novembre 2019 : conférence Ignite

+ L’[indexation incrémentielle (en préversion)](cognitive-search-incremental-indexing-conceptual.md) vous permet de choisir les étapes à retraiter quand vous apportez des modifications à un pipeline d’enrichissement. L’indexation incrémentielle s’avère utile si vous avez du contenu d’image que vous avez analysé précédemment. La sortie de l’analyse coûteuse est stockée, puis utilisée comme base pour une indexation ou un enrichissement supplémentaires.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ L’[Extraction de document (préversion)](cognitive-search-skill-document-extraction.md) est une compétence cognitive utilisée lors de l’indexation, qui vous permet d’extraire le contenu d’un fichier à partir d’un ensemble de compétences. Auparavant, le craquage de document avait exclusivement lieu avant l’exécution de l’ensemble de compétences. Avec l’ajout de cette compétence, vous pouvez également effectuer cette opération dans le cadre de l’exécution de l’ensemble de compétences.

+ La [Traduction de texte (préversion)](cognitive-search-skill-text-translation.md) est une compétence cognitive utilisée lors de l’indexation, qui évalue le texte et qui, pour chaque enregistrement, retourne le texte traduit dans la langue cible spécifiée.

+ Les [Modèles Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) peuvent lancer rapidement vos visualisations et l’analyse de contenu enrichi dans un magasin de connaissances dans Power BI Desktop. Ce modèle est conçu pour les projections de tables Azure créées à l’aide de l’[Assistant Importer des données](knowledge-store-create-portal.md).

+ [Azure Data Lake Storage Gen2 (préversion)](search-howto-index-azure-data-lake-storage.md), l’[API Gremlin de Cosmos DB (préversion)](search-howto-index-cosmosdb.md) et l’[API Cassandra de Cosmos DB (préversion)](search-howto-index-cosmosdb.md) sont désormais pris en charge dans les indexeurs. Vous pouvez vous inscrire avec [ce formulaire](https://aka.ms/azure-cognitive-search/indexer-preview). Vous recevez un e-mail de confirmation une fois que vous avez été accepté dans le programme de préversion.

24 juillet 2019

+ Mise à la disposition générale dans le [Cloud Azure Government](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Mises à jour de service

[Les annonces de mise à jour de service](https://azure.microsoft.com/updates/?product=search&status=all) pour Recherche cognitive Azure sont disponibles sur le site web Azure.