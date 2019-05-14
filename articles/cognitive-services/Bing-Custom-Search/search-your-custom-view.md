---
title: Rechercher une vue personnalisée | Recherche personnalisée Bing
titlesuffix: Azure Cognitive Services
description: Explique comment rechercher une vue personnalisée du web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: maheshb
ms.openlocfilehash: 7a60ea934c6bb9008889992726ddca5dad21a640
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595612"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Appeler votre instance de Recherche personnalisée Bing à partir du portail

Une fois votre expérience de recherche personnalisée configurée, vous pouvez la tester à partir du [portail](https://customsearch.ai) Recherche personnalisée Bing. 

![une capture d’écran du portail de recherche personnalisée Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Créer une requête de recherche 

Une fois connecté au [portail](https://customsearch.ai) Recherche personnalisée Bing, sélectionnez votre instance de recherche et cliquez sur l'onglet **Production**. Sous **Points de terminaison**, sélectionnez le point de terminaison d'une API (par exemple, de l'API Web). Les points de terminaison affichés dépendent de votre abonnement.

Pour créer une requête de recherche, entrez les valeurs des paramètres de votre point de terminaison. Notez que les paramètres affichés sur le portail peuvent varier en fonction du point de terminaison choisi. Consultez le [référence de l’API recherche personnalisée](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) pour plus d’informations. Pour modifier l’abonnement qu'utilise votre instance de recherche, ajoutez la clé de l’abonnement approprié et mettre à jour les paramètres de mise sur le marché et/ou de langage appropriés.

Les paramètres suivants sont importants :


|Paramètre  |Description   |
|---------|---------|
|Interroger     | Terme à rechercher. Uniquement disponible pour les points de terminaison Web, Image, Vidéo et Suggestion automatique |
|ID de configuration personnalisée | L'ID de configuration de l'instance de Recherche personnalisée sélectionnée. Ce champ est en lecture seule. |
|Market     | Marché d'où proviendront les résultats. Uniquement disponible pour les points de terminaison Web, Image, Vidéo et Interface utilisateur hébergée.        |
|Clé d'abonnement | Clé d'abonnement à utiliser pour le test. Vous pouvez sélectionner une clé dans la liste déroulante ou en entrer une manuellement.          |

Si vous cliquez sur **Paramètres supplémentaires**, les paramètres suivants s’affichent :  

|Paramètre  |Description   |
|---------|---------|
|Safe Search     | Filtre servant à exclure le contenu pour adultes des pages web. Uniquement disponible pour les points de terminaison Web, Image, Vidéo et Interface utilisateur hébergée.        |
|Langue de l'interface utilisateur    | Langue utilisée pour les chaînes de l'interface utilisateur. Par exemple, si vous activez des images et des vidéos dans l’interface utilisateur hébergée, les onglets **Image** et **Vidéo** utilisent la langue spécifiée.        |
|Nombre     | Nombre de résultats de recherche à renvoyer dans la réponse. Disponible uniquement pour les points de terminaison Web, Image et vidéo.         |
|Offset    | Nombre de résultats de recherche à ignorer avant de renvoyer les résultats. Disponible uniquement pour les points de terminaison Web, Image et vidéo.        |
    
Après avoir spécifié toutes les options nécessaires, cliquez sur **Appeler** pour voir la réponse JSON dans le volet droit. Si vous sélectionnez le point de terminaison Interface utilisateur hébergée, vous pouvez tester l’expérience de recherche dans le volet inférieur.

## <a name="change-your-bing-custom-search-subscription"></a>Modifier votre abonnement recherche personnalisée Bing

Vous pouvez modifier l’abonnement associé à votre instance de recherche personnalisée Bing sans créer une nouvelle instance. Pour que les appels d’API envoyé et facturés à un nouvel abonnement, créez une nouvelle ressource de recherche personnalisée Bing dans le portail Azure. Utiliser la nouvelle clé d’abonnement dans les demandes d’API, ainsi que votre ID de configuration personnalisée. d’instance

## <a name="next-steps"></a>Étapes suivantes

- [Appeler votre vue personnalisée avec C#](./call-endpoint-csharp.md)
- [Appeler votre vue personnalisée avec Java](./call-endpoint-java.md)
- [Appeler votre vue personnalisée avec NodeJs](./call-endpoint-nodejs.md)
- [Appeler votre vue personnalisée avec Python](./call-endpoint-python.md)

- [Appeler votre vue personnalisée avec le SDK C#](./sdk-csharp-quick-start.md)
