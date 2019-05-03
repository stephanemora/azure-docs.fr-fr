---
title: Afficher un aperçu des API REST pour Azure Search 2019-05-06-Preview - recherche Azure
description: Azure Search Service REST API Version 2019-05-06-Preview comprend des fonctionnalités expérimentales telles que le magasin de la base de connaissances et les clés de chiffrement gérées par le client.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 91c58507d8758a65772110afba71354deecd3b12
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024280"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Version de l’api REST de Service de recherche Azure 2019-05-06-Preview
Cet article décrit la version `api-version=2019-05-06-Preview` de l’API REST du service Recherche Azure, qui offre des fonctionnalités expérimentales pas encore mises à la disposition générale.

> [!NOTE]
> Les fonctionnalités en préversion sont disponibles pour le test et l’expérimentation en vue de recueillir des commentaires, elles sont susceptibles de changer. Nous vous conseillons vivement d’utiliser des API en version préliminaire dans les applications de production.


## <a name="new-in-2019-05-06-preview"></a>Nouveautés de 2019-05-06-Preview

[**Magasin de la base de connaissances** ](knowledge-store-concept-intro.md) est une nouvelle destination d’un pipeline d’enrichissement par intelligence artificielle. En plus d’un index, vous pouvez maintenant conserver les structures de données remplis créés lors de l’indexation dans le stockage Azure. Vous contrôlez les structures physiques de vos données via les éléments de compétences, y compris la façon dont les données sont mises en forme, si les données sont stockées dans le stockage de Table ou de stockage d’objets Blob, et s’il existe plusieurs vues.

[**Clés de chiffrement gérées par le client** ](search-security-manage-encryption-keys.md) pour le chiffrement au repos côté service est également une nouvelle fonctionnalité préliminaire. Outre l’intégrés chiffrement au repos géré par Microsoft, vous pouvez appliquer une couche supplémentaire d’où vous êtes l’unique propriétaire des clés de chiffrement.

## <a name="other-preview-features"></a>Autres fonctionnalités en préversion

Les fonctionnalités annoncées dans les préversions antérieures sont toujours en préversion publique. Si vous appelez une API avec une api-version d’une préversion antérieure, vous pouvez continuer à utiliser cette version ou basculer sur `2019-05-06-Preview` sans aucun effet sur le comportement attendu.

+ Le [paramètre de requête moreLike This](search-more-like-this.md) permet de rechercher des documents correspondant à un document spécifique. Cette fonctionnalité existait dans les préversions antérieures. 


## <a name="how-to-call-a-preview-api"></a>Comment appeler une API en préversion

Les préversions plus anciennes sont toujours opérationnelles mais deviennent obsolètes au fil du temps. Si votre code appelle `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, ces appels sont toujours valides. Toutefois, seule la dernière préversion est actualisée avec des améliorations. 

La syntaxe de l’exemple suivant illustre un appel à l’API en préversion.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Le service Azure Search est disponible dans plusieurs versions. Pour plus d’informations, consultez [Versions d’API](search-api-versions.md).

## <a name="next-steps"></a>Étapes suivantes

Passez en revue la documentation de référence API REST de Service de recherche Azure. Si vous rencontrez des problèmes, nous demander de l’aide sur [StackOverflow](https://stackoverflow.com/) ou [contactez le support technique](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Référence de l’API REST du service de recherche](https://docs.microsoft.com/rest/api/searchservice/)