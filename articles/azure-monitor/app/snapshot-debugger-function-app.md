---
title: Activer le Débogueur de capture instantanée pour les applications .NET et .NET Core dans Azure Functions | Microsoft Docs
description: Activer le Débogueur de capture instantanée pour les applications .NET et .NET Core dans Azure Functions
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: ac25962cac36a149807b67a44b3b88a4f40c954a
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211938"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>Activer le Débogueur de capture instantanée pour les applications .NET et .NET Core dans Azure Functions

Le Débogueur de capture instantanée fonctionne actuellement pour les applications ASP.NET et ASP.NET Core s’exécutant sur Azure Functions sur des plans de service Windows.

Lorsque vous utilisez le Débogueur de capture instantanée, nous vous recommandons d’exécuter votre application au niveau de service De base ou supérieur.

Pour la plupart des applications, la mémoire disponible ou l’espace disque avec les niveaux de service Gratuit et Partagé est insuffisant pour enregistrer les captures instantanées.

## <a name="prerequisites"></a>Prérequis

* [Activer la surveillance d’Application Insights dans votre application de fonction](../../azure-functions/configure-monitoring.md#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a> Activer le Débogueur de capture instantanée

Si vous exécutez un autre type de service Azure, voici les instructions à suivre pour activer Débogueur de capture instantanée sur d’autres plateformes prises en charge :
* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Services Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles Azure et Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles ou physiques locales](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Pour activer le Débogueur de capture instantanée dans votre application de fonction, vous devez mettre à jour votre fichier `host.json` en ajoutant la propriété `snapshotConfiguration` comme indiqué ci-dessous et redéployer votre fonction.

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

Le Débogueur de capture instantanée est préinstallé dans le cadre du runtime Azure Functions. Il est désactivé par défaut.

Étant donné que le Débogueur de capture instantanée est inclus dans le runtime Azure Functions, il n’est pas nécessaire d’ajouter des packages NuGet ou des paramètres d’application supplémentaires.

Pour information, pour une simple application de fonction (.NET Core), voici comment se présentent les paramètres `.csproj`, `{Your}Function.cs` et `host.json` une fois le Débogueur de capture instantanée activé.

Projet csproj

```xml
<Project Sdk="Microsoft.NET.Sdk">
<PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.31" />
</ItemGroup>
<ItemGroup>
    <None Update="host.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
</ItemGroup>
</Project>
```

Classe de fonction

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace SnapshotCollectorAzureFunction
{
    public static class ExceptionFunction
    {
        [FunctionName("ExceptionFunction")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            throw new NotImplementedException("Dummy");
        }
    }
}
```

Fichier hôte

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

## <a name="enable-snapshot-debugger-for-other-clouds"></a>Activer le Débogueur de capture instantanée pour les autres clouds

Actuellement, seules les régions [Azure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) et [Azure Chine](https://docs.microsoft.com/azure/china/resources-developer-guide) nécessitent des modifications de leurs points de terminaison.

Voici un exemple d’un `host.json` mis à jour avec le point de terminaison de l’agent cloud US Government :
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

Voici les valeurs de substitution prises en charge pour le point de terminaison de l’agent Débogueur de capture instantanée :

|Propriété    | Cloud US Government | China Cloud |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

## <a name="disable-snapshot-debugger"></a>Désactiver le Débogueur de capture instantanée

Pour désactiver le Débogueur de capture instantanée dans votre application de fonction, il vous suffit de mettre à jour votre fichier `host.json` en définissant sur `false` la propriété `snapshotConfiguration.isEnabled`.

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": false
      }
    }
  }
}
```

Nous vous recommandons d’activer Débogueur de capture instantanée sur toutes vos applications afin de faciliter le diagnostic des exceptions d’application.

## <a name="next-steps"></a>Étapes suivantes

- Générez du trafic vers votre application pouvant déclencher une exception. Attendez ensuite 10 à 15 minutes le temps que des captures instantanées soient envoyées à l’instance Application Insights.
- [Afficher les captures instantanées](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) dans le portail Azure.
- Personnalisez la configuration du Débogueur de capture instantanée en fonction de votre utilisation sur votre application de fonction. Pour plus d’informations, consultez [Configuration des captures instantanés dans host.json](../../azure-functions/functions-host-json.md#applicationinsightssnapshotconfiguration).
- Pour obtenir de l’aide sur la résolution des problèmes liés au débogueur de capture instantanée, consultez [Résolution des problèmes liés au Débogueur de capture instantanée](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).