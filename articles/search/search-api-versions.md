---
title: Versions d’API
titleSuffix: Azure Cognitive Search
description: Stratégie de version pour les API REST de Recherche cognitive Azure et la bibliothèque de client dans le SDK .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 9a53eba2b46333f3be08432ce69b22cfb3d9ef9c
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073522"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versions d’API dans la Recherche cognitive Azure

Le service Recherche cognitive Azure déploie régulièrement des mises à jour de fonctionnalités. Parfois, ces mises à jour requièrent une nouvelle version de l’API pour maintenir la compatibilité descendante. La publication d’une nouvelle version vous permet de contrôler quand et comment intégrer les mises à jour du service de recherche dans votre code.

En règle générale, l’équipe de Recherche cognitive Azure publie de nouvelles versions quand cela s’avère nécessaire uniquement. En effet, la mise à niveau de votre code pour utiliser une nouvelle version de l’API peut demander un certain travail. Une nouvelle version est requise uniquement si certains aspects de l’API ont changé d’une manière qui interrompt la compatibilité descendante. Ces changements peuvent se produire en raison de correctifs de fonctionnalités existantes ou en raison de nouvelles fonctionnalités qui modifient la surface d’exposition des API existantes.

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>Versions non prises en charge

Certaines versions d’API ne sont plus prises en charge et seront rejetées par un service de recherche :

+ **2015-02-28**
+ **2015-02-28-Preview**
+ **2014-07-31-Preview ;**
+ **2014-10-20-Preview.**

Les versions du kit SDK .NET Recherche cognitive Azure antérieures à la version [**3.0.0-rc**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc) seront également retirées, car elles ciblent l’une de ces versions de l’API REST.

La prise en charge des versions listées ci-dessus a été abandonnée le 15 octobre 2020. Si vous avez un code qui utilise une version interrompue, vous pouvez [migrer le code existant](search-api-migration.md) vers une nouvelle [version de l’API REST](/rest/api/searchservice/) ou un nouveau SDK Azure.

## <a name="rest-apis"></a>API REST

| API REST | Lien |
|----------|------|
| Service de recherche (plan de données) | [Versions d’API](/rest/api/searchservice/search-service-api-versions) |
| Gestion (plan de contrôle) | [Versions d’API](/rest/api/searchmanagement/management-api-versions) |

## <a name="azure-sdk-for-net"></a>Kit SDK Azure pour .NET

Le tableau suivant fournit des liens vers des versions plus récentes du Kit de développement logiciel (SDK). 

| Version du SDK | Statut | Description |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11](/dotnet/api/overview/azure/search.documents-readme) | Stable | Nouvelle bibliothèque de client du Kit de développement logiciel (SDK) .NET Azure, initialement publiée en juillet 2020. |
| [Microsoft.Azure.Search 10](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | Publiée en mai 2019. Il s’agit de la version la plus récente du package Microsoft.Azure.Search. Elle est suivie par Azure.Search.Documents. |
| [Microsoft.Azure.Management.Search 4.0.0](/dotnet/api/overview/azure/search/management) | Stable | Cible l’API REST Gestion-version=2020-08-01.  |
| Microsoft.Azure.Management.Search 3.0.0 | Stable | Cible l’API REST de gestion de versions 19-08-2015.  |

## <a name="azure-sdk-for-java"></a>Kit SDK Azure pour Java

| Version du SDK | Statut | Description  |
|-------------|--------|------------------------------|
| [Java azure-search-documents 11](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | Stable | Nouvelle bibliothèque de client du Kit de développement logiciel (SDK) Java Azure, publiée en juillet 2020. Cible l’API REST de recherche api-version=2019-05-06. |
| [Java Management Client 1.35.0](/java/api/overview/azure/search/management) | Stable | Cible l’API REST de gestion de versions 19-08-2015. |

## <a name="azure-sdk-for-javascript"></a>Kit SDK Azure pour JavaScript

| Version du SDK | Statut | Description  |
|-------------|--------|------------------------------|
| [JavaScript @azure/search-documents 11.0](https://www.npmjs.com/package/@azure/search-documents) | Stable | Nouvelle bibliothèque de client du Kit de développement logiciel (SDK) JavaScript et TypesScript, publiée en juillet 2020. Cible l’API REST de recherche api-version=2016-09-01. |
| [JavaScript @azure/arm-search](https://www.npmjs.com/package/@azure/arm-search) | Stable | Cible l’API REST de gestion de versions 19-08-2015. |

## <a name="azure-sdk-for-python"></a>Kit SDK Azure pour Python

| Version du SDK | Statut | Description  |
|-------------|--------|------------------------------|
| [Python azure-search-documents 11.0](https://pypi.org/project/azure-search-documents/) | Stable | Nouvelle bibliothèque de client du Kit de développement logiciel (SDK) Python Azure, publiée en juillet 2020. Cible l’API REST de recherche api-version=2019-05-06. |
| [Python azure-mgmt-search 8.0](https://pypi.org/project/azure-mgmt-search/) | Stable | Cible l’API REST de gestion de versions 19-08-2015. |

## <a name="all-azure-sdks"></a>Tous les kits SDK Azure

Si vous recherchez la documentation et les bibliothèques clientes bêta, [cette page](https://azure.github.io/azure-sdk/releases/latest/index.html) contient des liens vers tous les documents, le code et les packages de bibliothèque du kit SDK Azure. 
