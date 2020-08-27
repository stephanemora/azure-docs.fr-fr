---
title: Versions d’API
titleSuffix: Azure Cognitive Search
description: Stratégie de version pour les API REST de Recherche cognitive Azure et la bibliothèque de client dans le SDK .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: bf3e4262f4342788f343ab287fd3db53d12736c7
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918109"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versions d’API dans la Recherche cognitive Azure

Le service Recherche cognitive Azure déploie régulièrement des mises à jour de fonctionnalités. Parfois, ces mises à jour requièrent une nouvelle version de l’API pour maintenir la compatibilité descendante. La publication d’une nouvelle version vous permet de contrôler quand et comment intégrer les mises à jour du service de recherche dans votre code.

En règle générale, l’équipe de Recherche cognitive Azure publie de nouvelles versions quand cela s’avère nécessaire uniquement. En effet, la mise à niveau de votre code pour utiliser une nouvelle version de l’API peut demander un certain travail. Une nouvelle version est requise uniquement si certains aspects de l’API ont changé d’une manière qui interrompt la compatibilité descendante. Ces changements peuvent se produire en raison de correctifs de fonctionnalités existantes ou en raison de nouvelles fonctionnalités qui modifient la surface d’exposition des API existantes.

La même règle s’applique pour les mises à jour du Kit de développement logiciel (SDK). Le SDK de Recherche cognitive Azure suit les règles de [gestion sémantique de version](https://semver.org/), ce qui signifie que sa version comprend trois parties : majeure, mineure et numéro de version (par exemple, 1.1.0). Une nouvelle version majeure du Kit de développement logiciel (SDK) est publiée uniquement en cas de modifications qui interrompent la compatibilité descendante. Les mises à jour de fonctionnalités sans rupture incrémentent la version mineure. Pour corriger les bogues, nous augmentons uniquement le numéro de version.

> [!Important]
> Les Kits de développement logiciel (SDK) Azure pour .NET, Java, Python et JavaScript déploient de nouvelles bibliothèques de client pour Recherche cognitive Azure. Actuellement, aucune des bibliothèques des Kits de développement logiciel (SDK) Azure ne prend entièrement en charge les API REST de recherche (2020-06-30) ou les API REST de gestion (2020-03-13) les plus récentes, mais cela changera au fil du temps. Vous pouvez consulter régulièrement cette page ou la rubrique [Nouveautés](whats-new.md) pour connaître les annonces sur les améliorations fonctionnelles. 

## <a name="rest-apis"></a>API REST

Une instance du service Recherche cognitive Azure prend en charge plusieurs versions de l’API REST, y compris la plus récente. Vous pouvez continuer à utiliser une version lorsqu’elle n’est pas la plus récente, mais nous vous recommandons de [migrer votre code](search-api-migration.md) pour utiliser la dernière version. Lorsque vous utilisez l’API REST, vous devez spécifier la version de l’API dans chaque requête via le paramètre « api-version » (Version de l’API). Lorsque vous utilisez le Kit de développement logiciel (SDK) .NET, la version du Kit de développement logiciel (SDK) que vous utilisez détermine la version correspondante de l’API REST. Si vous utilisez un Kit de développement logiciel (SDK) plus ancien, vous pouvez continuer à exécuter ce code sans changement, même si le service est mis à niveau pour prendre en charge une version plus récente de l’API.

Le tableau suivant fournit l’historique des versions actuelles et précédentes de l’API REST du service de recherche. La documentation est publiée uniquement pour les versions préliminaires et celles stables les plus récentes.

### <a name="search-service-apis"></a>API du service de recherche

Créez et gérez du contenu sur un service de recherche.

| Version&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Statut | Problème de compatibilité descendante |
|-------------------------|--------|------------------------------|
| [Recherche 30-06-2020](/rest/api/searchservice/index)| Stable | Version stable la plus récente des API REST de recherche, avec des progrès dans le scoring de la pertinence et la disponibilité générale de la base de connaissances.|
| [Recherche 30-06-2020 - Préversion](/rest/api/searchservice/index-preview)| PRÉVERSION | Préversion associée à la version stable. Comprend plusieurs [fonctionnalités d’évaluation](search-api-preview.md). |
| Recherche 2019-05-06 | Stable | Ajoute des [types complexes](search-howto-complex-data-types.md). |
| Recherche 2019-05-06-Preview | PRÉVERSION | Préversion associée à la version stable. |
| Recherche 11-11-2017 | Stable  | Ajoute des ensembles de compétences et l’[enrichissement par IA](cognitive-search-concept-intro.md). |
| Recherche 11-11-2017 - Préversion | PRÉVERSION | Préversion associée à la version stable. |
| Recherche 01-09-2016 |Stable | Ajoute des [indexeurs](search-indexer-overview.md). |
| Recherche 01-09-2016 - Préversion | PRÉVERSION | Préversion associée à la version stable.|
| Recherche 28-02-2015 | Stable  | Première version en disponibilité générale.  |
| Recherche 28-02-2015 - Préversion | PRÉVERSION | Préversion associée à la version stable. |
| Recherche 20-10-2014 - Préversion | PRÉVERSION | Deuxième préversion publique. |
| Recherche 31-07-2014 - Préversion | PRÉVERSION | Première préversion publique. |

### <a name="management-rest-apis"></a>API REST de gestion

Créez et configurez un service de recherche et gérez les clés API.

| Version&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Statut | Problème de compatibilité descendante |
|-------------------------|--------|------------------------------|
| [Gestion 13-03-2020](/rest/api/searchmanagement/) | Stable | Version stable la plus récente des API REST de gestion, avec des avancées dans la protection des points de terminaison. Ajoute un [point de terminaison privé](service-create-private-endpoint.md) via une liaison privée et des [règles d’IP réseau](service-configure-firewall.md) pour les nouveaux services. |
| [Gestion 01-10-2019 - Préversion](/rest/api/searchmanagement/index-2019-10-01-preview) | PRÉVERSION  | En dépit de son numéro de version, il s’agit encore de la préversion actuelle des API REST de gestion. Il n’existe pas de fonctionnalité en préversion pour l’instant. Toutes les fonctionnalités en préversion sont récemment passées en disponibilité générale. |
| Gestion 19-08-2015  | Stable | Première version en disponibilité générale des API REST de gestion. Fournit le provisionnement des services, le scale-up et la gestion des clés API. |
| Gestion 19-08-2015 - Préversion | PRÉVERSION | Première préversion des API REST de gestion. |

## <a name="azure-sdk-for-net"></a>Kit SDK Azure pour .NET

L’historique des versions du package est disponible sur NuGet.org. Ce tableau fournit des liens vers chaque page de package.


| Version du SDK | Statut | Description |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11.0](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | Stable | Nouvelle bibliothèque de client du Kit de développement logiciel (SDK) .NET Azure, publiée en juillet 2020. Cible l’API REST Recherche (api-version = 2020-06-30), mais ne prend pas encore en charge les filtres géographiques et [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder?view=azure-dotnet). |
| [Microsoft.Azure.Search 10.0](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | Publiée en mai 2019. Cible l’API REST de recherche api-version=2019-05-06.|
| [Microsoft.Azure.Search 8.0-preview](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | PRÉVERSION | Publiée en avril 2019. Cible l’API REST de recherche api-version=2019-05-06-Preview.|
| [Microsoft.Azure.Management.Search 3.0.0](/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Stable | Cible l’API REST de gestion de versions 19-08-2015.  |

## <a name="azure-sdk-for-java"></a>Kit SDK Azure pour Java

| Version du SDK | Statut | Description  |
|-------------|--------|------------------------------|
| [Java azure-search-documents 11](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-search-documents/11.0.0/index.html) | Stable | Nouvelle bibliothèque de client du Kit de développement logiciel (SDK) .NET Azure, publiée en juillet 2020. Cible l’API REST de recherche api-version=2019-05-06. |
| [Java Management Client 1.35.0](/java/api/overview/azure/search/management?view=azure-java-stable) | Stable | Cible l’API REST de gestion de versions 19-08-2015. |

## <a name="azure-sdk-for-javascript"></a>Kit SDK Azure pour JavaScript

| Version du SDK | Statut | Description  |
|-------------|--------|------------------------------|
| [JavaScript azure-search 11.0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | Stable | Nouvelle bibliothèque de client du Kit de développement logiciel (SDK) .NET Azure, publiée en juillet 2020. Cible l’API REST de recherche api-version=2016-09-01. |
| [JavaScript azure-arm-search](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | Stable | Cible l’API REST de gestion de versions 19-08-2015. |

## <a name="azure-sdk-for-python"></a>Kit SDK Azure pour Python

| Version du SDK | Statut | Description  |
|-------------|--------|------------------------------|
| [Python azure-search-documents 11.0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | Stable | Nouvelle bibliothèque de client du Kit de développement logiciel (SDK) .NET Azure, publiée en juillet 2020. Cible l’API REST de recherche api-version=2019-05-06. |
| [Python azure-mgmt-search 1.0](/python/api/overview/azure/search?view=azure-python) | Stable | Cible l’API REST de gestion de versions 19-08-2015. |