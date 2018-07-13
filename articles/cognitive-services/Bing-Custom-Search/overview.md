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
ms.openlocfilehash: 7cd61fc63d0d7734b842ed222c67c6753da9a418
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368841"
---
# <a name="what-is-bing-custom-search"></a>Qu’est-ce que la Recherche personnalisée Bing ?

La Recherche personnalisée Bing vous permet de créer des expériences de recherche adaptées pour les sujets qui vous intéressent. Par exemple, si vous possédez un site web qui fournit une expérience de recherche, vous pouvez spécifier les domaines, sites web et pages web dans lesquels Bing effectue les recherches. Vos utilisateurs consultent des résultats de recherche adaptés au contenu qui les intéresse au lieu d’être obligé de compulser des pages de résultats de recherche générale contenant des informations inappropriées.

Pour créer votre vue personnalisée du web, utilisez le [portail](https://customsearch.ai) de la Recherche personnalisée Bing. Ce portail vous permet de créer une instance de recherche qui spécifie les domaines, sites web et pages web que Bing doit utiliser pour ses recherches, et ceux qu’il ne doit pas utiliser. Non seulement vous spécifiez les URL du contenu que vous connaissez, mais vous pouvez également utiliser le portail pour trouver du contenu susceptible de vous intéresser et que vous pouvez ajouter.

Le portail vous permet également d’épingler une page web particulière en haut des résultats de recherche si l’utilisateur entre un terme de recherche spécifique. 

Après avoir défini votre instance, vous pouvez intégrer la recherche personnalisée à votre site web, application de bureau ou application mobile en appelant l’API Recherche personnalisée. Si vous avez un site ou une application web, vous pouvez laisser l’IU hébergée restituer l’interface de recherche pour vous.

L’illustration suivante montre la simplicité de l’intégration de la recherche personnalisée.

![picture alt](./media/bcs-overview.png "Fonctionnement de la Recherche personnalisée Bing.")

## <a name="customize-search-suggestions"></a>Personnaliser les suggestions de recherche

Si vous êtes abonné au niveau approprié de la Recherche personnalisée (voir les [Tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), vous pouvez personnaliser les suggestions de recherche proposées dans votre expérience de Recherche personnalisée. L’API Suggestion automatique personnalisée retourne une liste de requêtes suggérées en fonction d’une chaîne de requête partielle fournie par l’utilisateur. Avec la Suggestion automatique personnalisée, vous fournissez des suggestions de recherche personnalisées en rapport avec votre expérience de recherche. Vous spécifiez s’il faut uniquement retourner des suggestions personnalisées ou inclure des suggestions Bing. Si les suggestions Bing sont englobées, les suggestions personnalisées apparaissent avant les suggestions fournies par Bing. Les suggestions Bing sont limitées au contexte de votre instance Recherche personnalisée.

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer rapidement, consultez [Créer votre première instance Recherche personnalisée Bing](quick-start.md).

Pour obtenir plus d’informations sur les options disponibles et personnaliser votre instance de recherche, consultez [Définir une instance de recherche personnalisée](define-your-custom-view.md).

Familiarisez-vous avec les informations de référence sur [l’API Recherche personnalisée](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference). Vous y trouverez la liste des points de terminaison, en-têtes et paramètres de requête à utiliser pour demander les résultats de la recherche. Vous y trouverez également les définitions des objets de réponse.

Pour savoir comment personnaliser des suggestions, consultez [Définir des suggestions de recherche personnalisées](define-custom-suggestions.md).

Prenez soin de lire les [exigences relatives à l’affichage et à l’utilisation de Bing](./use-and-display-requirements.md) pour respecter les règles d’utilisation des résultats de la recherche.