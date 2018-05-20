---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 5/9/2018
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 4db9fe907ab6625fcad74ceae59f17115458a3ea
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
---
# <a name="write-accelerator"></a>Accélérateur des écritures
L’Accélérateur des écritures est une fonctionnalité de disque des machines virtuelles Série M sur stockage Premium avec des Azure Managed Disks exclusivement. Comme son nom l’indique, cette fonctionnalité vise à améliorer la latence d’E/S des écritures dans le stockage Azure Premium. L’Accélérateur des écritures est particulièrement adapté lorsque les mises à jour du fichier journal doivent être conservées sur disque dans un système à hautes performances pour répondre aux besoins des bases de données modernes.

L’Accélérateur des écritures est généralement disponible pour les machines virtuelles Série M dans le cloud public.

## <a name="planning-for-using-write-accelerator"></a>Planification de l’utilisation de l’Accélérateur des écritures
Utilisez l’Accélérateur des écritures pour les volumes qui stockent le journal des transactions ou les journaux de restauration d’un SGBD (système de gestion de base de données). Il n’est pas recommandé d’utiliser l’Accélérateur des écritures pour les volumes de données d’une SGBD car la fonctionnalité a été optimisée pour être utilisée sur les disques journaux.

L’Accélérateur des écritures fonctionne uniquement en association avec [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). 


> [!IMPORTANT]
> Si vous souhaitez activer ou désactiver l’Accélérateur des écritures pour un volume existant constitué de plusieurs disques de stockage Azure Premium et agrégé par bandes à l’aide des gestionnaires de disques ou de volumes Windows, des espaces de stockage Windows, de la fonctionnalité SOFS Windows, de LVM ou MDADM pour Linux, vous devez l’activer ou le désactiver sur tous les disques du volume en effectuant plusieurs étapes distinctes. **Avant d’activer ou de désactiver l’Accélérateur des écritures Azure dans cette configuration, arrêtez la machine virtuelle Azure**. 


> [!IMPORTANT]
> Si vous souhaitez activer l’Accélérateur des écritures sur un disque Azure existant qui ne fait PAS partie d’un volume constitué de plusieurs disques à l’aide des gestionnaires de disques ou de volumes Windows, des espaces de stockage Windows, de la fonctionnalité SOFS Windows, de LVM ou MDADM pour Linux, vous devez arrêter la charge de travail qui accède au disque Azure. Les applications de base de données qui utilisent le disque Azure DOIVENT également être arrêtées.

> [!IMPORTANT]
> L’activation de l’Accélérateur des écritures sur le disque système de la machine virtuelle provoque le redémarrage de la machine virtuelle. 

L’activation de l’Accélérateur des écritures sur les disques système n’est généralement pas nécessaire pour les configurations de machines virtuelles SAP

### <a name="restrictions-when-using-write-accelerator"></a>Restrictions relatives à l’utilisation de l’Accélérateur des écritures
Quand vous utilisez l’Accélérateur des écritures sur un disque/disque dur virtuel Azure, les restrictions suivantes s’appliquent :

- La mise en cache du disque Premium doit être définie sur Aucune ou Lecture seule. Les autres modes de mise en cache ne sont pas pris en charge.
- Les instantanés sur un disque avec l’Accélérateur des écritures activé ne sont pas pris en charge. Cette restriction empêche le service Sauvegarde Azure d’effectuer un instantané cohérent des applications sur tous les disques de la machine virtuelle.
- Seules les tailles d’E/S plus petites (<=32 Kio) prennent le chemin d’accès accéléré. Dans les cas de charge de travail où les données sont chargées en bloc ou les tampons de journal des transactions des différents systèmes de gestion de base de données (SGBD) sont davantage remplis avant d’être conservés dans le stockage, les E/S écrites sur le disque risquent de ne pas prendre le chemin d’accès accéléré.

L’Accélérateur des écritures prend en charge un nombre limité de disques durs virtuels de stockage Azure Premium par machine virtuelle. Les limites actuelles sont :

| Référence de la machine virtuelle | Nombre de disques avec Accélérateur des écritures | IOPS de disque avec Accélérateur des écritures par machine virtuelle |
| --- | --- | --- |
| M128ms | 16 | 8000 |
| M128s | 16 | 8000 |
| M64ms | 8 | 4000 |
| M64s | 8 | 4000 | 

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Activation de l’Accélérateur des écritures sur un disque spécifique
Les sections ci-après décrivent comment activer l’Accélérateur des écritures sur des disques durs virtuels de stockage Azure Premium.


### <a name="prerequisites"></a>Prérequis

Assurez-vous de remplir les prérequis suivants pour pouvoir utiliser l’Accélérateur des écritures :

- Les disques sur lesquels vous voulez activer l’Accélérateur des écritures Azure doivent être des [disques managés Azure](https://azure.microsoft.com/services/managed-disks/) sur stockage Premium.
- Vous devez utiliser une machine virtuelle Série M

### <a name="enabling-through-power-shell"></a>Activation par le biais de PowerShell
Dans les versions 5.5.0 et ultérieures du module Azure PowerShell, des modifications ont été apportées aux applets de commande concernées pour permettre l’activation ou la désactivation de l’Accélérateur des écritures sur des disques de stockage Azure Premium spécifiques.
Pour permettre l’activation ou le déploiement de disques pris en charge par l’Accélérateur des écritures, les commandes PowerShell suivantes ont été modifiées et étendues avec un paramètre pour l’accélérateur.

Le nouveau paramètre de commutateur WriteAccelerator a été ajouté aux applets de commande suivantes : 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

Si le paramètre n’est pas spécifié, la propriété est définie sur false, et les disques déployés ne sont pas pris en charge par l’Accélérateur des écritures.

Le nouveau paramètre de commutateur OsDiskWriteAccelerator a été ajouté aux applets de commande suivantes : 

- Set-AzureRmVmssStorageProfile

Si le paramètre n’est pas spécifié, la propriété est définie sur false, et les disques déployés n’utilisent pas l’Accélérateur des écritures.

Le nouveau paramètre booléen OsDiskWriteAccelerator (paramètre facultatif et qui n’accepte pas les valeurs Null) a été ajouté aux applets de commande suivantes : 

- Update-AzureRmVM
- Update-AzureRmVmss

Spécifiez $true ou $false pour activer ou désactiver la prise en charge de l’Accélérateur des écritures Azure avec les disques.

Exemples de commandes :

```

New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false 

```

Les scripts montrés dans les sections suivantes correspondent à deux scénarios courants.

#### <a name="adding-a-new-disk-supported-by-write-accelerator"></a>Ajout d’un nouveau disque pris en charge par l’Accélérateur des écritures
Vous pouvez utiliser ce script pour ajouter un nouveau disque à votre machine virtuelle. Le disque créé avec ce script pourra utiliser l’Accélérateur des écritures.

```

# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzureRmVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```
Changez le nom de la machine virtuelle, le nom du disque, le nom du groupe de ressources, la taille du disque et le LunID du disque en fonction de votre déploiement spécifique.


#### <a name="enabling-azure-write-accelerator-on-an-existing-azure-disk"></a>Activation de l’Accélérateur des écritures Azure sur un disque Azure existant
Si vous devez activer l’Accélérateur des écritures sur un disque existant, faites-le à l’aide de ce script :

```

#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

Changez les noms de la machine virtuelle, du disque et du groupe de ressources pour votre propre déploiement. Le script ci-dessus ajoute l’Accélérateur des écritures à un disque existant si le paramètre $newstatus est défini sur $true. Si ce paramètre est défini sur $false, l’Accélérateur des écritures est désactivé sur le disque.

> [!Note]
> L’exécution du script ci-dessus détache le disque spécifié, active l’Accélérateur des écritures sur le disque, puis réattache le disque

### <a name="enabling-through-azure-portal"></a>Activation via le portail Azure

Vous pouvez activer l’Accélérateur des écritures sur le portail, en spécifiant vos paramètres de mise en cache du disque : 

![Accélérateur des écritures sur le portail Azure](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

### <a name="enabling-through-azure-cli"></a>Activation par le biais d’Azure CLI
Vous pouvez utiliser [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) pour activer l’accélérateur d’écriture. 

Pour activer l’accélérateur d’écriture sur un disque existant, utilisez la commande ci-dessous, en remplaçant diskName, VMName et ResourceGroup par vos propres valeurs : 
```
az vm update -g group1 -n vm1 –write-accelerator 1=true
```
Pour attacher un disque alors que l’accélérateur d’écriture est activé, utilisez la commande ci-dessous avec vos valeurs :
```
az vm disk attach -g group1 –vm-name vm1 –disk d1 --enable-write-accelerator
```
Pour désactiver l’accélérateur d’écriture, définissez la propriété sur false : 
```
az vm update -g group1 -n vm1 –write-accelerator 0=false 1=false
```

### <a name="enabling-through-rest-apis"></a>Activation par le biais des API Rest
Pour effectuer le déploiement à l’aide de l’API Rest Azure, vous devez installer Azure armclient

#### <a name="install-armclient"></a>Installer armclient

Pour pouvoir utiliser armclient, installez-le via Chocolatey, à l’aide de cmd.exe ou PowerShell. Exécutez ces commandes avec des droits élevés (« Exécuter en tant qu’administrateur »).

Avec cmd.exe, exécutez la commande suivante :

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

Avec PowerShell, exécutez la commande suivante :

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

Vous pouvez maintenant installer armclient en exécutant la commande suivante dans cmd.exe ou PowerShell

```
choco install armclient
```

#### <a name="getting-your-current-vm-configuration"></a>Obtenir la configuration actuelle de votre machine virtuelle
Pour changer les attributs de configuration du disque, vous devez d’abord obtenir la configuration actuelle et l’enregistrer dans un fichier JSON. Pour cela, exécutez la commande suivante :

```
armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>
```
Remplacez le contenu entre les balises <<   >> par vos propres données, dont le nom souhaité pour le fichier JSON.

Le résultat doit être semblable à ceci :

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

L’étape suivante consiste à mettre à jour le fichier JSON et à activer l’Accélérateur des écritures sur le disque appelé « log1 ». Cette étape peut être effectuée en ajoutant cet attribut après l’entrée du cache du disque dans le fichier JSON. 

```
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Ensuite, mettez à jour le déploiement existant avec cette commande :

```
armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>

```

Le résultat doit ressembler à l’exemple ci-dessous. Vous pouvez voir que l’Accélérateur des écritures est activé pour un seul disque.

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

À présent, le disque est normalement pris en charge par l’Accélérateur des écritures.

 
