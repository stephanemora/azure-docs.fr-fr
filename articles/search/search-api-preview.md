---
title: Afficher un aperçu de l’API REST pour Azure Search 2019-05-06-Preview – Recherche Azure
description: L’API REST du service Recherche Azure, version 2019-05-06-Preview, comprend des fonctionnalités expérimentales, par exemple, la base de connaissances et les clés de chiffrement gérées par le client.
services: search
author: HeidiSteen
manager: nitinme
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7fa280742556b7bc42d2c7fb30c880f836eef62b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649982"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>API REST du service Recherche Azure, version 2019-05-06-Preview
Cet article décrit la version `api-version=2019-05-06-Preview` de l’API REST du service Recherche Azure, qui offre des fonctionnalités expérimentales pas encore mises à la disposition générale.

> [!NOTE]
> Les fonctionnalités en préversion sont disponibles pour le test et l’expérimentation en vue de recueillir des commentaires, elles sont susceptibles de changer. Nous vous conseillons vivement d’utiliser des API en version préliminaire dans les applications de production.


## <a name="new-in-2019-05-06-preview"></a>Nouveauté de 2019-05-06-Preview

[**Base de connaissances**](knowledge-store-concept-intro.md) est une nouvelle destination d’un pipeline d’enrichissement par intelligence artificielle. En plus d’un index, vous pouvez maintenant conserver des structures de données remplis créées lors de l’indexation dans le stockage Azure. Vous contrôlez les structures physiques de vos données via les éléments dans un ensemble de compétences, y compris la façon dont les données sont mises en forme, si elles sont stockées dans le stockage de table ou un stockage Blob, et s’il existe plusieurs vues.

[**Clés de chiffrement gérées par le client**](search-security-manage-encryption-keys.md) pour le chiffrement au repos côté service est également une nouvelle fonctionnalité d’évaluation. Outre le chiffrement au repos intégré géré par Microsoft, vous pouvez appliquer une couche supplémentaire de chiffrement lorsque vous êtes l’unique propriétaire des clés.

## <a name="other-preview-features"></a>Autres fonctionnalités en préversion

Les fonctionnalités annoncées dans les préversions antérieures sont toujours en préversion publique. Si vous appelez une API avec une api-version d’une préversion antérieure, vous pouvez continuer à utiliser cette version ou basculer sur `2019-05-06-Preview` sans aucun effet sur le comportement attendu.

+ Le [paramètre de requête moreLike This](search-more-like-this.md) permet de rechercher des documents correspondant à un document spécifique. Cette fonctionnalité existait dans les préversions antérieures. 
* L’[indexation des objets blob CSV](search-howto-index-csv-blobs.md) crée un document par ligne, par opposition à un document par l’objet blob de texte.
* La [prise en charge de l’API MongoDB pour les indexeurs Cosmos DB](search-howto-index-cosmosdb.md) est en préversion.


## <a name="how-to-call-a-preview-api"></a>Comment appeler une API en préversion

Les préversions plus anciennes sont toujours opérationnelles mais deviennent obsolètes au fil du temps. Si votre code appelle `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, ces appels sont toujours valides. Toutefois, seule la dernière préversion est actualisée avec des améliorations. 

La syntaxe de l’exemple suivant illustre un appel à l’API en préversion.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Le service Azure Search est disponible dans plusieurs versions. Pour plus d’informations, consultez [Versions d’API](search-api-versions.md).

## <a name="next-steps"></a>Étapes suivantes

Consultez la documentation de référence relative à l’API REST du service Recherche Azure. Si vous rencontrez des problèmes, sollicitez notre aide sur [StackOverflow](https://stackoverflow.com/) ou [contactez le support](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Référence de l’API REST du service Recherche](https://docs.microsoft.com/rest/api/searchservice/)