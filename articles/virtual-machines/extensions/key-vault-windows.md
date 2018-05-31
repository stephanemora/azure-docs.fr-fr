---
title: Extension de machine virtuelle Key Vault pour Windows | Microsoft Docs
description: Déployez un agent réalisant une actualisation automatique des secrets Key Vault sur des machines virtuelles à l’aide d’une extension de machine virtuelle.
services: virtual-machines-windows
documentationcenter: ''
author: dragav
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: dragosav
ms.openlocfilehash: 19e177f086ea9fa817a36e67ace77aed39ee89b5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944751"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Extension de machine virtuelle Key Vault pour Windows

## <a name="overview"></a>Vue d'ensemble

L’extension de machine virtuelle Key Vault fournit une actualisation automatique des secrets stockés dans un coffre de clés Azure. Plus précisément, l’extension surveille une liste de certificats examinés qui sont stockés dans des coffres de clés et, lorsqu’une modification est détectée, récupère et installe les certificats correspondants. Actuellement, l’extension de machine virtuelle Key Vault est publiée et prise en charge par Microsoft sur les machines virtuelles Windows. Ce document présente les plateformes, configurations et options de déploiement qui sont prises en charge pour l’extension de machine virtuelle Key Vault pour Windows. 

## <a name="prerequisites"></a>Prérequis


L’extension de machine virtuelle Key Vault est tributaire de l’extension de machine virtuelle Managed Service Identity (MSI) pour son authentification auprès du service Key Vault. Pour plus d’informations, consultez la documentation sur l’extension de machine virtuelle MSI.

### <a name="operating-system"></a>Système d’exploitation

L’extension de machine virtuelle Key Vault prend actuellement en charge toutes les versions de Windows.

| Distribution | Version |
|---|---|
| Windows | Principal |

### <a name="internet-connectivity"></a>Connectivité Internet

La machine virtuelle cible doit être connectée à Internet pour pouvoir utiliser l’extension de machine virtuelle Key Vault pour Windows. 

## <a name="extension-schema"></a>Schéma d’extensions

L’extrait JSON suivant illustre le schéma de l’extension de machine virtuelle Key Vault. L’extension ne nécessite aucun paramètre protégé, car tous ses paramètres sont considérés comme des informations sans impact sur la sécurité. Par contre, la liste des secrets surveillés, la fréquence d’interrogation et le magasin de certificats de destination lui sont indispensables. Plus précisément :  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

### <a name="property-values"></a>Valeurs de propriétés

| NOM | Valeur/Exemple | Type de données |
| ---- | ---- | ---- |
| apiVersion | 2016-10-01 | date |
| publisher | Microsoft.Azure.KeyVault.Edp | chaîne |
| Type | KeyVaultForWindows | chaîne |
| typeHandlerVersion | 0.0 | int |
| pollingIntervalInS | 3600 | int |
| certificateStoreName | MY | chaîne |
| certificateStoreLocation  | LOCAL_MACHINE | chaîne |
| observedCertificates  | ["https://myvault.vault.azure.net:443/secrets/mycertificate"] | tableau de chaînes


## <a name="template-deployment"></a>Déploiement de modèle

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Les modèles sont idéaux lorsque vous déployez une ou plusieurs machines virtuelles nécessitant une actualisation de certificats après le déploiement. Vous pouvez déployer l’extension sur des machines virtuelles individuelles ou dans des groupes de machines virtuelles identiques. La configuration et le schéma sont communs aux deux types de modèle. 

La configuration JSON d’une extension de machine virtuelle doit être imbriquée dans le fragment de la ressource de machine virtuelle du modèle, plus précisément dans l’objet `"resources": []` de la machine virtuelle.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

## <a name="azure-powershell-deployment"></a>Déploiement d’Azure PowerShell

Azure PowerShell vous permet de déployer l’extension de machine virtuelle Key Vault sur une machine virtuelle ou un groupe de machines virtuelles identiques existants. 

* Pour déployer l’extension sur une machine virtuelle :
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMName> + '/KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Set-AzureRmVmExtension -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Pour déployer l’extension sur un groupe de machines virtuelles identiques, par exemple dans le cadre d’un cluster Service Fabric :

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMSSName> + '_KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Add-AzureRmVmssExtension -VirtualMachineScaleSet <VMSS> -Name $extName -Publisher $extPublisher -Type $extType -Setting $settings
    
    ```

Tenez compte des restrictions et conditions suivantes :
- Le déploiement doit être effectué dans la région Sud du centre des États-Unis
- Restrictions de Key Vault :
    - Doit exister au moment du déploiement 
    - Doit se trouver dans la même région et le même groupe de ressources que le déploiement
    - Doit être activé pour le déploiement et le déploiement de modèles

## <a name="azure-cli-deployment"></a>Déploiement de l’interface de ligne de commande Azure

Des exemples de déploiement de l’extension de machine virtuelle Key Vault dans Azure CLI seront bientôt disponibles. 

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Résolution des problèmes

Vous pouvez récupérer des données sur l’état des déploiements des extensions à partir du portail Azure et par le biais d’Azure PowerShell. Pour voir l’état de déploiement des extensions d’une machine virtuelle donnée, exécutez la commande suivante à l’aide d’Azure PowerShell.

```powershell
Get-AzureRMVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

La sortie de l’exécution de l’extension est enregistrée dans le fichier suivant :

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.Edp.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).
