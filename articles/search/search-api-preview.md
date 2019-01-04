---
title: Afficher un aperçu de l’API REST pour Azure Search 2017-11-11-Preview – Recherche Azure
description: L’API REST du service Recherche Azure, version 2017-11-11-Preview, comprend des fonctionnalités expérimentales, par exemple, les recherches de synonymes et de contenus similaires.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 06/28/2018
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: e496e4e2e7e2f66033d090a0534911bff4c53baa
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310229"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>API REST du service Recherche Azure, version 2017-11-11-Preview
Cet article décrit la version `api-version=2017-11-11-Preview` de l’API REST du service Recherche Azure, qui offre des fonctionnalités expérimentales pas encore mises à la disposition générale.

> [!NOTE]
> Les fonctionnalités en préversion sont disponibles pour le test et l’expérimentation en vue de recueillir des commentaires, elles sont susceptibles de changer. Nous vous conseillons vivement d’utiliser des API en version préliminaire dans les applications de production.


## <a name="new-in-2017-11-11-preview"></a>Nouveauté de 2017-11-11-Preview

La [**Saisie semi-automatique** ](search-autocomplete-tutorial.md) a rejoint les [API Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) existantes pour ajouter des expériences prédictives complémentaires dans la barre de recherche. La saisie semi-automatique retourne des suggestions de termes de recherche qu’un utilisateur peut choisir comme chaîne de requête d’une prochaine requête. Les suggestions retournent les documents en réponse à des entrées partielles : les résultats de recherche sont immédiats et changent dynamiquement au fur et à mesure que l’entrée du terme de recherche devient plus longue et plus spécifique.

La [**recherche cognitive**](cognitive-search-concept-intro.md), nouvelle fonctionnalité d’enrichissement du service Recherche Azure, trouve des informations latentes dans des sources non textuelles et le texte indifférencié, puis les transforme en contenu avec possibilité de recherche en texte intégral dans Recherche Azure. Les ressources suivantes sont introduites ou modifiées dans la préversion de l’API REST. Toutes les autres API REST sont les mêmes que vous appeliez la version mise à la disposition générale ou la préversion.

+ [Opérations d’un ensemble de compétences (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/skillset-operations)

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
