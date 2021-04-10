---
title: Activer le Débogueur de capture instantanée pour les applications .NET dans Azure App Service | Microsoft Docs
description: Activer le Débogueur de capture instantanée pour les applications .NET dans Azure App Service
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 26538f48213d025c6fe71fb55abb17a025a23b45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025677"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Activer le Débogueur de capture instantanée pour les applications .NET dans Azure App Service

Le Débogueur de capture instantanée prend en charge les applications ASP.NET et ASP.NET Core qui s’exécutent sur Azure App Service dans des plans de service Windows.

Lorsque vous utilisez le Débogueur de capture instantanée, nous vous recommandons d’exécuter votre application au niveau de service De base ou supérieur.

Pour la plupart des applications, la mémoire disponible ou l’espace disque avec les niveaux de service Gratuit et Partagé est insuffisant pour enregistrer les captures instantanées.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> Activer le Débogueur de capture instantanée
Pour activer le Débogueur de capture instantanée pour une application, suivez les instructions ci-dessous.

Si vous exécutez un autre type de service Azure, voici les instructions à suivre pour activer Débogueur de capture instantanée sur d’autres plateformes prises en charge :
* [Fonction Azure](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Services Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles Azure et Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles ou physiques locales](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> Si vous utilisez une préversion de .NET Core ou si votre application référence le SDK Application Insights, directement ou indirectement via un assembly dépendant, suivez les instructions permettant d’[activer le Débogueur de capture instantanée pour d’autres environnements](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) en vue d’inclure le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) avec l’application, puis suivez le reste des instructions ci-dessous. 
>
> L’installation sans code du Débogueur de capture instantanée Application Insights suit la politique de support .NET Core.
> Pour plus d’informations sur les runtimes pris en charge, consultez [Politique de support .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

Débogueur de capture instantanée est préinstallé dans le cadre du runtime d’App Services, mais vous devez l’activer pour obtenir des captures instantanées pour votre application App Service.

Une fois que vous avez déployé une application, suivez les étapes ci-dessous pour activer le débogueur de capture instantanée :

1. Accédez au panneau de configuration Azure pour votre App Service.
2. Accédez à la page **Paramètres > Application Insights**.

   ![Activer App Insights sur le portail App Services](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Suivez les instructions dans la page pour créer une ressource ou sélectionnez une ressource App Insights existante pour superviser votre application. Vérifiez également que les deux commutateurs du Débogueur de capture instantanée sont définis sur **On** (Activé).

   ![Ajouter l’extension de site Application Insights][Enablement UI]

4. Le Débogueur de capture instantanée est maintenant activé à l’aide d’un paramètre d’application App Services.

    ![Paramètre d’application pour le Débogueur de capture instantanée][snapshot-debugger-app-setting]

## <a name="enable-snapshot-debugger-for-other-clouds"></a>Activer le Débogueur de capture instantanée pour les autres clouds

Seules les régions [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) et [Azure Chine](/azure/china/resources-developer-guide) nécessitent la modification de leurs points de terminaison par le biais d’une chaîne de connexion Application Insights.

|Propriété de chaîne de connexion    | Cloud US Government | China Cloud |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Pour plus d’informations sur les autres substitutions de connexion, consultez la [documentation Application Insights](./sdk-connection-string.md?tabs=net#connection-string-with-explicit-endpoint-overrides).

## <a name="disable-snapshot-debugger"></a>Désactiver le Débogueur de capture instantanée

Suivez les mêmes étapes que pour **Activer le Débogueur de capture instantanée**, mais basculez les deux commutateurs du Débogueur de capture instantanée sur **Off** (Désactivé).

Nous vous recommandons d’activer Débogueur de capture instantanée sur toutes vos applications afin de faciliter le diagnostic des exceptions d’application.

## <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager

Pour un service Azure App Service, vous pouvez définir des paramètres d’application dans le modèle Azure Resource Manager pour activer Débogueur de capture instantanée et Profiler, voir l’extrait de code du modèle ci-dessous :

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>Étapes suivantes

- Générez du trafic vers votre application pouvant déclencher une exception. Attendez ensuite 10 à 15 minutes le temps que des captures instantanées soient envoyées à l’instance Application Insights.
- Consultez [Captures instantanées](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) dans le portail Azure.
- Pour obtenir de l’aide sur la résolution des problèmes liés au débogueur de capture instantanée, consultez [Résolution des problèmes liés au Débogueur de capture instantanée](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png