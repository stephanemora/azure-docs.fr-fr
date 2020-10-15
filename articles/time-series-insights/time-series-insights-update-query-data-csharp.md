---
title: Interroger des données à partir d’un environnement Gen2 en utilisant C# – Azure Time Series Insights | Microsoft Docs
description: Découvrez comment interroger des données à partir d’un environnement Azure Time Series Insights Gen2 à l’aide d’une application écrite en C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 99cde78f0944544941224927e84e117bd0e660d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91667841"
---
# <a name="query-data-from-the-azure-time-series-insights-gen2-environment-using-c-sharp"></a>Interroger des données à partir d’un environnement Azure Time Series Insights Gen2 en utilisant C#

Cet exemple de code C# montre comment interroger des données à partir des [API d’accès aux données Gen2](https://docs.microsoft.com/rest/api/time-series-insights/reference-data-access-overview) dans des environnements Azure Time Series Insights Gen2.

> [!TIP]
> Consultez des exemples de code C# Gen2 à l’adresse [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample).

## <a name="summary"></a>Résumé

L’exemple de code ci-dessous illustre les fonctionnalités suivantes :

* Prise en charge de la génération automatique du Kit de développement logiciel (SDK) à partir d’[Azure AutoRest](https://github.com/Azure/AutoRest).
* Comment acquérir un jeton d’accès via Azure Active Directory à l’aide de [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Comment passer ce jeton d’accès acquis dans l’en-tête `Authorization` des requêtes ultérieures d’API d’accès aux données.
* L’exemple fournit une interface de console qui illustre la façon dont les requêtes HTTP sont adressées à ce qui suit :
  * [API d’environnements Gen2](https://docs.microsoft.com/rest/api/time-series-insights/reference-environments-apis)
    * [API Get Environments Availability](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability) et [API Get Event Schema](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)
  * [API de requête Gen2](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis)
    * [API Get Events](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents), [API Get Series](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries) et [API Get Aggregate Series](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
  * [API Time Series Model](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
    * [API Get Hierarchies](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies) et [API Hierarchies Batch](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    * [API Get Types](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes) et [API Types Batch](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    * [API Get Instances](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances) et [API Instances Batch](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)

* Fonctionnalités avancées [Recherche](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#search-features) et [TSX](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).

## <a name="prerequisites-and-setup"></a>Composants requis et configuration

Effectuez les étapes suivantes avant de compiler et d'exécuter l’exemple de code :

1. [Approvisionner un environnement Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment).
1. Configurez votre environnement Azure Time Series Insights pour Azure Active Directory, comme décrit dans [Authentification et autorisation](time-series-insights-authentication-and-authorization.md).
1. Exécutez le fichier [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/GenerateCode.bat) comme spécifié dans le fichier [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/Readme.md) pour générer les dépendances de client d’Azure Time Series Insights Gen2.
1. Ouvrez la solution `TSIPreviewDataPlaneclient.sln` et définissez `DataPlaneClientSampleApp` comme projet par défaut dans Visual Studio.
1. Installez les dépendances de projet requises à l’aide des étapes décrites [ci-dessous](#project-dependencies) et compilez l’exemple en un fichier exécutable `.exe`.
1. Exécutez le fichier `.exe` en double-cliquant dessus.

## <a name="project-dependencies"></a>Dépendances du projet

Il est recommandé d’utiliser la version la plus récente de Visual Studio :

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Version 16.4.2+

L’exemple de code comporte plusieurs dépendances requises qui peuvent être affichées dans le fichier [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/packages.config).

Téléchargez les packages dans Visual Studio 2019 en sélectionnant l’option **Générer** > **Générer la solution**.

Vous pouvez également ajouter chaque package à l’aide de [NuGet 2.12+](https://www.nuget.org/). Par exemple :

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Exemple de code C#

Pour accéder à l’exemple de code C#, reportez-vous au référentiel [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample).

> [!NOTE]
>
> * L’exemple de code peut être exécuté sans modification des variables d’environnement par défaut.
> * L’exemple de code se compilera en une application console exécutable .NET.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur l’interrogation, consultez les [informations de référence sur l’API de requête](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis).

* Découvrez comment [connecter une application JavaScript avec le Kit de développement logiciel (SDK) client](https://github.com/microsoft/tsiclient) à Azure Time Series Insights.
