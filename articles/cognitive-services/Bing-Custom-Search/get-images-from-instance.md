---
title: Obtenir des images à partir de votre vue personnalisée | Microsoft Docs
titleSuffix: Cognitive Services
description: Vue d’ensemble de l’utilisation de la Recherche personnalisée Bing pour obtenir des images à partir de votre vue personnalisée du web.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: ba836bbafaf67238664862ee2afce7840a573e44
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953887"
---
# <a name="get-images-from-your-custom-view"></a>Obtenir des images à partir de votre vue personnalisée

La Recherche personnalisée d’images Bing vous permet d’enrichir votre expérience de recherche personnalisée avec des images. Comme les résultats web, la recherche personnalisée prend en charge la recherche d’images dans la liste de sites web de votre instance. Vous pouvez obtenir les images en utilisant l’API Recherche personnalisée d’images de Bing ou la fonctionnalité d’interface utilisateur hébergée. La fonctionnalité d’interface utilisateur hébergée est simple à utiliser et recommandée pour rendre votre expérience de recherche rapidement opérationnelle.  Pour plus d’informations sur la configuration de votre interface utilisateur hébergée visant à inclure des images, consultez [Configurer votre expérience d’interface utilisateur hébergée](hosted-ui.md).

Si vous souhaitez contrôler davantage l’affichage des résultats de recherche, vous pouvez utiliser l’API Recherche personnalisée d’images de Bing. Étant donné que l’appel de l’API ressemble à l’appel de l’API Recherche d’images Bing, consultez [Recherche d’images Bing](../Bing-Image-Search/overview.md) pour des exemples d’appel de l’API. Au préalable, familiarisez-vous avec le contenu des [Informations de référence sur l’API Recherche personnalisée d’images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference). Les principales différences sont les paramètres de requête pris en charge (vous devez inclure le paramètre de requête customConfig) et le point de terminaison auquel vous envoyez vos demandes.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->