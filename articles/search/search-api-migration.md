---
title: Mise à niveau vers la version la plus récente de l’API REST du service Recherche Azure | Microsoft Docs
description: Mise à niveau vers la version la plus récente de l’API REST du service Recherche Azure
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 3848f317fd6d760961756f132edf9cbcb5f431ee
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181968"
---
# <a name="upgrading-to-the-latest-azure-search-service-rest-api-version"></a>Mise à niveau vers la version la plus récente de l’API REST du service Recherche Azure
Si vous utilisez une version antérieure de [l’API REST du service Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/), cet article vous aidera à mettre à niveau votre application pour utiliser la nouvelle version de l’API (2017-11-11).

La version 2017-11-11 de l’API REST contient des modifications des versions antérieures. Ces modifications sont, pour la plupart, à compatibilité descendante. La modification de votre code est donc facilitée, selon la version que vous utilisiez précédemment. Consultez la page [Procédure de mise à niveau](#UpgradeSteps) pour obtenir des instructions sur la façon de modifier votre code pour qu’il utilise la nouvelle version de l’API.

> [!NOTE]
> Votre instance de service Recherche Azure prend en charge plusieurs versions de l’API REST, y compris la plus récente. Vous pouvez continuer à utiliser une version lorsqu’elle n’est pas la plus récente, mais nous vous recommandons de migrer votre code pour utiliser la dernière version.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>Nouveautés de la version 2017-11-11
La version 2017-11-11 est la version la plus récente généralement disponible de l’API REST du service Recherche Azure. Cette version de l’API comprend les nouvelles fonctionnalités suivantes :

* [Synonymes](search-synonyms.md). La nouvelle fonctionnalité Synonymes vous permet de définir des termes équivalents et d’étendre la portée de la requête.
* [Prise en charge de l’indexation efficace des objets blob de texte](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText). Le nouveau mode d’analyse `text` pour les indexeurs d’objets Blob Azure améliore considérablement les performances d’indexation.
* [API Statistiques du service](https://aka.ms/azure-search-stats). Affichez l’utilisation actuelle et les limites des ressources dans Recherche Azure Search avec cette nouvelle API.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Procédure de mise à niveau
Si vous effectuez une mise à niveau à partir d’une version généralement disponible, 2015-02-28 ou 2016-09-01, vous n’aurez probablement pas à modifier votre code, en dehors du numéro de version. Les seules situations dans lesquelles vous pouvez avoir à modifier votre code sont les suivantes :

* Lorsque votre code échoue, car des propriétés non reconnues sont renvoyées dans une réponse de l’API. Par défaut, votre application doit ignorer les propriétés qu’elle ne comprend pas.
* Votre code conserve des demandes d’API et tente de les renvoyer à la nouvelle version de l’API. Par exemple, cela peut se produire si votre application conserve les jetons de continuation renvoyés par l’API Recherche (pour plus d’informations, recherchez `@search.nextPageParameters` dans les [références sur l’API Recherche](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Si vous êtes concerné par l’une de ces situations, vous aurez peut-être à modifier votre code en conséquence. Dans le cas contraire, aucune modification n’est nécessaire, sauf si vous souhaitez commencer à utiliser les [nouvelles fonctionnalités](#WhatsNew) de la version 2017-11-11.

Cela s’applique également si vous effectuez une mise à niveau à partir d’une version de l’API en préversion. Cependant, certaines fonctionnalités ne sont pas disponibles dans la version 2017-11-11 :

* Prise en charge des indexeurs Stockage Blob Azure pour les fichiers CSV et les objets Blob contenant des tableaux JSON.
* Requêtes « More like this »

Si votre code utilise ces fonctionnalités, vous ne pourrez pas effectuer une mise à niveau vers la version 2017-11-11 sans supprimer votre utilisation de ces fonctionnalités.

> [!IMPORTANT]
> N’oubliez pas que les API d’évaluation sont destinées à être utilisées à des fins de test et d’évaluation, et non dans les environnements de production.
> 
> 

## <a name="conclusion"></a>Conclusion
Pour plus d’informations sur l’utilisation de l’API REST du service Recherche Azure, consultez les [références d’API](https://docs.microsoft.com/rest/api/searchservice/) récemment mises à jour sur MSDN.

Vos commentaires sur la Recherche Azure sont les bienvenus. Si vous rencontrez des problèmes, n’hésitez pas à nous demander de l’aide sur le [forum MSDN de la Recherche Azure ](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) ou sur [StackOverflow](http://stackoverflow.com/). Si vous souhaitez poser une question sur la Recherche Azure sur StackOverflow, veillez à utiliser le mot clé `azure-search`.

Merci d’utiliser Azure Search !

