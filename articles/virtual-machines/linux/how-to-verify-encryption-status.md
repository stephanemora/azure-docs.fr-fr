---
title: Comment vérifier l’état du chiffrement pour Linux
description: Cet article fournit des instructions sur la vérification de l’état du chiffrement au niveau de la plateforme et du système d’exploitation.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123423"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Comment vérifier l’état du chiffrement pour Linux 

**Ce scénario s’applique aux extensions ADE à deux passes et à une seule passe.**  
L’objectif de ce document est de valider l’état du chiffrement d’une machine virtuelle selon différentes méthodes.

### <a name="environment"></a>Environnement

- Distributions Linux

### <a name="procedure"></a>Procédure

Une machine virtuelle a été chiffrée en une seule passe ou en deux passes.

L’état du chiffrement peut être validé pendant ou après le chiffrement selon différentes méthodes.

>[!NOTE] 
>Nous utilisons des variables dans le document. Vous devez donc remplacer les valeurs en conséquence.

### <a name="verification"></a>Vérification

La vérification peut être effectuée à partir du portail, de PowerShell, de l’interface CLI Azure ou du système d’exploitation de la machine virtuelle. 

Cette vérification peut être effectuée en vérifiant les disques attachés à une machine virtuelle en particulier. 

Ou en interrogeant les paramètres de chiffrement sur chaque disque, que ce disque soit attaché ou non.

Voici les différentes méthodes de validation :

## <a name="using-the-portal"></a>Utilisation du Portail

Validez l’état du chiffrement en vérifiant la section des extensions sur le Portail Azure.

Dans la section **Extensions**, vous verrez l’extension ADE indiquée. 

Cliquez dessus et examinez le **message d’état**, qui indique l’état de chiffrement actuel :

![Vérification numéro 1 sur le portail](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

Dans la liste des extensions, vous verrez la version de l’extension ADE correspondante. La version 0. x correspond à ADE double-passe et la version 1. x correspond à ADE simple passe.

Vous pouvez obtenir plus de détails en cliquant sur l’extension, puis sur *Voir l’état détaillé*.

Vous verrez un état plus détaillé du processus de chiffrement au format json :

![Vérification numéro 2 sur le portail](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Vérification numéro 3 sur le portail](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Pour valider l’état du chiffrement, vous pouvez également consulter la section **Disques**.

![Vérification numéro 4 sur le portail](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Cet état indique que les disques ont des paramètres de chiffrement, mais qu’ils n’ont pas été réellement chiffrés au niveau du système d’exploitation. Par défaut, les disques sont d’abord marqués, puis chiffrés ultérieurement. Si le processus de chiffrement échoue, les disques peuvent finir par être marqués, mais pas chiffrés. Pour vérifier si les disques sont réellement chiffrés, vous pouvez vérifier le chiffrement de chaque disque au niveau du système d’exploitation.

## <a name="using-powershell"></a>Utilisation de PowerShell

Vous pouvez valider l’état **général** du chiffrement d’une machine virtuelle chiffrée à l’aide des commandes PowerShell suivantes :

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![check PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Vous pouvez capturer les paramètres de chiffrement de chaque disque à l’aide des commandes PowerShell suivantes :

### <a name="single-pass"></a>Une passe
Si vous effectuez une seule passe, les paramètres de chiffrement sont marqués sur chacun des disques (système d’exploitation et données), vous pouvez capturer les paramètres de chiffrement de disque du système d’exploitation en une seule passe comme suit :

``` powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Verify OS Single pass 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Si le disque ne comporte pas de paramètres de chiffrement, le résultat est vide comme indiqué ci-dessous :

![OS Encryption settings 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Capturer les paramètres de chiffrement des disques de données :

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Verify data single ps 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Double passe
Dans le cas d’une double passe, les paramètres de chiffrement sont marqués sur le modèle de machine virtuelle et non sur chaque disque.

Pour vérifier que les paramètres de chiffrement ont été marqués dans une double passe, vous pouvez utiliser les commandes suivantes :

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Verify dual pass PowerShell  1](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Disques non attachés

Vérifiez les paramètres de chiffrement pour les disques qui ne sont pas attachés à une machine virtuelle.

### <a name="managed-disks"></a>Disques managés
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="using-az-cli"></a>Utilisation de l’interface CLI AZ

Vous pouvez valider l’état **général** du chiffrement d’une machine virtuelle chiffrée à l’aide des commandes d’interface CLI AZ suivantes :

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Vérification générale à l’aide de l’interface CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Une passe
Vous pouvez valider les paramètres de chiffrement de chaque disque à l’aide des commandes d’interface CLI AZ suivantes :

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Paramètres de chiffrement des données](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Si le disque ne comporte pas de paramètres de chiffrement, le disque sera présenté comme non chiffré.

Paramètres détaillés d’état et de chiffrement :

Disque du système d’exploitation :

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![OSSingleCLI](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Disques de données :

```bash
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Data single CLI ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Deux passes

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Vérifier la double passe générale à l’aide de l’interface CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) Vous pouvez également vérifier les paramètres de chiffrement sur le profil de stockage du modèle de machine virtuelle du disque du système d’exploitation :

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Vérifier le profil de la machine virtuelle double avec l’interface CLI ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Disques non attachés

Vérifiez les paramètres de chiffrement pour les disques qui ne sont pas attachés à une machine virtuelle.

### <a name="managed-disks"></a>Disques managés

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Disques non managés

Les disques non managés sont des fichiers VHD qui sont stockés en tant qu’objets blob de pages dans des comptes de stockage Azure.

Pour obtenir les détails sur un disque en particulier, vous devez fournir les éléments suivants :

ID du compte de stockage qui contient le disque.
Chaîne de connexion de ce compte de stockage.
Nom du conteneur qui stocke le disque.
Nom du disque.

Cette commande répertorie tous les ID de tous vos comptes de stockage :

```bash
az storage account list --query [].[id] -o tsv
```
Les ID de compte de stockage sont répertoriés sous la forme suivante :

/subscriptions/\<subscription id/resourceGroups/\<nom du groupe de ressources>/providers/Microsoft.Storage/storageAccounts/\<nom du compte de stockage>

Sélectionnez l’ID approprié et stockez-le sur une variable :
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
Chaîne de connexion.

Cette commande obtient la chaîne de connexion pour un compte de stockage et la stocke sur une variable :

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Nom du conteneur.

La commande suivante répertorie tous les conteneurs sous un compte de stockage :
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Le conteneur utilisé pour les disques est normalement appelé « VHD ».

Stocker le nom du conteneur sur une variable 
```bash
ContainerName="name of the container"
```

Nom du disque.

Utiliser cette commande pour répertorier tous les objets blob d’un conteneur particulier
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Choisissez le disque que vous souhaitez interroger et stockez son nom sur une variable.
```bash
DiskName="diskname.vhd"
```
Requête sur les paramètres de chiffrement du disque
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>À partir du système d’exploitation
Vérifier si les partitions du disque de données sont chiffrées (et que le disque du système d’exploitation ne l’est pas)

Lorsqu’une partition/un disque est chiffré(e), le type est affiché en tant que **crypt**. Lorsque ce n’est pas le cas, le type est affiché en tant que **part/disk**.

``` bash
lsblk
```

![Couche de chiffrement du système d’exploitation ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Vous pouvez obtenir plus de détails à l’aide de la variante « lsblk » suivante. 

Vous verrez une couche de type **crypt**, qui est montée par l’extension.

L’exemple suivant présente des volumes logiques et des disques normaux avec « **crypto\_LUKS FSTYPE** ».

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Couche de chiffrement du système d’exploitation 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

En guise d’étape supplémentaire, vous pouvez également vérifier si des clés sont chargées sur le disque de données.

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

Et quels appareils DM sont répertoriés comme chiffrés

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Étapes suivantes

- [Résolution des problèmes liés à Azure Disk Encryption](disk-encryption-troubleshooting.md)
