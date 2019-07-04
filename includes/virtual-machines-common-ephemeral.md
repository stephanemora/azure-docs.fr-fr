---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 24c2bfa4aae94642d3ed66f2cfa6e31ba1e6b19a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457327"
---
Les disques de système d’exploitation éphémères sont créés sur le stockage local de la machine virtuelle (VM) et ne sont pas conservés dans le Stockage Azure à distance. Les disques de système d’exploitation éphémères conviennent particulièrement bien aux charges de travail sans état, car les applications tolèrent les défaillances individuelles des machines virtuelles mais se préoccupent davantage du temps nécessaire aux déploiements à grande échelle ou à la réinitialisation des instances de machine virtuelle individuelles. Ils conviennent également au transfert des applications déployées à l’aide du modèle de déploiement classique vers le modèle de déploiement Resource Manager. Comparé à un disque de système d’exploitation standard, un disque éphémère offre une latence plus faible pour les opérations de lecture/écriture et permet une réinitialisation plus rapide des machines virtuelles. De plus, le disque de système d’exploitation éphémère est gratuit ; autrement dit, aucun coût de stockage ne s’applique à l’utilisation de celui-ci. 
 
Les principales caractéristiques des disques éphémères sont les suivantes : 
- Ils peuvent être utilisés avec des images de la Place de marché et des images personnalisées.
- Vous pouvez déployer des images de machines virtuelles jusqu’à la taille du cache des machines virtuelles.
- Capacité de réinitialisation rapide de leurs machines virtuelles à l’état de démarrage d’origine.  
- Faible latence d’exécution, comparable à celle d’un disque temporaire. 
- Aucun coût pour le disque de système d’exploitation. 
 
 
Différences clés entre les disques de système d’exploitation persistants et éphémères :

|                             | Disque de système d’exploitation persistant                          | Disque de système d’exploitation éphémère                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Limite de taille pour le disque de système d’exploitation      | 2 Tio                                                                                        | Taille du cache pour la taille de la machine virtuelle ou 2TiB (la valeur la plus petite étant retenue) : [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md) et [GS](../articles/virtual-machines/linux/sizes-memory.md)              |
| Tailles de machines virtuelles prises en charge          | Tous                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Prise en charge du type de disque           | Disque de système d’exploitation managé et non managé                                                                | Disque de système d’exploitation managé uniquement                                                               |
| Prise en charge de la région              | Toutes les régions                                                                                  | Toutes les régions                              |
| Persistance des données            | Les données écrites sur le disque de système d’exploitation sont stockées dans le Stockage Azure                                  | Les données écrites sur le disque du système d’exploitation sont stockées sur le stockage local de la machine virtuelle et ne sont pas persistantes dans le Stockage Azure. |
| État arrêté/libéré      | Les machines virtuelles et instances de groupe identique peuvent être arrêtées-libérées et redémarrées à partir de l’état arrêté-libéré | Les machines virtuelles et instances de groupe identique ne peuvent pas être arrêtées-libérées                                  |
| Prise en charge des disques de système d’exploitation spécialisés | OUI                                                                                          | Non                                                                                 |
| Redimensionnement du disque de système d’exploitation              | Pris en charge durant la création de la machine virtuelle et une fois que la machine virtuelle est arrêtée-libérée                                | Prise en charge lors de la création d’une machine virtuelle uniquement                                                  |
| Redimensionnement à une nouvelle taille de machine virtuelle   | Les données du disque de système d’exploitation sont conservées                                                                    | Les données sur le disque du système d’exploitation sont supprimées, le système d’exploitation est redéployé                                      |

## <a name="scale-set-deployment"></a>Déploiement d’un groupe identique  
Le processus de création d’un groupe identique utilisant un disque de système d’exploitation éphémère consiste à ajouter la propriété `diffDiskSettings` au type de ressource `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` dans le modèle. En outre, la stratégie de mise en cache doit être définie sur `ReadOnly` pour le disque de système d’exploitation éphémère. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-deployment"></a>Déploiement de machine virtuelle 
Vous pouvez déployer une machine virtuelle avec un disque de système d’exploitation éphémère à l’aide d’un modèle. Le processus de création d’une machine virtuelle utilisant des disques de système d’exploitation éphémères consiste à ajouter la propriété `diffDiskSettings` au type de ressource Microsoft.Compute/virtualMachines dans le modèle. En outre, la stratégie de mise en cache doit être définie sur `ReadOnly` pour le disque de système d’exploitation éphémère. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Réinitialiser une machine virtuelle à l’aide de REST
Actuellement, la réinitialisation d’une instance de machine virtuelle avec un disque de système d’exploitation éphémère ne peut se faire que par le biais de l’API REST. Pour les groupes identiques, cette opération est déjà disponible via Powershell, l’interface CLI et le Portail.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Q : Quelle est la taille des disques de système d’exploitation locaux ?**

R : Pour la préversion, nous prenons en charge des plateformes et/ou images atteignant la taille du cache de la machine virtuelle, dans lequel toutes les écritures/lecture sur le disque de système d’exploitation seront effectuées à un niveau local sur le même nœud que la machine virtuelle. 

**Q : Le disque de système d’exploitation éphémère peut-il être redimensionné ?**

R : Non, une fois le disque de système d’exploitation éphémère configuré, il ne peut plus être redimensionné. 

**Q : Puis-je attacher un disque managé à une machine virtuelle éphémère ?**

R : Oui, vous pouvez attacher un disque de données managé à une machine virtuelle qui utilise un disque de système d’exploitation éphémère. 

**Q : Toutes les tailles de machine virtuelle sont-elles prises en charge par les disques de système d’exploitation éphémères ?**

R : Non, toutes les tailles de machines virtuelles Stockage Premium sont prises en charge (DS, ES, FS, GS et M), à l’exception des tailles de séries B, N et H.  
 
**Q : Le disque de système d’exploitation éphémère peut-il être appliqué aux machines virtuelles et groupes identiques existants ?**

R : Non, le disque de système d’exploitation éphémère peut uniquement être utilisé au cours de la création de machines virtuelles et de groupes identiques. 

**Q : Peut-on combiner des disques de système d’exploitation normaux et éphémères dans un groupe identique ?**

R : Non, vous ne pouvez pas combiner des instances de disques de système d’exploitation éphémères et persistants dans le même groupe identique. 

**Q : Le disque de système d’exploitation éphémère peut-il être créé à l’aide de Powershell ou de l’interface CLI ?**

R : Oui, vous pouvez créer des machines virtuelles avec un disque de système d’exploitation éphémère à l’aide de REST, des modèles, de PowerShell et de l’interface CLI.

**Q : Quelles fonctionnalités ne sont pas prises en charge avec le disque de système d’exploitation éphémère ?**

R : Les disques éphémères ne prennent pas en charge :
- Capture des images de machine virtuelle
- Captures instantanées de disque 
- Azure Disk Encryption 
- Sauvegarde Azure
- Azure Site Recovery  
- Échange du disque de système d’exploitation 
