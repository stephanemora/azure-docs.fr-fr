---
title: Sauvegarder des objets blob Azure dans un compte de stockage à l’aide d’Azure PowerShell
description: Découvrez comment sauvegarder tous les objets blob Azure dans un compte de stockage à l’aide d’Azure PowerShell.
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: ed657d40a5967431c9bf2017d47d0d3c13cd9908
ms.sourcegitcommit: 2cb7772f60599e065fff13fdecd795cce6500630
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108803131"
---
# <a name="back-up-all-azure-blobs-in-a-storage-account-using-azure-powershell"></a>Sauvegarder tous les objets blob Azure dans un compte de stockage à l’aide d’Azure PowerShell

Cet article explique comment sauvegarder tous les [objets blob Azure](/azure/backup/blob-backup-overview) dans un compte de stockage à l’aide d’Azure PowerShell.

Dans cet article, vous allez apprendre à :

- Créer un coffre de sauvegarde

- Créer une stratégie de sauvegarde

- Configurer une sauvegarde de tous les objets blob Azure dans les comptes de stockage

Pour plus d’informations sur la disponibilité par région, les scénarios pris en charge et les limitations des objets blob Azure, consultez la [matrice de prise en charge](blob-backup-support-matrix.md).

> [!IMPORTANT]
> La prise en charge des objets blob Azure est disponible à partir de la version Az 5.9.0.

## <a name="create-a-backup-vault"></a>Créer un coffre de sauvegarde

Un coffre Sauvegarde est une entité de stockage dans Azure qui contient les données de sauvegarde de diverses charges de travail plus récentes prises en charge par la Sauvegarde Azure, notamment des serveurs Azure Database pour PostgreSQL et des objets blob Azure. Les coffres de sauvegarde facilitent l’organisation de vos données de sauvegarde tout en réduisant le temps nécessaire à leur gestion. Les coffres Sauvegarde sont basés sur le modèle Azure Resource Manager, qui fournit des fonctionnalités améliorées pour sécuriser les données de sauvegarde.

Avant de créer un coffre de sauvegarde, choisissez la redondance de stockage des données dans le coffre. Ensuite, procédez à la création du coffre de sauvegarde avec cette redondance de stockage et l’emplacement. Dans cet article, nous allons créer un coffre de sauvegarde _TestBkpVault_ dans la région _westus_ sous le groupe de ressources _testBkpVaultRG_. Utilisez la commande [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.9.0&preserve-view=true) pour créer un coffre de sauvegarde. Apprenez-en plus sur [la création d’un coffre de sauvegarde](./backup-vault-overview.md#create-a-backup-vault).

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

Après la création du coffre, nous allons créer une stratégie de sauvegarde pour protéger les objets blob Azure.

> [!IMPORTANT]
> Même si vous voyez la Redondance du stockage de sauvegarde du coffre, celle-ci ne s’applique pas à la sauvegarde opérationnelle d’objets blob, car la sauvegarde est locale par nature et aucune donnée n’est stockée dans le coffre de sauvegarde. Le coffre de sauvegarde. Le coffre de sauvegarde est ici l’entité de gestion destinée à vous aider à gérer la protection des objets blob de blocs dans vos comptes de stockage.

## <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

> [!IMPORTANT]
> Lisez [cette section](blob-backup-configure-manage.md#before-you-start) avant de commencer à créer la stratégie et à configurer des sauvegardes pour les objets blob Azure.

Pour comprendre les composants internes d’une stratégie de sauvegarde pour la sauvegarde d’objet blob Azure, récupérez le modèle de stratégie à l’aide de la commande [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.9.0&preserve-view=true). Cette commande retourne un modèle de stratégie par défaut pour un type de source de données donné. Utilisez ce modèle de stratégie pour créer une nouvelle stratégie.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureBlob
$policyDefn | fl


DatasourceType : {Microsoft.Storage/storageAccounts/blobServices}
ObjectType     : BackupPolicy
PolicyRule     : {Default}

$policyDefn.PolicyRule | fl

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api202101.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Le modèle de stratégie se compose d’un cycle de vie uniquement (qui décide quand supprimer/copier/déplacer la sauvegarde). Étant donné que la sauvegarde opérationnelle d’objets blob est continue par nature, vous n’avez pas besoin d’une planification pour effectuer des sauvegardes.

```azurepowershell-interactive
$policyDefn.PolicyRule.Lifecycle | fl


DeleteAfterDuration        : P30D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

> [!NOTE]
> Restaurer sur de longues durées peut conduire à des opérations de restauration plus longues. De plus, le temps nécessaire à la restauration d’un jeu de données dépend du nombre d’opérations d’écriture et de suppression effectuées au cours de la période de restauration. Par exemple, la restauration à un point situé 30 jours auparavant d’un compte avec 1 million d’objets, 3 000 objets ajoutés par jour et 1 000 objets supprimés par jour nécessite environ deux heures.<br><br>Une période de rétention et une restauration à plus de 90 jours dans le passé ne sont pas recommandées pour un compte avec ce taux de changement.

Une fois que l’objet de stratégie dispose de toutes les valeurs souhaitées, passez à la création d’une nouvelle stratégie à partir de l’objet de stratégie à l’aide de la commande [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.9.0&preserve-view=true).

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name blobBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
blobBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$blobBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "blobBkpPolicy"
```

## <a name="configure-backup"></a>Configurer une sauvegarde

Une fois le coffre et la stratégie créés, il y a deux points critiques que l’utilisateur doit prendre en compte pour protéger tous les objets blob Azure dans un compte de stockage.

### <a name="key-entities-involved"></a>Entités clés impliquées

#### <a name="storage-account-which-contains-the-blobs-to-be-protected"></a>Compte de stockage qui contient les objets blob à protéger

Récupérez l’ID Azure Resource Manager du compte de stockage qui contient les objets blob à protéger. Il s’agit de l’identificateur du compte de stockage. Nous allons utiliser un exemple de compte de stockage nommé _PSTestSA_, sous le groupe de ressources _blobrg_, dans un autre abonnement.

```azurepowershell-interactive
$SAId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/PSTestSA"
```

#### <a name="backup-vault"></a>Archivage de sauvegarde

Le coffre de sauvegarde requiert des autorisations sur le compte de stockage pour permettre l’activation des sauvegardes sur les objets blob présents dans le compte de stockage. L’identité managée affectée par le système du coffre est utilisée pour affecter de telles autorisations.

### <a name="assign-permissions"></a>Attribuer des autorisations

Vous devez affecter quelques autorisations via RBAC au coffre (représenté par le fichier MSI de coffre) et le compte de stockage approprié. Elles peuvent être effectuées via le Portail ou PowerShell. En savoir plus sur toutes les [autorisations associées](blob-backup-configure-manage.md#grant-permissions-to-the-backup-vault-on-storage-accounts).

### <a name="prepare-the-request"></a>Préparer la requête

Une fois que toutes les autorisations appropriées sont définies, la configuration de la sauvegarde est effectuée en 2 étapes. Tout d’abord, nous préparons la requête appropriée en utilisant le coffre, la stratégie et le compte de stockage appropriés à l’aide de la commande [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.9.0&preserve-view=true). Ensuite, nous soumettons la requête pour protéger les objets blob dans le compte de stockage à l’aide de la commande [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.9.0&preserve-view=true).

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureBlob -DatasourceLocation $TestBkpvault.Location -PolicyId $blobBkpPol[0].Id -DatasourceId $SAId 
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
blobrg-PSTestSA-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances blobrg-PSTestSA-3df6ac08-9496-4839-8fb5-8b78e594f166
```

> [!IMPORTANT]
> Une fois qu’un compte de stockage est configuré pour la sauvegarde des objets blob, certaines fonctionnalités sont affectées, telles que le flux de modification et le verrou de suppression. [Plus d’informations](blob-backup-configure-manage.md#effects-on-backed-up-storage-accounts)

## <a name="next-steps"></a>Étapes suivantes

[Restaurer des objets blob Azure via Azure PowerShell](restore-blobs-storage-account-ps.md)
