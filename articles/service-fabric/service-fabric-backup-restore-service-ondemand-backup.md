---
title: Sauvegarde à la demande dans Azure Service Fabric | Microsoft Docs
description: Utilisez la fonctionnalité de sauvegarde et de restauration de Service Fabric pour sauvegarder vos données d’application selon vos besoins.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 35d97f1da6b5d1c75073264c70e1cd1607d5fe0d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730585"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Sauvegarde à la demande dans Azure Service Fabric

Vous pouvez sauvegarder les données des services fiables avec état et Reliable Actors pour pallier les sinistres et la perte de données.

Service Fabric est équipé de fonctionnalités pour la [sauvegarde périodique des données](service-fabric-backuprestoreservice-quickstart-azurecluster.md) et la sauvegarde des données selon les besoins. La sauvegarde à la demande est utile car elle protège de la _perte des données_/_l’altération des données_ causées par des changements prévus dans le service sous-jacent ou son environnement.

Les fonctionnalités de sauvegarde à la demande sont utiles pour capturer l’état des services, avant toute opération déclenchée manuellement liée au service ou à l’environnement du service. Par exemple, lors du changement des binaires du service, c’est-à-dire lors du passage à une version ultérieure ou antérieure du service, les données de celui-ci sont protégées d’une altération causée par les bogues du code de l’application.

## <a name="triggering-on-demand-backup"></a>Déclenchement d’une sauvegarde à la demande

La sauvegarde à la demande nécessite les détails de stockage pour le chargement des fichiers de sauvegarde. La sauvegarde à la demande se produit dans le stockage spécifié dans la stratégie de sauvegarde périodique ou dans le stockage spécifié dans la demande de sauvegarde à la demande.

### <a name="on-demand-backup-to-storage-specified-by-periodic-backup-policy"></a>Sauvegarde à la demande dans le stockage spécifié dans la stratégie de sauvegarde périodique

La partition d’un service fiable avec état ou Reliable Actor peut être demandée pour une sauvegarde à la demande dans le stockage spécifié dans la stratégie de sauvegarde périodique. 

Le cas suivant est la suite d’un exemple mentionné dans [Activation de la sauvegarde périodique pour le service fiable avec état et Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), où la partition a une stratégie de sauvegarde activée et effectue une sauvegarde à une fréquence souhaitée dans Stockage Azure.

La sauvegarde à la demande pour l’ID de partition `974bd92a-b395-4631-8a7f-53bd4ae9cf22` peut être déclenchée par l’API [BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition). 

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Vous pouvez suivre la [progression de la sauvegarde à la demande](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress) avec l’API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress).

### <a name="on-demand-backup-to-specified-storage"></a>Sauvegarde à la demande dans le stockage spécifié

Vous pouvez demander une sauvegarde à la demande pour une partition d’un service fiable avec état ou Reliable Actor, ainsi que les informations de stockage. Les informations de stockage doivent être fournies dans le cadre de la demande de la sauvegarde à la demande.

La sauvegarde à la demande pour l’ID de partition `974bd92a-b395-4631-8a7f-53bd4ae9cf22` peut être déclenchée par l’API [BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) avec les informations de stockage Azure, comme illustré ci-dessous.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Vous pouvez suivre la [progression de la sauvegarde à la demande](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress) avec l’API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress).


## <a name="tracking-on-demand-backup-progress"></a>Suivi de la progression de la sauvegarde à la demande

Une partition d’un service fiable avec état ou Reliable Actor accepte une seule demande de sauvegarde à la demande à la fois. Une autre demande peut être acceptée seulement si la demande de sauvegarde à la demande en cours est terminée. 

Plusieurs demandes de sauvegarde à la demande peuvent être déclenchées en même temps sur des partitions différentes.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

La progression de la demande de sauvegarde à la demande peut avoir l’un des états suivants :

* **Accepted** : La sauvegarde a été lancée sur la partition et est en cours.
    ```
    BackupState             : Accepted
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          : 
    EpochOfLastBackupRecord : 
    LsnOfLastBackupRecord   : 0
    FailureError            : 
    ```
    
* **Success/ Failure/ Timeout** : Une demande de sauvegarde à la demande peut être effectuée dans l’un des états suivants. Chaque état présente les détails d’importance et de réponse suivants.

    * **Success** :L’état de sauvegarde _Success_ indique que l’état de la partition est sauvegardé avec succès. La réponse fournit __BackupEpoch__ et __BackupLSN__ pour la partition, ainsi que l’heure UTC.
        ```
        BackupState             : Success
        TimeStampUtc            : 2018-11-21T20:00:01Z
        BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
        BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
        EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
        LsnOfLastBackupRecord   : 36
        FailureError            : 
        ```
        
    * **Failure** : L’état de sauvegarde _Failure_ indique qu’un échec s’est produit lors de la sauvegarde de l’état de la partition. La cause de l’échec est indiquée dans la réponse.
        ```
        BackupState             : Failure
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
        ```
       
    * **Timeout** : L’état de sauvegarde _Timeout_ indique que la sauvegarde de l’état de la partition n’a pas pu être créée dans les temps ; la valeur du délai d’expiration par défaut est de 10 minutes. Le lancement d’une nouvelle demande de sauvegarde avec un [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) plus long dans la demande de sauvegarde à la demande est recommandée dans ce scénario.

        ```
        BackupState             : Timeout
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
        ```

## <a name="next-steps"></a>Étapes suivantes
- [Présentation de la configuration de la sauvegarde périodique](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Informations de référence sur l’API REST de sauvegarde et restauration](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

