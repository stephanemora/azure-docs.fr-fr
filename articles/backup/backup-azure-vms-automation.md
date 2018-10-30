---
title: Déploiement et gestion des sauvegardes pour les machines virtuelles déployées avec le modèle Resource Manager à l’aide de PowerShell
description: Utilisation de PowerShell pour déployer et gérer des sauvegardes dans Azure pour les machines virtuelles déployées avec le modèle Resource Manager
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: markgal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c29a91a40df34ecd9270d5805209d361cf990754
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638033"
---
# <a name="use-powershell-to-back-up-and-restore-virtual-machines"></a>Utilisation de PowerShell pour sauvegarder et restaurer des machines virtuelles

Cet article vous montre comment utiliser des cmdlets Azure PowerShell pour sauvegarder et restaurer une machine virtuelle Azure à partir d’un coffre Recovery Services. Un coffre Recovery Services est une ressource Azure Resource Manager utilisée pour protéger les données et les actifs dans Sauvegarde Azure et les services Azure Site Recovery. 

> [!NOTE]
> Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Azure Resource Manager et Azure Classic](../azure-resource-manager/resource-manager-deployment-model.md). Cet article concerne l’utilisation avec des machines virtuelles créées à l’aide du modèle Resource Manager.
>
>

Cet article vous guide tout au long de l’utilisation de PowerShell pour protéger une machine virtuelle et la restauration de données à partir d’un point de récupération.

## <a name="concepts"></a>Concepts
Si vous n’êtes pas familiarisé avec le service Sauvegarde Azure, consultez l’article [Qu’est-ce que la Sauvegarde Azure ?](backup-introduction-to-azure-backup.md) afin d’obtenir une vue d’ensemble du service. Avant de commencer, assurez-vous de connaître les conditions préalables nécessaires pour travailler avec la Sauvegarde Azure ainsi que les limites de la solution actuelle de sauvegarde des machines virtuelles.

Pour pouvoir utiliser efficacement PowerShell, il est nécessaire de comprendre la hiérarchie des objets et par où commencer.

![Hiérarchie des objets dans Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Pour voir les informations de référence sur l’applet de commande PowerShell AzureRmRecoveryServicesBackup, consultez [Sauvegarde Azure - Applets de commande de Recovery Services](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) dans la bibliothèque Azure.

## <a name="setup-and-registration"></a>Installation et inscription

Pour commencer :

1. [Téléchargez la dernière version de PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (version minimale requise : 1.4.0)

2. Rechercher les applets de commande PowerShell Azure Backup disponibles en tapant la commande suivante :
   
    ```powershell
    Get-Command *azurermrecoveryservices*
    ```    
    Les alias et cmdlets pour Sauvegarde Azure, Azure Site Recovery et le coffre Recovery Services s’affichent. L’image suivante est un exemple de ce que vous allez voir. Il ne s’agit pas de la liste complète des cmdlets.

    ![Liste des services Recovery Services](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Connectez-vous à votre compte Azure à l’aide de **Connect-AzureRmAccount**. Cette applet de commande permet d’afficher une page web qui vous demande les informations d’identification de votre compte :

    * Vous pouvez également ajouter les informations d’identification de votre compte en tant que paramètre dans l’applet de commande **Connect-AzureRmAccount** à l’aide du paramètre **-Credential**.
    * Si vous êtes partenaire CSP travaillant pour le compte d’un locataire, spécifiez le client en tant que locataire à l’aide de son ID locataire ou de son nom de domaine principal. Par exemple : **Connect-AzureRmAccount -Tenant "fabrikam.com"**

4. Associez l’abonnement que vous souhaitez utiliser avec le compte, car un compte peut compter plusieurs abonnements :

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Si vous utilisez Sauvegarde Azure pour la première fois, vous devez utiliser l’applet de commande **[Register-AzureRMResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** pour inscrire le fournisseur Azure Recovery Service auprès de votre abonnement.

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Vous pouvez vérifier que les fournisseurs ont été correctement inscrits à l’aide des commandes suivantes :
    ```powershell
    Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ``` 
    Dans la sortie de commande, **RegistrationState** doit prendre la valeur **Inscrit**. Dans le cas contraire, réexécutez la cmdlet **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)**.

Les tâches ci-après peuvent être automatisées avec PowerShell :

* [Créer un coffre Recovery Services](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Sauvegarder des machines virtuelles Azure](backup-azure-vms-automation.md#back-up-azure-vms)
* [Déclencher une tâche de sauvegarde](backup-azure-vms-automation.md#trigger-a-backup-job)
* [Surveiller une tâche de sauvegarde](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Restaurer une machine virtuelle Azure](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Les étapes suivantes vous montrent comment créer un coffre Recovery Services. Un coffre Recovery Services diffère d’un coffre de sauvegarde.

1. Le coffre Recovery Services étant une ressource Resource Manager, vous devez le placer dans un groupe de ressources. Si vous ne disposez pas d’un groupe de ressources, créez-en un avec la cmdlet **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)**. Quand vous créez un groupe de ressources, spécifiez son nom et son emplacement.  

    ```powershell
    New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Utilisez l’applet de commande **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** pour créer un coffre Recovery Services. Spécifiez pour le coffre le même emplacement que pour le groupe de ressources.

    ```powershell
    New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Spécifiez le type de redondance de stockage à utiliser : [Stockage localement redondant (LRS)](../storage/common/storage-redundancy-lrs.md) ou [Stockage géo-redondant (GRS)](../storage/common/storage-redundancy-grs.md). L’exemple suivant montre que l’option -BackupStorageRedundancy pour testvault est définie sur GeoRedundant.

    ```powershell
    $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > De nombreuses applets de commande Azure Backup nécessitent l’objet coffre Recovery Services en tant qu’entrée. Pour cette raison, il est pratique de stocker l’objet coffre Backup Recovery Services dans une variable.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Afficher les coffres dans un abonnement

Pour afficher tous les coffres de l’abonnement, utilisez **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)** :

```powershell
Get-AzureRmRecoveryServicesVault
```

La sortie ressemble à celle de l’exemple suivant. Notez que le paramètre ResourceGroupName et l’emplacement associés sont fournis.

```
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Sauvegarder des machines virtuelles Azure

Utilisez un coffre Recovery Services pour protéger vos machines virtuelles. Avant d’appliquer la protection, définissez le contexte du coffre (le type de données qu’il protège) et vérifiez la stratégie de protection. La stratégie de protection consiste à planifier l’exécution du travail de sauvegarde et la durée de conservation de chaque instantané de sauvegarde.

### <a name="set-vault-context"></a>Définir le contexte du coffre

Avant d’activer la protection sur une machine virtuelle, utilisez **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** pour définir le contexte du coffre. Une fois le contexte du coffre défini, il s’applique à toutes les applets de commande suivantes. L’exemple suivant définit le contexte du coffre pour le coffre *testvault*.

```powershell
Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Création d’une stratégie de protection

Quand vous créez un coffre Recovery Services, il est fourni avec des stratégies de protection et de conservation par défaut. La stratégie de protection par défaut déclenche une tâche de sauvegarde chaque jour à une heure spécifiée. La stratégie de conservation par défaut conserve le point de récupération quotidien pendant 30 jours. Vous pouvez utiliser la stratégie par défaut pour protéger rapidement votre machine virtuelle et modifier la stratégie ultérieurement avec différents détails.

Utilisez **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** pour afficher les stratégies de protection disponibles dans le coffre. Vous pouvez utiliser cette applet de commande pour obtenir une stratégie spécifique ou pour afficher les stratégies associées à un type de charge de travail. L’exemple suivant obtient les stratégies pour le type de charge de travail AzureVM.

```powershell
Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

Le résultat ressemble à l’exemple suivant :

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Le fuseau horaire du champ BackupTime dans PowerShell est UTC. Toutefois, lorsque l’heure de sauvegarde s’affiche dans le portail Azure, elle est alignée sur votre fuseau horaire.
>
>

Une stratégie de protection de la sauvegarde est associée à au moins une stratégie de rétention. La stratégie de conservation définit la durée de conservation d’un point de restauration avant sa suppression. Utilisez **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** pour afficher la stratégie de conservation par défaut.  De même, vous pouvez utiliser **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** pour obtenir la stratégie de planification par défaut. L’applet de commande **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** crée un objet PowerShell contenant des informations sur la stratégie de sauvegarde. Les objets de la stratégie de planification et de conservation sont utilisés comme entrées pour l’applet de commande **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)**. L’exemple suivant stocke la stratégie de planification et la stratégie de conservation dans des variables. L’exemple utilise ces variables pour définir les paramètres lors de la création d’une stratégie de protection, *NewPolicy*.

```powershell
$schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Le résultat ressemble à l’exemple suivant :

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Activer la protection

Une fois que vous avez défini la stratégie de protection, vous devez encore activer la stratégie pour un élément. Utilisez **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** pour activer la protection. L’activation de la protection nécessite deux objets : l’élément et la stratégie. Une fois que la stratégie a été associée au coffre, le flux de travail de la sauvegarde est déclenché à l’heure planifiée dans la planification de la stratégie.

Les exemples suivants activent la protection pour l’élément V2VM. Ils utilisent la stratégie NewPolicy. Les exemples diffèrent selon que la machine virtuelle est chiffrée et le type de chiffrement. 

Pour activer la protection sur des **machines virtuelles Resource Manager non chiffrées** :

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Pour activer la protection sur des **machines virtuelles chiffrées (à l’aide de clés BEK et KEK)**, vous devez accorder au service Sauvegarde Azure l’autorisation de lire les clés et les secrets depuis le coffre de clés.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Pour activer la protection sur des **machines virtuelles chiffrées (à l’aide de clés BEK uniquement)**, vous devez accorder au service Sauvegarde Azure l’autorisation de lire les secrets depuis le coffre de clés.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Si vous êtes sur le cloud Azure Government, utilisez la valeur ff281ffe-705c-4f53-9f37-a40e6f2c68f3 pour le paramètre **- ServicePrincipalName** dans l’applet de commande [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy).
>
>

Pour activer la protection sur une machine virtuelle classique :

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Modifier une stratégie de protection

Pour modifier la stratégie de protection, utilisez [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) pour modifier les objets SchedulePolicy ou RetentionPolicy.

L’exemple suivant change la conservation des points de récupération en 365 jours.

```powershell
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Déclencher une sauvegarde

Utilisez  **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** pour déclencher une tâche de sauvegarde. S’il s’agit de la sauvegarde initiale, cette sauvegarde est complète. Les sauvegardes suivantes prennent une copie incrémentielle. Veillez à utiliser **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** pour définir le contexte du coffre avant de déclencher la tâche de sauvegarde. L’exemple suivant suppose que le contexte du coffre a déjà été défini.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

Le résultat ressemble à l’exemple suivant :

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Le fuseau horaire des champs StartTime et EndTime affichés dans PowerShell est UTC. Toutefois, lorsque l’heure s’affiche dans le portail Azure, elle est alignée sur votre fuseau horaire.
>
>

## <a name="monitoring-a-backup-job"></a>Surveillance d’une tâche de sauvegarde

Vous pouvez surveiller les opérations à exécution longue, comme les tâches de sauvegarde, sans utiliser le portail Azure. Pour obtenir l’état d’une tâche en cours d’exécution, utilisez l’applet de commande **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)**. Cette applet de commande obtient les tâches de sauvegarde pour un coffre spécifique, et ce coffre est spécifié dans le contexte du coffre. L’exemple suivant obtient l’état d’une tâche en cours d’exécution sous forme de tableau et stocke l’état dans la variable $joblist.

```powershell
$joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

Le résultat ressemble à l’exemple suivant :

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Au lieu d’interroger ces travaux pour connaître leur état d’avancement, ce qui implique du code supplémentaire inutile, utilisez l’applet de commande **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)**. Cette applet de commande suspend l’exécution jusqu’à la fin de la tâche ou jusqu’à ce que la valeur spécifiée pour l’expiration du délai soit atteinte.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Restauration d’une machine virtuelle Azure

Il existe une différence importante entre restaurer une machine virtuelle à l’aide du portail Microsoft Azure et restaurer une machine virtuelle à l’aide de PowerShell. Avec PowerShell, l’opération de restauration est terminée dès que sont créés les disques et les informations de configuration à partir d’un point de restauration. L’opération de restauration ne crée pas la machine virtuelle. Pour créer une machine virtuelle à partir d’un disque, consultez la section [Créer une machine virtuelle à partir de disques restaurés](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Si vous ne voulez pas restaurer l’intégralité de la machine virtuelle, mais que vous voulez restaurer ou récupérer quelques fichiers seulement depuis une sauvegarde de machine virtuelle Azure, reportez-vous à la [section de récupération des fichiers](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> L’opération de restauration ne crée pas la machine virtuelle.
>
>

L’illustration suivante montre la hiérarchie d’objets à partir de RecoveryServicesVault jusqu’à BackupRecoveryPoint.

![Hiérarchie d’objets Recovery Services montrant BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Pour restaurer les données de sauvegarde, identifiez l’élément sauvegardé et le point de récupération contenant les données ponctuelles. Utilisez la cmdlet **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** pour restaurer les données dans votre compte, à partir du coffre.

Les étapes de base pour restaurer une machine virtuelle Azure sont :

* Sélectionnez la machine virtuelle.
* Choisissez un point de récupération.
* Restaurez les disques.
* Créez la machine virtuelle à partir des disques stockés.

### <a name="select-the-vm"></a>Sélection de la machine virtuelle

Pour obtenir l’objet PowerShell permettant d’identifier l’élément à restaurer, vous devez commencer au niveau du conteneur dans le coffre et descendre dans la hiérarchie d’objets. Pour sélectionner le conteneur qui représente la machine virtuelle, utilisez l’applet de commande **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** et dirigez-la vers l’applet de commande **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)**.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Choisir un point de récupération

Utilisez l’applet de commande **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** pour répertorier tous les points de récupération pour l’élément de sauvegarde. Ensuite, choisissez le point de récupération à restaurer. Si vous ne savez pas quel point de récupération utiliser, nous vous conseillons de choisir le point RecoveryPointType = AppConsistent le plus récent dans la liste.

Dans le script suivant, la variable **$rp**est un tableau de points de récupération des 7 derniers jours pour l’élément de sauvegarde sélectionné. Le tableau est trié dans l’ordre chronologique inverse, le point de récupération le plus récent détenant l’index 0. Utilisez l'indexation de tableau PowerShell standard pour sélectionner le point de récupération. Dans l’exemple, $rp[0] sélectionne le dernier point de récupération.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Le résultat ressemble à l’exemple suivant :

```
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>Restaurer les disques

Utilisez l’applet de commande **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** pour restaurer les données et la configuration d’un élément de sauvegarde à un point de récupération. Lorsque vous avez identifié un point de récupération, utilisez-le comme valeur du paramètre **-RecoveryPoint**. Dans l’exemple de code précédent, **$rp[0]** était le point de récupération à utiliser. Dans l’exemple de code suivant, **$rp [0]** est le point de récupération à utiliser pour restaurer le disque.

Pour restaurer les disques et les informations de configuration :

```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```
#### <a name="restore-managed-disks"></a>Restaurer des disques managés

> [!NOTE]
> Si la machine virtuelle sauvegardée possède des disques managés que vous souhaitez restaurer, vous pouvez désormais le faire dans Azure Powershell v6.7.0 et versions ultérieures.
>
>

Précisez un paramètre supplémentaire **TargetResourceGroupName** correspondant au groupe de ressources dans lequel les disques managés seront restaurés.


```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

Le fichier **VMConfig.JSON** sera restauré dans le compte de stockage, et les disques managés seront restaurés dans le groupe de ressources cible spécifié.


Le résultat ressemble à l’exemple suivant :
```
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Utilisez l’applet de commande **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** pour attendre que la tâche de restauration soit terminée.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Une fois le travail de restauration terminé, utilisez l’applet de commande **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** pour obtenir les détails du travail de restauration. La propriété JobDetails contient les informations nécessaires pour régénérer la machine virtuelle.

```powershell
$restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Une fois que vous avez restauré les disques, accédez à la section suivante pour créer la machine virtuelle.

## <a name="create-a-vm-from-restored-disks"></a>Créer une machine virtuelle à partir de disques restaurés

Après avoir restauré les disques, utilisez les étapes ci-après pour créer et configurer la machine virtuelle à partir du disque.

> [!NOTE]
> Pour créer des machines virtuelles chiffrées à partir de disques restaurés, votre rôle Azure doit avoir l’autorisation d’effectuer l’action **Microsoft.KeyVault/vaults/deploy/action**. Si votre rôle ne dispose pas de cette autorisation, créez un rôle personnalisé avec cette action. Pour plus d’informations, consultez [Rôles personnalisés dans le contrôle d’accès en fonction du rôle (RBAC) Azure](../role-based-access-control/custom-roles.md).
>
>

1. Interrogez les propriétés des disques restaurés pour obtenir les détails du travail.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Définissez le contexte de stockage Azure et restaurez le fichier de configuration JSON.

   ```powershell
   Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzureStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Utilisez le fichier de configuration JSON pour créer la configuration de la machine virtuelle.

   ```powershell
   $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Attachez le disque du système d’exploitation et les disques de données. Cette étape fournit des exemples pour différentes configurations de machines virtuelles managées et chiffrées. Utilisez l’exemple qui correspond le mieux à votre configuration de machine virtuelle.

   * **Machines virtuelles non managées et non chiffrées** : utilisez l’exemple suivant pour une machine virtuelle non managée et non chiffrée.

       ```powershell
       Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **Machines virtuelles non managées et chiffrées (à l’aide de clés BEK uniquement)** : pour les machines virtuelles non managées et chiffrées (à l’aide de clés BEK uniquement), vous devez restaurer le secret dans le coffre de clés avant d’attacher les disques. Pour plus d’informations, consultez l’article [Restaurer une machine virtuelle chiffrée à partir d’un point de récupération Sauvegarde Azure](backup-azure-restore-key-secret.md). L’exemple suivant montre comment attacher des disques de système d’exploitation et de données pour les machines virtuelles chiffrées. Au moment de définir le disque du système d’exploitation, vérifiez que le type de système d’exploitation approprié est mentionné.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```
      Utilisez la commande suivante pour activer manuellement le chiffrement pour les disques de données.

      ```powershell
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -VolumeType Data
      ```

   * **Machines virtuelles non managées et chiffrées (à l’aide de clés BEK et KEK)** : pour les machines virtuelles non managées et chiffrées (à l’aide de clés BEK et KEK), vous devez restaurer la clé et le secret dans le coffre de clés avant d’attacher les disques. Pour plus d’informations, consultez l’article [Restaurer une machine virtuelle chiffrée à partir d’un point de récupération Sauvegarde Azure](backup-azure-restore-key-secret.md). L’exemple suivant montre comment attacher des disques de système d’exploitation et de données pour les machines virtuelles chiffrées.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
       {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
      ```

      Utilisez la commande suivante pour activer manuellement le chiffrement des disques de données.

      ```powershell
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Machines virtuelles managées et non chiffrées** : utilisez l’exemple suivant pour une machine virtuelle managée mais pas chiffrée. Pour plus d’informations, consultez l’article [Attacher un disque de données à une machine virtuelle Windows à l’aide de PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Machines virtuelles managées et chiffrées (à l’aide de clés BEK uniquement)**  : pour les machines virtuelles managées et chiffrées (à l’aide de clés BEK uniquement), joignez les disques managés restaurés. Pour plus d’informations, consultez l’article [Attacher un disque de données à une machine virtuelle Windows à l’aide de PowerShell](../virtual-machines/windows/attach-disk-ps.md).
   
      Utilisez la commande suivante pour activer manuellement le chiffrement des disques de données.

       ```powershell
       Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
       ```

   * **Machines virtuelles managées et chiffrées (à l’aide de clés BEK et KEK)**  : pour les machines virtuelles managées et chiffrées (à l’aide de clés BEK et KEK), joignez les disques managés restaurés. Pour plus d’informations, consultez l’article [Attacher un disque de données à une machine virtuelle Windows à l’aide de PowerShell](../virtual-machines/windows/attach-disk-ps.md). 

      Utilisez la commande suivante pour activer manuellement le chiffrement des disques de données.

      ```powershell
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

5. Définissez les paramètres réseau.

    ```powershell
    $nicName="p1234"
    $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzureRmVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzureRmVirtualNetwork
    $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Créez la machine virtuelle.

    ```powershell  
    New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Restaurer des fichiers à partir d’une sauvegarde de machine virtuelle Azure

En plus de la restauration des disques, vous pouvez également restaurer des fichiers individuels à partir d’une sauvegarde de machine virtuelle Azure. La fonctionnalité de restauration des fichiers permet d’accéder à tous les fichiers dans un point de récupération. Gérez les fichiers via l’Explorateur de fichiers comme vous le feriez pour des fichiers normaux.

Les étapes élémentaires pour restaurer un fichier à partir d’une sauvegarde de machine virtuelle Azure sont les suivantes :

* Sélection de la machine virtuelle
* Choisir un point de récupération
* Monter les disques du point de récupération
* Copier les fichiers nécessaires
* Démonter le disque


### <a name="select-the-vm"></a>Sélection de la machine virtuelle

Pour obtenir l’objet PowerShell permettant d’identifier l’élément à restaurer, vous devez commencer au niveau du conteneur dans le coffre et descendre dans la hiérarchie d’objets. Pour sélectionner le conteneur qui représente la machine virtuelle, utilisez l’applet de commande **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** et dirigez-la vers l’applet de commande **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)**.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Choisir un point de récupération

Utilisez l’applet de commande **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** pour répertorier tous les points de récupération pour l’élément de sauvegarde. Ensuite, choisissez le point de récupération à restaurer. Si vous ne savez pas quel point de récupération utiliser, nous vous conseillons de choisir le point RecoveryPointType = AppConsistent le plus récent dans la liste.

Dans le script suivant, la variable **$rp**est un tableau de points de récupération des 7 derniers jours pour l’élément de sauvegarde sélectionné. Le tableau est trié dans l’ordre chronologique inverse, le point de récupération le plus récent détenant l’index 0. Utilisez l'indexation de tableau PowerShell standard pour sélectionner le point de récupération. Dans l’exemple, $rp[0] sélectionne le dernier point de récupération.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Le résultat ressemble à l’exemple suivant :

```
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Monter les disques du point de récupération

Utilisez l’applet de commande **[Get-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript)** pour le montage de tous les disques du point de récupération par le script.

> [!NOTE]
> Les disques sont montés en tant que disques iSCSI attachés à la machine sur laquelle le script est exécuté. Le montage a lieu immédiatement et vous évitez les frais.
>
>

```powershell
Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

Le résultat ressemble à l’exemple suivant :

```
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Exécutez le script sur la machine où vous souhaitez récupérer les fichiers. Pour exécuter le script, vous devez entrer le mot de passe fourni. Lorsque les disques sont attachés, utilisez l’Explorateur de fichiers Windows pour parcourir les nouveaux volumes et fichiers. Pour plus d’informations, consultez l’article sur la sauvegarde [Récupérer des fichiers de sauvegarde de machine virtuelle Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Démonter les disques

Lorsque les fichiers nécessaires à l’opération sont copiés, utilisez  **[Disable-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** pour démonter les disques. Veillez à démonter les disques afin que l’accès aux fichiers du point de récupération soit supprimé.

```powershell
Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>Étapes suivantes

Si vous préférez utiliser PowerShell pour gérer vos ressources Azure, consultez l’article PowerShell [Déployer et gérer une sauvegarde pour Windows Server](backup-client-automation.md). Si vous gérez des sauvegardes DPM, consultez l’article [Déployer et gérer la sauvegarde pour DPM](backup-dpm-automation.md). Ces deux articles ont une version concernant les déploiements avec le modèle Resource Manager et le modèle Classic.  
