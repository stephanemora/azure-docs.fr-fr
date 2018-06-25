---
title: API REST du service Recherche Azure version 2017-11-11-Preview | Microsoft Docs
description: L’API REST du service Recherche Azure, version 2017-11-11-Preview, comprend des fonctionnalités expérimentales, par exemple, les recherches de synonymes et de contenus similaires.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/17/2018
ms.author: HeidiSteen
ms.openlocfilehash: ce5771777762414a0229cf83425c2f3601cb979a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659628"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>API REST du service Recherche Azure, version 2017-11-11-Preview
Cet article décrit la version `api-version=2017-11-11-Preview` de l’API REST du service Recherche Azure, qui offre des fonctionnalités expérimentales pas encore mises à la disposition générale.

> [!NOTE]
> Les fonctionnalités en préversion sont disponibles pour le test et l’expérimentation en vue de recueillir des commentaires, elles sont susceptibles de changer. Nous vous conseillons vivement d’utiliser des API en version préliminaire dans les applications de production.


## <a name="new-in-2017-11-11-preview"></a>Nouveauté de 2017-11-11-Preview

La [recherche cognitive](cognitive-search-concept-intro.md), une nouvelle fonctionnalité d’enrichissement pour la Recherche Azure qui trouve des informations latentes dans des sources non textuelles et le texte indifférencié, et les transforme en contenu avec possibilité de recherche en texte intégral dans la Recherche Azure.

Les ressources suivantes sont introduites ou modifiées dans la préversion de l’API REST. Toutes les autres API REST sont les mêmes que vous appeliez la version mise à la disposition générale ou la préversion.

+ [Créer un ensemble de compétences (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)

+ [Créer un indexeur (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)

Toutes les autres API REST sont les mêmes, quelle que soit la façon dont vous avez défini api-version. Par exemple, `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` et `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (sans `Preview`) sont fonctionnellement équivalents.

## <a name="other-preview-features"></a>Autres fonctionnalités en préversion

Les fonctionnalités annoncées dans les préversions antérieures sont toujours en préversion publique. Si vous appelez une API avec une api-version d’une préversion antérieure, vous pouvez continuer à utiliser cette version ou basculer sur `2017-11-11-Preview` sans aucun effet sur le comportement attendu.

+ Les [fichiers CSV dans l’indexation des objets Blob Azure](search-howto-index-csv-blobs.md), introduits dans `api-version=2015-02-28-Preview`, restent une fonctionnalité de la préversion. Cette fonctionnalité fait partie de l’indexation des objets Blob Azure et est appelée via la configuration d’un paramètre. Chaque ligne d’un fichier CSV est indexée sous forme de document distinct.

+ Les [tableaux JSON dans l’indexation des objets Blob Azure](search-howto-index-json-blobs.md), introduits dans `api-version=2015-02-28-Preview`, restent une fonctionnalité de la préversion. Cette fonctionnalité fait partie de l’indexation des objets Blob Azure et est appelée via la configuration d’un paramètre. où chaque élément de la table est indexé sous forme de document distinct.

+ Le [paramètre de requête moreLike This](search-more-like-this.md) permet de rechercher des documents correspondant à un document spécifique. Cette fonctionnalité existait dans les préversions antérieures. 


## <a name="how-to-call-a-preview-api"></a>Comment appeler une API en préversion

Les préversions plus anciennes sont toujours opérationnelles mais deviennent obsolètes au fil du temps. Si votre code appelle `api-version=2016-09-01-Preview` ou `api-version=2015-02-28-Preview`, ces appels sont toujours valides. Toutefois, seule la dernière préversion est actualisée avec des améliorations. 

La syntaxe de l’exemple suivant illustre un appel à l’API en préversion.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Le service Azure Search est disponible dans plusieurs versions. Pour plus d’informations, consultez [Versions d’API](search-api-versions.md).
