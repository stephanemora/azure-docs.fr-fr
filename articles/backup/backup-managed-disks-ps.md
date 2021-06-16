---
title: Sauvegarder des disques managés Azure avec Azure PowerShell
description: Découvrez comment sauvegarder des disques managés Azure avec Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 31259bdbdc99fd307337cd6059f9160a0aeaf05e
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110672129"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>Sauvegarder des disques managés Azure avec Azure PowerShell

Cet article explique comment sauvegarder [un disque managé Azure](../virtual-machines/managed-disks-overview.md) avec Azure PowerShell.

Dans cet article, vous allez apprendre à :

- Créer un coffre de sauvegarde

- Créer une stratégie de sauvegarde

- Configurer une sauvegarde d’un disque Azure

- Exécuter un travail de sauvegarde à la demande

Pour plus d’informations sur la disponibilité par région, les scénarios pris en charge et les limitations de la sauvegarde de disque Azure, consultez la [matrice de prise en charge](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Créer un coffre de sauvegarde

Un coffre Sauvegarde est une entité de stockage dans Azure qui contient les données de sauvegarde de diverses charges de travail plus récentes prises en charge par la Sauvegarde Azure, notamment des serveurs Azure Database pour PostgreSQL et des disques Azure. Les coffres de sauvegarde facilitent l’organisation de vos données de sauvegarde tout en réduisant le temps nécessaire à leur gestion. Les coffres Sauvegarde sont basés sur le modèle Azure Resource Manager, qui fournit des fonctionnalités améliorées pour sécuriser les données de sauvegarde.

Avant de créer un coffre de sauvegarde, choisissez la redondance de stockage des données dans le coffre. Ensuite, procédez à la création du coffre de sauvegarde avec cette redondance de stockage et l’emplacement. Dans cet article, nous allons créer un coffre de sauvegarde « TestBkpVault » dans la région « westus » sous le groupe de ressources « testBkpVaultRG ». Utilisez la commande [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) pour créer un coffre de sauvegarde. Apprenez-en plus sur [la création d’un coffre de sauvegarde](./backup-vault-overview.md#create-a-backup-vault).

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

Après la création du coffre, nous allons créer une stratégie de sauvegarde pour protéger les disques Azure.

## <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

Pour comprendre les composants internes d’une stratégie de sauvegarde pour la sauvegarde de disque Azure, récupérez le modèle de stratégie à l’aide de la commande [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true). Cette commande retourne un modèle de stratégie par défaut pour un type de source de données donné. Utilisez ce modèle de stratégie pour créer une nouvelle stratégie.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Le modèle de stratégie est constitué d’un déclencheur (qui détermine le déclenchement de la sauvegarde) et d’un cycle de vie (qui décide quand supprimer/copier/déplacer la sauvegarde). Dans la sauvegarde de disque Azure, la valeur par défaut pour le déclencheur est un déclencheur horaire planifié pour toutes les 4 heures (PT4H) et pour conserver chaque sauvegarde pendant 7 jours.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

La sauvegarde de disque Azure propose plusieurs sauvegardes par jour. Si vous avez besoin de sauvegardes plus fréquentes, choisissez la fréquence de sauvegarde **Horaire**, qui offre la possibilité d’effectuer des sauvegardes à intervalles de 4, 6, 8 ou 12 heures. Les sauvegardes sont planifiées en fonction de l’intervalle **Temps** sélectionné. Par exemple, si vous sélectionnez **Toutes les 4 heures**, les sauvegardes sont effectuées à intervalles de 4 heures environ, de façon à être réparties équitablement sur la journée. Si une sauvegarde par jour suffit, choisissez la fréquence de sauvegarde **Quotidienne**. Vous pouvez alors spécifier l’heure à laquelle s’effectuent vos sauvegardes. Il est à noter qu’il s’agit de l’heure de début de la sauvegarde, et non de l’heure de fin. Le temps nécessaire pour effectuer l’opération de sauvegarde dépend de différents facteurs, notamment la taille du disque et le taux d’attrition entre les sauvegardes consécutives. Toutefois, la sauvegarde de disque Azure est une sauvegarde sans agent qui utilise des [instantanés incrémentiels](../virtual-machines/disks-incremental-snapshots.md) sans impact sur le niveau de performance des applications de production.

   >[!NOTE]
   > Bien que le coffre sélectionné puisse posséder le paramètre de redondance globale, la sauvegarde de disque Azure ne prend en charge que le magasin de données d’instantanés. Toutes les sauvegardes sont stockées dans l’un des groupes de ressources de l’abonnement. Elles ne sont pas copiées dans le stockage du coffre Sauvegarde.

Pour plus d’informations sur la création de stratégies, reportez-vous au document sur la [stratégie de sauvegarde de disque Azure](backup-managed-disks.md#create-backup-policy).

Si vous souhaitez modifier la fréquence horaire ou la période de rétention, utilisez les commandes [Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) et/ou [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true). Une fois que l’objet de stratégie dispose de toutes les valeurs souhaitées, passez à la création d’une nouvelle stratégie à partir de l’objet de stratégie à l’aide de la commande [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>Configurer une sauvegarde

Une fois le coffre et la stratégie créés, il y a 3 points critiques que l’utilisateur doit prendre en compte pour protéger un disque Azure.

### <a name="key-entities-involved"></a>Entités clés impliquées

#### <a name="disk-to-be-protected"></a>Disque à protéger

Récupérez (fetch) l’ID ARM du disque à protéger. Il s’agit de l’identificateur du disque. Nous allons utiliser un exemple de disque nommé « PSTestDisk » sous un groupe de ressources « diskrg » dans un autre abonnement.

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>Groupe de ressources d'instantanés

Les instantanés de disque sont stockés dans l’un des groupes de ressources de votre abonnement. En règle générale, il est recommandé de créer un groupe de ressources dédié comme magasin de stockage d’instantanés utilisable par le service Sauvegarde Azure. Cela permet en effet de restreindre les autorisations d’accès sur le groupe de ressources, garantissant ainsi la sécurité et la facilité de gestion des données de sauvegarde. Notez l’ID ARM du groupe de ressources dans lequel vous souhaitez placer les instantanés de disque

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Archivage de sauvegarde

Les coffres de sauvegarde requièrent des autorisations sur le disque et le groupe de ressources d’instantanés pour pouvoir déclencher des instantanés et gérer leur cycle de vie. L’identité managée affectée par le système du coffre est utilisée pour affecter de telles autorisations. Utilisez la commande [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true) pour activer l’identité managée attribuée par le système pour le coffre Recovery Services.

### <a name="assign-permissions"></a>Attribuer des autorisations

L’utilisateur doit affecter peu d’autorisations via RBAC au coffre (représenté par le fichier MSI de coffre) et le disque approprié et/ou le RG de disque. Elles peuvent être effectuées via le Portail ou PowerShell. Toutes les autorisations associées sont détaillées dans les points 1, 2, 3 de [cette section](backup-managed-disks.md#configure-backup).

### <a name="prepare-the-request"></a>Préparer la requête

Une fois que toutes les autorisations appropriées sont définies, la configuration de la sauvegarde est effectuée en 2 étapes. Tout d’abord, nous préparons la requête appropriée en utilisant le coffre, la stratégie, le disque et le groupe de ressources d’instantanés appropriés à l’aide de la commande [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true). Ensuite, nous soumettons la requête pour protéger le disque à l’aide de la commande [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande

Récupérer (fetch) l’instance de sauvegarde appropriée sur laquelle l’utilisateur souhaite déclencher une sauvegarde à l’aide de la commande [AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true)

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

Vous pouvez spécifier une règle de rétention lors du déclenchement de la sauvegarde. Pour afficher les règles de rétention dans la stratégie, accédez à l’objet de stratégie pour les règles de rétention. Dans l’exemple ci-dessous, la règle portant le nom « default » s’affiche et nous allons utiliser cette règle pour la sauvegarde à la demande

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Déclenchez une sauvegarde à la demande à l’aide de la commande [Backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>Suivi des travaux

Effectuez le suivi de tous les travaux à l’aide de la commande [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true). Vous pouvez répertorier tous les travaux et extraire le détail d’un travail particulier.

Vous pouvez également utiliser Az.ResourceGraph pour effectuer le suivi de tous les travaux dans tous les coffres de sauvegarde. Utilisez la commande [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) pour récupérer le travail approprié qui peut s’appliquer à n’importe quel coffre de sauvegarde.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Étapes suivantes

- [Restaurer des disques managés Azure avec Azure PowerShell](restore-managed-disks-ps.md)
