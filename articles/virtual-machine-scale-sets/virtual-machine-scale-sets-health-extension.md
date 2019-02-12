---
title: Utiliser l’extension Intégrité de l’application avec des groupes de machines virtuelles identiques Azure | Microsoft Docs
description: Découvrez comment utiliser l’extension Intégrité de l’application pour surveiller l’intégrité de vos applications déployées sur des groupes de machines virtuelles identiques.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: 34f1b023b2ea2451f3308666d156278e92afb4aa
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565970"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Utilisation de l’extension Intégrité de l’application avec des groupes de machines virtuelles identiques
La surveillance de l’intégrité de votre application est un signal important pour la gestion et la mise à niveau votre déploiement. Les groupes de machines virtuelles identiques prennent en charge les [mises à niveau propagées](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model), notamment les [mises à niveau automatiques d’image de système d’exploitation](virtual-machine-scale-sets-automatic-upgrade.md), qui reposent sur l’analyse du fonctionnement des instances individuelles pour mettre à niveau votre déploiement.

Cet article décrit comment utiliser l’extension Intégrité de l’application pour analyser le fonctionnement de vos applications déployées sur des groupes de machines virtuelles identiques.

## <a name="prerequisites"></a>Prérequis
Cet article suppose de connaître :
-   Les [extensions](../virtual-machines/extensions/overview.md) de machine virtuelle Azure
-   La [modification](virtual-machine-scale-sets-upgrade-scale-set.md) des groupes de machines virtuelles identiques

## <a name="when-to-use-the-application-health-extension"></a>Quand utiliser l’extension Intégrité de l’application
L’extension Intégrité de l’application est déployée à l’intérieur d’une instance de groupe de machines virtuelles identiques et rend compte de l’intégrité des machines virtuelles à partir de l’instance de groupe identique. Vous pouvez configurer l’extension pour sonder un point de terminaison d’application et mettre à jour l’état de l’application sur cette instance. Cet état de l’instance est vérifié par Azure pour déterminer si une instance est éligible pour des opérations de mise à niveau.

Étant donné que l'extension rend compte de l'intégrité à partir d'une machine virtuelle, l'extension peut être utilisée dans les situations où les sondes externes telles que les sondes Intégrité de l'application (qui utilisent des [sondes](../load-balancer/load-balancer-custom-probe-overview.md) Azure Load Balancer personnalisées) ne peuvent pas être utilisées.

## <a name="extension-schema"></a>Schéma d’extensions

Le JSON suivant montre le schéma pour l’extension Intégrité de l’application. L’extension nécessite au minimum une requête « tcp » ou « http » avec respectivement un port ou un chemin d’accès à la demande associés.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>Valeurs de propriétés

| Nom | Valeur/Exemple | Type de données
| ---- | ---- | ---- | ----
| apiVersion | `2018-10-01` | date |
| publisher | `Microsoft.ManagedServices` | chaîne |
| Type | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Windows) | chaîne |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Paramètres

| Nom | Valeur/Exemple | Type de données
| ---- | ---- | ----
| protocol | `http` ou `tcp` | chaîne |
| port | Facultatif si le protocole est `http`, obligatoire si le protocole est `tcp` | int |
| requestPath | Obligatoire si le protocole est `http`, non autorisé si le protocole est `tcp` | chaîne |

## <a name="deploy-the-application-health-extension"></a>Déployer l’extension Intégrité de l’application
Il existe plusieurs façons de déployer l’extension Intégrité de l’application sur vos groupes identiques, comme indiqué dans les exemples ci-dessous.

### <a name="rest-api"></a>API REST

L'exemple suivant ajoute l'extension Intégrité de l'application (avec le nom myHealthExtension) à extensionProfile dans le modèle d'un groupe identique basé sur Windows.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
Utilisez `PATCH` pour modifier une extension déjà déployée.

### <a name="azure-powershell"></a>Azure PowerShell

Utilisez la cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) pour ajouter l'extension Intégrité de l'application à la définition du modèle de groupe identique.

L'exemple suivant ajoute l'extension Intégrité de l'application à `extensionProfile` dans le modèle d'un groupe identique basé sur Windows. L'exemple utilise le nouveau module Az PowerShell.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Azure CLI 2.0

Utilisez la commande [az vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set) pour ajouter l’extension Intégrité de l’application à la définition du modèle de groupe identique.

L’exemple suivant ajoute l’extension Intégrité de l’application au modèle de groupe identique d’un groupe identique basé sur Windows.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Résolution des problèmes
La sortie de l’exécution de l’extension est journalisées dans des fichiers figurant dans les répertoires suivants :

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

Les journaux capturent également régulièrement l’état d’intégrité de l’application.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [déployer votre application](virtual-machine-scale-sets-deploy-app.md) sur des groupes de machines virtuelles identiques.
