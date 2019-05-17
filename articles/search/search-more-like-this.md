---
title: moreLikeThis dans la Recherche Azure (préversion) - Recherche Azure
description: Documentation préliminaire pour la fonctionnalité moreLikeThis (préversion) exposée dans l’API REST Recherche Azure.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 5723f1ab7258a9e0d672b5c0fd9fd0b9c4dc8721
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522919"
---
# <a name="morelikethis-in-azure-search"></a>moreLikeThis dans la recherche Azure

> [!Note]
> moreLikeThis est en version préliminaire et les a pas été conçu pour la production. Le [API REST version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’existe aucune prise en charge du SDK .NET pour l’instant.

`moreLikeThis=[key]` est un paramètre de requête dans le [recherche Documents API](https://docs.microsoft.com/rest/api/searchservice/search-documents) qui recherche des documents similaires au document spécifié par la clé de document. Lorsqu’une requête de recherche est formulée avec `moreLikeThis`, une demande est générée en utilisant les termes de recherche extraits du document donné et qui décrivent le mieux ce document. La demande générée est ensuite utilisée pour effectuer la requête de recherche. Par défaut, le contenu de tous les champs de recherche est considérées comme, moins tous les champs restreints que vous avez spécifié à l’aide de le `searchFields` paramètre. Le paramètre `moreLikeThis` ne peut pas être utilisé avec le paramètre de recherche, `search=[string]`.

Par défaut, le contenu de tous les champs de recherche de niveau supérieur est considérés comme. Si vous souhaitez spécifier des champs spécifiques à la place, vous pouvez utiliser le `searchFields` paramètre. 

Vous ne pouvez pas utiliser moreLikeThis sur sous-Champs interrogeables dans un [type complexe](search-howto-complex-data-types.md).

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
> [Explorer les API REST de recherche Azure à l’aide de Postman](search-fiddler.md)