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
ms.openlocfilehash: d18069335bb20f78a5bcda85eb6fcb2a5abe75f7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024672"
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis dans la Recherche Azure (préversion)

`moreLikeThis=[key]` est un paramètre de requête dans le [recherche Documents API](https://docs.microsoft.com/rest/api/searchservice/search-documents) qui recherche des documents similaires au document spécifié par la clé de document. Lorsqu’une requête de recherche est formulée avec `moreLikeThis`, une demande est générée en utilisant les termes de recherche extraits du document donné et qui décrivent le mieux ce document. La demande générée est ensuite utilisée pour effectuer la requête de recherche. Par défaut, le contenu de tous les champs de recherche est considérées comme, moins tous les champs restreints que vous avez spécifié à l’aide de le `searchFields` paramètre. Le paramètre `moreLikeThis` ne peut pas être utilisé avec le paramètre de recherche, `search=[string]`.

Par défaut, le contenu de tous les champs de recherche de niveau supérieur est considérés comme. Si vous souhaitez spécifier des champs spécifiques à la place, vous pouvez utiliser le `searchFields` paramètre. 

> [!NOTE]
> `moreLikeThis` version préliminaire ne fonctionne pas sur des sous-Champs interrogeables dans un [type complexe](search-howto-complex-data-types.md).

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

## <a name="feature-availability"></a>Disponibilité des fonctionnalités

Le `moreLikeThis` paramètre est disponible en version préliminaire API REST uniquement (`api-version=2019-05-06-Preview`).

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez utiliser n’importe quel outil de test web pour faire des essais avec cette fonctionnalité.  Nous vous recommandons d’utiliser Postman pour cet exercice.

> [!div class="nextstepaction"]
> [Explorer les API REST de recherche Azure à l’aide de Postman](search-fiddler.md)