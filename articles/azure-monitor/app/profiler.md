---
title: Profiler des applications Azure App Service en production avec Application Insights | Microsoft Docs
description: Profilez des applications en production sur Azure App Service avec Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: f3ec10a970406cbb1bb6a1a52ffa8508e37fc516
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414165"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profiler des applications Azure App Service en production avec Application Insights

Profiler fonctionne actuellement pour les applications ASP.NET et ASP.NET Core s’exécutant sur Azure App Service. L’utilisation de Profiler nécessite au minimum le niveau de service De base. À l’heure actuelle, l’activation de Profiler sur Linux est uniquement possible via [cette méthode](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Activer Profiler pour votre application
Pour activer Profiler pour une application, suivez les instructions ci-dessous. Si vous exécutez un autre type de service Azure, voici les instructions nécessaires pour activer Profiler sur d’autres plateformes prises en charge :
* [Cloud Services](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Applications Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler est préinstallé dans le cadre de l’exécution d’App Services, mais vous devez l’activer pour obtenir des profils pour votre application App Service. Une fois que vous avez déployé une application, même si vous avez inclus le SDK App Insights dans le code source, suivez les étapes ci-dessous pour activer le profileur.

1. Accédez au volet **App Services** dans le portail Azure.
2. Accédez au volet **Paramètres > Application Insights**.

   ![Activer App Insights sur le portail App Services](./media/profiler/AppInsights-AppServices.png)

3. Suivez les instructions dans le volet pour créer une ressource ou sélectionnez une ressource App Insights existante pour superviser votre application. Vérifiez également que le profileur est **activé**.

   ![Ajouter l’extension de site Application Insights][Enablement UI]

4. Le profileur est maintenant activé à l’aide d’un paramètre d’application App Services.

    ![Paramètre d’application pour Profiler][profiler-app-setting]

## <a name="disable-profiler"></a>Désactiver Profiler

Pour arrêter ou redémarrer Profiler pour une instance d’application individuelle, accédez à la ressource d’application sous **Travaux web**. Pour supprimer Profiler, accédez à **Extensions**.

![Désactiver Profiler pour une tâche web][disable-profiler-webjob]

Nous vous recommandons d’activer dès que possible Profiler sur toutes vos applications afin de découvrir d’éventuels problèmes de performance.

Si vous utilisez WebDeploy pour déployer des modifications sur votre application web, veillez à ne pas supprimer le dossier App_Data lors du déploiement. Dans le cas contraire, les fichiers de l’extension de Profiler seront supprimés la prochaine fois que vous déploierez l’application web dans Azure.



## <a name="next-steps"></a>Étapes suivantes

* [Utilisation d’Application Insights dans Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
