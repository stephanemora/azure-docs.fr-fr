---
title: Versions de l’API Recherche Azure | Microsoft Docs
description: Stratégie de version pour les API REST Azure Search et la bibliothèque cliente dans le Kit de développement logiciel (SDK) .NET.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: brjohnst
ms.openlocfilehash: 7bbc26402b30c7796ba11fef159d5cedefa5d58d
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802467"
---
# <a name="api-versions-in-azure-search"></a>Versions d’API dans Azure Search
Azure Search déploie régulièrement des mises à jour de fonctionnalités. Parfois, ces mises à jour requièrent une nouvelle version de l’API pour maintenir la compatibilité descendante. La publication d’une nouvelle version vous permet de contrôler quand et comment intégrer les mises à jour du service de recherche dans votre code.

En règle générale, l’équipe Recherche Azure publie de nouvelles versions uniquement lorsque cela est nécessaire, car certains efforts peuvent être requis pour mettre à niveau votre code afin d’utiliser une nouvelle version de l’API. Une nouvelle version est requise uniquement si certains aspects de l’API ont changé d’une manière qui interrompt la compatibilité descendante. Ces changements peuvent se produire en raison de correctifs de fonctionnalités existantes ou en raison de nouvelles fonctionnalités qui modifient la surface d’exposition des API existantes.

La même règle s’applique pour les mises à jour du Kit de développement logiciel (SDK). Le Kit de développement logiciel (SDK) de la Recherche Azure suit les règles de [gestion sémantique de version](http://semver.org/), ce qui signifie que sa version comprend trois parties : majeure, mineure et numéro de version (par exemple, 1.1.0). Une nouvelle version majeure du Kit de développement logiciel (SDK) est publiée uniquement en cas de modifications qui interrompent la compatibilité descendante. Les mises à jour de fonctionnalités sans rupture incrémentent la version mineure. Pour corriger les bogues, nous augmentons uniquement le numéro de version.

> [!NOTE]
> Votre instance de service Recherche Azure prend en charge plusieurs versions de l’API REST, y compris la plus récente. Vous pouvez continuer à utiliser une version lorsqu’elle n’est pas la plus récente, mais nous vous recommandons de migrer votre code pour utiliser la dernière version. Lorsque vous utilisez l’API REST, vous devez spécifier la version de l’API dans chaque requête via le paramètre « api-version » (Version de l’API). Lorsque vous utilisez le Kit de développement logiciel (SDK) .NET, la version du Kit de développement logiciel (SDK) que vous utilisez détermine la version correspondante de l’API REST. Si vous utilisez un Kit de développement logiciel (SDK) plus ancien, vous pouvez continuer à exécuter ce code sans changement, même si le service est mis à niveau pour prendre en charge une version plus récente de l’API.

## <a name="snapshot-of-current-versions"></a>Instantané des versions actuelles
Vous trouverez ci-dessous un instantané des versions actuelles de toutes les interfaces de programmation d’Azure Search.

| Interfaces | Version majeure la plus récente | Statut |
| --- | --- | --- |
| [Kit de développement logiciel (SDK) .NET](https://aka.ms/search-sdk) |5.0 |Mise à la disposition générale en avril 2018 |
| [Version préliminaire du Kit de développement logiciel (SDK) .NET](https://aka.ms/search-sdk-preview) |4.0.1-preview |Préversion, publiée en mai 2017 |
| [API REST du service](https://docs.microsoft.com/rest/api/searchservice/) |2017-11-11 |Mise à la disposition générale |
| [API REST du service 2017-11-11-Preview](search-api-2017-11-11-preview.md) |2017-11-11-Preview |VERSION PRÉLIMINAIRE |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |2.0 |Mise à la disposition générale |
| [l’API REST de gestion ;](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Mise à la disposition générale |

Pour les API REST, il est nécessaire d’inclure la version d’API ( `api-version` ) sur chaque appel. L’utilisation de `api-version` permet de cibler facilement une version spécifique, par exemple une API en préversion. L’exemple suivant montre la manière dont le paramètre `api-version` est spécifié :

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2017-11-11

> [!NOTE]
> Bien que chaque demande ait une version d’API (`api-version`), nous vous recommandons d’utiliser la même version pour toutes les demandes d’API. Cela est particulièrement vrai lorsque de nouvelles versions d’API introduisent des attributs ou des opérations qui ne sont pas reconnus par les versions précédentes. La combinaison de versions d’API peut avoir des conséquences inattendues et doit être évitée.
>
> L’API REST Service et l’API REST Gestion sont contrôlées indépendamment l’une de l’autre. Toute ressemblance dans les numéros de version est une coïncidence.

Les API mises à la disposition générale peuvent être utilisées en production et sont soumises aux contrats SLA Azure. Les versions préliminaires offrent les fonctionnalités expérimentales qui ne sont pas toujours migrées vers la version mise à la disposition générale. **Nous vous conseillons vivement d’éviter d’utiliser des API en préversion dans les applications de production.**

## <a name="about-preview-and-generally-available-versions"></a>À propos des versions préliminaires et mises à la disposition générale
Azure Search publie toujours les fonctionnalités expérimentales par le biais de l’API REST, puis par le biais des versions préliminaires du Kit de développement logiciel (SDK) .NET.

Les fonctionnalités en préversion sont disponibles pour le test et l’expérimentation en vue de recueillir des commentaires sur la conception et la mise en œuvre de la fonctionnalité. Ainsi, les fonctionnalités en préversion peuvent changer au fil du temps, voire de manière à empêcher la compatibilité descendante. Ce comportement diffère de celui des fonctionnalités dans la version à disposition générale, qui sont stables et peu susceptibles de changer à l’exception d’améliorations et de correctifs peu importants pour la compatibilité descendante. En outre, les fonctionnalités en préversion ne sont pas toujours intégrées à une version à disposition générale.

C’est pour ces raisons que nous vous recommandons de ne pas écrire un code de production dépendant des préversions. Si vous utilisez une préversion antérieure, nous vous recommandons de migrer vers la version mise à la disposition générale.

Pour le Kit de développement logiciel (SDK) .NET, des conseils sur la migration du code sont disponibles à la page [Mise à niveau du Kit de développement logiciel (SDK) .NET](search-dotnet-sdk-migration.md).

La mise à la disposition générale signifie qu’Azure Search est désormais associé à un contrat SLA. Ce contrat SLA est disponible à la page sur les [contrats SLA Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
