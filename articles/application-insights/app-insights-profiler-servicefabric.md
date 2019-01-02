---
title: Profiler des applications Azure Service Fabric en production avec Application Insights | Microsoft Docs
description: Activer le profileur pour une application Service Fabric
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 3eb5b2300ea2af7bc778e0831d105f286eab247c
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721406"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profiler des applications Azure Service Fabric en production avec Application Insights

Vous pouvez également déployer Application Insights Profiler sur ces services :
* [Azure Web Apps](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


## <a name="set-up-the-environment-deployment-definition"></a>Configurer la définition du déploiement d’environnement

Application Insights Profiler est fourni avec Windows Azure Diagnostics (WAD). L’extension WAD peut être installée à l’aide d’un modèle Azure RM pour votre cluster Service Fabric. Vous trouverez un exemple de modèle ici : [**Modèle qui installe WAD sur un cluster Service Fabric.**](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Pour configurer votre environnement, effectuez les actions suivantes :
1. Pour vérifier que vous utilisez [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou version ultérieure, il suffit de confirmer que le système d’exploitation déployé est `Windows Server 2012 R2` ou version ultérieure.

1. Recherchez l’extension [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) dans le fichier de modèle de déploiement, puis ajoutez la section `SinksConfig` suivante comme élément enfant de `WadCfg`. Remplacez la valeur de propriété `ApplicationInsightsProfiler` par votre propre clé d’instrumentation Application Insights :  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Pour plus d’informations sur l’ajout de l’extension Diagnostics à votre modèle de déploiement, consultez [Utiliser la surveillance et les diagnostics avec une machine virtuelle Windows et des modèles Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
1. Déployez votre cluster Service Fabric à l’aide de votre modèle Azure Resource Manager. Si vos paramètres sont corrects, Application Insights Profiler sera installé et activé une fois l’extension WAD installée. 
1. Ajoutez Application Insights à votre application Service Fabric. Votre application doit envoyer des données de requête à Application Insights afin que le profileur collecte des profils pour vos requêtes. Vous trouverez des instructions [ici](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).
1. Redéployez votre application.

> [ASTUCE] Pour les machines virtuelles, une alternative à la procédure basée sur json ci-dessus consiste à naviguer dans le portail Azure vers **Machines virtuelles** > **Paramètres de diagnostic** > **Récepteurs** > Définissez Envoyer des données de diagnostic vers Application Insights sur **Activé** et sélectionnez un compte Application Insights ou un ikey spécifique.

## <a name="next-steps"></a>Étapes suivantes

- Générez le trafic vers votre application (par exemple, lancez un [test de disponibilité](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Attendez ensuite 10 à 15 minutes le temps que les traces soient envoyées à l’instance Application Insights.
- Consultez [Traces du profileur](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) dans le portail Azure.
- Consultez l’article [Résolution des problèmes du profileur](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json) pour obtenir de l’aide pour résoudre les problèmes rencontrés avec le profileur.