---
title: Profiler des services cloud Azure en production avec Application Insights | Microsoft Docs
description: Activez Application Insights Profiler pour Azure Cloud Services.
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
ms.openlocfilehash: 01147f19a6a10361609c01bc6b3f1ac07d1ff86b
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358029"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profiler des services cloud Azure en production avec Application Insights

Vous pouvez aussi déployer Application Insights Profiler sur ces services :
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Applications Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler est installé avec l’extension Azure Diagnostics. Vous devez simplement configurer Azure Diagnostics pour installer Profiler et envoyer des profils à votre ressource Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Activer Profiler pour Azure Cloud Services
1. Veillez à utiliser [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou ultérieur. Il suffit de confirmer que les fichiers *ServiceConfiguration\*.cscfg* ont une valeur `osFamily` au moins égale à « 5 ».

1. Ajoutez le [SDK Application Insights à Azure Cloud Services](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

1. Effectuer le suivi des requêtes avec Application Insights :

    * Pour les rôles web ASP.Net, Application Insights peut suivre automatiquement les demandes.

    * Pour les rôles de travail, [ajoutez du code pour suivre les demandes](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Configurez l’extension Azure Diagnostics pour activer Profiler en effectuant les étapes suivantes :

    a. Recherchez le fichier *diagnostics.wadcfgx* [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) pour votre rôle d’application, comme indiqué ici :  

      ![Recherche du fichier config de diagnostics](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Si vous ne trouvez pas le fichier, consultez [Configurer les diagnostics pour les services Azure Cloud Services et Machines virtuelles](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

    b. Ajoutez la section `SinksConfig` suivante comme élément enfant de `WadCfg` :  

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

    > [!NOTE]
    > Si le fichier *diagnostics.wadcfgx* contient également un autre récepteur de type ApplicationInsights, les trois clés d’instrumentation suivantes doivent correspondre :  
    > * La clé qui est utilisée par votre application. 
    > * La clé qui est utilisée par le récepteur ApplicationInsights. 
    > * La clé qui est utilisée par le récepteur ApplicationInsightsProfiler. 
    >
    > Vous trouverez la valeur réelle de la clé d’instrumentation utilisée par le récepteur `ApplicationInsights` dans les fichiers *ServiceConfiguration\*.cscfg*. 
    > Après la publication du SDK Azure Visual Studio 15.5, seules les clés d’instrumentation utilisées par l’application et le récepteur ApplicationInsightsProfiler doivent correspondre.

1. Déployez votre service avec la nouvelle configuration de diagnostic pour qu’Application Insights Profiler soit configuré pour s’exécuter sur votre service.
 
## <a name="next-steps"></a>Étapes suivantes

* Générez le trafic vers votre application (par exemple, lancez un [test de disponibilité](https://docs.microsoft.com/azure/application-insights/monitor-web-app-availability)). Attendez ensuite 10 à 15 minutes le temps que les traces soient envoyées à l’instance Application Insights.
* Consultez [Traces du profileur](https://docs.microsoft.com/azure/application-insights/profiler-overview?toc=/azure/azure-monitor/toc.json) dans le portail Azure.
* Pour résoudre les problèmes de Profiler, consultez [Résolution des problèmes de Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
