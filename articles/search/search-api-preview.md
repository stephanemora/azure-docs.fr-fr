---
title: Fonctionnalités d’évaluation dans l’API REST
titleSuffix: Azure Cognitive Search
description: La version API REST du service Recherche cognitive Azure 2019-05-06-Preview comprend des fonctionnalités expérimentales telles que la base de connaissances et la mise en cache de l’indexeur pour l’enrichissement incrémentiel.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: fd21a4b821e1911e94d542a0922e5269786c365d
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76991063"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Fonctionnalités d’évaluation dans Recherche cognitive Azure

Cet article liste les fonctionnalités actuellement en préversion. Les fonctionnalités qui passent de la préversion à la disponibilité générale sont supprimées de cette liste. Vous pouvez vérifier les [Mises à jour de service](https://azure.microsoft.com/updates/?product=search) ou les [Nouveautés](whats-new.md) pour accéder aux annonces concernant la disponibilité générale.

Alors que certaines fonctionnalités d’évaluation peuvent être disponibles dans le portail et le SDK .NET, l’API REST dispose toujours de fonctionnalités d’évaluation. 

+ Pour les opérations de recherche, la version actuelle de l’API en préversion est [`2019-05-06-Preview`](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview).
+ Pour les opérations de gestion, la préversion actuelle est [`2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview).

> [!IMPORTANT]
> Les fonctionnalités en préversion sont fournies sans contrat de niveau de service et ne sont pas recommandées pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Fonctionnalités d’enrichissement par intelligence artificielle

Explorez les dernières améliorations apportées à l’enrichissement par IA par le biais de l’[API Recherche en préversion](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview).

+ La [compétence Recherche d’entité personnalisée (préversion)](cognitive-search-skill-custom-entity-lookup.md ) est une compétence cognitive qui recherche du texte dans une liste personnalisée de mots et d’expressions définie par l’utilisateur. À l’aide de cette liste, elle étiquète tous les documents contenant des entités correspondantes. La compétence prend également en charge un degré de correspondance approximative qui peut être appliqué pour rechercher des correspondances similaires sans être rigoureusement exactes. 

+ La [compétence Détection PII (préversion) ](cognitive-search-skill-pii-detection.md) est une compétence cognitive utilisée lors de l’indexation, qui extrait des informations d’identification personnelle d’un texte en entrée, et vous donne la possibilité de les masquer dans ce texte de différentes façons.

+ L’[enrichissement incrémentiel (préversion)](cognitive-search-incremental-indexing-conceptual.md) ajoute la mise en cache à un pipeline d’enrichissement, ce qui vous permet de réutiliser la sortie existante si une modification ciblée, telle qu’une mise à jour d’un ensemble de compétences ou d’un autre objet, ne modifie pas le contenu. La mise en cache s’applique uniquement aux documents enrichis générés par un ensemble de compétences.

+ La [Base de connaissances (préversion)](knowledge-store-concept-intro.md) est une nouvelle destination d’un pipeline d’enrichissement par IA. La structure des données physique existe dans Stockage Blob Azure et dans Stockage Table Azure, et elle est créée et remplie quand vous exécutez un indexeur auquel un ensemble de compétences cognitives est attaché. La définition d’une base de connaissances elle-même est spécifiée dans une définition d’ensemble de compétences. Dans la définition de la base de connaissances, vous contrôlez les structures physiques de vos données via des éléments de *projection* qui déterminent la façon dont les données sont mises en forme, si les données sont stockées dans Stockage Table ou dans Stockage Blob, et s’il existe plusieurs vues.

## <a name="indexing-and-query-features"></a>Fonctionnalités d’indexation et de requête

Les fonctionnalités d’évaluation de l’indexeur sont disponibles dans l’API Recherche en préversion. 

+ L’[indexeur Cosmos DB](search-howto-index-cosmosdb.md) prend en charge l’API MongoDB (préversion), l’API Gremlin (préversion) et l’API Cassandra (préversion).

+ L’[indexeur Azure Data Lake Storage Gen2 (préversion)](search-howto-index-azure-data-lake-storage.md) peut indexer du contenu et des métadonnées à partir de Data Lake Storage Gen2.

+ Le [paramètre de requête moreLikeThis (préversion)](search-more-like-this.md) permet de rechercher des documents correspondant à un document spécifique. Cette fonctionnalité existait dans les préversions antérieures. 

## <a name="management-features"></a>Fonctionnalités de gestion

+ [Prise en charge de Private Endpoint](service-create-private-endpoint.md) par le biais de [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) de l’API REST Gestion. Vous pouvez créer un service qui a des restrictions sur la façon dont son point de terminaison est accessible.

## <a name="earlier-preview-features"></a>Fonctionnalités antérieures en préversion

Les fonctionnalités annoncées dans les préversions antérieures, si elles ne sont pas passées en disponibilité générale, sont toujours en préversion publique. Si vous appelez une API avec une api-version d’une préversion antérieure, vous pouvez continuer à utiliser cette version ou basculer sur `2019-05-06-Preview` sans aucun effet sur le comportement attendu.

## <a name="how-to-call-a-preview-api"></a>Comment appeler une API en préversion

Les préversions plus anciennes sont toujours opérationnelles mais deviennent obsolètes au fil du temps. Si votre code appelle `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, ces appels sont toujours valides. Toutefois, seule la dernière préversion est actualisée avec des améliorations. 

La syntaxe de l’exemple suivant illustre un appel à l’API en préversion.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Le service Recherche cognitive Azure est disponible sous plusieurs versions. Pour plus d’informations, consultez [Versions d’API](search-api-versions.md).

## <a name="next-steps"></a>Étapes suivantes

Consultez la documentation de référence relative à l’API REST du service Recherche. Si vous rencontrez des problèmes, sollicitez notre aide sur [StackOverflow](https://stackoverflow.com/) ou [contactez le support](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Référence de l’API REST du service Recherche](https://docs.microsoft.com/rest/api/searchservice/)