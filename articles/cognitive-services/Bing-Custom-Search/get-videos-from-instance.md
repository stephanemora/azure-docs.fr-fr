---
title: Obtenir des vidéos à partir de votre vue personnalisée – Recherche personnalisée Bing
titleSuffix: Azure Cognitive Services
description: Présentation générale de l’utilisation de la Recherche personnalisée Bing pour obtenir des vidéos à partir d’une vue personnalisée du web.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.openlocfilehash: 1266349f169875d788ad65f7fc6fa7382908e31b
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130039160"
---
# <a name="get-videos-from-your-custom-view"></a>Obtenir des vidéos à partir d’une vue personnalisée

> [!WARNING]
> Les API Recherche Bing passent de Cognitive Services aux services de recherche Bing. À compter du **30 octobre 2020**, toutes les nouvelles instances de Recherche Bing doivent être provisionnées en suivant le processus documenté [ici](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Les API Recherche Bing provisionnées à l’aide de Cognitive Services seront prises en charge les trois prochaines années ou jusqu’à la fin de votre Contrat Entreprise, selon la première éventualité.
> Pour obtenir des instructions de migration, consultez [Services de recherche Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

La Recherche personnalisée de vidéos Bing vous permet d’enrichir votre expérience de recherche personnalisée avec des vidéos. Comme les résultats web, la recherche personnalisée prend en charge la recherche de vidéos dans la liste de sites web de votre instance. Vous pouvez obtenir les vidéos en utilisant l’API Recherche personnalisée de vidéos Bing ou la fonctionnalité d’interface utilisateur hébergée. La fonctionnalité d’interface utilisateur hébergée est simple à utiliser et recommandée pour rendre votre expérience de recherche rapidement opérationnelle. Pour plus d’informations sur la configuration de votre interface utilisateur hébergée dans le but d’inclure des vidéos, consultez [Configurer votre expérience d’interface utilisateur hébergée](hosted-ui.md).

Si vous souhaitez contrôler davantage l’affichage des résultats de recherche, vous pouvez utiliser l’API Recherche personnalisée de vidéos Bing. Étant donné que l’appel de cette API ressemble à celui de l’API Recherche de vidéos Bing, consultez [Recherche de vidéos Bing](../bing-video-search/overview.md) pour des exemples d’appel de cette API. Au préalable, familiarisez-vous avec les [Informations de référence sur l’API Recherche personnalisée de vidéos](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference). Les principales différences sont les paramètres de requête pris en charge (vous devez inclure le paramètre de requête customConfig) et le point de terminaison auquel vous envoyez vos requêtes.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->