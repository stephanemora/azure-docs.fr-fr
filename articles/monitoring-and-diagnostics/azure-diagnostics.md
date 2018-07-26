---
title: Vue d’ensemble de l’extension Azure Diagnostics
description: Utilisation des diagnostics Azure pour le débogage, la mesure des performances, la surveillance, l’analyse du trafic dans Cloud Services, Virtual Machines et Service Fabric
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: b00d774ec59755288b8660d238c7b8dfc9a89eab
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089891"
---
# <a name="what-is-azure-diagnostics-extension"></a>Présentation de l’extension Azure Diagnostics
L’extension Azure Diagnostics est un agent au sein d’Azure qui permet la collecte de données de diagnostic d’une application déployée. Vous pouvez utiliser l'extension de diagnostic à partir de plusieurs sources différentes. Les sources actuellement prises en charge sont les rôles Web et Worker Azure Cloud Service (classique), les machines virtuelles, les groupes de machines virtuelles identiques et Service Fabric. Les autres services Azure ont des méthodes de diagnostic différentes. Consultez [Vue d’ensemble du monitoring dans Azure](monitoring-overview.md). 

## <a name="linux-agent"></a>Agent Linux
Une [version Linux de l’extension](../virtual-machines/linux/diagnostic-extension.md) est disponible pour les machines virtuelles exécutant Linux. Les statistiques collectées ainsi que le comportement varient par rapport à la version Windows. 

## <a name="data-you-can-collect"></a>Données que vous pouvez collecter
L’extension Azure Diagnostics peut collecter les types de données suivants :

| source de données | Description |
| --- | --- |
| Compteurs de performances |Système d’exploitation et compteurs de performances personnalisés |
| Journaux d’application |Messages de trace écrits par votre application |
| Journaux d’événements Windows |Informations envoyées au système de journalisation des événements Windows |
| .NET EventSource |Événements d’écriture de code à l'aide de la classe .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) |
| Journaux IIS |Informations au sujet des sites Web IIS |
| ETW basé sur les manifestes |Événements de suivi d’événements pour Windows générés par n’importe quel processus.(1) |
| Vidages sur incident |Informations sur l’état du processus en cas d’incident d’application |
| Journaux d'erreurs personnalisés |Journaux créés par votre application ou service |
| Journaux d’infrastructure Azure Diagnostic |Informations au sujet des diagnostics eux-mêmes |

(1) pour obtenir la liste des fournisseurs d’ETW, exécutez `c:\Windows\System32\logman.exe query providers` dans une fenêtre de console sur la machine à partir de laquelle vous souhaitez recueillir des informations. 

## <a name="data-storage"></a>Stockage des données
L’extension stocke ses données dans un [compte de stockage Azure](azure-diagnostics-storage.md) que vous spécifiez. 

Vous pouvez également les envoyer vers [Application Insights](../application-insights/app-insights-cloudservices.md). Vous pouvez également les diffuser en continu vers [Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md), ce qui vous permet ensuite de les envoyer vers des services de surveillance non Azure. 


## <a name="versioning-and-configuration-schema"></a>Contrôle de version et schéma de configuration
Consultez [Versions et historique des schémas de configuration de l’extension Azure Diagnostics](azure-diagnostics-versioning-history.md).


## <a name="next-steps"></a>Étapes suivantes
Choisissez le service sur lequel vous voulez collecter des diagnostics et utilisez les articles suivants pour commencer. Utilisez les liens des diagnostics Azure généraux comme référence pour des tâches spécifiques.

## <a name="cloud-services-using-azure-diagnostics"></a>Cloud Services avec les diagnostics Azure
* Si vous utilisez Visual Studio, consultez [Utilisation de Visual Studio pour suivre une application Cloud Services](../vs-azure-tools-debug-cloud-services-virtual-machines.md) pour commencer. Sinon, voir
* [Surveillance de Cloud Services à l’aide des diagnostics Azure](../cloud-services/cloud-services-how-to-monitor.md)
* [Configuration des diagnostics Azure dans une application Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics.md)

Pour des rubriques plus avancées, voir

* [Utilisation des diagnostics avec Application Insights pour Cloud Services](../application-insights/app-insights-cloudservices.md)
* [Assurer le suivi du flux dans une application Cloud Services avec les diagnostics Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Utilisation de PowerShell pour configurer les diagnostics sur Cloud Services](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtual Machines
* Si vous utilisez Visual Studio, consultez [Utilisation de Visual Studio pour suivre les machines virtuelles Azure](../vs-azure-tools-debug-cloud-services-virtual-machines.md) pour commencer. Sinon, voir
* [Configurer les diagnostics Azure sur une machine virtuelle Azure](../virtual-machines-dotnet-diagnostics.md)

Pour des rubriques plus avancées, voir

* [Utilisation de PowerShell pour configurer les diagnostics sur les machines virtuelles Azure](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Créer une machine virtuelle Windows avec des fonctionnalités de surveillance et de diagnostics à l’aide d’un modèle Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Commencez avec [Surveillance d’une application Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). De nombreux autres articles sur les diagnostics Service Fabric sont disponibles dans l'arborescence de navigation sur la gauche, à partir de cet article.

## <a name="general-articles"></a>Articles généraux
* Apprenez à [utiliser les compteurs de performances dans les diagnostics Azure](../cloud-services/diagnostics-performance-counters.md).
* Si vous rencontrez des difficultés avec le lancement des diagnostics ou la recherche des données dans les tables de stockage Azure, consultez [Résolution des problèmes des diagnostics Azure](azure-diagnostics-troubleshooting.md).
