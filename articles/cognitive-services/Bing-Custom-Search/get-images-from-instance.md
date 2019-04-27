---
title: Obtenir des images à partir de votre vue personnalisée – Recherche personnalisée Bing
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble de l’utilisation de la Recherche personnalisée Bing pour obtenir des images à partir de votre vue personnalisée du web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 42679635aab8df7174eb06928631e2b677d0a20b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591896"
---
# <a name="get-images-from-your-custom-view"></a>Obtenir des images à partir de votre vue personnalisée

La Recherche personnalisée d’images Bing vous permet d’enrichir votre expérience de recherche personnalisée avec des images. Comme les résultats web, la recherche personnalisée prend en charge la recherche d’images dans la liste de sites web de votre instance. Vous pouvez obtenir les images en utilisant l’API Recherche personnalisée d’images de Bing ou la fonctionnalité d’interface utilisateur hébergée. La fonctionnalité d’interface utilisateur hébergée est simple à utiliser et recommandée pour rendre votre expérience de recherche rapidement opérationnelle.  Pour plus d’informations sur la configuration de votre interface utilisateur hébergée visant à inclure des images, consultez [Configurer votre expérience d’interface utilisateur hébergée](hosted-ui.md).

Si vous souhaitez contrôler davantage l’affichage des résultats de recherche, vous pouvez utiliser l’API Recherche personnalisée d’images de Bing. Étant donné que l’appel de l’API ressemble à l’appel de l’API Recherche d’images Bing, consultez [Recherche d’images Bing](../Bing-Image-Search/overview.md) pour des exemples d’appel de l’API. Au préalable, familiarisez-vous avec le contenu des [Informations de référence sur l’API Recherche personnalisée d’images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference). Les principales différences sont les paramètres de requête pris en charge (vous devez inclure le paramètre de requête customConfig) et le point de terminaison auquel vous envoyez vos demandes.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
