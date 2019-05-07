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
ms.openlocfilehash: d7737f73ee4eb9ae9dc8c4845020b7543a5b3495
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159164"
---
Les disques du système d’exploitation éphémères sont créés sur le stockage local de la Machine virtuelle (VM) et pas conservés dans le stockage Azure à distance. Disques du système d’exploitation éphémères fonctionnent bien pour les charges de travail de sans état, où les applications tolèrent les défaillances isolées de machine virtuelle, mais sont plus préoccupé par le temps que nécessaire pour les déploiements à grande échelle ou de temps pour réimage les instances de machine virtuelle individuelles. Il est également idéale pour les applications déployées à l’aide du modèle de déploiement classique, à déplacer vers le modèle de déploiement Resource Manager. Avec un disque du système d’exploitation éphémère, vous serez observer une latence plus faible en lecture/écriture pour le disque du système d’exploitation et créer une nouvelle image de machine virtuelle plus rapidement. En outre, disque de système d’exploitation éphémère est gratuit, vous encourez sans coût de stockage pour le disque du système d’exploitation. 
 
Les fonctionnalités clées de disques éphémères sont : 
- Ils peuvent être utilisés avec les images Marketplace et des images personnalisées.
- Vous pouvez déployer des Images de machine virtuelle jusqu'à la taille du Cache de la machine virtuelle.
- Possibilité de réinitialiser ou de réinitialiser leurs machines virtuelles à l’état d’origine de démarrage rapide.  
- Exécution une latence plus faible similaire à un disque temporaire. 
- Aucun coût pour le disque du système d’exploitation. 
 
 
Différences clés entre les disques du système d’exploitation persistants et éphémères :

|                             | Disque de système d’exploitation persistant                          | Disque de système d’exploitation éphémère                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Limite de taille de disque de système d’exploitation      | 2 Tio                                                                                        | Taille de la taille de machine virtuelle ou 2TiB, mettre en cache plus petite étant retenue - [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), et [GS](../articles/virtual-machines/linux/sizes-memory.md)              |
| Prise en charge des tailles de machine virtuelle          | Tous                                                                                          | DSv1, DSv2, DSv3, Esv2, Fs, FsV2, GS, M                                               |
| Prise en charge du type de disque           | Disque de système d’exploitation managée et non managées                                                                | Disque du système d’exploitation géré uniquement                                                               |
| Prise en charge de la région              | Toutes les régions                                                                                  | Toutes les régions                              |
| Persistance des données            | Les données de disque du système d’exploitation écrites sur le disque du système d’exploitation sont stockées dans le stockage Azure                                  | Données écrites sur le disque du système d’exploitation sont stockées sur le stockage local de la machine virtuelle et ne sont pas persistant dans le stockage Azure. |
| État arrêtée-désallouée      | Machines virtuelles et instances de groupe identique peuvent être arrêtée-désallouée et redémarrés à partir de l’état arrêtée-désallouée | Machines virtuelles et instances de groupe identique ne peut pas être arrêtée-désallouée                                  |
| Prise en charge des disques de système d’exploitation spécialisé | Oui                                                                                          | Non                                                                                  |
| Redimensionnement du disque du système d’exploitation              | Prise en charge lors de la création de machine virtuelle et une fois que la machine virtuelle est arrêtée-désallouée                                | Prise en charge lors de la création de machine virtuelle uniquement                                                  |
| Redimensionnement à une nouvelle taille de machine virtuelle   | Les données de disque du système d’exploitation sont conservées.                                                                    | Données sur le disque du système d’exploitation sont supprimées, le système d’exploitation est redéployé                                      |

## <a name="register-for-the-preview"></a>S’inscrire pour la préversion


S’inscrire automatiquement pour la version préliminaire de disques du système d’exploitation éphémères à l’aide de la dernière version de l’interface CLI Azure ou Azure PowerShell.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzRmProviderFeature –FeatureName LocalDiffDiskPreview
```

Pour vérifier si vous êtes inscrit pour la version préliminaire :

```azurepowershell-interactive
Get-AzRmProviderFeature –FeatureName LocalDiffDiskPreview
```

### <a name="cli"></a>Interface de ligne de commande

```azurecli-interactive
az provider register --namespace Microsoft.Compute
az feature register --namespace Microsoft.Compute --name LocalDiffDiskPreview
```

Pour vérifier si vous êtes inscrit pour la version préliminaire :
 
```azurecli-interactive
az provider show –namespace ‘Microsoft.Compute’
```


## <a name="scale-set-deployment"></a>Ensemble d’échelle de déploiement  
Le processus de création d’un groupe identique qui utilise un disque de système d’exploitation éphémère consiste à ajouter le `diffDiskSettings` propriété à la `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` type de ressource dans le modèle. En outre, la stratégie mise en cache doit être définie sur `ReadOnly` pour le disque du système d’exploitation éphémère. 


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

## <a name="vm-deployment"></a>Déploiement de machines virtuelles 
Vous pouvez déployer une machine virtuelle avec un disque de système d’exploitation éphémère à l’aide d’un modèle. Le processus de création d’une machine virtuelle qui utilise des disques du système d’exploitation éphémères consiste à ajouter le `diffDiskSettings` propriété au type de ressource Microsoft.Compute/virtualmachines dans le modèle. En outre, la stratégie mise en cache doit être définie sur `ReadOnly` pour le disque du système d’exploitation éphémère. 

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


## <a name="reimage-a-vm-using-rest"></a>Réinitialisez une machine virtuelle à l’aide de REST
Actuellement, la seule méthode pour réimage une instance de Machine virtuelle avec disque du système d’exploitation éphémère est via l’API REST. Pour les jeux de mise à l’échelle, cette opération est déjà disponible via Powershell, CLI et le portail.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Q : Quelle est la taille des disques du système d’exploitation local ?**

R : Pour la version préliminaire, nous prendrons en charge plateforme et/ou images jusqu'à la taille du cache de machine virtuelle, où toutes les écritures en lecture sur le disque du système d’exploitation à un niveau locales sur le même nœud que la Machine virtuelle. 

**Q : Le disque du système d’exploitation éphémère peut être redimensionné ?**

R : Non, une fois le disque du système d’exploitation éphémère est configuré, le disque du système d’exploitation ne peut pas être redimensionné. 

**Q : Puis-je attacher un disque géré à une machine virtuelle éphémère ?**

R : Oui, vous pouvez attacher un disque de données géré à une machine virtuelle qui utilise un disque de système d’exploitation éphémère. 

**Q : Toutes les tailles de machine virtuelle est pris en charge pour les disques du système d’exploitation éphémères ?**

R : Non, toutes les tailles de machines virtuelles stockage Premium sont pris en charge (DS, ES, FS, GS et M), à l’exception de la série B, série N et les tailles de série H.  
 
**Q : Le disque du système d’exploitation éphémère est applicable aux machines virtuelles existantes et des groupes identiques ?**

R : Non, ephemeral disque de système d’exploitation peut uniquement être utilisé au cours de la machine virtuelle et mettre à l’échelle de la création du jeu. 

**Q : Peut combiner des disques du système d’exploitation éphémères et normales dans un groupe identique ?**

R : Non, ne peut pas avoir une combinaison d’instances de disque du système d’exploitation éphémères et persistantes dans le même jeu de mise à l’échelle. 

**Q : Le disque du système d’exploitation éphémère peut être créé à l’aide de Powershell ou CLI ?**

R : Oui, vous pouvez créer des machines virtuelles avec disque du système d’exploitation éphémère à l’aide de REST, des modèles, PowerShell et CLI.

**Q : Quelles fonctionnalités ne sont pas pris en charge avec le disque du système d’exploitation éphémère ?**

R : Disques éphémères ne gèrent pas :
- Capture d’images de machine virtuelle
- Captures instantanées de disque 
- Azure Disk Encryption 
- Sauvegarde Azure
- Azure Site Recovery  
- Échange de disque de système d’exploitation 
