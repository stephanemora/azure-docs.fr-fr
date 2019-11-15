---
title: Fonctionnalité de requête moreLikeThis (préversion)
titleSuffix: Azure Cognitive Search
description: Décrit la fonctionnalité moreLikeThis (préversion), qui est disponible dans les versions préliminaires de l’API REST de Recherche cognitive Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fdde89f9ff88b15c464af805b81708b268e5ddf5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721736"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (préversion) dans Recherche cognitive Azure

> [!IMPORTANT] 
> Cette fonctionnalité est actuellement disponible en préversion publique. Les fonctionnalités en préversion sont fournies sans contrat de niveau de service et ne sont pas recommandées pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). L’[API REST version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’y a actuellement pas de prise en charge du portail ou du SDK .NET.

`moreLikeThis=[key]` est un paramètre de requête dans [L’API Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Recherche de documents) qui recherche des documents similaires au document spécifié par la clé de document. Lorsqu’une requête de recherche est formulée avec `moreLikeThis`, une demande est générée en utilisant les termes de recherche extraits du document donné et qui décrivent le mieux ce document. La demande générée est ensuite utilisée pour effectuer la requête de recherche. Par défaut, le contenu de tous les champs pouvant faire l’objet d’une recherche est pris en compte, à l’exception des champs restreints que vous avez spécifiés à l’aide du paramètre `searchFields`. Le paramètre `moreLikeThis` ne peut pas être utilisé avec le paramètre de recherche, `search=[string]`.

Par défaut, le contenu de tous les champs de niveau supérieur pouvant faire l’objet d’une recherche est pris en compte. Si vous souhaitez spécifier des champs spécifiques à la place, vous pouvez utiliser le paramètre `searchFields`. 

Vous ne pouvez pas utiliser moreLikeThis sur des sous-champs pouvant faire l’objet d’une recherche dans un [type complexe](search-howto-complex-data-types.md).

## <a name="examples"></a>Exemples 

Voici l’exemple d’une requête moreLikeThis. La requête recherche les documents dont les champs de description sont les plus proches du champ du document source, tel que spécifié par le paramètre `moreLikeThis`.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez utiliser n’importe quel outil de test web pour faire des essais avec cette fonctionnalité.  Nous vous recommandons d’utiliser Postman pour cet exercice.

> [!div class="nextstepaction"]
> [Explorer les API REST de Recherche cognitive Azure avec Postman](search-get-started-postman.md)