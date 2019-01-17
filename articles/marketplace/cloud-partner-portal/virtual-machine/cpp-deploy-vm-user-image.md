---
title: Déployer une machine virtuelle Azure à partir d’un disque dur virtuel utilisateur | Microsoft Docs
description: Explique comment déployer une image de disque dur virtuel utilisateur pour créer une instance de machine virtuelle Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 48be60a7ba5770f8c329cb6323a5caa8fcf7f961
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265054"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Déployer une machine virtuelle Azure à partir d’un disque dur virtuel utilisateur

Cet article explique comment déployer une image de disque dur virtuel généralisée pour créer une nouvelle ressource de machine virtuelle Azure, en utilisant le modèle Azure Resource Manager fourni et un script Azure PowerShell.


## <a name="vhd-deployment-template"></a>Modèle de déploiement de disque dur virtuel

Copiez le modèle Azure Resource Manager de [déploiement de disque dur virtuel](cpp-deploy-json-template.md) dans un fichier local nommé `VHDtoImage.json`.  Modifiez ce fichier et définissez la valeur des paramètres suivants. 

|  **Paramètre**             |   **Description**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Le nom du groupe de ressources Azure existant.  En général, utilisez le même groupe de ressources que celui associé à votre coffre de clés  |
| TemplateFile               | Chemin d’accès complet du fichier `VHDtoImage.json`                                    |
| userStorageAccountName     | Nom du compte de stockage                                                    |
| sNameForPublicIP           | Nom DNS de l’adresse IP publique. Il doit être en minuscules                                  |
| subscriptionId             | Identificateur de l’abonnement Azure                                                  |
| Lieu                   | Sélectionnez un emplacement géographique Azure standard pour le groupe de ressources                       |
| vmName                     | Nom de la machine virtuelle                                                    |
| vaultName                  | Nom du Key Vault.                                                          |
| vaultResourceGroup         | Groupe de ressources du Key Vault.
| certificateUrl             | URL du certificat, y compris la version stockée dans le coffre de clés, par exemple : `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | URL du disque dur virtuel                                                   |
| vmSize                     | Taille de l’instance de machine virtuelle                                           |
| publicIPAddressName        | Nom de l’adresse IP publique                                                  |
| virtualNetworkName         | Nom du réseau virtuel                                                    |
| nicName                    | Nom de la carte d’interface réseau du réseau virtuel                     |
| adminUsername              | Nom d’utilisateur du compte administrateur                                          |
| adminPassword              | Mot de passe de l’administrateur                                                          |
|  |  |


## <a name="powershell-script"></a>Script PowerShell

Copiez et modifiez le script suivant pour indiquer les valeurs des variables `$storageaccount` et `$vhdUrl` variables.  Exécutez-le pour créer une ressource de machine virtuelle Azure à partir de votre disque dur virtuel généralisé existant.

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzureRMResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzureRMResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>Étapes suivantes

Une fois votre machine virtuelle déployée, vous pouvez [certifier votre image de machine virtuelle](./cpp-certify-vm.md).
