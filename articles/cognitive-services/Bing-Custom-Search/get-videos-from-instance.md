---
title: Obtenir des vidéos à partir de votre vue personnalisée – Recherche personnalisée Bing
titleSuffix: Azure Cognitive Services
description: Présentation générale de l’utilisation de la Recherche personnalisée Bing pour obtenir des vidéos à partir d’une vue personnalisée du web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 7dbd9f609944fc63c186ca150d5b9921f3e86622
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090577"
---
# <a name="get-videos-from-your-custom-view"></a>Obtenir des vidéos à partir d’une vue personnalisée

> [!WARNING]
> Les API Recherche Bing passent de Cognitive Services aux services de recherche Bing. À compter du **30 octobre 2020** , toutes les nouvelles instances de Recherche Bing doivent être provisionnées en suivant le processus documenté [ici](https://aka.ms/cogsvcs/bingmove).
> Les API Recherche Bing provisionnées à l’aide de Cognitive Services seront prises en charge les trois prochaines années ou jusqu’à la fin de votre Accord Entreprise, selon la première éventualité.
> Pour obtenir des instructions de migration, consultez [Services de recherche Bing](https://aka.ms/cogsvcs/bingmigration).

La Recherche personnalisée de vidéos Bing vous permet d’enrichir votre expérience de recherche personnalisée avec des vidéos. Comme les résultats web, la recherche personnalisée prend en charge la recherche de vidéos dans la liste de sites web de votre instance. Vous pouvez obtenir des vidéos en utilisant l’API Recherche personnalisée de vidéos Bing ou la fonctionnalité Hosted UI (Interface utilisateur hébergée). La fonctionnalité d’interface utilisateur hébergée est simple à utiliser et recommandée pour rendre votre expérience de recherche rapidement opérationnelle. Pour plus d’informations sur la configuration de votre interface utilisateur hébergée dans le but d’inclure des vidéos, consultez [Configurer votre expérience d’interface utilisateur hébergée](hosted-ui.md).

Si vous souhaitez contrôler davantage l’affichage des résultats de recherche, vous pouvez utiliser l’API Recherche personnalisée de vidéos Bing. Étant donné que l’appel de cette API ressemble à celui de l’API Recherche de vidéos Bing, consultez [Recherche de vidéos Bing](../Bing-Video-Search/search-the-web.md) pour des exemples d’appel de cette API. Au préalable, familiarisez-vous avec les [Informations de référence sur l’API Recherche personnalisée de vidéos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference). Les principales différences sont les paramètres de requête pris en charge (vous devez inclure le paramètre de requête customConfig) et le point de terminaison auquel vous envoyez vos requêtes.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
