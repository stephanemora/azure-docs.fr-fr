---
title: Versions de Azure Service Fabric
description: Notes de publication sur les dernières fonctionnalités et améliorations de Service Fabric.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 829963cf13eb1dc1b1113b6a296fe77dadb63bc4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224948"
---
# <a name="service-fabric-releases"></a>Versions de Azure Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Guides de dépannage</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">Suivi des problèmes</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Options de support</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">Versions prises en charge</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Exemples de code</a>

Cet article fournit des informations sur les version et mises à jour les plus récentes du runtime Service Fabric et des Kits de développement logiciel (SDK).

## <a name="whats-new-in-service-fabric"></a>Nouveautés de Service Fabric

### <a name="service-fabric-65"></a>Service Fabric 6.5

La dernière version de Service Fabric inclut la prise en charge, la fiabilité, les améliorations de performances, les nouvelles fonctionnalités, les correctifs de bogues et les améliorations destinées à faciliter la gestion de cycle de vie du cluster et des applications.

> [!IMPORTANT]
> Service Fabric 6.5 est la version finale avec prise en charge des outils Service Fabric dans Visual Studio 2015. Les clients sont invités à passer à [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) à l’avenir.

Voici les nouveautés de Service Fabric 6.5 :

- Service Fabric Explorer inclut [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) pour examiner les applications téléchargées dans le magasin d’images.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) version [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) comprend de nombreuses améliorations d’autodiagnostic. Il est recommandé aux clients de POA de passer à cette version.

- [EventStore Service est activé par défaut](service-fabric-visualizing-your-cluster.md#event-store) pour les clusters Service Fabric 6.5, sauf si vous l’avez refusé.

- Ajout d’[événements de cycle de vie des réplicas](service-fabric-diagnostics-event-generation-operational.md#replica-events) pour les services avec état.

- [Meilleure visibilité de l’état du nœud initial](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), y compris des avertissements au niveau du cluster si un nœud initial est incorrect (*Inactif*, *Supprimé* ou *Inconnu*).

- L’[outil de récupération d’urgence de l’application Service Fabric](https://github.com/Microsoft/Service-Fabric-AppDRTool) permet une récupération rapide en cas d’incident sur le cluster principal. Les données du cluster principal sont synchronisées en continu sur l’application secondaire de secours à l’aide de la restauration et de sauvegarde périodiques.

- Prise en charge de Visual Studio pour la [publication d’applications .NET Core sur les clusters Linux](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) sera automatiquement installé pour Service Fabric 6.5 (et versions ultérieures) lors de la mise à niveau ou de la création d’un nouveau cluster Linux sur Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) est installé par défaut sur les clusters OneBox Linux/Mac OS.

Pour plus d’informations, consultez les [Notes de publication relatives à Service Fabric 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Versions Release de Service Fabric 6.5

| Date de lancement | Libérer | En savoir plus |
|---|---|---|
| 11 juin 2019 | [Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Notes de publication](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 juillet 2019 | [Version Refresh Release d’Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Notes de publication](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |

## <a name="previous-versions"></a>Versions antérieures

### <a name="service-fabric-64-releases"></a>Versions de Service Fabric 6.4

| Date de lancement | Libérer | En savoir plus |
|---|---|---|
| 30 novembre 2018 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Notes de publication](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 décembre 2018 | [Azure Service Fabric 6.4 Refresh Release for Windows clusters](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Notes de publication](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 février 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Notes de publication](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 mars 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Notes de publication](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 avril 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Notes de publication](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 mai 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Notes de publication](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 mai 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Notes de publication](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
