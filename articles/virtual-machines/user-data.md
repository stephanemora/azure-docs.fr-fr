---
title: Données utilisateur dans les machines virtuelles Azure
description: Permettez au client d'insérer un script ou d'autres métadonnées dans une machine virtuelle Azure au moment de l'approvisionnement.
services: virtual-machines-linux
author: ningk
manager: rogardle
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/30/2021
ms.author: ningk
ms.reviewer: azmetadata
ms.openlocfilehash: d149ead5cd45b55c846852f92342e6bbc56ba3cf
ms.sourcegitcommit: 19dfdfa85e92c6a34933bdd54a7c94e8b00eacfd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109665296"
---
# <a name="user-data-for-azure-virtual-machine"></a>Données utilisateur dans les machines virtuelles Azure 

Les données utilisateur vous permettent de transmettre vos propres scripts ou métadonnées à votre machine virtuelle.

## <a name="what-is-user-data"></a>Que désigne le terme « données utilisateur » ?

Il s'agit d'un ensemble de scripts ou d'autres métadonnées insérés dans une machine virtuelle Azure au moment de l'approvisionnement. Toute application installée sur la machine virtuelle peut accéder aux données utilisateur à partir du service Azure IMDS (Instance Metadata Service) à l'issue de l'approvisionnement. 

Les données utilisateur constituent une nouvelle version des [données personnalisées](./custom-data.md) et offrent des avantages supplémentaires :

* Les données utilisateur peuvent être récupérées à partir du service Azure IMDS (Instance Metadata Service) à l'issue de l'approvisionnement.

* Les données utilisateur sont persistantes. Elles restent disponibles pendant toute la durée de vie de la machine virtuelle.

* Les données utilisateur peuvent être mises à jour depuis l'extérieur de la machine virtuelle, sans arrêter ni redémarrer celle-ci.

* Les données utilisateur peuvent être interrogées via l'API GET VM/VMSS avec l'option $expand.

 En outre, si les données utilisateur ne sont pas ajoutées au moment de l'approvisionnement, vous pouvez toujours les ajouter ultérieurement.

**Avertissement de sécurité**

> [!WARNING]
> Les données utilisateur ne seront pas chiffrées, et n'importe quel processus de la machine virtuelle peut les interroger. Vous ne devez pas stocker d'informations confidentielles dans les données utilisateur.

Assurez-vous que vous disposez de la version de la plus récente de l'API Azure Resource Manager pour utiliser les nouvelles fonctionnalités des données utilisateur. Le contenu doit être encodé en base64 avant d'être transmis à l'API. La taille ne peut pas dépasser 64 ko.

## <a name="create-user-data-for-azure-vmvmss"></a>Créer des données utilisateur pour les machines virtuelles/groupes de machines virtuelles identiques Azure

**Ajout de données utilisateur lors de la création d'une machine virtuelle**

Utilisez [ce modèle Azure Resource Manager](https://aka.ms/ImdsUserDataArmTemplate) pour créer une machine virtuelle avec des données utilisateur.
Si vous utilisez l'API REST, pour les machines virtuelles individuelles, ajoutez « UserData » à la section « properties » avec la requête PUT pour créer la machine virtuelle.

```json
{
  "name": "testVM",
  "location": "West US",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_A1"
    },
    "storageProfile": {
      "osDisk": {
        "osType": "Windows",
        "name": "osDisk",
        "createOption": "Attach",
        "vhd": {
          "uri": "http://myaccount.blob.core.windows.net/container/directory/blob.vhd"
        }
      }
    },
    "userData": "c2FtcGxlIHVzZXJEYXRh",
    "networkProfile": { "networkInterfaces" : [ { "name" : "nic1" } ] },
  }
}
```

**Ajout de données utilisateur lors de la création d'un groupe de machines virtuelles identiques**

À l'aide de l'API REST, ajoutez « UserData » à la section « virtualMachineProfile » avec la requête PUT lors de la création du groupe de machines virtuelles identiques.
```json
{
  "location": "West US",
  "sku": {
    "name": "Standard_A1",
    "capacity": 1
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "userData": "VXNlckRhdGE=",
      "osProfile": {
        "computerNamePrefix": "TestVM",
        "adminUsername": "TestUserName",
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "timeZone": "Dateline Standard Time"
        }
      },
      "storageProfile": {
        "osDisk": {
          "createOption": "FromImage",
          "caching": "ReadOnly"
        },
        "imageReference": {
          "publisher": "publisher",
          "offer": "offer",
          "sku": "sku",
          "version": "1.2.3"
        }
      },
      "networkProfile": {"networkInterfaceConfigurations":[{"name":"nicconfig1","properties":{"ipConfigurations":[{"name":"ip1","properties":{"subnet":{"id":"vmssSubnet0"}}}]}}]},
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": true,
          "storageUri": "https://crputest.blob.core.windows.net"
        }
      }
    },
    "provisioningState": 0,
    "overprovision": false,
    "uniqueId": "00000000-0000-0000-0000-000000000000"
  }
}
```


## <a name="retrieving-user-data"></a>Récupération des données utilisateur

Les applications exécutées à l'intérieur de la machine virtuelle peuvent récupérer les données utilisateur à partir du point de terminaison IMDS. Pour plus d'informations, consultez l'[exemple de code IMDS disponible ici.](./linux/instance-metadata-service.md?tabs=linux#get-user-data)

Les clients peuvent récupérer la valeur existante des données utilisateur via l'API REST en utilisant le point de terminaison \$expand=userData (le corps de la requête peut rester vierge).

Machines virtuelles individuelles :

`GET "/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/virtualMachines/{VMName}?$expand=userData"`

Groupe de machines virtuelles identiques :

`GET "/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMSSName}?$expand=userData"`

Machine virtuelle d'un groupe de machines virtuelles identiques :

` GET "/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMSSName}/virtualmachines/{vmss instance id}?$expand=userData" `

## <a name="updating-user-data"></a>Mise à jour des données utilisateur

Avec l'API REST, vous pouvez utiliser une requête PUT ou PATCH normale pour mettre à jour les données utilisateur. Les données utilisateur seront mises à jour sans qu'il soit nécessaire d'arrêter ou de redémarrer la machine virtuelle.

`PUT
"/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/ virtualMachines/{VMName}
`

`PATCH
"/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/ virtualMachines/{VMName}
`

Les propriétés VM.Properties de ces requêtes doivent contenir le champ UserData souhaité, comme suit :

```json
"properties": {
        "hardwareProfile": {
          "vmSize": "Standard_D1_v2"
        },
        "storageProfile": {
          "imageReference": {
            "sku": "2016-Datacenter",
            "publisher": "MicrosoftWindowsServer",
            "version": "latest",
            "offer": "WindowsServer"
          },
          "osDisk": {
            "caching": "ReadWrite",
            "managedDisk": {
              "storageAccountType": "Standard_LRS"
            },
            "name": "vmOSdisk",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
              "properties": {
                "primary": true
              }
            }
          ]
        },
        "osProfile": {
          "adminUsername": "{your-username}",
          "computerName": "{vm-name}",
          "adminPassword": "{your-password}"
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
            "storageUri": "http://{existing-storage-account-name}.blob.core.windows.net",
            "enabled": true
          }
        },
        "userData": "U29tZSBDdXN0b20gRGF0YQ=="
      } 
```
> [!NOTE]
> Si vous transmettez une chaîne vide pour « userData », les données utilisateur seront supprimées.

## <a name="user-data-and-custom-data"></a>Données utilisateur et données personnalisées

Les données personnalisées vont continuer à fonctionner de la même manière qu'aujourd'hui. Notez que vous ne pouvez pas récupérer des données personnalisées à partir d'IMDS. 

## <a name="adding-user-data-to-an-existing-vm"></a>Ajout de données utilisateur à une machine virtuelle existante 

Si vous disposez d'une machine virtuelle ou d'un groupe de machines virtuelles identiques sans données utilisateur, vous pouvez toujours y ajouter des données utilisateur à l'aide des commandes de mise à jour, comme décrit dans la section [« Mise à jour des données utilisateur »](#updating-user-data). Veillez à effectuer la mise à niveau vers la version la plus récente de l'API Azure Resource Manager.

## <a name="next-steps"></a>Étapes suivantes 
 
Essayez [Azure Instance Metadata Service](./linux/instance-metadata-service.md), et apprenez à récupérer les données utilisateur et les métadonnées d'une instance de machine virtuelle à partir de son point de terminaison. 
