---
title: Gestion des versions d’API pour .NET et REST
titleSuffix: Azure Cognitive Search
description: Stratégie de version pour les API REST de Recherche cognitive Azure et la bibliothèque de client dans le SDK .NET.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 178f56354120bf7a65c51f1c9cf54e34bd011d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137286"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versions d’API dans la Recherche cognitive Azure

Le service Recherche cognitive Azure déploie régulièrement des mises à jour de fonctionnalités. Parfois, ces mises à jour requièrent une nouvelle version de l’API pour maintenir la compatibilité descendante. La publication d’une nouvelle version vous permet de contrôler quand et comment intégrer les mises à jour du service de recherche dans votre code.

En règle générale, l’équipe de Recherche cognitive Azure publie de nouvelles versions quand cela s’avère nécessaire uniquement. En effet, la mise à niveau de votre code pour utiliser une nouvelle version de l’API peut demander un certain travail. Une nouvelle version est requise uniquement si certains aspects de l’API ont changé d’une manière qui interrompt la compatibilité descendante. Ces changements peuvent se produire en raison de correctifs de fonctionnalités existantes ou en raison de nouvelles fonctionnalités qui modifient la surface d’exposition des API existantes.

La même règle s’applique pour les mises à jour du Kit de développement logiciel (SDK). Le SDK de Recherche cognitive Azure suit les règles de [gestion sémantique de version](https://semver.org/), ce qui signifie que sa version comprend trois parties : majeure, mineure et numéro de version (par exemple, 1.1.0). Une nouvelle version majeure du Kit de développement logiciel (SDK) est publiée uniquement en cas de modifications qui interrompent la compatibilité descendante. Les mises à jour de fonctionnalités sans rupture incrémentent la version mineure. Pour corriger les bogues, nous augmentons uniquement le numéro de version.

> [!NOTE]
> Votre instance de service Recherche cognitive Azure prend en charge plusieurs versions de l’API REST, y compris la plus récente. Vous pouvez continuer à utiliser une version lorsqu’elle n’est pas la plus récente, mais nous vous recommandons de migrer votre code pour utiliser la dernière version. Lorsque vous utilisez l’API REST, vous devez spécifier la version de l’API dans chaque requête via le paramètre « api-version » (Version de l’API). Lorsque vous utilisez le Kit de développement logiciel (SDK) .NET, la version du Kit de développement logiciel (SDK) que vous utilisez détermine la version correspondante de l’API REST. Si vous utilisez un Kit de développement logiciel (SDK) plus ancien, vous pouvez continuer à exécuter ce code sans changement, même si le service est mis à niveau pour prendre en charge une version plus récente de l’API.

## <a name="snapshot-of-current-versions"></a>Instantané des versions actuelles
Vous trouverez ci-dessous un instantané des versions actuelles de toutes les interfaces de programmation pour la Recherche cognitive Azure.


| Interfaces | Version majeure la plus récente | Statut |
| --- | --- | --- |
| [Kit de développement logiciel (SDK) .NET](https://aka.ms/search-sdk) |9.0 |Mise à la disposition générale en mai 2019 |
| [Version préliminaire du Kit de développement logiciel (SDK) .NET](https://aka.ms/search-sdk-preview) |8.0-preview |Publication de la préversion : avril 2019 |
| [API REST du service](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Mise à la disposition générale |
| [API REST du service 2019-05-06-Preview](search-api-preview.md) |2019-05-06-Preview |PRÉVERSION |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Mise à la disposition générale |
| [l’API REST de gestion ;](https://docs.microsoft.com/rest/api/searchmanagement/) |2020-03-13|Mise à la disposition générale |

Pour les API REST, il est nécessaire d’inclure la version d’API ( `api-version` ) sur chaque appel. L’utilisation de `api-version` permet de cibler facilement une version spécifique, par exemple une API en préversion. L’exemple suivant montre la manière dont le paramètre `api-version` est spécifié :

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Bien que chaque demande ait une version d’API (`api-version`), nous vous recommandons d’utiliser la même version pour toutes les demandes d’API. Cela est particulièrement vrai lorsque de nouvelles versions d’API introduisent des attributs ou des opérations qui ne sont pas reconnus par les versions précédentes. La combinaison de versions d’API peut avoir des conséquences inattendues et doit être évitée.
>
> L’API REST Service et l’API REST Gestion sont contrôlées indépendamment l’une de l’autre. Toute ressemblance dans les numéros de version est une coïncidence.

Les API mises à la disposition générale peuvent être utilisées en production et sont soumises aux contrats SLA Azure. Les versions préliminaires offrent les fonctionnalités expérimentales qui ne sont pas toujours migrées vers la version mise à la disposition générale. **Nous vous conseillons vivement d’éviter d’utiliser des API en préversion dans les applications de production.**

## <a name="update-to-the-latest-version-of-the-rest-api-by-october-15-2020"></a>Mettez à jour vers la version la plus récente de l’API REST pour le 15 octobre 2020
Les versions suivantes de l’API REST Recherche cognitive Azure seront retirées et ne seront plus prises en charge à compter du 15 octobre 2020 : **2014-07-31-Preview**, **2014-10-20-Preview**, **2015-02-28-Preview** et **2015-02-28**. Les versions du kit SDK .NET Recherche cognitive Azure antérieures à la version **3.0.0-rc** seront également retirées, car elles ciblent l’une de ces versions de l’API REST. Après cette date, les applications utilisant l’API REST déconseillée ou les versions du Kit de développement logiciel (SDK) ne fonctionneront plus et nécessiteront une mise à niveau. Comme pour toute modification de ce type, nous donnons un préavis de 12 mois pour vous laisser le temps de vous adapter.  Pour continuer à utiliser la Recherche cognitive Azure, migrez le code existant ciblant [l’API REST](search-api-migration.md) vers la [version 2019-05-06 de l’API REST](https://docs.microsoft.com/rest/api/searchservice/) ou une version ultérieure, ou le kit SDK .NET vers la [version 3.0](search-dotnet-sdk-migration.md) ou une version ultérieure, d’ici au 15 octobre 2020.  Si vous avez des questions sur la mise à jour vers la dernière version, envoyez un courrier à azuresearch_contact@microsoft.com pour le 15 mai 2020 afin d’être certain de disposer de suffisamment de temps pour mettre à jour votre code.

## <a name="about-preview-and-generally-available-versions"></a>À propos des versions préliminaires et mises à la disposition générale
Le service Recherche cognitive Azure publie toujours les fonctionnalités expérimentales par le biais de l’API REST, puis par le biais des préversions du SDK .NET.

Les fonctionnalités en préversion sont disponibles pour le test et l’expérimentation en vue de recueillir des commentaires sur la conception et la mise en œuvre de la fonctionnalité. Ainsi, les fonctionnalités en préversion peuvent changer au fil du temps, voire de manière à empêcher la compatibilité descendante. Ce comportement diffère de celui des fonctionnalités dans la version à disposition générale, qui sont stables et peu susceptibles de changer à l’exception d’améliorations et de correctifs peu importants pour la compatibilité descendante. En outre, les fonctionnalités en préversion ne sont pas toujours intégrées à une version à disposition générale.

C’est pour ces raisons que nous vous recommandons de ne pas écrire un code de production dépendant des préversions. Si vous utilisez une préversion antérieure, nous vous recommandons de migrer vers la version mise à la disposition générale.

Pour le Kit de développement logiciel (SDK) .NET : des conseils sur la migration du code sont disponibles à la page [Mise à niveau vers la version du Kit de développement logiciel Azure Search .NET SDK version 3](search-dotnet-sdk-migration-version-9.md).

La disponibilité générale signifie que le service Recherche cognitive Azure est désormais associé à un contrat de niveau de service. Ce contrat est disponible sur la page des [contrats SLA du service Recherche cognitive Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
