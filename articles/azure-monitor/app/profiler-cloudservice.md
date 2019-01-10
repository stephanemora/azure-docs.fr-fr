---
title: Profiler des services cloud Azure en production avec Application Insights | Microsoft Docs
description: Activer Application Insights Profiler pour Cloud Services.
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
ms.openlocfilehash: 6ae662c57c5196ff495edafeee0d6ba5f79e76d1
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081890"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profiler des services cloud Azure en production avec Application Insights

Vous pouvez également déployer Application Insights Profiler sur ces services :
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Applications Service Fabric](profiler-servicefabric.md ?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler est installé avec l’extension Windows Azure Diagnostics (WAD). Il vous suffit de configurer WAD pour installer le profileur et envoyer des profils à votre ressource Application Insights.

## <a name="enable-profiler-for-your-azure-cloud-service"></a>Activer le profileur pour votre service cloud Azure
1. Vérifiez que vous utilisez [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou version ultérieure.  Il suffit de confirmer que les fichiers *ServiceConfiguration\*.cscfg* ont une valeur `osFamily` au moins égale à « 5 ».
1. Ajouter le [kit de développement logiciel (SDK) Application Insights à un service cloud](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).
1. Effectuer le suivi des requêtes avec Application Insights :

    Pour les rôles web ASP.Net, Application Insights peut suivre automatiquement les requêtes.

    Pour les rôles de travail, [ajouter du code pour effectuer le suivi des requêtes.](profiler-trackrequests.md ?toc=/azure/azure-monitor/toc.json)

    

1. Configurez l’extension de Windows Azure Diagnostics (WAD) pour activer le profileur.



    1. Recherchez le fichier *diagnostics.wadcfgx* [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) pour votre rôle d’application, comme indiqué ici :  

       ![Recherche du fichier config de diagnostics](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

        Pour savoir comment activer l’extension Diagnostics dans votre projet Azure Cloud Services si vous ne trouvez pas le fichier, consultez [Configurer les diagnostics pour les services cloud et les machines virtuelles Azure](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

    1. Ajoutez la section `SinksConfig` suivante comme élément enfant de `WadCfg` :  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    >   **REMARQUE :** Si le fichier diagnostics.wadcfgx* contient également un autre récepteur de type `ApplicationInsights`, ces trois clés d’instrumentation doivent correspondre :  
    >  * La clé qui est utilisée par votre application.  
    >  * La clé qui est utilisée par le récepteur `ApplicationInsights`.  
    >  * La clé qui est utilisée par le récepteur `ApplicationInsightsProfiler`.  
    >
    > Vous trouverez la valeur réelle de la clé d’instrumentation utilisée par le récepteur `ApplicationInsights` dans les fichiers *ServiceConfiguration\*.cscfg*.  
    > Après la publication du Kit de développement logiciel (SDK) Azure Visual Studio 15.5, seules les clés d’instrumentation utilisées par l’application et le récepteur `ApplicationInsightsProfiler` doivent correspondre.
1. Déployez votre service avec la nouvelle configuration de diagnostique, et d’Application Insights Profiler sera configuré pour s’exécuter sur votre service.
 
## <a name="next-steps"></a>Étapes suivantes

- Générez le trafic vers votre application (par exemple, lancez un [test de disponibilité](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Attendez ensuite 10 à 15 minutes le temps que les traces soient envoyées à l’instance Application Insights.
- Consultez [Traces du profileur](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) dans le portail Azure.
- Consultez l’article [Résolution des problèmes du profileur](profiler-troubleshooting.md ?toc=/azure/azure-monitor/toc.json) pour obtenir de l’aide pour résoudre les problèmes rencontrés avec le profileur.