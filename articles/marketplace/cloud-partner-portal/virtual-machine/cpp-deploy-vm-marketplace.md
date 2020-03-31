---
title: Déployer une machine virtuelle provenant de la Place de marché Azure
description: Explique comment déployer une machine virtuelle à partir d’une machine virtuelle préconfigurée de la Place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 7d5269cf8865faeb65356bc8fd3eea087cb7653c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277971"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Déployer une machine virtuelle de la Place de marché Azure

Cet article explique comment déployer une machine virtuelle préconfigurée à partir d’une Place de marché Azure, en utilisant le script Azure PowerShell fourni.  Ce script expose également les points de terminaison WinRM HTTP et HTTPS sur la machine virtuelle.  Le script requiert que vous ayez déjà un certificat téléchargé dans Azure Key Vault, comme décrit dans [Créer des certificats pour Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Modèle de déploiement de machine virtuelle

Le modèle de déploiement de machine virtuelle Azure (démarrage rapide) est disponible en tant que fichier en ligne [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Il contient les paramètres suivants :

|  **Paramètre**        |   **Description**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | Nom du compte de stockage                       |
| dnsNameForPublicIP    | Nom DNS de l’adresse IP publique. Il doit être en minuscules.    |
| adminUsername         | Nom d’utilisateur de l’administrateur                          |
| adminPassword         | Mot de passe de l’administrateur                          |
| imagePublisher        | Éditeur de l’image                                   |
| imageOffer            | Offre de l’image                                       |
| imageSku              | Référence d’image                                         |
| vmSize                | Taille de la machine virtuelle                                    |
| vmName                | Nom de la machine virtuelle                                    |
| VaultName             | Nom du Key Vault.                             |
| vaultResourceGroup    | Groupe de ressources du Key Vault.                   |
| certificateUrl        | URL du certificat, notamment la version dans Key Vault, par exemple : `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Script de déploiement

Modifiez le script Azure PowerShell suivant et exécutez-le pour déployer la machine virtuelle spécifiée de la Place de marché Azure.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez déployé une machine virtuelle préconfigurée, vous pouvez configurer et exploiter les solutions et services qu’elle contient ou l’utiliser pour un développement ultérieur. 
