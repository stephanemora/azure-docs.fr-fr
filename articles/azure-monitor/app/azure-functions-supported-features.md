---
title: Azure Application Insights - Fonctionnalités prises en charge par Azure Functions | Microsoft Docs
description: Fonctionnalités Application Insights prises en charge pour Azure Functions
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 101c985178b8269b4ff542b94b057330d0c2652a
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471662"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Fonctionnalités Application Insights prises en charge pour Azure Functions

Azure Functions offre une [intégration prédéfinie](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) à Application Insights, elle est disponible via l’interface ILogger. La liste des fonctionnalités actuellement prises en charge est indiquée ci-dessous. Servez-vous du guide d’Azure Functions pour [Bien démarrer](https://github.com/Azure/Azure-Functions/wiki/App-Insights).

## <a name="supported-features"></a>Fonctionnalités prises en charge

| Azure Functions                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights SDK .NET**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Collecte automatique de**        |                 |                   |               
| &bull; Requêtes                     | Oui             | Oui               | 
| &bull; Exceptions                   | Oui             | Oui               | 
| &bull; Compteurs de performances         | Oui             | Oui               |
| &bull; Dépendances                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Oui               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | Oui               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | Oui               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Oui               | 
| | | | 
| **Fonctionnalités prises en charge**                |                   |                   |               
| &bull; Pulsation rapide/Métriques temps réel       | Oui             | Oui               | 
| &nbsp;&nbsp;&nbsp;&mdash; Canal de contrôle sécurisé|                 | Oui               | 
| &bull; Échantillonnage                     | Oui             | Oui               | 
| &bull; Pulsations                   |                 | Oui               | 
| | | | 
| **Corrélation**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Oui               | 
| &bull; EventHub                       |                   | Oui               | 
| | | | 
| **Configurable**                      |                   |                   |           
| &bull; Entièrement configurable.<br/>Consultez [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) pour obtenir des instructions.<br/>Consultez [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) pour connaître toutes les options.               |                   | Oui                   | 


## <a name="performance-counters"></a>Compteurs de performance

La collecte automatique des compteurs de performances fonctionne uniquement sur les machines Windows.


## <a name="live-metrics--secure-control-channel"></a>Métriques en temps réel et canal de contrôle sécurisé

Les critères de filtres personnalisés que vous spécifiez sont renvoyés au composant de métriques temps réel dans le Kit de développement logiciel (SDK) Application Insights. Les filtres peuvent potentiellement contenir des informations sensibles telles que des ID clients. Vous pouvez sécuriser le canal avec une clé API secrète. Pour obtenir des instructions, consultez [Sécuriser le canal de contrôle](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel).

## <a name="sampling"></a>échantillonnage

Azure Functions permet l’échantillonnage par défaut dans sa configuration. Pour plus d’informations, consultez [Configurer l’échantillonnage](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Si votre projet ait une dépendance sur le SDK Application Insights pour effectuer la télémétrie manuelle de suivi, vous pouvez rencontrer un comportement étrange si votre configuration d’échantillonnage est différente de la configuration d’échantillonnage des fonctions. 

Nous vous recommandons d’utiliser la même configuration en tant que fonctions. Avec **fonctions v2**, vous pouvez obtenir la même configuration à l’aide de l’injection de dépendances dans votre constructeur :

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
