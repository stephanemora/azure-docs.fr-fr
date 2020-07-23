---
title: Profiler des services cloud Azure en production avec Application Insights | Microsoft Docs
description: Activez Application Insights Profiler pour Azure Cloud Services.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: eba250f3ca6a7af8480d24a99e6c3e8278009b82
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499441"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profiler des services cloud Azure en production avec Application Insights

Vous pouvez également déployer Application Insights Profiler sur les services suivants :
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Applications Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler est installé avec l’extension Diagnostics Azure. Vous devez simplement configurer Diagnostics Azure pour installer Profiler et envoyer des profils à votre ressource Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Activer Profiler pour Azure Cloud Services
1. Veillez à utiliser [.NET Framework 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou une version plus récente. Si vous utilisez la famille OS 4, vous devez installer .NET Framework 4.6.1 ou une version plus récente avec une [tâche de démarrage](../../cloud-services/cloud-services-dotnet-install-dotnet.md). La famille OS 5 comprend une version compatible de .NET Framework par défaut. 

1. Ajoutez le [SDK Application Insights à Azure Cloud Services](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

    **Le bogue du profileur fourni avec WAD pour les Services cloud a été corrigé.** La dernière version de WAD (1.12.2.0) pour les Services cloud fonctionne avec toutes les versions récentes du kit de développement logiciel (SDK) App Insights. Les hôtes des Services cloud mettent automatiquement à niveau WAD, mais ce n'est pas immédiat. Pour forcer une mise à niveau, vous pouvez redéployer votre service ou redémarrer le nœud.

1. Effectuer le suivi des requêtes avec Application Insights :

    * Pour les rôles web ASP.Net, Application Insights peut suivre automatiquement les demandes.

    * Pour les rôles de travail, [ajoutez du code pour suivre les demandes](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Configurez l'extension Diagnostics Azure pour activer Profiler :

    a. Recherchez le fichier [diagnostics.wadcfgx](../platform/diagnostics-extension-overview.md) de *Diagnostics Azure* pour votre rôle d’application, comme indiqué ici :  

      ![Recherche du fichier config de diagnostics](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Si vous ne trouvez pas le fichier, consultez [Configurer les diagnostics pour les services Azure Cloud Services et Machines virtuelles](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

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

* Générez le trafic vers votre application (par exemple, lancez un [test de disponibilité](monitor-web-app-availability.md)). Attendez ensuite 10 à 15 minutes le temps que les traces soient envoyées à l’instance Application Insights.
* Consultez [Traces du profileur](profiler-overview.md?toc=/azure/azure-monitor/toc.json) dans le portail Azure.
* Pour résoudre les problèmes de Profiler, consultez [Résolution des problèmes de Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
