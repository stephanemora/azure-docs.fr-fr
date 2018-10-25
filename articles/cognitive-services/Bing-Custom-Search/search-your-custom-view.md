---
title: Rechercher une vue personnalisée | Recherche personnalisée Bing
titlesuffix: Azure Cognitive Services
description: Explique comment rechercher une vue personnalisée du web.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: maheshb
ms.openlocfilehash: 15c5b3c58c4f3617111707ed82d031b67b6ad4c1
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465133"
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

    - **Query** (Requête) : le terme de recherche à rechercher. Disponible uniquement pour les points de terminaison Web, Image, Vidéo et Suggestion automatique.
    - **Custom Configuration ID** (ID de configuration personnalisée) : l’ID de configuration de l’instance Recherche personnalisée sélectionnée. Ce champ est en lecture seule.
    - **Market** (Marché) : le marché d’où proviennent les résultats. Disponible uniquement pour les points de terminaison Web, Image, Vidéo et Interface utilisateur hébergée.
    - **Subscription Key** (Clé d’abonnement) : la clé d’abonnement pour le test. Vous pouvez sélectionner une clé dans la liste déroulante ou en entrer une manuellement.  
      
    Si vous cliquez sur **Paramètres supplémentaires**, les paramètres suivants s’affichent :  
      
    - **Safe Search** (Recherche sécurisée) : un filtre servant à exclure le contenu pour adultes des pages web. Disponible uniquement pour les points de terminaison Web, Image, Vidéo et Interface utilisateur hébergée.
    - **Langue de l’interface utilisateur** : langue utilisée pour les chaînes de l’interface utilisateur. Par exemple, si vous activez des images et des vidéos dans l’interface utilisateur hébergée, les onglets **Image** et **Vidéo** utilisent la langue spécifiée.
    - **Count** (Nombre de) : le nombre de résultats de recherche à retourner dans la réponse. Disponible uniquement pour les points de terminaison Web, Image et vidéo.
    - **Décalage** : Nombre de résultats de recherche à ignorer avant de retourner les résultats. Disponible uniquement pour les points de terminaison Web, Image et vidéo.

6. Après avoir spécifié toutes les options nécessaires, cliquez sur **Appeler** pour voir la réponse JSON dans le volet droit. 

Si vous sélectionnez le point de terminaison Interface utilisateur hébergée, vous pouvez tester l’expérience de recherche dans le volet inférieur.

## <a name="next-steps"></a>Étapes suivantes

- [Appeler votre vue personnalisée avec C#](./call-endpoint-csharp.md)
- [Appeler votre vue personnalisée avec Java](./call-endpoint-java.md)
- [Appeler votre vue personnalisée avec NodeJs](./call-endpoint-nodejs.md)
- [Appeler votre vue personnalisée avec Python](./call-endpoint-python.md)

- [Appeler votre vue personnalisée avec le SDK C#](./sdk-csharp-quick-start.md)