---
title: 'Recherche personnalisée Bing : Obtenir des suggestions personnalisées de Suggestion automatique | Microsoft Docs'
description: Décrit comment récupérer des suggestions personnalisées de Suggestion automatique
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 73059038018602f7aa76377bf7c98d4658576576
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368828"
---
# <a name="get-custom-suggestions"></a>Obtenir des suggestions personnalisées
Avant l’envoi des requêtes à la Recherche personnalisée Bing, appelez l’API Suggestion automatique personnalisée pour faire des suggestions de termes de recherche et améliorer l’expérience de recherche. L’API Suggestion automatique personnalisée retourne une liste de requêtes suggérées qui s’appuient sur une chaîne de requête partielle fournie par l’utilisateur. Tout terme de requête personnalisée pertinent que vous spécifiez est placé avant les suggestions émises par la Suggestion automatique. Pour plus d’informations, consultez [Définir les suggestions de la recherche personnalisée](define-custom-suggestions.md).

## <a name="endpoint"></a>Point de terminaison
Pour obtenir des suggestions de requête à l’aide de l’API Recherche personnalisée Bing, envoyez une requête `GET` au point de terminaison suivant.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

## <a name="response-json"></a>Réponse JSON
La réponse contient une liste d’objets SearchAction contenant les termes de requête suggérés.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Chaque suggestion comporte les champs `displayText` et `query`. Le champ `displayText` correspond à la requête suggérée que vous utilisez pour remplir la liste déroulante de la zone de recherche.

Si l’utilisateur sélectionne une requête suggérée dans la liste déroulante, utilisez le terme de requête dans le champ `query` lors de l’appel de l’[API Recherche personnalisée Bing](overview.md).

## <a name="throttling-requests"></a>Demandes de limitation

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Appeler votre recherche personnalisée](./search-your-custom-view.md)
- [Configurer votre expérience d’interface utilisateur hébergée](./hosted-ui.md)