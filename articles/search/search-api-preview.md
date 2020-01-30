---
title: Version API REST 2019-05-06-préversion
titleSuffix: Azure Cognitive Search
description: La version API REST du service Recherche cognitive 2019-05-06-préversion, comprend des fonctionnalités expérimentales, par exemple, la base de connaissances et la mise en cache de l’indexeur pour l’enrichissement incrémentiel.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/04/2020
ms.openlocfilehash: 940ada83aeabf4bf8746ad5f90592e0917f7b403
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844443"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Version API REST du service Recherche Azure 2019-05-06-préversion

Cet article décrit la version `api-version=2019-05-06-Preview` de l’API REST du service Recherche, qui offre des fonctionnalités expérimentales pas encore mises à la disposition générale.

> [!NOTE]
> Les fonctionnalités en préversion sont disponibles pour le test et l’expérimentation en vue de recueillir des commentaires, elles sont susceptibles de changer. Nous vous conseillons vivement d’utiliser des API en version préliminaire dans les applications de production.


## <a name="new-in-2019-05-06-preview"></a>Nouveauté de 2019-05-06-Preview

+ L’[enrichissement incrémentiel (préversion)](cognitive-search-incremental-indexing-conceptual.md) ajoute la mise en cache à un pipeline d’enrichissement, ce qui vous permet de réutiliser la sortie existante si une modification ciblée, telle qu’une mise à jour d’un ensemble de compétences ou d’un autre objet, ne modifie pas le contenu. La mise en cache s’applique uniquement aux documents enrichis générés par un ensemble de compétences.

+ L’[indexeur Cosmos DB](search-howto-index-cosmosdb.md) prend en charge l’API MongoDB (préversion), l’API Gremlin (préversion) et l’API Cassandra (préversion).

+ L’[indexeur Azure Data Lake Storage Gen2 (préversion)](search-howto-index-azure-data-lake-storage.md) peut indexer du contenu et des métadonnées à partir de Data Lake Storage Gen2.

+ La [Base de connaissances](knowledge-store-concept-intro.md) est une nouvelle destination d’un pipeline d’enrichissement par IA. La structure des données physique existe dans Stockage Blob Azure et dans Stockage Table Azure, et elle est créée et remplie quand vous exécutez un indexeur auquel un ensemble de compétences cognitives est attaché. La définition d’une base de connaissances elle-même est spécifiée dans une définition d’ensemble de compétences. Dans la définition de la base de connaissances, vous contrôlez les structures physiques de vos données via des éléments de *projection* qui déterminent la façon dont les données sont mises en forme, si les données sont stockées dans Stockage Table ou dans Stockage Blob, et s’il existe plusieurs vues.

## <a name="earlier-preview-features"></a>Fonctionnalités antérieures en préversion

Les fonctionnalités annoncées dans les préversions antérieures sont toujours en préversion publique. Si vous appelez une API avec une api-version d’une préversion antérieure, vous pouvez continuer à utiliser cette version ou basculer sur `2019-05-06-Preview` sans aucun effet sur le comportement attendu.

+ Le [paramètre de requête moreLike This](search-more-like-this.md) permet de rechercher des documents correspondant à un document spécifique. Cette fonctionnalité existait dans les préversions antérieures. 

+ L’[indexation des objets blob CSV](search-howto-index-csv-blobs.md) crée un document par ligne, par opposition à un document par l’objet blob de texte.

## <a name="how-to-call-a-preview-api"></a>Comment appeler une API en préversion

Les préversions plus anciennes sont toujours opérationnelles mais deviennent obsolètes au fil du temps. Si votre code appelle `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, ces appels sont toujours valides. Toutefois, seule la dernière préversion est actualisée avec des améliorations. 

La syntaxe de l’exemple suivant illustre un appel à l’API en préversion.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Le service Recherche cognitive Azure est disponible sous plusieurs versions. Pour plus d’informations, consultez [Versions d’API](search-api-versions.md).

## <a name="next-steps"></a>Étapes suivantes

Consultez la documentation de référence relative à l’API REST du service Recherche. Si vous rencontrez des problèmes, sollicitez notre aide sur [StackOverflow](https://stackoverflow.com/) ou [contactez le support](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Référence de l’API REST du service Recherche](https://docs.microsoft.com/rest/api/searchservice/)