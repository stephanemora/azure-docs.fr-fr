---
title: Sauvegarde et restauration périodiques dans Azure Service Fabric (préversion) | Microsoft Docs
description: Utilisez la fonctionnalité de sauvegarde et de restauration périodiques de Service Fabric pour activer la sauvegarde périodique des données de votre application.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAADBCAB-F0CF-4CBC-B663-4A6DCCB4DEE1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: hrushib
ms.openlocfilehash: bcbb8e60d14615d4bddb4a1efa5ecf1487aab093
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234678"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric-preview"></a>Sauvegarde et restauration périodiques dans Azure Service Fabric (préversion)
> [!div class="op_single_selector"]
> * [Clusters sur Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Clusters autonomes](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric est une plateforme de systèmes distribués qui facilite le développement et la gestion d’applications cloud basées sur des microservices distribués et fiables. Il permet l’exécution de microservices avec et sans état. Les services avec état peuvent conserver un état mutable faisant autorité au-delà de la demande et la réponse ou d’une transaction complète. Si un service avec état tombe en panne pendant un long moment ou perd des informations en raison d’un sinistre, il doit peut-être être restauré à l’état qu’il avait lors d’une sauvegarde récente afin de continuer à assurer le service une fois redevenu opérationnel.

Service Fabric réplique l’état sur plusieurs nœuds afin de garantir une haute disponibilité du service. Même si un nœud du cluster échoue, le service continue d’être disponible. Toutefois, dans certains cas, il est toujours souhaitable que les données de service soient fiables par rapport à des défaillances plus importantes.
 
Par exemple, le service peut souhaiter sauvegarder ses données afin de les protéger contre les scénarios suivants :
- Perte définitive de la totalité d’un cluster Service Fabric.
- Perte définitive de la majorité des réplicas d’une partition de service.
- Erreurs d’administration dans le cadre desquelles l’état est accidentellement supprimé ou endommagé. Par exemple, un administrateur disposant de privilèges suffisants supprime le service par erreur.
- Bogues dans le service qui provoquent l’altération des données. Par exemple, cela peut se produire lorsqu’une mise à niveau de code de service écrit des données erronées dans une collection fiable. Dans ce cas, le code et les données devront peut-être être restaurés à un état antérieur.
- Traitement des données hors connexion. Il peut être utile de disposer du traitement des données hors connexion pour le décisionnel qui a lieu séparément du service qui génère les données.

Service Fabric fournit une API intégrée pour effectuer des opérations de [sauvegarde et restauration](service-fabric-reliable-services-backup-restore.md) dans le temps. Les développeurs d’applications peuvent utiliser ces API pour sauvegarder régulièrement l’état du service. De plus, si les administrateurs de service souhaitent déclencher une sauvegarde depuis l’extérieur du service à un moment donné, comme avant la mise à niveau de l’application, les développeurs doivent exposer la sauvegarde (et la restauration) en tant qu’API du service. La maintenance des sauvegardes constitue un coût supplémentaire. Par exemple, vous souhaitez effectuer 5 sauvegardes incrémentielles toutes les demi-heures, suivies d’une sauvegarde complète. Après la sauvegarde complète, vous pouvez supprimer les sauvegardes incrémentielles précédentes. Cette approche nécessite du code supplémentaire, ce qui entraîne un coût supplémentaire durant le développement d’applications.

La sauvegarde régulière des données d’application est nécessaire à la gestion d’une application distribuée et la protection contre la perte de données ou une perte prolongée de la disponibilité du service. Service Fabric fournit un service de sauvegarde et restauration facultatif, ce qui vous permet de configurer une sauvegarde périodique des services fiables (Reliable Services) avec état (dont les services d’acteur) sans avoir à écrire du code supplémentaire. Il facilite également la restauration des sauvegardes précédemment effectuées. 

> [!NOTE]
> La fonctionnalité de sauvegarde et restauration périodiques existe pour l’instant en **préversion** et n’est pas prise en charge pour les charges de travail de production. 
>

Service Fabric fournit un ensemble d’API pour obtenir les fonctions suivantes associées à la fonctionnalité de sauvegarde et restauration périodiques :

- Planifier la sauvegarde périodique des services avec état fiables et des acteurs fiables (Reliable Actors) avec prise en charge pour charger la sauvegarde sur des emplacements de stockage (externes). Emplacements de stockage pris en charge
    - Stockage Azure
    - Partage de fichiers (localement)
- Énumérer les sauvegardes
- Déclencher une sauvegarde ad hoc d’une partition
- Restaurer une partition à l’aide d’une sauvegarde précédente
- Suspendre temporairement les sauvegardes
- Gérer la rétention des sauvegardes (à venir)

## <a name="prerequisites"></a>Prérequis
* Cluster Service Fabric avec Fabric versions 6.2 et ultérieure. Le cluster doit être configuré sur Windows Server. Consultez [l’article](service-fabric-cluster-creation-for-windows-server.md) pour obtenir la procédure de téléchargement du package requis.
* Certificat X.509 pour le chiffrement des secrets nécessaire pour se connecter au stockage pour stocker les sauvegardes. Consultez [l’article](service-fabric-windows-cluster-x509-security.md) pour savoir comment acquérir ou créer un certificat X.509 auto-signé.
* Application avec état fiable Service Fabric générée avec le kit SDK Service Fabric version 3.0 ou ultérieure. Pour les applications qui ciblent .Net Core 2.0, l’application doit être générée à l’aide du kit SDK Service Fabric version 3.1 ou ultérieure.

## <a name="enabling-backup-and-restore-service"></a>Activation du service de sauvegarde et de restauration
Vous devez d’abord activer le _service de sauvegarde et de restauration_ dans votre cluster. Récupérez le modèle approprié pour le cluster que vous souhaitez déployer. Vous pouvez utiliser les [exemples de modèles](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Activez le _service de sauvegarde et de restauration_ en effectuant les étapes suivantes :

1. Vérifiez si `apiversion` a la valeur `10-2017` dans le fichier de configuration de cluster et, si ce n’est pas le cas, procédez à une mise à jour, comme indiqué dans l’extrait de code suivant :

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. À présent, activez le _service de sauvegarde et de restauration_ en ajoutant la section `addonFeatures` suivante sous la section `properties`, comme indiqué dans l’extrait de code ci-après : 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Configurez un certificat X.509 pour le chiffrement des informations d’identification. Ceci est important afin de vous assurer que les informations d’identification fournies, le cas échéant, pour vous connecter au stockage sont chiffrées avant d’être rendues persistantes. Configurez le certificat de chiffrement en ajoutant la section `BackupRestoreService` suivante sous la section `fabricSettings`, comme indiqué dans l’extrait de code ci-après : 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Après avoir mis à jour votre fichier de configuration de cluster avec les modifications précédentes, appliquez-les et laissez le déploiement/la mise à niveau s’accomplir. Une fois l’opération terminée, le _service de sauvegarde et de restauration_ commence à s’exécuter dans votre cluster. L’URI de ce service est `fabric:/System/BackupRestoreService` et le service peut être situé sous la section du service système dans Service Fabric Explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Activation de la sauvegarde périodique pour le service avec état fiable et les acteurs fiables (Reliable Actors)
Examinons la procédure pour activer la sauvegarde périodique pour le service avec état fiable et les acteurs fiables (Reliable Actors). Cette procédure suppose les éléments suivants :
- Le cluster est configuré avec le _service de sauvegarde et restauration_.
- Un service avec état fiable est déployé sur le cluster. Pour les besoins de ce guide de démarrage rapide, l’URI de l’application est `fabric:/SampleApp` et l’URI du service avec état fiable appartenant à cette application est `fabric:/SampleApp/MyStatefulService`. Ce service est déployé avec une partition unique et l’ID de partition est `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Créer la stratégie de sauvegarde

La première étape consiste à créer la stratégie de sauvegarde qui décrit la planification de la sauvegarde, le stockage cible pour les données de sauvegarde, le nom de la stratégie et les sauvegardes incrémentielles maximales autorisées avant le déclenchement de la sauvegarde complète. 

Pour le stockage de sauvegarde, créez le partage de fichiers et accordez un accès Lecture/écriture à ce partage pour tous les ordinateurs du nœud Service Fabric. Cet exemple suppose que le partage nommé `BackupStore` est présent sur `StorageServer`.

Exécutez le script PowerShell suivant pour appeler l’API REST requise afin de créer une stratégie.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

### <a name="enable-periodic-backup"></a>Activer la sauvegarde périodique
Après avoir défini la stratégie de sauvegarde pour répondre aux exigences de protection des données de l’application, la stratégie doit être associée à l’application. Selon les besoins, la stratégie de sauvegarde peut être associée à une application, un service ou une partition.

Exécutez le script PowerShell suivant pour appeler l’API REST requise afin d’associer la stratégie de sauvegarde nommée `BackupPolicy1` créée à l’étape ci-dessus à l’application `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Vérifier le fonctionnement des sauvegardes périodiques

Après avoir activé la sauvegarde pour l’application, toutes les partitions appartenant à des services avec état fiables et des acteurs fiables (Reliable Actors) sous l’application commencent à être sauvegardées régulièrement conformément à la stratégie de sauvegarde associée. 

![Événement d’intégrité de partition sauvegardée][0]

### <a name="list-backups"></a>Répertorier les sauvegardes

Les sauvegardes associées à toutes les partitions appartenant à des services avec état fiables et des acteurs fiables (Reliable Actors) de l’application peuvent être énumérées à l’aide de l’API _GetBackups_. Selon les besoins, les sauvegardes peuvent être énumérées pour une application, un service ou une partition.

Exécutez le script PowerShell suivant pour appeler l’API HTTP afin d’énumérer les sauvegardes créées pour toutes les partitions à l’intérieur de l’application `SampleApp`.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Exemple de sortie pour l’exécution ci-dessus :

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
FailureError            : 
```

## <a name="preview-limitation-caveats"></a>Limitations/mises en garde de la préversion
- Pas d’intégration de Service Fabric aux applets de commande PowerShell.
- Pas de prise en charge de l’interface CLI Service Fabric.
-  Pas de prise en charge du vidage de sauvegarde automatisé. Le [script de rétention de sauvegarde](https://github.com/Microsoft/service-fabric-scripts-and-templates/tree/master/scripts/BackupRetentionScript) peut être transféré au script d’installation sur la base d’une automation externe pour la purge des sauvegardes.
- Pas de prise en charge des clusters Service Fabric sur Linux.

## <a name="next-steps"></a>Étapes suivantes
- [Présentation de la configuration de la sauvegarde périodique](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Informations de référence sur l’API REST de sauvegarde et restauration](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent.png

