---
title: Extension du pilote GPU NVIDIA - Machines virtuelles Windows Azure
description: Extension Microsoft Azure pour l’installation des pilotes GPU NVIDIA sur des machines virtuelles de calcul de gamme N sous Windows.
services: virtual-machines
documentationcenter: ''
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/14/2021
ms.author: amverma
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8e164fad73fd04ea2f9093e99b454f43aa441088
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038305"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Extension du pilote GPU NVIDIA pour Windows

## <a name="overview"></a>Vue d’ensemble

Cette extension installe des pilotes GPU NVIDIA sur des machines virtuelles Windows de gamme N. En fonction de la famille de machine virtuelle, l’extension installe des pilotes CUDA ou GRID. Lorsque vous installez des pilotes NVIDIA à l’aide de cette extension, vous acceptez les termes du [contrat de licence utilisateur final NVIDIA](https://go.microsoft.com/fwlink/?linkid=874330). Pendant le processus d’installation, la machine virtuelle peut redémarrer pour terminer l’installation du pilote.

Des instructions sur l’installation manuelle des pilotes et les versions actuellement prises en charge sont disponibles. Pour plus d’informations, consultez [Configuration du pilote GPU NVIDIA de la série N pour Windows](../windows/n-series-driver-setup.md).
Une extension est également disponible pour installer les pilotes GPU NVIDIA sur [des machines virtuelles de gamme N Linux](hpccompute-gpu-linux.md).

## <a name="prerequisites"></a>Prérequis

### <a name="operating-system"></a>Système d’exploitation

Cette extension prend en charge les systèmes d’exploitation suivants :

| Distribution | Version |
|---|---|
| Windows 10 | Core |
| Windows Server 2019 | Core |
| Windows Server 2016 | Core |
| Windows Server 2012 R2 | Core |

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
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Propriétés

| Nom | Valeur/Exemple | Type de données |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | Date |
| publisher | Microsoft.HpcCompute | string |
| type | NvidiaGpuDriverWindows | string |
| typeHandlerVersion | 1.4 | int |


## <a name="deployment"></a>Déploiement

### <a name="azure-portal"></a>Portail Azure

Vous pouvez déployer des extensions de machine virtuelle Azure NVIDIA dans le Portail Azure.

1. Dans votre navigateur, accédez au [portail Azure](https://portal.azure.com).

2. Connectez-vous à la machine sur laquelle vous souhaitez installer le pilote.

3. Sélectionnez **Extensions** dans le menu de gauche.

    :::image type="content" source="./media/nvidia-ext-portal/extensions-menu.png" alt-text="Capture d’écran montrant comment sélectionner des extensions dans le menu du portail Azure.":::

4. Sélectionnez **Ajouter**.

    :::image type="content" source="./media/nvidia-ext-portal/add-extension.png" alt-text="Capture d’écran montrant comment ajouter une extension de machine virtuelle pour la machine virtuelle sélectionnée.":::

5. Faites défiler et sélectionnez l’**Extension du pilote GPU NVIDIA pour Linux**, puis **Suivant**.

    :::image type="content" source="./media/nvidia-ext-portal/select-nvidia-extension.png" alt-text="Capture d’écran montrant la sélection du pilote NVIDIA G P U.":::

6. Sélectionnez **Vérifier + créer**, puis attendez quelques minutes pour que le pilote soit déployé.

    :::image type="content" source="./media/nvidia-ext-portal/create-nvidia-extension.png" alt-text="Capture d’écran montrant la sélection du bouton Vérifier + créer.":::
  
7. Vérifiez que l’extension est ajoutée à la liste des extensions installées.

    :::image type="content" source="./media/nvidia-ext-portal/verify-extension.png" alt-text="Capture d’écran montrant la nouvelle extension dans la liste des extensions pour la machine virtuelle.":::

### <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager 

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Les modèles sont idéaux lorsque vous déployez une ou plusieurs machines virtuelles nécessitant une configuration post-déploiement.

La configuration JSON pour une extension de machine virtuelle peut être imbriquée à l’intérieur de la ressource de machine virtuelle ou placée à la racine ou au niveau supérieur d’un modèle de Resource Manager JSON. Le positionnement de la configuration JSON affecte la valeur du nom de la ressource et son type. Pour plus d’informations, consultez [Définition du nom et du type des ressources enfants](../../azure-resource-manager/templates/child-resource-name-type.md). 

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
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.4 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.4 \
  --settings '{ \
  }'
```

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Dépanner

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure, ainsi qu’à l’aide d’Azure PowerShell et de l’interface de ligne de commande Azure. Pour afficher l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

La sortie de l’exécution de l’extension est enregistrée dans le répertoire suivant :

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\
```

### <a name="error-codes"></a>Codes d’erreur

| Code d'erreur | Signification | Action possible |
| :---: | --- | --- |
| 0 | L’opération a réussi |
| 1 | L’opération a réussi. Redémarrage requis. |
| 100 | L’opération n’est pas prise en charge ou n’a pas pu être effectuée. | Causes possibles : La version de PowerShell n’est pas prise en charge, la machine virtuelle n’est pas de série N, le téléchargement des données a échoué. Vérifiez les fichiers journaux pour déterminer la cause de l’erreur. |
| 240, 840 | Délai d’expiration de l’opération. | Réessayez l’opération. |
| -1 | Une exception s’est produite. | Vérifiez les fichiers journaux pour déterminer la cause de l’exception. |
| -5x | Opération interrompue en raison d’un redémarrage en attente. | Redémarrez la machine virtuelle. L’installation se poursuivra après le redémarrage. La désinstallation doit être appelée manuellement. |


### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/community/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les extensions de machine virtuelle, consultez la page [Extensions et fonctionnalités de machine virtuelle pour Windows](features-windows.md).

Pour plus d’informations sur les machines virtuelles de gamme N, consultez la page [Tailles de machine virtuelle à GPU optimisé](../sizes-gpu.md).
