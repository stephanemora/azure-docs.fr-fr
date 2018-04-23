---
title: 'API REST du service Recherche Azure : Version 2016-09-01-Preview | Microsoft Docs'
description: L’API REST du service Recherche Azure, version 2016-09-01-Preview, comprend des fonctionnalités expérimentales, telles que les synonymes et les recherches moreLikeThis.
author: mhko
manager: jlembicz
ms.service: search
ms.devlang: rest-api
ms.topic: reference
ms.date: 10/25/2017
ms.author: nateko
ms.openlocfilehash: bbfdb2641d68f1262cc6293462c7022701357374
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>API REST du service Recherche Azure : Version 2016-09-01-Preview
Cet article constitue la documentation de référence de `api-version=2016-09-01-Preview`. Cette préversion étend la version actuelle disponible, [api-version=2016-09-01](https://msdn.microsoft.com/library/dn798935.aspx), en fournissant les fonctionnalités expérimentales suivantes :

* [API Synonymes](search-synonyms.md) pour charger des mappages de synonyme et développer la recherche.
* [`moreLikeThis` Paramètre de requête](search-more-like-this.md) pour rechercher des documents correspondant à un document spécifique.

Assurez-vous de choisir la version de l’API en préversion `api-version=2016-09-01-Preview` pour essayer ces fonctionnalités expérimentales. L’exemple suivant illustre comment la version de l’api en préversion est spécifiée dans une formulation de requête more-like-this.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Les fonctionnalités en préversion sont disponibles pour le test et l’expérimentation en vue de recueillir des commentaires, elles sont susceptibles de changer. **Nous vous conseillons vivement d’utiliser des API en version préliminaire dans les applications de production.**

Le service Azure Search est disponible dans plusieurs versions. Pour plus d'informations, consultez [Contrôle de version de service Azure Search](http://msdn.microsoft.com/library/azure/dn864560.aspx) .
