---
title: Déprovisionner un point de terminaison de serveur Azure File Sync | Microsoft Docs
description: Conseils sur la façon de déprovisionner de votre point de terminaison de serveur Azure File Sync en fonction de votre cas d’utilisation
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 4/23/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 49eeef80dd93c8dd87668485db6def84a4d74427
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110676213"
---
# <a name="deprovision-your-azure-file-sync-server-endpoint"></a>Déprovisionner votre point de terminaison de serveur Azure File Sync

Avant de déprovisionner votre point de terminaison de serveur, vous devez effectuer quelques étapes pour maintenir l’intégrité et la disponibilité des données. Cet article décrit plusieurs méthodes de déprovisionnement et les recommandations appropriées, classées par scénario. Suivez les étapes pour le cas d’usage qui vous convient le mieux.

S’il est possible de perdre définitivement les données que vous êtes en train de synchroniser, vous pouvez passer directement au déprovisionnement de votre point de terminaison de serveur.

> [!Important]
> N’essayez pas de résoudre les problèmes de synchronisation en déprovisionnant un point de terminaison de serveur. Pour obtenir de l’aide sur la résolution des problèmes, consultez [Résolution des problèmes pour Azure File Sync](./file-sync-troubleshoot.md). Une perte de données permanente peut se produire si vous supprimez votre point de terminaison de serveur sans que le serveur ou le cloud se synchronisent entre eux. 

## <a name="scenario-1-you-intend-to-delete-your-server-endpoint-and-stop-using-your-local-servervm"></a>Scénario 1 : Vous avez l’intention de supprimer votre point de terminaison de serveur et d’arrêter d’utiliser votre serveur/machine virtuelle en local

L’objectif ici est de vous assurer que vos données sont à jour dans votre point de terminaison cloud. Pour que votre ensemble complet de fichiers soit à jour dans vos points de terminaison de serveur, consultez [Scénario 2 : Vous avez l’intention de supprimer votre point de terminaison de serveur et de cesser d’utiliser ce partage de fichiers Azure spécifique](#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share).

Voici quelques cas d’utilisation appartenant à cette catégorie :
-   Migration vers un partage de fichiers Azure
-   Passage à une solution sans serveur
-   Interruption de l’utilisation d’un chemin d’accès de point de terminaison de serveur spécifique tout en gardant intact le reste du groupe de synchronisation

Pour ce scénario, il existe trois étapes à suivre avant de supprimer votre point de terminaison de serveur : supprimer l’accès utilisateur, lancer une session de téléchargement VSS spéciale et attendre la fin d’une session de synchronisation finale.

### <a name="remove-user-access-to-your-server-endpoint"></a>Supprimer l’accès utilisateur à votre point de terminaison de serveur

Avant de déprovisionner votre point de terminaison de serveur, vous devez vous assurer que toutes les modifications apportées à partir du serveur peuvent se synchroniser dans le cloud. La première étape permettant au cloud d’être à jour consiste à supprimer l’opportunité d’autres modifications apportées aux fichiers et aux dossiers sur le point de terminaison de serveur. 

La suppression de l’accès signifie un temps d’arrêt. Pour réduire les temps d’arrêt, vous pouvez également envisager de rediriger l’accès utilisateur à votre point de terminaison cloud. 

Notez la date et l’heure auxquelles vous avez supprimé l’accès utilisateur pour votre propre référence, puis passez à la section suivante.

### <a name="initiate-a-special-volume-snapshot-service-vss-upload-session"></a>Lancer une session de chargement de service d’instantané de volume (VSS) spéciale

Chaque jour, Azure File Sync crée un instantané VSS temporaire sur le serveur pour synchroniser les fichiers avec des descripteurs ouverts. Pour vous assurer que votre session de synchronisation finale télécharge les données les plus récentes et pour réduire les erreurs par élément, lancez une session spéciale pour le téléchargement VSS. Cela déclenche également une session de chargement de synchronisation spéciale qui commence une fois que l’instantané est pris.  

Pour ce faire, ouvrez le **Planificateur de tâches** sur votre serveur local, accédez à **Microsoft\StorageSync**, cliquez avec le bouton droit sur la tâche **VssSyncScheduledTask**, puis sélectionnez **Exécuter**.

> [!Important]
> Notez la date et l’heure auxquelles vous effectuez cette étape. Vous en aurez besoin dans la prochaine section.

![Capture d’écran de la planification d’une session de chargement VSS.](media/storage-sync-deprovision-server-endpoint/vss-task-scheduler.png)

### <a name="wait-for-a-final-sync-upload-session-to-complete"></a>Attendre la fin d’une session de chargement de synchronisation finale

Pour vous assurer que les données les plus récentes se trouvent dans le cloud, vous devez attendre la fin de la session de téléchargement de la synchronisation finale. 

Pour vérifier l’état de la session de synchronisation, ouvrez l’**Observateur d’événements** sur votre serveur local. Accédez au journal des événements de télémétrie **(Applications and Services\Microsoft\FileSync\Agent)** . Vérifiez que vous voyez un événement 9102 avec ‘sync direction’ = upload, ‘HResult’ = 0 et ‘PerItemErrorCount’ = 0 après que vous avez initié manuellement une session de téléchargement VSS.

![Capture d’écran de vérification de la fin d’une session de synchronisation finale.](media/storage-sync-deprovision-server-endpoint/event-viewer.png)

Si « PerItemErrorCount » est supérieur à 0, les fichiers ne sont pas synchronisés. Utilisez **FileSyncErrorsReport.ps1** pour voir les fichiers qui ne sont pas synchronisés. Ce script PowerShell se trouve généralement à ce chemin d’accès sur un serveur sur lequel un agent Azure File Sync est installé : **C:\Program Files\Azure\StorageSyncAgent\FileSyncErrorsReport.ps1**

Si ces fichiers ne sont pas importants, vous pouvez supprimer le point de terminaison de votre serveur. Si ces fichiers sont importants, corrigez leurs erreurs et attendez qu’un autre événement 9102 avec ‘sync direction’ = upload, ‘HResult’ = 0 et ‘PerItemErrorCount’ = 0 se produise avant de supprimer votre point de terminaison de serveur.

## <a name="scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share"></a>Scénario 2 : Vous avez l’intention de supprimer votre point de terminaison de serveur et de cesser d’utiliser ce partage de fichiers Azure spécifique

L’objectif ici est de vous assurer que vos données sont à jour sur votre serveur/machine virtuelle en local. Pour que votre ensemble complet de fichiers soit à jour dans votre point de terminaison cloud, consultez [Scénario 1 : vous avez l’intention de supprimer votre point de terminaison de serveur et d’arrêter d’utiliser votre serveur/machine virtuelle local](#scenario-1-you-intend-to-delete-your-server-endpoint-and-stop-using-your-local-servervm).

Pour ce scénario, il existe quatre étapes à suivre avant de supprimer votre point de terminaison de serveur : désactiver la hiérarchisation cloud, rappeler les fichiers hiérarchisés, lancer la détection des modifications dans le cloud et attendre la fin d’une session de synchronisation finale.

### <a name="disable-cloud-tiering"></a>Désactiver la hiérarchisation cloud
Accédez à la section Hiérarchisation cloud dans **Propriétés du point de terminaison de serveur** pour le point de terminaison de serveur que vous souhaitez déprovisionner, et désactivez la hiérarchisation cloud.

### <a name="recall-all-tiered-files"></a>Rappeler tous les fichiers hiérarchisés
Même si la hiérarchisation cloud est désactivée, vous devez rappeler tous les fichiers hiérarchisés pour vous assurer que chaque fichier est stocké localement.

Avant de rappeler les fichiers, assurez-vous que vous disposez de suffisamment d’espace libre pour stocker tous vos fichiers. L’espace libre doit être approximativement égal à la taille de votre partage de fichiers Azure dans le cloud moins la taille mise en cache sur votre serveur.

Utilisez la cmdlet PowerShell **Invoke-StorageSyncFileRecall** et spécifiez le paramètre **SyncGroupName** pour rappeler tous les fichiers. 
```powershell
Invoke-StorageSyncFileRecall  -SyncGroupName “samplesyncgroupname”
```
Une fois l’exécution de la cmdlet terminée, vous pouvez passer à la section suivante.

### <a name="initiate-cloud-change-detection"></a>Lancer la détection des modifications dans le cloud
L’initiation de la détection des modifications dans le cloud garantit la synchronisation de vos dernières modifications.

Vous pouvez lancer la détection des modifications avec la cmdlet Invoke-AzStorageSyncChangeDetection : 

```powershell
Invoke-AzStorageSyncChangeDetection -ResourceGroupName "myResourceGroup" -StorageSyncServiceName "myStorageSyncServiceName" -SyncGroupName "mySyncGroupName" -Path "Data","Reporting\Templates" 
```

Cette étape peut prendre un certain temps. 

> [!Important]
> Une fois que l’analyse de la détection des modifications du cloud a été lancée, notez la date et l’heure de la fin de l’opération. Ces données sont nécessaires pour la section suivante.

### <a name="wait-for-a-final-sync-session-to-complete"></a>Attendre la fin d’une session de synchronisation finale
Pour vous assurer que vos données sont à jour sur votre serveur local, vous devez attendre la fin d’une session de chargement de synchronisation finale. 

Pour vérifier cela, accédez à l’**Observateur d’événements** sur votre serveur local. Accédez au journal des événements de télémétrie **(Applications and Services\Microsoft\FileSync\Agent)** . Vérifiez que vous voyez un événement 9102 avec ‘sync direction’ = download, ‘HResult’ = 0 et ‘PerItemErrorCount’ = 0 survenu après la date/heure de détection des modifications du cloud.

![Capture d’écran de vérification de la fin d’une session de synchronisation finale.](media/storage-sync-deprovision-server-endpoint/event-viewer.png)

Si « PerItemErrorCount » est supérieur à 0, les fichiers ne sont pas synchronisés. Utilisez **FileSyncErrorsReport.ps1** pour voir les fichiers qui ne sont pas synchronisés. Ce script PowerShell se trouve généralement à ce chemin d’accès sur un serveur sur lequel un agent Azure File Sync est installé : **C:\Program Files\Azure\StorageSyncAgent\FileSyncErrorsReport.ps1**

Si ces fichiers ne sont pas importants, vous pouvez supprimer le point de terminaison de votre serveur. Si ces fichiers sont importants, corrigez leurs erreurs et attendez qu’un autre événement 9102 avec ‘sync direction’ = download, ‘HResult’ = 0 et ‘PerItemErrorCount’ = 0 se produise avant de supprimer votre point de terminaison de serveur.

## <a name="next-steps"></a>Étapes suivantes
* [Modifier la topologie d’Azure File Sync](./file-sync-modify-sync-topology.md)







