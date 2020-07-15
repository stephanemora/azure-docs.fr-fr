---
title: Versions d’API
titleSuffix: Azure Cognitive Search
description: Stratégie de version pour les API REST de Recherche cognitive Azure et la bibliothèque de client dans le SDK .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a7179f88f507f0deedc79e7ae49988c8b5a32f86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830092"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versions d’API dans la Recherche cognitive Azure

Le service Recherche cognitive Azure déploie régulièrement des mises à jour de fonctionnalités. Parfois, ces mises à jour requièrent une nouvelle version de l’API pour maintenir la compatibilité descendante. La publication d’une nouvelle version vous permet de contrôler quand et comment intégrer les mises à jour du service de recherche dans votre code.

En règle générale, l’équipe de Recherche cognitive Azure publie de nouvelles versions quand cela s’avère nécessaire uniquement. En effet, la mise à niveau de votre code pour utiliser une nouvelle version de l’API peut demander un certain travail. Une nouvelle version est requise uniquement si certains aspects de l’API ont changé d’une manière qui interrompt la compatibilité descendante. Ces changements peuvent se produire en raison de correctifs de fonctionnalités existantes ou en raison de nouvelles fonctionnalités qui modifient la surface d’exposition des API existantes.

La même règle s’applique pour les mises à jour du Kit de développement logiciel (SDK). Le SDK de Recherche cognitive Azure suit les règles de [gestion sémantique de version](https://semver.org/), ce qui signifie que sa version comprend trois parties : majeure, mineure et numéro de version (par exemple, 1.1.0). Une nouvelle version majeure du Kit de développement logiciel (SDK) est publiée uniquement en cas de modifications qui interrompent la compatibilité descendante. Les mises à jour de fonctionnalités sans rupture incrémentent la version mineure. Pour corriger les bogues, nous augmentons uniquement le numéro de version.

> [!NOTE]
> Une instance du service Recherche cognitive Azure prend en charge plusieurs versions de l’API REST, y compris la plus récente. Vous pouvez continuer à utiliser une version lorsqu’elle n’est pas la plus récente, mais nous vous recommandons de migrer votre code pour utiliser la dernière version. Lorsque vous utilisez l’API REST, vous devez spécifier la version de l’API dans chaque requête via le paramètre « api-version » (Version de l’API). Lorsque vous utilisez le Kit de développement logiciel (SDK) .NET, la version du Kit de développement logiciel (SDK) que vous utilisez détermine la version correspondante de l’API REST. Si vous utilisez un Kit de développement logiciel (SDK) plus ancien, vous pouvez continuer à exécuter ce code sans changement, même si le service est mis à niveau pour prendre en charge une version plus récente de l’API.

## <a name="rest-apis"></a>API REST

Ce tableau fournit l’historique des versions actuelles et précédentes de l’API REST de Search Service. La documentation est publiée pour les versions stables et en préversion actuelles.

| Version&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Statut | Problème de compatibilité descendante |
|-------------|--------|------------------------------|
| [Gestion 13-03-2020](https://docs.microsoft.com/rest/api/searchmanagement/) | Mise à la disposition générale | Version stable la plus récente des API REST de gestion, avec des avancées dans la protection des points de terminaison. Ajoute les points de terminaison privés, la prise en charge de Private Link et les règles réseau pour les nouveaux services. |
| [Gestion 01-10-2019 - Préversion](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) | PRÉVERSION  | En dépit de son numéro de version, il s’agit encore de la préversion actuelle des API REST de gestion. Il n’existe pas de fonctionnalité en préversion pour l’instant. Toutes les fonctionnalités en préversion sont récemment passées en disponibilité générale. |
| Gestion 19-08-2015  | Stable| Première version en disponibilité générale des API REST de gestion. Fournit le provisionnement des services, le scale-up et la gestion des clés API. |
| Gestion 19-08-2015 - Préversion | PRÉVERSION| Première préversion des API REST de gestion. |
| [Recherche 30-06-2020](https://docs.microsoft.com/rest/api/searchservice/index)| Stable | Version stable la plus récente des API REST Recherche, avec des avancées dans le scoring de la pertinence. |
| [Recherche 30-06-2020 - Préversion](https://docs.microsoft.com/rest/api/searchservice/index-preview)| PRÉVERSION | Préversion associée à la version stable. |
| Recherche 2019-05-06 | Stable | Ajoute des types complexes. |
| Recherche 2019-05-06-Preview | PRÉVERSION | Préversion associée à la version stable. |
| Recherche 11-11-2017 | Stable  | Ajoute les ensemble de compétences et l’enrichissement IA. |
| Recherche 11-11-2017 - Préversion | PRÉVERSION | Préversion associée à la version stable. |
| Recherche 01-09-2016 |Stable | Ajoute des indexeurs|
| Recherche 01-09-2016 - Préversion | PRÉVERSION | Préversion associée à la version stable.|
| Recherche 28-02-2015 | Stable  | Première version en disponibilité générale.  |
| Recherche 28-02-2015 - Préversion | PRÉVERSION | Préversion associée à la version stable. |
| Recherche 20-10-2014 - Préversion | PRÉVERSION | Deuxième préversion publique. |
| Recherche 31-07-2014 - Préversion | PRÉVERSION | Première préversion publique. |

## <a name="azure-sdk-for-net"></a>Kit SDK Azure pour .NET

L’historique des versions du package est disponible sur NuGet.org. Ce tableau fournit des liens vers chaque page de package.

| Version du SDK | Statut | Description |
|-------------|--------|------------------------------|
| [**Azure.Search.Documents 1.0.0-preview.4**](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | PRÉVERSION | Nouvelle bibliothèque cliente du SDK Azure .NET, publiée en mai 2020. Cible la version de l’API REST 30-06-2020|
| [**Microsoft.Azure.Search 10.0**](https://www.nuget.org/packages/Microsoft.Azure.Search/) | En disponibilité générale, publiée en mai 2019. Cible la version de l’API REST 06-05-2019.|
| [**Microsoft.Azure.Search 8.0-preview**](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | Préversion, publiée en avril 2019. Cible la version de l’API REST 06-05-2019 - Préversion.|
| [**Microsoft.Azure.Management.Search 3.0.0**](https://docs.microsoft.com/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Stable | Cible l’API REST de gestion de versions 19-08-2015. |

## <a name="azure-sdk-for-java"></a>Kit SDK Azure pour Java

| Version du SDK | Statut | Description  |
|-------------|--------|------------------------------|
| [**Java SearchManagementClient 1.35.0**](https://docs.microsoft.com/java/api/overview/azure/search/management?view=azure-java-stable) | Stable | Cible l’API REST de gestion de versions 19-08-2015.|

## <a name="azure-sdk-for-python"></a>Kit SDK Azure pour Python

| Version du SDK | Statut | Description  |
|-------------|--------|------------------------------|
| [**Python azure-mgmt-search 1.0**](https://docs.microsoft.com/python/api/overview/azure/search?view=azure-python) | Stable | Cible l’API REST de gestion de versions 19-08-2015. |