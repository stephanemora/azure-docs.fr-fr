---
title: Déployer une machine virtuelle provenant de la Place de marché Azure
description: Explique comment déployer une machine virtuelle à partir d’une machine virtuelle préconfigurée de la Place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 6e39f1c70cd94c14b12e54817941ea9106aacfdd
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273866"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Déployer une machine virtuelle de la Place de marché Azure

> [!IMPORTANT]
> Depuis le 13 avril 2020, nous avons commencé à déplacer la gestion de vos offres de machines virtuelles Azure vers l’Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions fournies dans [Certification d’une image de machine virtuelle Azure](https://aks.ms/CertifyVMimage) pour gérer vos offres migrées.

Cet article explique comment déployer une machine virtuelle préconfigurée à partir d’une Place de marché Azure, en utilisant le script Azure PowerShell fourni.  Ce script expose également les points de terminaison WinRM HTTP et HTTPS sur la machine virtuelle.  Le script requiert que vous ayez déjà un certificat téléchargé dans Azure Key Vault, comme décrit dans [Créer des certificats pour Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Modèle de déploiement de machine virtuelle

Le modèle de déploiement de machine virtuelle Azure (démarrage rapide) est disponible en tant que fichier en ligne [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Il contient les paramètres suivants :

|  **Paramètre**        |   **Description**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName    | Nom du compte de stockage                       |
| dnsNameForPublicIP    | Nom DNS de l’adresse IP publique. Il doit être en minuscules.    |
| adminUsername            | Nom d’utilisateur de l’administrateur                          |
| adminPassword            | Mot de passe de l’administrateur                          |
| imagePublisher        | Éditeur de l’image                                   |
| imageOffer            | Offre de l’image                                       |
| imageSku                | Référence d’image                                         |
| vmSize                | Taille de la machine virtuelle                                    |
| vmName                | Nom de la machine virtuelle                                    |
| VaultName                | Nom du Key Vault.                             |
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
