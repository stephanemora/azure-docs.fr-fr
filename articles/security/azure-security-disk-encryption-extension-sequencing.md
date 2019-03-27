---
title: Azure Disk Encryption et Azure machines virtuelles identiques séquencement d’extensions
description: Cet article fournit des instructions sur l’activation Microsoft Azure Disk Encryption pour les machines virtuelles IaaS Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/21/2019
ms.openlocfilehash: e98e501806971f3cf1bec29960ad15ef9c0024fc
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498143"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Utilisez Azure Disk Encryption avec mise à l’échelle de machine virtuelle définie séquencement d’extensions

Extensions telles que le chiffrement de disque Azure peuvent être ajoutées à un groupe identique de machines virtuelles définies dans un ordre spécifié. Pour ce faire, utilisez [séquencement d’extensions](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). 

En règle générale, le chiffrement doit être appliqué à un disque :

- Une fois les extensions ou des scripts personnalisés qui préparent les disques ou volumes.
- Avant d’extensions ou des scripts personnalisés qui accèdent ou consomment les données sur les disques chiffrés ou les volumes.

Dans les deux cas, le `provisionAfterExtensions` propriété désigne quelle extension doit être ajoutée plus loin dans la séquence.

## <a name="sample-azure-templates"></a>Exemples modèles Azure

Si vous souhaitez avoir Azure Disk Encryption est appliquée après une autre extension, placer le `provisionAfterExtensions` propriété dans le bloc d’extension AzureDiskEncryption. 

Voici un exemple d’utilisation de « CustomScriptExtension », un script Powershell qui initialise et met en forme un disque de Windows, suivi de « AzureDiskEncryption » :

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

Si vous souhaitez avoir Azure Disk Encryption est appliqué avant une autre extension, placer le `provisionAfterExtensions` propriété dans le bloc de l’extension à suivre.

Voici un exemple d’utilisation de « AzureDiskEncryption » suivie de « VMDiagnosticsSettings » une extension qui permet de surveiller les fonctionnalités et de diagnostics sur une machine virtuelle Azure basée sur Windows :


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

Pour d’autres modèles détaillées, consultez :
* Appliquer l’extension Azure Disk Encryption après un script d’interpréteur de commandes personnalisé qui formate le disque (Linux) : [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)
* Appliquer l’extension Azure Disk Encryption après un script Powershell personnalisé qui initialise et formate le disque (Windows) : [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-ADE-after-customscript.json)
* Appliquer l’extension Azure Disk Encryption avant un script Powershell personnalisé qui initialise et formate le disque (Windows) : [deploy-extseq-windows-CustomScript-after-ADE.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-CustomScript-after-ADE.json)

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur le séquencement d’extensions : [Séquence d’approvisionnement de l’extension dans les machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md).
- En savoir plus sur la `provisionAfterExtensions` propriété : [Référence du modèle Microsoft.Compute/virtualMachineScaleSets les extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
