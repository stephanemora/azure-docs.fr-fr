---
title: Qu’est-ce que la Recherche personnalisée Bing ? | Microsoft Docs
description: Fournit une vue d’ensemble globale de la Recherche personnalisée Bing
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: b6f50844d6571cca6d63c1db7a85863e3d22d411
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948075"
---
# <a name="what-is-bing-custom-search"></a>Qu’est-ce que la Recherche personnalisée Bing ?

La Recherche personnalisée Bing vous permet de créer des expériences de recherche adaptées pour les sujets qui vous intéressent. Par exemple, si vous possédez un site web qui fournit une expérience de recherche, vous pouvez spécifier les domaines, sites web et pages web dans lesquels Bing effectue les recherches. Vos utilisateurs consultent des résultats de recherche adaptés au contenu qui les intéresse au lieu d’être obligé de compulser des pages de résultats de recherche générale contenant des informations inappropriées.

Pour créer votre vue personnalisée du web, utilisez le [portail](https://customsearch.ai) de la Recherche personnalisée Bing. Ce portail vous permet de créer une instance de recherche qui spécifie les domaines, sites web et pages web que Bing doit utiliser pour ses recherches, et ceux qu’il ne doit pas utiliser. Non seulement vous spécifiez les URL du contenu que vous connaissez, mais vous pouvez également utiliser le portail pour trouver du contenu susceptible de vous intéresser et que vous pouvez ajouter.

Le portail vous permet également d’épingler une page web particulière en haut des résultats de recherche si l’utilisateur entre un terme de recherche spécifique. 

Après avoir défini votre instance, vous pouvez intégrer la recherche personnalisée à votre site web, application de bureau ou application mobile en appelant l’API Recherche personnalisée. Si vous avez un site ou une application web, vous pouvez laisser l’IU hébergée restituer l’interface de recherche pour vous.

L’illustration suivante montre la simplicité de l’intégration de la recherche personnalisée.

![picture alt](./media/bcs-overview.png "Fonctionnement de la Recherche personnalisée Bing.")

## <a name="adding-custom-search-box-suggestions"></a>Ajout de suggestions de zone de recherche personnalisées

Vous pouvez enrichir votre expérience de recherche personnalisée avec des suggestions de zone de recherche personnalisées. Cette fonctionnalité vous permet de fournir des suggestions de recherche personnalisée appropriées à votre expérience de recherche. Quand l’utilisateur tape dans la zone de recherche, il voit une liste déroulante qui contient des suggestions de chaînes de requête basées sur la chaîne de requête qu’il a commencé à taper. Vous pouvez spécifier que seules vos suggestions personnalisées soient retournées ou aussi celles de Bing. [En savoir plus](define-custom-suggestions.md).

## <a name="adding-custom-image-search-experience"></a>Ajout d’une expérience de recherche d’images personnalisée

Vous pouvez enrichir votre expérience de recherche personnalisée avec des images. Comme les résultats web, la recherche personnalisée prend en charge la recherche d’images dans la liste de sites web de votre instance. [En savoir plus](get-images-from-instance.md).

## <a name="adding-custom-video-search-experience"></a>Ajout d’une expérience de recherche de vidéos personnalisée

Vous pouvez enrichir votre expérience de recherche personnalisée avec des vidéos. Comme les résultats web, la recherche personnalisée prend en charge la recherche de vidéos dans la liste de sites web de votre instance. [En savoir plus](get-videos-from-instance.md).

## <a name="sharing-your-custom-search-instance-with-others"></a>Partage de votre instance de recherche personnalisée avec d’autres utilisateurs

Vous pouvez facilement autoriser des modifications et des tests collaboratifs sur votre instance en la partageant avec les membres de votre équipe. [En savoir plus](share-your-custom-search.md).

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer rapidement, consultez [Créer votre première instance Recherche personnalisée Bing](quick-start.md).

Pour plus d’informations sur la personnalisation de votre instance de recherche, consultez [Définir une instance de recherche personnalisée](define-your-custom-view.md).

Familiarisez-vous avec le contenu de référence pour chacun des points de terminaison de la recherche personnalisée. Vous y trouverez les points de terminaison, les en-têtes et les paramètres de requête à utiliser pour demander les résultats de la recherche. Vous y trouverez également les définitions des objets de réponse.

- [API Recherche personnalisée](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [API Image personnalisée](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [API Vidéo personnalisée](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [API Suggestion automatique personnalisée](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


Prenez soin de lire les [exigences relatives à l’affichage et à l’utilisation de Bing](./use-and-display-requirements.md) pour respecter les règles d’utilisation des résultats de la recherche.