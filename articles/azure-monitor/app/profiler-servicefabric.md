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
ms.openlocfilehash: f816086293d663e141b2d6efe5791cc8e37eba36
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358590"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profiler des applications Azure Service Fabric en production avec Application Insights

Vous pouvez également déployer Application Insights Profiler sur les services suivants :
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Configurer la définition du déploiement d’environnement

Application Insights Profiler est fourni avec Azure Diagnostics. Vous pouvez installer l'extension Azure Diagnostics à l'aide d'un modèle Azure Resource Manager pour votre cluster Service Fabric. Procurez-vous un [modèle qui installe Azure Diagnostics sur un cluster Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Pour configurer votre environnement, effectuez les actions suivantes :

1. Pour vérifier que vous utilisez [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou version ultérieure, il suffit de confirmer que le système d’exploitation déployé est `Windows Server 2012 R2` ou version ultérieure.

1. Recherchez l'extension [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) dans le fichier du modèle de déploiement.

1. Ajoutez la section `SinksConfig` suivante comme élément enfant de `WadCfg`. Remplacez la valeur de propriété `ApplicationInsightsProfiler` par votre propre clé d’instrumentation Application Insights :  

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

1. Déployez votre cluster Service Fabric à l'aide de votre modèle Azure Resource Manager.  
  Si vos paramètres sont corrects, Application Insights Profiler sera installé et activé une fois l'extension Azure Diagnostics installée. 

1. Ajoutez Application Insights à votre application Service Fabric.  
  Pour que le profileur collecte des profils pour vos requêtes, votre application doit envoyer des données de requête à Application Insights. Pour plus d'informations, consultez la page [Projets du kit de développement logiciel (SDK) Application Insights pour Service Fabric](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

1. Redéployez votre application.

> [ASTUCE] Pour les machines virtuelles, il existe une alternative à la précédente procédure basée sur JSON : sur le portail Azure, accédez à **Machines virtuelles** > **Paramètres de diagnostic** > **Récepteurs** > **, définissez Envoyer des données de diagnostic vers Application Insights sur Activé**, puis sélectionnez un compte Application Insights ou un ikey spécifique.

## <a name="next-steps"></a>Étapes suivantes

* Générez le trafic vers votre application (par exemple, lancez un [test de disponibilité](https://docs.microsoft.com/azure/application-insights/monitor-web-app-availability)). Attendez ensuite 10 à 15 minutes le temps que les traces soient envoyées à l’instance Application Insights.
* Consultez [Traces du profileur](https://docs.microsoft.com/azure/application-insights/profiler-overview?toc=/azure/azure-monitor/toc.json) dans le portail Azure.
* Pour obtenir de l'aide sur la résolution des problèmes liés au profileur, consultez [Résolution des problèmes liés au profileur](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
