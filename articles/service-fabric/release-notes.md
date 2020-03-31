---
title: Versions de Azure Service Fabric
description: Notes de publication pour Azure Service Fabric. Inclut des informations sur les dernières fonctionnalités et améliorations incluses dans Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: cb4fdd56e9cf67c71ac690d423499929167f8977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064239"
---
# <a name="service-fabric-releases"></a>Versions de Azure Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Guides de dépannage</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">Suivi des problèmes</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Options de support</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">Versions prises en charge</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Exemples de code</a>

Cet article fournit des informations sur les version et mises à jour les plus récentes du runtime Service Fabric et des Kits de développement logiciel (SDK).

## <a name="whats-new-in-service-fabric"></a>Nouveautés de Service Fabric

### <a name="service-fabric-70"></a>Service Fabric 7.0

Azure Service Fabric 7.0 est désormais disponible ! Vous pouvez effectuer la mise à jour vers 7.0 via le Portail Azure ou via un déploiement d’Azure Resource Manager. En raison des commentaires des clients sur les versions pendant les vacances, nous ne commencerons pas à mettre à jour automatiquement les clusters définis pour recevoir des mises à niveau automatiques jusqu’au mois de janvier.
En janvier, nous reprendrons la procédure de déploiement standard et les clusters avec des mises à niveau automatiques activées commenceront à recevoir automatiquement la mise à jour 7.0. Nous ferons une autre annonce avant le début du déploiement.
Nous mettrons également à jour les dates de publication planifiées pour indiquer que cette stratégie est prise en compte. Recherchez des mises à jour sur nos futures [planifications de version](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule).
 
Il s’agit de la dernière version de Service Fabric qui est chargée avec des fonctionnalités et améliorations clés.

### <a name="key-announcements"></a>Principales annonces
 - [**Prise en charge de KeyVaultReference pour les secrets de l’application (préversion)** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references) : Les applications Service Fabric qui ont activé [Identités managées](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) peuvent désormais référencer directement une URL de secret Key Vault en tant que variable d’environnement, paramètre d’application ou informations d’identification de référentiel de conteneur. Service Fabric résoudra automatiquement le secret à l’aide de l’identité managée de l’application. 
     
- **Amélioration de la sécurité de la mise à niveau pour les services sans état** : Pour garantir la disponibilité lors de la mise à niveau d’une application, nous avons introduit de nouvelles configurations pour définir le [nombre minimal d’instances pour les services sans état](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) à considérer comme disponibles. Auparavant, cette valeur était 1 pour tous les services et n’était pas modifiable. Grâce à cette nouvelle vérification de la sécurité par service, vous pouvez vous assurer que vos services conservent un nombre minimal d’instances lors des mises à niveau de l’application, des mises à niveau de cluster et d’autres opérations de maintenance qui s’appuient sur les contrôles d’intégrité et de sécurité de Service Fabric.
  
- [**Limites des ressources des services utilisateur**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services) : Les utilisateurs peuvent configurer des limites de ressources pour les services utilisateur sur un nœud afin d’éviter des scénarios tels que l’épuisement des ressources des services système Service Fabric. 
  
- [**Coût de déplacement de service très élevé**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) pour un type de réplica. Les réplicas avec un coût de déplacement « très élevé » sont déplacés uniquement si une violation de contrainte existant dans le cluster ne peut pas être résolue d’une autre façon. Consultez les documents pour plus d’informations sur l’utilisation raisonnable du coût de déplacement « très élevé » et sur les considérations supplémentaires.
  
-  **Vérifications supplémentaires de la sécurité des clusters** : Dans cette version, nous avons introduit une vérification de la sécurité du quorum de nœuds initiaux configurables. Cela vous permet de personnaliser le nombre de nœuds initiaux qui doivent être disponibles pendant les scénarios de gestion et de cycle de vie du cluster. Les opérations qui prennent le cluster en dessous de la valeur configurée sont bloquées. Aujourd’hui la valeur par défaut est toujours un quorum de nœuds initiaux. Par exemple, si vous avez 7 nœuds initiaux, une opération qui nécessiterait en dessous de 5 nœuds initiaux serait bloquée par défaut. Avec cette modification, vous pourriez définir la valeur de sécurité minimale sur 6, ce qui permettrait d’autoriser seulement un nœud initial d’être en panne à la fois.
   
- Support ajouté pour [**gérer le service de sauvegarde et de restauration dans Service Fabric Explorer**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster). Cela rend les activités suivantes possibles directement depuis SFX : découverte du service de sauvegarde et de restauration, création d’une politique de sauvegarde, activation des sauvegardes automatiques, prise de sauvegardes ad hoc, déclenchement d’opération de restauration et recherche de sauvegarde existantes.

- Annonce de la disponibilité de [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool) : Cet outil permet de valider que les types utilisés dans les collections fiables ont une compatibilité ascendante et descendante pendant le déploiement d’une mise à niveau d’application. Cela permet d’éviter les échecs de mise à niveau ou la perte et l’altération de données en raison de types manquants ou incompatibles.

- [**Activer les lectures stables sur les réplicas secondaires**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1) : le fait d’activer les lectures stables aura pour effet de restreindre les réplicas secondaires aux valeurs retournées demandées par le quorum.

De plus, cette version contient d’autres nouvelles fonctionnalités, corrections de bogues, et améliorations en termes de prise en charge, de fiabilité et de performances. Pour la liste complète des modifications, reportez-vous aux [notes de publication](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Mises en production Service Fabric 7.0

| Date de publication | Libérer | En savoir plus |
|---|---|---|
| 18 novembre 2019 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Notes de publication](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 janvier 2020 | [Mise en production actualisée d’Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Notes de publication](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 février 2020 | [Mise en production actualisée d’Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Notes de publication](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6.5

Cette version inclut la prise en charge, la fiabilité, les améliorations de performances, les nouvelles fonctionnalités, les correctifs de bogues et les améliorations destinées à faciliter la gestion de cycle de vie du cluster et des applications.

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

| Date de publication | Libérer | En savoir plus |
|---|---|---|
| 11 juin 2019 | [Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Notes de publication](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 juillet 2019 | [Version Refresh Release d’Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Notes de publication](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 juillet 2019 | [Version Refresh Release d’Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Notes de publication](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 août 2019 | [Version Refresh Release d’Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Notes de publication](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 octobre 2019 | [Version Refresh Release d’Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Notes de publication](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>Versions précédentes

### <a name="service-fabric-64-releases"></a>Versions de Service Fabric 6.4

| Date de publication | Libérer | En savoir plus |
|---|---|---|
| 30 novembre 2018 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Notes de publication](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 décembre 2018 | [Azure Service Fabric 6.4 Refresh Release for Windows clusters](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Notes de publication](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 février 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Notes de publication](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 mars 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Notes de publication](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 avril 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Notes de publication](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 mai 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Notes de publication](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 mai 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Notes de publication](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
