---
title: Profiler des applications Azure App Service en production avec Application Insights | Microsoft Docs
description: Profilez des applications en production sur Azure App Service avec Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 2a5ba6cb0778e0c013f150d787d694b23f0497bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218007"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profiler des applications Azure App Service en production avec Application Insights

Vous pouvez exécuter Profiler sur des applications ASP.NET et ASP.NET Core qui s’exécutent sur Azure App Service avec le niveau de service De base ou un niveau supérieur. À l’heure actuelle, l’activation de Profiler sur Linux est uniquement possible via [cette méthode](profiler-aspnetcore-linux.md).

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> Activer Profiler pour votre application
Pour activer Profiler pour une application, suivez les instructions ci-dessous. Si vous exécutez un autre type de service Azure, voici les instructions permettant d’activer Profiler sur d’autres plateformes prises en charge :
* [Cloud Services](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Applications Service Fabric](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Machines virtuelles](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Application Insights Profiler est préinstallé dans le cadre de l’exécution d’App Service. Les étapes ci-dessous vous montrent comment l’activer pour votre App Service. Suivez ces étapes même si vous avez inclus le SDK App Insights dans votre application au moment de la génération.

> [!NOTE]
> L’installation sans code d’Application Insights Profiler suit la politique de support .NET Core.
> Pour plus d’informations sur les runtimes pris en charge, consultez [Politique de support .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

1. Accédez au panneau de configuration Azure pour votre App Service.
1. Activez le paramètre « Always On » pour votre service d’application. Pour accéder à ce paramètre, allez dans la page **Paramètres**, **Configuration** (voir la capture d’écran de l’étape suivante), puis sélectionnez l’onglet **Paramètres généraux**.
1. Accédez à la page **Paramètres > Application Insights**.

   ![Activer App Insights sur le portail App Services](./media/profiler/AppInsights-AppServices.png)

1. Suivez les instructions dans le volet pour créer une ressource ou sélectionnez une ressource App Insights existante pour superviser votre application. Vérifiez également que le profileur est **activé**. Si votre ressource Application Insights se trouve dans un autre abonnement que votre App Service, vous ne pouvez pas utiliser cette page pour configurer Application Insights. Toutefois, vous pouvez toujours le faire manuellement en créant manuellement les paramètres d’application nécessaires. [La section suivante contient des instructions permettant d’activer manuellement Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Ajouter l’extension de site Application Insights][Enablement UI]

1. Le profileur est maintenant activé à l’aide d’un paramètre d’application App Services.

    ![Paramètre d’application pour Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Activer Profiler manuellement ou avec Azure Resource Manager
Application Insights Profiler peut être activé en créant des paramètres d’application pour votre Azure App Service. La page avec les options affichées ci-dessus crée automatiquement ces paramètres d’application. Mais vous pouvez automatiser la création de ces paramètres à l’aide d’un modèle ou d’autres moyens. Ces paramètres fonctionnent également si votre ressource Application Insights se trouve dans un autre abonnement que votre Azure App Service.
Voici les paramètres nécessaires pour activer le profileur :

|Paramètre d'application    | Valeur    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey pour votre ressource Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Vous pouvez définir ces valeurs à l’aide de [modèles Azure Resource Manager](./azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), d’[Azure PowerShell](/powershell/module/az.websites/set-azwebapp) ou d’[Azure CLI](/cli/azure/webapp/config/appsettings).

## <a name="enable-profiler-for-other-clouds"></a>Activer Profiler pour d’autres Clouds

Actuellement, seules les régions [Azure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) et [Azure Chine](https://docs.microsoft.com/azure/china/resources-developer-guide) nécessitent des modifications de leurs points de terminaison.

|Paramètre d'application    | Cloud US Government | China Cloud |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Désactiver Profiler

Si vous souhaitez arrêter ou redémarrer Profiler pour une instance d’application, dans la barre latérale gauche, sélectionnez **WebJobs**, puis arrêtez le WebJob nommé `ApplicationInsightsProfiler3`.

  ![Désactiver Profiler pour une tâche web][disable-profiler-webjob]

Nous vous recommandons d’activer dès que possible Profiler sur toutes vos applications afin de découvrir d’éventuels problèmes de performance.

Les fichiers de Profiler peuvent être supprimés quand vous utilisez WebDeploy pour déployer des changements sur votre application web. Vous pouvez empêcher la suppression en excluant le dossier App_Data des éléments étant supprimés au cours du déploiement. 


## <a name="next-steps"></a>Étapes suivantes

* [Utilisation d’Application Insights dans Visual Studio](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png

