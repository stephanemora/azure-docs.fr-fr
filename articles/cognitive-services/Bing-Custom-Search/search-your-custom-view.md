---
title: 'Recherche personnalisée Bing : Rechercher une vue personnalisée | Microsoft Docs'
description: Décrit comment rechercher une vue personnalisée du web
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 7a2db7881dfe7efedb1a83637a6281f786652958
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964886"
---
# <a name="call-your-custom-search"></a>Appeler votre recherche personnalisée

Avant d’effectuer votre premier appel à l’API Recherche personnalisée pour obtenir les résultats de recherche de votre instance, vous devez obtenir une clé d’abonnement Cognitive Services. Pour obtenir une clé pour l’API Recherche personnalisée, consultez [Essayer Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).


## <a name="try-it-out"></a>Faites un essai

Une fois que vous avez configuré votre expérience de recherche personnalisée, vous pouvez tester sa configuration depuis le portail de la Recherche personnalisée. 

1. Connectez-vous à [Recherche personnalisée](https://customsearch.ai).
2. Cliquez sur une instance Recherche personnalisée dans votre liste d’instances.
3. Cliquez sur l’onglet **Production**. 
4. Sous l’onglet **Points de terminaison**, sélectionnez un point de terminaison (par exemple, API web). Votre abonnement détermine les points de terminaison qui s’affichent (voir les [prix](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) des options d’abonnement). 
5. Spécifiez les valeurs de paramètre. 

    Les éléments suivants sont les paramètres possibles que vous pouvez définir (la liste varie selon le point de terminaison sélectionné). Pour plus d’informations sur ces paramètres, consultez la référence [API Recherche personnalisée](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).

    - **Requête** : terme à rechercher. Disponible uniquement pour les points de terminaison Web, Image, Vidéo et Suggestion automatique.
    - **ID de configuration personnalisée** : ID de configuration de l’instance Recherche personnalisée sélectionnée. Ce champ est en lecture seule.
    - **Marché** : marché d’où proviennent les résultats. Disponible uniquement pour les points de terminaison Web, Image, Vidéo et Interface utilisateur hébergée.
    - **Clé d’abonnement** : clé d’abonnement pour le test. Vous pouvez sélectionner une clé dans la liste déroulante ou en entrer une manuellement.  
      
    Si vous cliquez sur **Paramètres supplémentaires**, les paramètres suivants s’affichent :  
      
    - **Recherche sécurisée** : filtre servant à exclure le contenu pour adultes des pages web. Disponible uniquement pour les points de terminaison Web, Image, Vidéo et Interface utilisateur hébergée.
    - **Langue de l’interface utilisateur** : langue utilisée pour les chaînes de l’interface utilisateur. Par exemple, si vous activez des images et des vidéos dans l’interface utilisateur hébergée, les onglets **Image** et **Vidéo** utilisent la langue spécifiée.
    - **Nombre** : nombre de résultats de recherche à retourner dans la réponse. Disponible uniquement pour les points de terminaison Web, Image et vidéo.
    - **Décalage** : Nombre de résultats de recherche à ignorer avant de retourner les résultats. Disponible uniquement pour les points de terminaison Web, Image et vidéo.

6. Après avoir spécifié toutes les options nécessaires, cliquez sur **Appeler** pour voir la réponse JSON dans le volet droit. 

Si vous sélectionnez le point de terminaison Interface utilisateur hébergée, vous pouvez tester l’expérience de recherche dans le volet inférieur.

## <a name="next-steps"></a>Étapes suivantes

- [Appeler votre vue personnalisée avec C#](./call-endpoint-csharp.md)
- [Appeler votre vue personnalisée avec Java](./call-endpoint-java.md)
- [Appeler votre vue personnalisée avec NodeJs](./call-endpoint-nodejs.md)
- [Appeler votre vue personnalisée avec Python](./call-endpoint-python.md)

- [Appeler votre vue personnalisée avec le SDK C#](./sdk-csharp-quick-start.md)