---
title: Activer le Débogueur de capture instantanée pour les applications .NET dans Azure App Service | Microsoft Docs
description: Activer le Débogueur de capture instantanée pour les applications .NET dans Azure App Service
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6928da704236c4bb5492f99a4a5327bf297a323d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84676838"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Activer le Débogueur de capture instantanée pour les applications .NET dans Azure App Service

Le Débogueur de capture instantanée fonctionne actuellement pour les applications ASP.NET et ASP.NET Core s’exécutant sur Azure App Service sur des plans de service Windows.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> Activer le Débogueur de capture instantanée
Pour activer le Débogueur de capture instantanée pour une application, suivez les instructions ci-dessous. Si vous exécutez un autre type de service Azure, voici les instructions à suivre pour activer le Débogueur de capture instantanée sur d’autres plateformes prises en charge :
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Services Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles Azure et Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles ou physiques locales](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Si vous utilisez une préversion de .NET Core, suivez les instructions pour [Activer le débogueur de capture instantanée pour d’autres environnements](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) pour d’abord inclure le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) avec l’application, puis effectuez le reste des instructions ci-dessous. 

Le Débogueur de capture instantanée Application Insights est préinstallé dans le cadre de l’exécution d’App Services, mais vous devez l’activer pour obtenir des captures instantanées pour votre application App Service. Une fois que vous avez déployé une application, même si vous avez inclus le SDK Application Insights dans le code source, suivez les étapes ci-dessous pour activer le débogueur de capture instantanée.

1. Accédez au panneau de configuration Azure pour votre App Service.
2. Accédez à la page **Paramètres > Application Insights**.

   ![Activer App Insights sur le portail App Services](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Suivez les instructions dans la page pour créer une ressource ou sélectionnez une ressource App Insights existante pour superviser votre application. Vérifiez également que les deux commutateurs du Débogueur de capture instantanée sont définis sur **On** (Activé).

   ![Ajouter l’extension de site Application Insights][Enablement UI]

4. Le Débogueur de capture instantanée est maintenant activé à l’aide d’un paramètre d’application App Services.

    ![Paramètre d’application pour le Débogueur de capture instantanée][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Désactiver le Débogueur de capture instantanée

Suivez les mêmes étapes que pour **Activer le Débogueur de capture instantanée**, mais basculez les deux commutateurs du Débogueur de capture instantanée sur **Off** (Désactivé).
Nous vous recommandons d’avoir le Débogueur de capture instantanée activé sur toutes vos applications afin de faciliter le diagnostic des exceptions d’application.

## <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager

Pour un service Azure App Service, vous pouvez définir des paramètres d’application dans un modèle Resource Manager pour activer le Débogueur de capture instantanée et le Profileur. Vous ajoutez une ressource de configuration contenant les paramètres de l’application en tant que ressource enfant du site web :

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

