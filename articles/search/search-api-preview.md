---
title: Version API REST 2019-05-06-préversion
titleSuffix: Azure Cognitive Search
description: La version API REST du service Recherche cognitive 2019-05-06-préversion, comprend des fonctionnalités expérimentales, par exemple, la base de connaissances et les clés de chiffrement gérées par le client.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24e16942410c72640628bd4120d05a85e68de993
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720027"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Version API REST du service Recherche Azure 2019-05-06-préversion

Cet article décrit la version `api-version=2019-05-06-Preview` de l’API REST du service Recherche, qui offre des fonctionnalités expérimentales pas encore mises à la disposition générale.

> [!NOTE]
> Les fonctionnalités en préversion sont disponibles pour le test et l’expérimentation en vue de recueillir des commentaires, elles sont susceptibles de changer. Nous vous conseillons vivement d’utiliser des API en version préliminaire dans les applications de production.


## <a name="new-in-2019-05-06-preview"></a>Nouveauté de 2019-05-06-Preview

+ L’[indexation incrémentielle](cognitive-search-incremental-indexing-conceptual.md) est un nouveau mode d’indexation qui ajoute l’état et la mise en cache à un ensemble de compétences, ce qui vous permet de réutiliser la sortie existante quand les définitions des données sources, des indexeurs et des ensembles de compétences sont inchangées. Cette fonctionnalité s’applique uniquement aux enrichissements définis pour un ensemble de compétences cognitives.

+ L’[indexeur Cosmos DB](search-howto-index-cosmosdb.md) prend en charge l’API MongoDB, l’API Gremlin et l’API Cassandra.

+ L’[indexeur Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) peut indexer du contenu et des métadonnées à partir de Data Lake Storage Gen2.

+ L’[Extraction de document (préversion)](cognitive-search-skill-document-extraction.md) est une compétence cognitive utilisée lors de l’indexation, qui vous permet d’extraire le contenu d’un fichier à partir d’un ensemble de compétences. Auparavant, le craquage de document avait exclusivement lieu avant l’exécution de l’ensemble de compétences. Avec l’ajout de cette compétence, vous pouvez également effectuer cette opération dans le cadre de l’exécution de l’ensemble de compétences.

+ La [Traduction de texte (préversion)](cognitive-search-skill-text-translation.md) est une compétence cognitive utilisée lors de l’indexation, qui évalue le texte et qui, pour chaque enregistrement, retourne le texte traduit dans la langue cible spécifiée.

+ La [Base de connaissances](knowledge-store-concept-intro.md) est une nouvelle destination d’un pipeline d’enrichissement par IA. La structure des données physique existe dans Stockage Blob Azure et dans Stockage Table Azure, et elle est créée et remplie quand vous exécutez un indexeur auquel un ensemble de compétences cognitives est attaché. La définition d’une base de connaissances elle-même est spécifiée dans une définition d’ensemble de compétences. Dans la définition de la base de connaissances, vous contrôlez les structures physiques de vos données via des éléments de *projection* qui déterminent la façon dont les données sont mises en forme, si les données sont stockées dans Stockage Table ou dans Stockage Blob, et s’il existe plusieurs vues.

+ Les [clés de chiffrement gérées par le client](search-security-manage-encryption-keys.md) pour le chiffrement au repos côté service constituent également une nouvelle fonctionnalité en préversion. Outre le chiffrement au repos intégré géré par Microsoft, vous pouvez appliquer une couche supplémentaire de chiffrement lorsque vous êtes l’unique propriétaire des clés.

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