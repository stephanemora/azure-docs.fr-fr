---
title: Redimensionner et rogner les images miniatures - API Recherche d’images Bing
titleSuffix: Azure Cognitive Services
description: Redimensionnez et rognez les images miniatures comprises dans les réponses retournées par l’API Recherche d’images Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: F4FFAE91-A003-4F7C-8E60-83A142485E28
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 7d6c59cc4a7f0a77db42ca5919f3af331c42762a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197124"
---
# <a name="resize-and-crop-thumbnail-images"></a>Redimensionner et rogner les images miniatures

Lors du traitement d’une requête de recherche, Bing génère des informations de miniatures pour toutes les images dans sa [réponse](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#bing-image-search-response-format). Ces informations peuvent être utilisées pour afficher la totalité ou un sous-ensemble des miniatures retournées. Si vous affichez un sous-ensemble, offrez une option pour afficher les images restantes.


<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Si l’utilisateur clique sur la miniature, vous pouvez utiliser [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) pour afficher l’image en taille réelle. Veillez à attribuer l’image.

Si `shoppingSourcesCount` ou `recipeSourcesCount` est supérieur à zéro, ajoutez un badge à la miniature, par exemple un panier d’achat, pour indiquer que l’élément dans l’image est associé à des achats ou à des recettes.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

Pour obtenir des insights sur l’image, par exemple les pages web dans lesquelles figure l’image ou les personnes reconnues dans l’image, utilisez [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken). Pour plus d’informations, consultez [Insights sur les images](image-insights.md).

## <a name="resizing-and-cropping-thumbnails"></a>Redimensionnement et rognage des miniatures

Vous pouvez également redimensionner et développer des miniatures, par exemple lorsque le curseur de l’utilisateur passe sur ces éléments.
> [!NOTE]
> Veillez à attribuer l’image si vous la développez. Vous pouvez par exemple extraire l’hôte de [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) et l’afficher sous l’image.

[!INCLUDE [cognitive-services-bing-resize-crop-thumbnails](../../../includes/cognitive-services-bing-resize-crop-thumbnails.md)]
