---
title: Interroger des données à partir d’un environnement en préversion à l’aide de C# - Azure Time Series Insights | Microsoft Docs
description: Découvrez comment interroger des données à partir d’un environnement Azure Time Series Insights à l’aide d’une application écrite en C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 20c1f1f9a8b0b0ef105893e44c9daaeae68604db
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889751"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Interroger des données à partir de l’environnement Azure Time Series Insights Preview en utilisant C#

Cet exemple de code C# montre comment interroger des données à partir de l’environnement Azure Time Series Insights Preview.

L’exemple montre plusieurs exemples de base de l’utilisation de l’API de requête :

1. En guise de préparation, obtenez le jeton d’accès via l’API Azure Active Directory. Transmettez le jeton dans l’en-tête `Authorization` de chaque demande de l’API de requête. Pour configurer les applications non interactives, voir [Authentification et autorisation](time-series-insights-authentication-and-authorization.md). Vérifiez aussi que toutes les constantes définies au début de l’exemple sont correctement configurées.
1. La liste des environnements auxquels l’utilisateur a accès est obtenue. L’un des environnements est récupéré en tant qu’environnement d’intérêt, et les autres données sont interrogées pour cet environnement.
1. Exemple de demande HTTPS : les données de disponibilité sont demandées pour l’environnement d’intérêt.
1. Fournit un exemple de prise en charge de la génération automatique du Kit de développement logiciel (SDK) à partir d’[Azure AutoRest](https://github.com/Azure/AutoRest).

> [!NOTE]
> L’exemple de code ainsi que les étapes permettant de le compiler et de l’exécuter sont disponibles à l’adresse [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="c-example"></a>Exemple en code C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> L’exemple de code ci-dessus peut être exécuté sans modification des valeurs d’environnement par défaut.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’interrogation, consultez les [informations de référence sur l’API de requête](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Découvrez comment [connecter une application JavaScript avec le SDK client](https://github.com/microsoft/tsiclient) à Time Series Insights.
