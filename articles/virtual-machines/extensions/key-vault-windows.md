---
title: Extension de machine virtuelle Azure Key Vault pour Windows
description: Déployez un agent réalisant une actualisation automatique des secrets Key Vault sur des machines virtuelles à l’aide d’une extension de machine virtuelle.
services: virtual-machines-windows
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 8e014e7a1c564377582e4503218c4129619daa91
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410733"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Extension de machine virtuelle Key Vault pour Windows

L’extension de machine virtuelle Key Vault assure l’actualisation automatique des certificats stockés dans un coffre de clés Azure. Plus précisément, l’extension surveille une liste de certificats examinés qui sont stockés dans des coffres de clés et, lorsqu’une modification est détectée, récupère et installe les certificats correspondants. Ce document présente les plateformes, configurations et options de déploiement qui sont prises en charge pour l’extension de machine virtuelle Key Vault pour Windows. 

### <a name="operating-system"></a>Système d’exploitation

L’extension de machine virtuelle Key Vault prend en charge les versions ci-dessous de Windows :

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

### <a name="supported-certificate-content-types"></a>Types de contenu de certificat pris en charge

- PKCS #12
- PEM

## <a name="extension-schema"></a>Schéma d’extensions

L’extrait JSON suivant illustre le schéma de l’extension de machine virtuelle Key Vault. L’extension ne nécessite pas de paramètres protégés, car tous ses paramètres sont considérés comme des informations publiques. L’extension requiert une liste de certificats surveillés, une fréquence d’interrogation et le magasin de certificats de destination. Plus précisément :  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

> [!NOTE]
> Vos URL de certificats observés doivent être de la forme `https://myVaultName.vault.azure.net/secrets/myCertName`.
> 
> Cela est dû au fait que le chemin `/secrets` retourne le certificat complet, y compris la clé privée, contrairement au chemin `/certificates`. Vous trouverez plus d’informations sur les certificats ici : [Certificats Key Vault](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)

### <a name="property-values"></a>Valeurs de propriétés

| Nom | Valeur/Exemple | Type de données |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | Date |
| publisher | Microsoft.Azure.KeyVault | string |
| type | KeyVaultForWindows | string |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | string |
| certificateStoreName | MY | string |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | LocalMachine | string |
| requiredInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | tableau de chaînes


## <a name="template-deployment"></a>Déploiement de modèle

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Les modèles sont idéaux lorsque vous déployez une ou plusieurs machines virtuelles nécessitant une actualisation de certificats après le déploiement. Vous pouvez déployer l’extension sur des machines virtuelles individuelles ou dans des groupes de machines virtuelles identiques. La configuration et le schéma sont communs aux deux types de modèle. 

La configuration JSON d’une extension de machine virtuelle doit être imbriquée dans le fragment de la ressource de machine virtuelle du modèle, plus précisément dans l’objet `"resources": []` de la machine virtuelle et, dans le cas d’un groupe de machines virtuelles identiques, sous l’objet `"virtualMachineProfile":"extensionProfile":{"extensions" :[]`.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
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
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Pour déployer l’extension sur un groupe de machines virtuelles identiques :

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Déploiement de l’interface de ligne de commande Azure

L’interface de ligne de commande Azure permet de déployer l’extension de machine virtuelle Key Vault sur une machine virtuelle ou un groupe de machines virtuelles identiques existants. 
 
* Pour déployer l’extension sur une machine virtuelle :
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Pour déployer l’extension sur un groupe de machines virtuelles identiques :

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Tenez compte des restrictions et conditions suivantes :
- Restrictions de Key Vault :
  - Doit exister au moment du déploiement 
  - La stratégie d’accès Key Vault est définie pour l’identité de machine virtuelle/VMSS à l’aide de MSI


## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Dépanner

Vous pouvez récupérer des données sur l’état des déploiements des extensions à partir du portail Azure et par le biais d’Azure PowerShell. Pour voir l’état de déploiement des extensions d’une machine virtuelle donnée, exécutez la commande suivante à l’aide d’Azure PowerShell.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

La sortie de l’exécution de l’extension est enregistrée dans le fichier suivant :

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).
