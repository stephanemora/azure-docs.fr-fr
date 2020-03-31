---
title: Rechercher une vue personnalisée | Recherche personnalisée Bing
titleSuffix: Azure Cognitive Services
description: Une fois votre expérience de recherche personnalisée configurée, vous pouvez la tester à partir du portail Recherche personnalisée Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: f00ffee47e3eb6366d632d8b6ee9beb01f048442
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76983110"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Appeler votre instance de Recherche personnalisée Bing à partir du portail

Une fois votre expérience de recherche personnalisée configurée, vous pouvez la tester à partir du [portail](https://customsearch.ai) Recherche personnalisée Bing. 

![capture d’écran du portail Recherche personnalisée Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Créer une requête de recherche 

Une fois connecté au [portail](https://customsearch.ai) Recherche personnalisée Bing, sélectionnez votre instance de recherche et cliquez sur l'onglet **Production**. Sous **Points de terminaison**, sélectionnez le point de terminaison d'une API (par exemple, de l'API Web). Les points de terminaison affichés dépendent de votre abonnement.

Pour créer une requête de recherche, entrez les valeurs des paramètres de votre point de terminaison. Notez que les paramètres affichés sur le portail peuvent varier en fonction du point de terminaison choisi. Pour plus d’informations, consultez [Référence sur l’API Recherche personnalisée](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters). Pour modifier l’abonnement utilisée par votre instance de recherche, ajoutez la clé d’abonnement appropriée et mettez à jour les paramètres de mise sur le marché et/ou de langue appropriés.

Les paramètres suivants sont importants :


|Paramètre  |Description  |
|---------|---------|
|Requête     | Terme à rechercher. Uniquement disponible pour les points de terminaison Web, Image, Vidéo et Suggestion automatique |
|ID de configuration personnalisée | L'ID de configuration de l'instance de Recherche personnalisée sélectionnée. Ce champ est en lecture seule. |
|Market     | Marché d'où proviendront les résultats. Uniquement disponible pour les points de terminaison Web, Image, Vidéo et Interface utilisateur hébergée.        |
|Clé d'abonnement | Clé d'abonnement à utiliser pour le test. Vous pouvez sélectionner une clé dans la liste déroulante ou en entrer une manuellement.          |

Si vous cliquez sur **Paramètres supplémentaires**, les paramètres suivants s’affichent :  

|Paramètre  |Description  |
|---------|---------|
|Safe Search     | Filtre servant à exclure le contenu pour adultes des pages web. Uniquement disponible pour les points de terminaison Web, Image, Vidéo et Interface utilisateur hébergée. Notez que Recherche de vidéos personnalisées Bing prend uniquement en charge deux valeurs : `moderate` et `strict`.        |
|Langue de l'interface utilisateur    | Langue utilisée pour les chaînes de l'interface utilisateur. Par exemple, si vous activez des images et des vidéos dans l’interface utilisateur hébergée, les onglets **Image** et **Vidéo** utilisent la langue spécifiée.        |
|Count     | Nombre de résultats de recherche à renvoyer dans la réponse. Disponible uniquement pour les points de terminaison Web, Image et vidéo.         |
|Offset    | Nombre de résultats de recherche à ignorer avant de renvoyer les résultats. Disponible uniquement pour les points de terminaison Web, Image et vidéo.        |
    
Après avoir spécifié toutes les options nécessaires, cliquez sur **Appeler** pour voir la réponse JSON dans le volet droit. Si vous sélectionnez le point de terminaison Interface utilisateur hébergée, vous pouvez tester l’expérience de recherche dans le volet inférieur.

## <a name="change-your-bing-custom-search-subscription"></a>Modifier votre abonnement Recherche personnalisée Bing

Vous pouvez modifier l’abonnement associé à votre instance de Recherche personnalisée Bing sans créer de nouvelle instance. Pour que les appels d’API soient envoyés et facturés à un nouvel abonnement, créez une nouvelle ressource Recherche personnalisée Bing dans le portail Azure. Utilisez la nouvelle clé d’abonnement dans vos requêtes d’API, ainsi que l’ID de configuration personnalisée de votre instance.

## <a name="next-steps"></a>Étapes suivantes

- [Appeler votre vue personnalisée avec C#](./call-endpoint-csharp.md)
- [Appeler votre vue personnalisée avec Java](./call-endpoint-java.md)
- [Appeler votre vue personnalisée avec NodeJs](./call-endpoint-nodejs.md)
- [Appeler votre vue personnalisée avec Python](./call-endpoint-python.md)

- [Appeler votre vue personnalisée avec le SDK C#](./sdk-csharp-quick-start.md)
