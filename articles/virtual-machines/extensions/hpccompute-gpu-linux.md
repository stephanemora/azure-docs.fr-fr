---
title: Extension du pilote GPU NVIDIA - Machines virtuelles Linux Azure | Microsoft Docs
description: Extension Microsoft Azure pour l’installation des pilotes GPU NVIDIA sur des machines virtuelles de calcul de gamme N sous Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/03/2018
ms.author: danis
ms.openlocfilehash: 1febf81745ed804de59113da4f48376e9564b68f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449190"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Extension du pilote GPU NVIDIA pour Linux

## <a name="overview"></a>Vue d'ensemble

Cette extension installe des pilotes GPU NVIDIA sur des machines virtuelles Linux de gamme N. En fonction de la famille de machine virtuelle, l’extension installe des pilotes CUDA ou GRID. Lorsque vous installez des pilotes NVIDIA à l’aide de cette extension, vous acceptez les termes du contrat de licence utilisateur final NVIDIA. Pendant le processus d’installation, votre machine virtuelle peut redémarrer pour terminer l’installation du pilote.

Une extension est également disponible pour installer les pilotes GPU NVIDIA sur [des machines virtuelles de gamme N Windows](hpccompute-gpu-windows.md).

Les termes du contrat de licence utilisateur final de NVIDIA se trouvent ici : https://go.microsoft.com/fwlink/?linkid=874330

## <a name="prerequisites"></a>Prérequis

### <a name="operating-system"></a>Système d’exploitation

Cette extension prend en charge les systèmes d’exploitation suivants :

| Distribution | Version |
|---|---|
| Linux : Ubuntu | LTS 16.04 |
| Linux : Red Hat Enterprise Linux | 7.3, 7.4 |
| Linux : CentOS | 7.3, 7.4 |

### <a name="internet-connectivity"></a>Connectivité Internet

L’extension Microsoft Azure pour les pilotes GPU NVIDIA nécessite que la machine virtuelle cible soit connectée à Internet et dispose d’un accès.

## <a name="extension-schema"></a>Schéma d’extensions

Le JSON suivant illustre le schéma pour l’extension.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
    }
  }
}
```

### <a name="property-values"></a>Valeurs de propriétés

| NOM | Valeur/Exemple | Type de données |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | chaîne |
| Type | NvidiaGpuDriverLinux | chaîne |
| typeHandlerVersion | 1.0 | int |


## <a name="deployment"></a>Déploiement


### <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager 

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Les modèles sont idéaux lorsque vous déployez une ou plusieurs machines virtuelles nécessitant une configuration post-déploiement.

La configuration JSON pour une extension de machine virtuelle peut être imbriquée à l’intérieur de la ressource de machine virtuelle ou placée à la racine ou au niveau supérieur d’un modèle de Resource Manager JSON. Le positionnement de la configuration JSON affecte la valeur du nom de la ressource et son type. Pour plus d’informations, consultez [Définition du nom et du type des ressources enfants](../../azure-resource-manager/resource-manager-template-child-resource.md). 

L’exemple suivant suppose que l’extension est imbriquée dans la ressource de machine virtuelle. Lors de l’imbrication de la ressource d’extension, le JSON est placé dans l’objet `"resources": []` de la machine virtuelle.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzureRmVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.0 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.0 `
  --settings '{ `
  }'
```

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Résolution des problèmes

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure, ainsi qu’à l’aide d’Azure PowerShell et de l’interface de ligne de commande Azure. Pour afficher l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

La sortie de l’exécution de l’extension est enregistrée dans le fichier suivant :

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Codes de sortie

| Code de sortie | Signification | Action possible |
| :---: | --- | --- |
| 0 | L’opération a réussi |
| 1 | Utilisation incorrecte de l’extension. | Contactez le support en fournissant le journal de sortie de l’exécution. |
| 10 | Les services d’intégration Linux pour Hyper-V et Azure ne sont pas disponibles ou installés. | Vérifiez la sortie de lspci. |
| 11 | GPU NVIDIA introuvable sur cette taille de machine virtuelle. | Utilisez une [taille de machine virtuelle et un système d’exploitation pris en charge](../linux/n-series-driver-setup.md). |
| 14 | Échec de l’opération | |
| 21 | Échec de la mise à jour sur Ubuntu | Vérifiez la sortie de « sudo apt-get update ». |


### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/community/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les extensions de machine virtuelle, voir [Extensions et fonctionnalités de machine virtuelle pour Linux](features-linux.md).

Pour plus d’informations sur les machines virtuelles de gamme N, voir [Tailles de machine virtuelle à GPU optimisé](../linux/sizes-gpu.md).