---
title: Interroger des données à partir d’un environnement Azure Time Series Insights Preview à l’aide de code C# | Microsoft Docs
description: Cet article décrit comment interroger des données à partir d’un environnement Azure Time Series Insights en codant une application personnalisée écrite en langage C# (C-sharp) .NET.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: da4e9626b2bf5b180cddba012af81059dd8b67fe
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883278"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Interroger des données à partir de l’environnement Azure Time Series Insights Preview en utilisant C#

Cet exemple de code C# montre comment interroger des données à partir de l’environnement Azure Time Series Insights Preview.

L’exemple montre plusieurs exemples de base de l’utilisation de l’API de requête :

1. En guise de préparation, obtenez le jeton d’accès via l’API Azure Active Directory. Transmettez le jeton dans l’en-tête `Authorization` de chaque demande de l’API de requête. Pour configurer les applications non interactives, voir [Authentification et autorisation](time-series-insights-authentication-and-authorization.md). Vérifiez aussi que toutes les constantes définies au début de l’exemple sont correctement configurées.
1. La liste des environnements auxquels l’utilisateur a accès est obtenue. L’un des environnements est récupéré en tant qu’environnement d’intérêt, et les autres données sont interrogées pour cet environnement.
1. Exemple de demande HTTPS : les données de disponibilité sont demandées pour l’environnement d’intérêt.
1. Exemple de demande de socket web : les données d’agrégats d’évènement sont demandées pour l’environnement d’intérêt. Des données sont demandées pour l’intégralité de la période de disponibilité.

> [!NOTE]
> Cet exemple de code est également disponible à l’adresse [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="c-example"></a>Exemple en code C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> L’exemple de code ci-dessus peut être exécuté sans modification des valeurs d’environnement par défaut.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’interrogation, consultez les [informations de référence sur l’API de requête](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Lisez la rubrique dédiée à la [connexion d’une application monopage JavaScript](tutorial-create-tsi-sample-spa.md) à Time Series Insights.