---
title: Suggestion automatique des termes de recherche - API Recherche Web Bing
titleSuffix: Azure Cognitive Services
description: Associez l’API Recherche Web Bing et l’API Suggestion automatique Bing pour fournir une expérience de recherche améliorée aux utilisateurs.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 5bd5ff0e49b125277255ec8e5c216583d75043a0
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381131"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Suggestion automatique des termes de recherche Bing dans votre application

> [!WARNING]
> Les API Recherche Bing passent de Cognitive Services aux services de recherche Bing. À compter du **30 octobre 2020** , toutes les nouvelles instances de Recherche Bing doivent être provisionnées en suivant le processus documenté [ici](https://aka.ms/cogsvcs/bingmove).
> Les API Recherche Bing provisionnées à l’aide de Cognitive Services seront prises en charge les trois prochaines années ou jusqu’à la fin de votre Accord Entreprise, selon la première éventualité.
> Pour obtenir des instructions de migration, consultez [Services de recherche Bing](https://aka.ms/cogsvcs/bingmigration).

Si vous fournissez une zone de recherche dans laquelle l’utilisateur entre son terme de recherche, utilisez [l’API Suggestion automatique Bing](../bing-autosuggest/get-suggested-search-terms.md) pour améliorer l’expérience. À mesure que l’utilisateur tape des termes de recherche, l’API suggère des chaînes de requête.

Lorsque l’utilisateur saisit un terme de recherche, il doit être codé avec URL avant que le paramètre de requête [q](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) soit défini. Par exemple, si l’utilisateur entre *sailing dinghies* , définissez `q` avec la valeur `sailing+dinghies` ou `sailing%20dinghies`.

Si le terme de recherche contient une faute d’orthographe, la réponse à la recherche inclut un objet [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext). L’objet affiche l’orthographe d’origine et l’orthographe corrigée utilisées par Bing pour la recherche.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Vous pouvez utiliser ces informations pour signaler à l’utilisateur que vous avez modifié sa chaîne de requête lorsque vous affichez les résultats de la recherche.

![Exemple d’expérience utilisateur pour un contexte de requête](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Étapes suivantes  

* Vérifiez l’exemple avec l’article [Réponses de l’API Recherche Web Bing](search-responses.md).  

## <a name="see-also"></a>Voir aussi  

* [Référence pour l’API Recherche Web Bing](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)