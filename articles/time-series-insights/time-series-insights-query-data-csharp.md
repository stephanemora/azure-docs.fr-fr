---
title: Interroger des données à partir d’un environnement mis à la disposition générale à l’aide du code C# - Azure Time Series Insights | Microsoft Docs
description: Découvrez comment interroger des données à partir d’un environnement Azure Time Series Insights à l’aide d’une application personnalisée écrite en C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: 754d1b80236d138693987cccee7a218ccd96b16b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383884"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Interroger des données à partir d’un environnement GA Azure Time Series Insights en utilisant C#

Cet exemple de code C# montre comment utiliser les [API de requête en disponibilité générale](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) pour interroger des données à partir d’environnements Azure Time Series Insights en disponibilité générale.

> [!TIP]
> Consultez des exemples de code C# en disponibilité générale sur [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="summary"></a>Résumé

L’exemple de code ci-dessous illustre les fonctionnalités suivantes :

* Comment acquérir un jeton d’accès via Azure Active Directory à l’aide de [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Comment passer ce jeton d’accès acquis dans l’en-tête `Authorization` des requêtes ultérieures d’API de requête. 

* L’exemple appelle chacune des API de requête en disponibilité générale illustrant la façon dont les requêtes HTTP sont adressées aux API :
    * [API Get Environments](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) pour retourner les environnements auxquels l’utilisateur a accès
    * [API Get Environment Availability](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [API Get Environment Metadata](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) pour récupérer les métadonnées d’environnement
    * [API Get Environments Events](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [API Get Environment Aggregates](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Comment interagir avec les API de requête en disponibilité générale à l’aide de WSS pour envoyer un message aux API :

   * [API Get Environment Events Streamed](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [API Get Environment Aggregates Streamed](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Composants requis et configuration

Effectuez les étapes suivantes avant de compiler et d'exécuter l’exemple de code :

1. [Provisionner un environnement Azure Time Series Insights en disponibilité générale](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Configurez votre environnement Azure Time Series Insights pour Azure Active Directory, comme décrit dans [Authentification et autorisation](time-series-insights-authentication-and-authorization.md). 
1. Installez les dépendances de projet nécessaires.
1. Modifiez l’exemple de code ci-dessous en remplaçant chaque espace réservé **#DUMMY#** par l’identificateur d’environnement qui convient.
1. Exécutez le code dans Visual Studio.

## <a name="project-dependencies"></a>Dépendances du projet

Il est recommandé d’utiliser la version la plus récente de Visual Studio :

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Version 16.4.2+

L’exemple de code comporte deux dépendances obligatoires :

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/), package 3.13.9.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json), package 9.0.1.

Téléchargez les packages dans Visual Studio 2019 en sélectionnant l’option **Générer** > **Générer la solution**.

Vous pouvez également ajouter les packages à l’aide de [NuGet 2.12+](https://www.nuget.org/) :

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Exemple de code C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’interrogation, consultez les [informations de référence sur l’API de requête](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Découvrez comment [connecter une application JavaScript avec le SDK client](https://github.com/microsoft/tsiclient) à Time Series Insights.
