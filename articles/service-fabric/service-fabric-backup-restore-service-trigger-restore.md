---
title: Restauration de la sauvegarde dans Azure Service Fabric | Microsoft Docs
description: Utilisez la fonctionnalité de sauvegarde et de restauration périodiques de Service Fabric pour restaurer les données de la sauvegarde des données de votre application.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 69604decab354368f336b85bfa1497671f0c3101
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730602"
---
#  <a name="restoring-backup-in-azure-service-fabric"></a>Restauration de la sauvegarde dans Azure Service Fabric


Les services fiables avec état et Reliable Actors dans Service Fabric peuvent conserver un état mutable faisant autorité au-delà de la demande/réponse ou d’une transaction complète. Si un service avec état tombe en panne pendant un long moment ou perd des informations en raison d’un sinistre, il a peut-être besoin d’être restauré à l’état qu’il avait lors de la dernière sauvegarde acceptable afin de continuer à fonctionner une fois qu’il est redevenu opérationnel.

Par exemple, le service peut souhaiter sauvegarder ses données afin de les protéger contre les scénarios suivants :

- Perte définitive de la totalité d’un cluster Service Fabric. **(Cas de reprise d’activité après sinistre)**
- Perte définitive de la majorité des réplicas d’une partition de service. **(Cas de perte de données)**
- Erreurs d’administration dans le cadre desquelles l’état est accidentellement supprimé ou endommagé. Par exemple, un administrateur disposant de privilèges suffisants supprime le service par erreur. **(Cas de perte de données)**
- Bogues dans le service qui provoquent l’altération des données. Par exemple, l’altération des données peut se produire lorsqu’une mise à niveau de code de service écrit des données erronées dans une collection fiable. Dans ce cas, le code et les données devront peut-être être restaurés à un état antérieur. **(Cas d’altération des données)**


## <a name="prerequisites"></a>Prérequis
* Pour déclencher la restauration, le _service FAS (Fault Analysis Service)_ doit être activé pour le cluster.
* La sauvegarde à restaurer doit avoir été effectuée par le _service BRS (Backup Restore Service)_.
* La restauration peut être déclenchée sur une partition uniquement.

## <a name="triggering-restore"></a>Déclenchement de la restauration

La restauration fonctionne pour tous les scénarios suivants. 
* Restauration des données en cas de _reprise d’activité après sinistre_
* Restauration des données en cas d’une _altération des données / perte des données_



### <a name="data-restore-in-the-event-of-disaster-recovery-dr"></a>Restauration des données en cas de _reprise d’activité après sinistre_
En cas de perte d’un cluster Service Fabric entier, les données des partitions du service fiable avec état et Reliable Actors peuvent être restaurées sur un autre cluster. Vous pouvez sélectionner la sauvegarde souhaitée à partir de l’énumération de [GetBackupAPI avec les détails de stockage de sauvegarde](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). L’énumération de sauvegardes peut fonctionner pour une application, un service ou une partition.

Supposons que le cluster perdu était le cluster mentionné dans [Activation de la sauvegarde périodique pour le service fiable avec état et Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), qui avait `SampleApp` déployé, où la partition avait une stratégie de sauvegarde activée et où les sauvegardes étaient effectuées dans Stockage Azure. 


Exécutez le script PowerShell suivant pour appeler l’API REST afin d’énumérer les sauvegardes créées pour toutes les partitions de l’application `SampleApp` dans le cluster Service Fabric perdu. L’API d’énumération nécessite des informations de stockage, où les sauvegardes d’une application sont stockées, pour énumérer les sauvegardes disponibles. 

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Exemple de sortie pour l’exécution ci-dessus :

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            : 
```



Pour déclencher la restauration, nous devons choisir la sauvegarde souhaitée. Décidons que la sauvegarde souhaitée pour la reprise d’activité après sinistre actuelle est la sauvegarde suivante :

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
```

Pour l’API de restauration, nous devons fournir les détails __BackupId__ et __BackupLocation__. La partition dans l’autre cluster doit être choisie conformément au [schéma de partition](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Il est de la responsabilité de l’utilisateur de choisir la partition cible pour restaurer la sauvegarde depuis l’autre cluster, conformément au schéma de partition dans le cluster perdu d’origine.

Supposons que l’ID de partition de l’autre cluster est `1c42c47f-439e-4e09-98b9-88b8f60800c6`, qui correspond à l’ID de partition du cluster d’origine `974bd92a-b395-4631-8a7f-53bd4ae9cf22` en comparant la clé haute et la clé basse du _partitionnement par plages de valeurs (UniformInt64Partition)_.

Pour le _partitionnement nommé_, la valeur de nom est comparée pour identifier la partition cible dans l’autre cluster.

La restauration est demandée sur la partition du cluster de sauvegarde par l’[API de restauration](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition) suivante :

```powershell 
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 
La progression de la restauration peut être [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress).

### <a name="data-restore-in-the-event-of-data-corruption--data-loss"></a>Restauration des données en cas d’une _altération des données / perte des données_

Dans le cas d’une _perte de données_ ou d’une _altération des données_, vous pouvez restaurer les données des partitions du service fiable avec état et Reliable Actors à l’une des sauvegardes choisies. Le cas suivant est la suite d’un exemple mentionné dans [Activation de la sauvegarde périodique pour le service fiable avec état et Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), où la partition a une stratégie de sauvegarde activée et effectue une sauvegarde à une fréquence souhaitée dans un stockage Azure. 

La sauvegarde souhaitée est sélectionnée à partir de la sortie de [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). Dans ce scénario, la sauvegarde est générée à partir du même cluster qu’avant.
Pour déclencher la restauration, nous devons choisir la sauvegarde souhaitée dans la liste. Décidons que notre sauvegarde souhaitée pour la _perte de données_ / _l’altération de données_ est la sauvegarde suivante :

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
```

Pour l’API de restauration, nous devons fournir les détails __BackupId__ et __BackupLocation__. Dans la mesure où le cluster a la sauvegarde activée, le _service BRS (Backup Restore Service)_ Service Fabric identifie le bon emplacement de stockage de la stratégie de sauvegarde associée.

```powershell
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4', 
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

La progression de la restauration peut être [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress).


## <a name="tracking-restore-progress"></a>Suivi de la progression de la restauration

Une partition d’un service fiable avec état ou Reliable Actor accepte une seule demande de restauration à la fois. Une autre demande peut être acceptée seulement si la demande de restauration actuelle est terminée. Plusieurs demandes de restauration peuvent être déclenchées en même temps sur des partitions différentes.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
 
$restoreResponse = (ConvertFrom-Json $response.Content) 
$restoreResponse | Format-List
```

La demande de restauration progresse dans l’ordre suivant :

1. __Accepted__ : L’état de restauration _Accepted_ indique que la demande a été déclenchée avec les bons paramètres de demande.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
2. __InProgress__ : L’état de restauration _InProgress_ indique que la partition fait l’objet d’une restauration avec la sauvegarde mentionnée dans la demande. La partition signale l’état _dataloss_.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. __Success__/ __Failure__/ __Timeout__ : Une restauration demandée peut être effectuée dans l’un des états suivants. Chaque état présente les détails d’importance et de réponse suivants.
       
    * __Success__ : L’état de restauration _Success_ indique que l’état de la partition est récupéré. La réponse fournit RestoreEpoch et RestoreLSN pour la partition, ainsi que l’heure UTC. 
    
        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```
        
    *. __Failure__ : L’état de restauration _Failure_ indique l’échec de la demande de restauration. La cause de l’échec est indiquée dans la demande.
        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    *. __Timeout__ : L’état de restauration _Timeout_ indique que la demande a expiré. Une nouvelle demande de restauration avec un [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) plus long est recommandée ; la valeur par défaut du délai d’expiration est de 10 minutes. Il est recommandé de vérifier que la partition n’est pas dans un état de perte de données (dataloss) avant de demander une nouvelle restauration.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="auto-restore"></a>Restauration automatique

Vous pouvez configurer les partitions du service fiable avec état et Reliable Actors dans le cluster Service Fabric pour la _restauration automatique_. Lors de la création de la stratégie de sauvegarde, la stratégie peut avoir `AutoRestore` défini avec la valeur _true_.  L’activation de la _restauration automatique_ pour une partition restaure les données de la dernière sauvegarde si une perte de données est signalée.
 
 [Activation de la restauration automatique dans la stratégie de sauvegarde](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)


[Informations de référence sur l’API RestorePartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
[Informations de référence sur l’API GetPartitionRestoreProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Étapes suivantes
- [Présentation de la configuration de la sauvegarde périodique](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Informations de référence sur l’API REST de sauvegarde et restauration](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
