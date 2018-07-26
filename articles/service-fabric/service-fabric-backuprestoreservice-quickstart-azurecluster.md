---
title: 'Démarrage rapide : Sauvegarde et restauration périodiques dans Azure Service Fabric (préversion) | Microsoft Docs'
description: Utilisez la fonctionnalité de sauvegarde et de restauration périodiques de Service Fabric pour activer la sauvegarde périodique des données de votre application.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAA58600-897E-4CEE-9D1C-93FACF98AD1C
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: hrushib
ms.openlocfilehash: 50ee0d91b27805e4db785e5df211660900333e7f
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990297"
---
# <a name="quickstart-periodic-backup-and-restore-in-azure-service-fabric-preview"></a>Démarrage rapide : Sauvegarde et restauration périodiques dans Azure Service Fabric (préversion)
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
* Cluster Service Fabric avec Fabric versions 6.2 et ultérieure. Le cluster doit être configuré sur Windows Server. Consultez [l’article](service-fabric-cluster-creation-via-arm.md) pour obtenir la procédure de création d’un cluster Service Fabric à l’aide du modèle de ressource Azure.
* Certificat X.509 pour le chiffrement des secrets nécessaire pour se connecter au stockage pour stocker les sauvegardes. Consultez [l’article](service-fabric-cluster-creation-via-arm.md) pour savoir comment obtenir ou créer un certificat X.509.
* Application avec état fiable Service Fabric générée avec le kit SDK Service Fabric version 3.0 ou ultérieure. Pour les applications qui ciblent .Net Core 2.0, l’application doit être générée à l’aide du kit SDK Service Fabric version 3.1 ou ultérieure.
* Créez un compte de stockage Azure pour stocker les sauvegardes de l’application.

## <a name="enabling-backup-and-restore-service"></a>Activation du service de sauvegarde et de restauration
Vous devez d’abord activer le _service de sauvegarde et de restauration_ dans votre cluster. Récupérez le modèle approprié pour le cluster que vous souhaitez déployer. Vous pouvez soit utiliser les [exemples de modèles](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype), soit créer un modèle Resource Manager. Activez le _service de sauvegarde et de restauration_ en effectuant les étapes suivantes :

1. Vérifiez si `apiversion` a la valeur **`2018-02-01`** pour la ressource `Microsoft.ServiceFabric/clusters` et, si ce n’est pas le cas, procédez à une mise à jour, comme indiqué dans l’extrait de code suivant :

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. À présent, activez le _service de sauvegarde et de restauration_ en ajoutant la section `addonFeatures` suivante sous la section `properties`, comme indiqué dans l’extrait de code ci-après : 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Configurez un certificat X.509 pour le chiffrement des informations d’identification. Ceci est important afin de vous assurer que les informations d’identification fournies pour vous connecter au stockage sont chiffrées avant d’être rendues persistantes. Configurez le certificat de chiffrement en ajoutant la section `BackupRestoreService` suivante sous la section `fabricSettings`, comme indiqué dans l’extrait de code ci-après : 

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

4. Après avoir mis à jour votre modèle de cluster avec les modifications précédentes, appliquez-les et laissez le déploiement/la mise à niveau s’accomplir. Une fois l’opération terminée, le _service de sauvegarde et de restauration_ commence à s’exécuter dans votre cluster. L’URI de ce service est `fabric:/System/BackupRestoreService` et le service peut être situé sous la section du service système dans Service Fabric Explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Activation de la sauvegarde périodique pour le service avec état fiable et les acteurs fiables (Reliable Actors)
Examinons la procédure pour activer la sauvegarde périodique pour le service avec état fiable et les acteurs fiables (Reliable Actors). Cette procédure suppose les éléments suivants :
- Le cluster est configuré à l’aide de la sécurité X.509 avec le _service de sauvegarde et restauration_.
- Un service avec état fiable est déployé sur le cluster. Pour les besoins de ce guide de démarrage rapide, l’URI de l’application est `fabric:/SampleApp` et l’URI du service avec état fiable appartenant à cette application est `fabric:/SampleApp/MyStatefulService`. Ce service est déployé avec une partition unique et l’ID de partition est `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- Le certificat client avec le rôle d’administrateur est installé dans le magasin _My_ (_Personal_) de l’emplacement du magasin de certificats _CurrentUser_ sur l’ordinateur à partir d’où les scripts ci-dessous sont appelés. Cet exemple utilise `1b7ebe2174649c45474a4819dafae956712c31d3` comme empreinte numérique de ce certificat. Pour plus d’informations sur les certificats clients, consultez [Contrôle d’accès en fonction du rôle pour les clients de Service Fabric](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Créer la stratégie de sauvegarde

La première étape consiste à créer la stratégie de sauvegarde qui décrit la planification de la sauvegarde, le stockage cible pour les données de sauvegarde, le nom de la stratégie et les sauvegardes incrémentielles maximales autorisées avant le déclenchement de la sauvegarde complète. 

Pour le stockage de sauvegarde, utilisez le compte de stockage Azure créé ci-dessus. Le conteneur `backup-container` est configuré pour stocker des sauvegardes. Lors du téléchargement de la sauvegarde, un conteneur portant ce nom est créé s’il n’existe pas déjà. Remplissez `ConnectionString` avec une chaîne de connexion valide pour le compte de stockage Azure, en remplaçant `account-name` par le nom de votre compte de stockage et `account-key` par la clé de votre compte de stockage.

Exécutez le script PowerShell suivant pour appeler l’API REST requise afin de créer une stratégie. Remplacez `account-name` par le nom de votre compte de stockage et `account-key` par la clé de votre compte de stockage.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

### <a name="enable-periodic-backup"></a>Activer la sauvegarde périodique
Après avoir défini la stratégie de sauvegarde pour répondre aux exigences de protection des données de l’application, la stratégie doit être associée à l’application. Selon les besoins, la stratégie de sauvegarde peut être associée à une application, un service ou une partition.

Exécutez le script PowerShell suivant pour appeler l’API REST requise afin d’associer la stratégie de sauvegarde nommée `BackupPolicy1` créée à l’étape ci-dessus à l’application `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Vérifier le fonctionnement des sauvegardes périodiques

Après avoir activé la sauvegarde au niveau de l’application, toutes les partitions appartenant à des services avec état fiables et des acteurs fiables (Reliable Actors) sous l’application commencent à être sauvegardées régulièrement conformément à la stratégie de sauvegarde associée. 

![Événement d’intégrité de partition sauvegardée][0]

### <a name="list-backups"></a>Répertorier les sauvegardes

Les sauvegardes associées à toutes les partitions appartenant à des services avec état fiables et des acteurs fiables (Reliable Actors) de l’application peuvent être énumérées à l’aide de l’API _GetBackups_. Les sauvegardes peuvent être énumérées pour une application, un service ou une partition.

Exécutez le script PowerShell suivant pour appeler l’API HTTP afin d’énumérer les sauvegardes créées pour toutes les partitions à l’intérieur de l’application `SampleApp`.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

## <a name="preview-limitation-caveats"></a>Limitations/mises en garde de la préversion
- Pas d’intégration de Service Fabric aux applets de commande PowerShell.
- Pas de prise en charge de l’interface CLI Service Fabric.
- Pas de prise en charge du vidage de sauvegarde automatisé. Le [script de rétention de sauvegarde](https://github.com/Microsoft/service-fabric-scripts-and-templates/tree/master/scripts/BackupRetentionScript) peut être transféré au script d’installation sur la base d’une automation externe pour la purge des sauvegardes.
- Pas de prise en charge des clusters Service Fabric sur Linux.

## <a name="next-steps"></a>Étapes suivantes
- [Présentation de la configuration de la sauvegarde périodique](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Informations de référence sur l’API REST de sauvegarde et restauration](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent_Azure.png

