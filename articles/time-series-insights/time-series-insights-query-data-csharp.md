---
title: Interroger des données à partir d’un environnement Azure temps série Insights GA à l’aide C# code | Microsoft Docs
description: Cet article décrit comment interroger des données à partir d’un environnement Azure Time Series Insights en codant une application personnalisée écrite en langage C# (C-sharp) .NET.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/09/2019
ms.custom: seodec18
ms.openlocfilehash: 5e8b8d47b04d7d0b93bc699064ee414bf4429c4a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510190"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Interroger des données à partir de l’environnement Azure temps série Insights GA à l’aideC#

Cela C# exemple montre comment interroger des données à partir de l’environnement Azure temps série Insights GA. 

L’exemple montre plusieurs exemples de base de l’utilisation de l’API de requête :

1. En guise de préparation, obtenez le jeton d’accès via l’API Azure Active Directory. Transmettez le jeton dans l’en-tête `Authorization` de chaque demande de l’API de requête. Pour configurer les applications non interactives, voir [Authentification et autorisation](time-series-insights-authentication-and-authorization.md). Vérifiez aussi que toutes les constantes définies au début de l’exemple sont correctement configurées.
1. La liste des environnements auxquels l’utilisateur a accès est obtenue. L’un des environnements est récupéré en tant qu’environnement d’intérêt, et les autres données sont interrogées pour cet environnement.
1. Exemple de demande HTTPS : les données de disponibilité sont demandées pour l’environnement d’intérêt.
1. Exemple de demande de socket web : les données d’agrégats d’évènement sont demandées pour l’environnement d’intérêt. Des données sont demandées pour l’intégralité de la période de disponibilité.

> [!NOTE]
> L’exemple de code est disponible à l’adresse [ https://github.com/Azure-Samples/Azure-Time-Series-Insights ](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-ga-preview-sample).

## <a name="project-dependencies"></a>Dépendances du projet

Ajout de packages NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory` et `Newtonsoft.Json`.

## <a name="c-example"></a>Exemple en code C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’interrogation, le [référence de l’API de requête](/rest/api/time-series-insights/ga-query-api).

- Lisez la rubrique à [connecter une application à page unique JavaScript](tutorial-create-tsi-sample-spa.md) à Time Series Insights.