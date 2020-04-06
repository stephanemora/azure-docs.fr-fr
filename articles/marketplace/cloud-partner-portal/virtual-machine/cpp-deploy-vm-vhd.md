---
title: Déployer une machine virtuelle à partir des disques durs virtuels pour la Place de marché Microsoft Azure
description: Explique comment inscrire une machine virtuelle à partir d’un disque dur virtuel déployé dans Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: dsindona
ms.openlocfilehash: 5263d24c411ef8de4187c2fd750013374d779f04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277937"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Déployer une machine virtuelle à partir de vos disques durs virtuels

Cette section explique comment déployer une machine virtuelle à partir d’un disque dur virtuel (VHD) déployé dans Azure.  Elle répertorie les outils nécessaires et explique comment les utiliser pour créer une image de machine virtuelle d’utilisateur, puis la déployer vers Azure à l’aide de scripts PowerShell.

Une fois que vous avez chargé les disques durs virtuels (disque dur virtuel de système d’exploitation généralisé, et aucun ou plusieurs disques VHD de données) sur votre compte de stockage Azure, vous pouvez les inscrire en tant qu’image de machine virtuelle d’utilisateur. Vous pouvez tester cette image. Comme votre disque dur virtuel de système d’exploitation est généralisé, vous ne pouvez pas déployer directement la machine virtuelle en indiquant l’URL du disque dur virtuel.

Pour en savoir plus sur les images de machine virtuelle, consultez les billets de blog suivants :

- [VM Image](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM Image PowerShell ’How To’](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/) (Procédure liée aux images de machine virtuelle à l’aide de PowerShell)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Condition préalable : installer les outils requis

Si ce n’est déjà fait, installez Azure PowerShell et l’interface de ligne de commande Azure en appliquant les instructions suivantes :

- [Installation d'Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Installation de l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Étapes du déploiement

Vous utiliserez les étapes suivantes pour créer et déployer une image de machine virtuelle d’utilisateur :

1. Créer l’image de machine virtuelle d’utilisateur, ce qui consiste à capturer et généraliser l’image. 
2. Créer des certificats et les stocker dans un nouvel Azure Key Vault. Un certificat est requis pour établir une connexion sécurisée entre WinRM et la machine virtuelle.  Un modèle Azure Resource Manager et un script Azure PowerShell sont fournis. 
3. Déployez la machine virtuelle à partir d’une image de machine virtuelle d’utilisateur, grâce au modèle et au script fournis.

Une fois votre machine virtuelle déployée, vous pouvez [certifier votre image de machine virtuelle](./cpp-certify-vm.md).

1. Cliquez sur **Nouveau**, recherchez **Déploiement de modèle**, puis sélectionnez **Build your own template in Editor** (Générez votre propre modèle dans l’éditeur).  <br/>
   ![Générer un modèle de déploiement de disque dur virtuel dans le portail Azure](./media/publishvm_021.png)

1. Copiez et collez ce [modèle JSON](./cpp-deploy-json-template.md) dans l’éditeur, puis cliquez sur **Enregistrer**. <br/>
   ![Enregistrer un modèle de déploiement de disque dur virtuel dans le portail Azure](./media/publishvm_022.png)

1. Indiquez les valeurs des paramètres pour les pages de propriétés **Déploiement personnalisé** affichées.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Paramètre**              |   **Description**                                                            |
   |  -------------              |   ---------------                                                            |
   | User Storage Account Name (Nom du compte de stockage de l’utilisateur)   | Nom du compte de stockage où se trouve le disque dur virtuel généralisé.                    |
   | User Storage Container Name (Nom du conteneur de stockage de l’utilisateur) | Nom du conteneur où se trouve le disque dur virtuel généralisé.                          |
   | DNS Name for Public IP (Nom DNS pour adresse IP publique)      | Nom DNS d’adresse IP publique. Vous définissez le nom DNS de la machine virtuelle dans le portail Azure, une fois l’offre déployée.  |
   | Nom d’utilisateur administrateur             | Nom d’utilisateur du compte administrateur pour une nouvelle machine virtuelle                                  |
   | Mot de passe d’administrateur              | Mot de passe du compte administrateur pour une nouvelle machine virtuelle                                  |
   | Type de système d'exploitation                     | Système d’exploitation de machine virtuelle : `Windows`\|`Linux`                                    |
   | Identifiant d’abonnement             | Identificateur de l’abonnement sélectionné                                      |
   | Emplacement                    | Emplacement géographique du déploiement                                        |
   | Taille de la machine virtuelle                     | [Taille de machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), par exemple `Standard_A2` |
   | Nom de l’adresse IP publique      | Nom de votre adresse IP publique                                               |
   | Nom de la machine virtuelle                     | Nom de la nouvelle machine virtuelle                                                           |
   | Nom du réseau virtuel        | Nom du réseau virtuel utilisé par la machine virtuelle                                   |
   | Nom de la carte réseau                    | Nom de la carte d’interface réseau exécutant le réseau virtuel               |
   | URL du disque dur virtuel                     | URL complète du disque dur virtuel du disque de système d’exploitation                                                     |
   |  |  |
            
1. Après avoir indiqué ces valeurs, cliquez sur **Achat**. 

Azure commence le déploiement : une nouvelle machine virtuelle est créée avec le disque dur virtuel non managé spécifié, dans le chemin d’accès au compte de stockage spécifié.  Pour en suivre la progression dans le portail Azure, cliquez sur **Machines virtuelles** sur le côté gauche du portail.  Une fois la machine virtuelle créée, l’état passe de `Starting` à `Running`. 


### <a name="deploy-a-vm-from-powershell"></a>Déployer une machine virtuelle à partir de PowerShell

Pour déployer une machine virtuelle de taille importante à partir de l’image de machine virtuelle généralisée que vous venez de créer, utilisez les cmdlets ci-dessous.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Étapes suivantes

Ensuite, vous allez [créer une image de machine virtuelle d’utilisateur](cpp-create-user-image.md) pour votre solution.

