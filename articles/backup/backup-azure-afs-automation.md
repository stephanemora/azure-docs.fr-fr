---
title: Sauvegarder Azure Files avec PowerShell
description: Dans cet article, découvrez comment sauvegarder Azure Files à l’aide du service Sauvegarde Azure et de PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 5147ab893d4ebad395d7dbd8cc25872177ec10a2
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773102"
---
# <a name="back-up-azure-files-with-powershell"></a>Sauvegarder Azure Files avec PowerShell

Cet article explique comment utiliser Azure PowerShell pour sauvegarder un partage de fichiers Azure Files à l’aide d’un coffre Recovery Services de [Sauvegarde Azure](backup-overview.md).

Cet article explique comment :

> [!div class="checklist"]
>
> * Configurer PowerShell et inscrire le fournisseur Azure Recovery Services.
> * Créez un coffre Recovery Services.
> * Configurer la sauvegarde d’un partage de fichiers Azure.
> * Exécuter un travail de sauvegarde.

## <a name="before-you-start"></a>Avant de commencer

* [En savoir plus](backup-azure-recovery-services-vault-overview.md) sur les coffres Recovery Services.
* En savoir plus sur les fonctionnalités en préversion de [sauvegarde de partages de fichiers Azure](backup-afs.md).
* Passez en revue la hiérarchie des objets PowerShell pour Recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Hiérarchie des objets dans Recovery Services

La hiérarchie des objets est résumée dans le schéma suivant.

![Hiérarchie des objets dans Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Passez en revue la [référence sur la cmdlet](/powershell/module/az.recoveryservices) **Az.RecoveryServices** dans la bibliothèque Azure.

## <a name="set-up-and-install"></a>Configurer et installer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configurez PowerShell comme suit :

1. [Téléchargez la dernière version d’Az PowerShell](/powershell/azure/install-az-ps). La version 1.0.0 est la version minimale requise.

2. Recherchez les cmdlets PowerShell Sauvegarde Azure avec cette commande :

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Passez en revue les alias et cmdlets pour Sauvegarde Azure, Azure Site Recovery et le coffre Recovery Services s’affiche. Voici un exemple de résultat possible. Il ne s’agit pas d’une liste complète des cmdlets.

    ![Liste des cmdlets Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Connectez-vous à votre compte Azure à l’aide de **Connect-AzAccount**.
5. Sur la page web qui s’affiche, vous êtes invité à entrer les informations d’identification de votre compte.

    * Vous pouvez également inclure les informations d’identification de votre compte en tant que paramètres dans la cmdlet **Connect-AzAccount** avec **-Credential**.
    * Si vous êtes partenaire CSP travaillant pour le compte d’un locataire, spécifiez le client en tant que locataire à l’aide de son ID locataire ou de son nom de domaine principal. Par exemple **Connect-AzAccount -Tenant** fabrikam.com.

6. Associez l’abonnement que vous souhaitez utiliser avec le compte, car un compte peut compter plusieurs abonnements.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Si vous utilisez le service Sauvegarde Azure pour la première fois, utilisez la cmdlet **Register-AzResourceProvider** pour associer le fournisseur Azure Recovery Service à votre abonnement.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Vérifiez que les fournisseurs ont été correctement inscrits :

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Dans la sortie de commande, vérifiez que **RegistrationState** a maintenant la valeur **Inscrit**. Si ce n’est pas ce cas, réexécutez la cmdlet **Register-AzResourceProvider**.

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Le coffre Recovery Services étant une ressource Resource Manager, vous devez le placer dans un groupe de ressources. Vous pouvez utiliser un groupe de ressources existant ou en créer un avec la cmdlet **New-AzResourceGroup**. Quand vous créez un groupe de ressources, spécifiez son nom et son emplacement.

Pour créer un coffre Recovery Services, procédez comme suit :

1. Un coffre est placé dans un groupe de ressources. Si vous n’avez pas de groupe de ressources, créez-en un avec la commande [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). Dans l’exemple suivant, nous créons un groupe de ressources dans la région USA Ouest.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. Utilisez la cmdlet [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) pour créer le coffre. Spécifiez pour le coffre le même emplacement que pour le groupe de ressources.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Spécifiez le type de redondance à utiliser pour le stockage de coffre.

   * Vous pouvez utiliser le [stockage localement redondant](../storage/common/storage-redundancy-lrs.md) ou le [stockage géoredondant](../storage/common/storage-redundancy-grs.md).
   * L’exemple suivant définit l’option **-BackupStorageRedundancy** pour la cmdlet [Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) de **testvault** définie sur **GeoRedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Afficher les coffres dans un abonnement

Pour afficher tous les coffres de l’abonnement, utilisez [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Le résultat ressemble à ce qui suit. Notez que le groupe de ressources et l’emplacement associés sont fournis.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Définir le contexte du coffre

Stockez l’objet de coffre dans une variable et définissez le contexte du coffre.

* Étant donné que de nombreuses cmdlets Sauvegarde Azure exigent l’objet de coffre Recovery Services en tant qu’entrée, il est judicieux de le stocker dans une variable.
* Le contexte du coffre spécifie le type de données protégées dans le coffre. Définissez-le avec [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Une fois le contexte défini, il s’applique à toutes les cmdlets suivantes.

L’exemple suivant définit le contexte pour le coffre **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Récupérer l’ID de coffre

Conformément aux instructions d’Azure PowerShell, nous prévoyons de déprécier la définition du contexte de coffre. À la place, vous pouvez stocker ou récupérer l’ID du coffre et le transmettre aux commandes appropriées. Ainsi, si vous n’avez pas défini le contexte du coffre ou si vous souhaitez spécifier la commande à exécuter pour un coffre donné, transmettez l’ID du coffre sous la forme « -VaultID » dans toutes les commandes concernées :

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Configurer une stratégie de sauvegarde

Une stratégie de sauvegarde spécifie la planification des sauvegardes et la durée de conservation des points de récupération de sauvegarde :

* Une stratégie de sauvegarde est associée à au moins une stratégie de rétention. Une stratégie de rétention définit la durée de conservation d’un point de restauration avant sa suppression.
* Affichez la stratégie de rétention de sauvegarde par défaut avec [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Affichez la planification de la stratégie de sauvegarde par défaut avec [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Vous utilisez la cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) pour créer une stratégie de sauvegarde. Vous entrez les objets de stratégie de planification et de rétention.

Par défaut, une heure de début est définie dans l’objet de la stratégie de planification. Utilisez l’exemple suivant pour modifier l’heure de début sur l’heure de début souhaitée. L’heure de début souhaitée doit également être au format UTC. L’exemple ci-dessous suppose que l’heure de début souhaitée est 01:00 AM UTC pour les sauvegardes quotidiennes.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Vous devez fournir l’heure de début en multiples de 30 minutes uniquement. Dans l’exemple ci-dessus, il peut s’agir uniquement de « 01:00:00 » ou de « 02:30:00 ». L’heure de début ne peut pas être « 01:15:00 ».

L’exemple suivant stocke la stratégie de planification et la stratégie de conservation dans des variables. Il utilise ensuite ces variables en tant que paramètres pour une nouvelle stratégie (**NewAFSPolicy**). La stratégie **NewAFSPolicy** effectue une sauvegarde quotidienne et la conserve pendant 30 jours.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Le résultat ressemble à ce qui suit.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Activer la sauvegarde

Une fois que vous avez défini la stratégie de sauvegarde, vous pouvez l’utiliser pour activer la protection du partage de fichiers Azure.

### <a name="retrieve-a-backup-policy"></a>Récupérer une stratégie de sauvegarde

Récupérez l’objet de stratégie approprié avec [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Utilisez cette cmdlet pour obtenir une stratégie spécifique ou pour afficher les stratégies associées à un type de charge de travail.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Récupérer une stratégie pour un type de charge de travail

L’exemple suivant permet de récupérer des stratégies pour le type de charge de travail **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Le résultat ressemble à ce qui suit.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Le fuseau horaire du champ **BackupTime** dans PowerShell est au format UTC. Lorsque l’heure de sauvegarde s’affiche dans le portail Azure, elle est alignée sur votre fuseau horaire.

### <a name="retrieve-a-specific-policy"></a>Récupérer une stratégie spécifique

La stratégie suivante récupère la stratégie de sauvegarde intitulée **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Activer la sauvegarde et appliquer la stratégie

Activez la protection avec [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Une fois la stratégie associée au coffre, les sauvegardes sont déclenchées conformément à la planification de la stratégie.

L’exemple suivant active la protection du partage de fichiers Azure **testAzureFileShare** dans le compte de stockage **testStorageAcct**, avec la stratégie **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

La commande attend que le travail de configuration de la protection soit terminé et produit une sortie similaire à celle illustrée ci-dessous.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>Déclencher une sauvegarde à la demande

Utilisez [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) pour exécuter une sauvegarde à la demande pour un partage de fichiers Azure protégé.

1. Récupérez le compte de stockage et le partage de fichiers d’un conteneur dans le coffre qui contient vos données de sauvegarde avec [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Pour démarrer un travail de sauvegarde, vous obtenez des informations sur la machine virtuelle avec [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Exécutez une sauvegarde à la demande avec [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Exécutez la sauvegarde à la demande comme suit :

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

La commande retourne un travail avec un ID à suivre, comme indiqué dans l’exemple suivant.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Lors des sauvegardes, nous utilisons les captures instantanées de partages de fichiers Azure. Par conséquent, le travail est généralement terminé au moment où la commande retourne cette sortie.

### <a name="using-on-demand-backups-to-extend-retention"></a>Utilisation de sauvegardes à la demande pour étendre la conservation

Vous pouvez utiliser des sauvegardes à la demande pour conserver vos captures instantanées pendant 10 ans. Vous pouvez utiliser des planificateurs pour exécuter des scripts PowerShell à la demande avec la conservation choisie, puis pour prendre des captures instantanées à intervalles réguliers chaque semaine, mois ou année. Quand vous prenez régulièrement des captures instantanées, reportez-vous aux [limitations des sauvegardes à la demande](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) à l’aide de Sauvegarde Azure.

Si vous recherchez des exemples de scripts, vous pouvez vous référer à l’exemple de script sur GitHub (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>) qui utilise un runbook Azure Automation permettant de planifier des sauvegardes régulières et de les conserver jusqu’à 10 ans.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur](backup-afs.md) la sauvegarde Azure Files dans le Portail Azure.
