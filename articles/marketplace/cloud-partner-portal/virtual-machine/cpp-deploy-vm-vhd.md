---
title: Déployer une machine virtuelle à partir des disques durs virtuels pour la Place de marché Microsoft Azure | Microsoft Docs
description: Explique comment inscrire une machine virtuelle à partir d’un disque dur virtuel déployé dans Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2771549af29b3e717d117afb42de6db03fbee226
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639128"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Déployer une machine virtuelle à partir de vos disques durs virtuels

Cet article explique comment inscrire une machine virtuelle à partir d’un disque dur virtuel (VHD) déployé dans Azure.  Il répertorie les outils nécessaires et indique comment les utiliser pour créer une image de machine virtuelle d’utilisateur, puis la déployer vers Azure à l’aide du [portail Microsoft Azure](https://ms.portal.azure.com/) ou de scripts PowerShell. 

Une fois que vous avez chargé les disques durs virtuels (disque dur virtuel de système d’exploitation généralisé, et aucun ou plusieurs disques VHD de données) sur votre compte de stockage Azure, vous pouvez les inscrire en tant qu’image de machine virtuelle d’utilisateur. Vous pouvez tester cette image. Comme votre disque dur virtuel de système d’exploitation est généralisé, vous ne pouvez pas déployer directement la machine virtuelle en indiquant l’URL du disque dur virtuel.

Pour en savoir plus sur les images de machine virtuelle, consultez les billets de blog suivants :

- [VM Image](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM Image PowerShell ’How To’](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/) (Procédure liée aux images de machine virtuelle à l’aide de PowerShell)


## <a name="set-up-the-necessary-tools"></a>Configurer les outils nécessaires

Si ce n’est déjà fait, installez Azure PowerShell et l’interface de ligne de commande Azure en appliquant les instructions suivantes :

<!-- TD: Change the following URLs (in this entire topic) to relative paths.-->

- [Installer Azure PowerShell sur Windows avec PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)
- [Installation d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="create-a-user-vm-image"></a>Créer une image de machine virtuelle d’utilisateur

Vous allez créer une image non managée à partir de votre disque dur virtuel généralisé.

#### <a name="capture-the-vm-image"></a>Capturer l’image de machine virtuelle

Suivez les instructions de l’article suivant sur la capture de la machine virtuelle qui correspond à votre méthode d’accès :

-  PowerShell : [Comment créer une image de machine virtuelle non managée à partir d’une machine virtuelle Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI : [Créer une image d’une machine virtuelle ou d’un disque dur virtuel](../../../virtual-machines/linux/capture-image.md)
-  API : [Virtual Machines - Capture](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture) (Machines virtuelles : capturer)

### <a name="generalize-the-vm-image"></a>Généraliser l’image de machine virtuelle

Étant donné que vous avez généré l’image utilisateur à partir d’un disque dur virtuel généralisé précédemment, elle doit également être généralisée.  Là encore, sélectionnez l’article suivant qui correspond à votre méthode d’accès.  (Il se peut que vous ayez déjà généralisé votre disque lorsque vous l’avez capturé.)

-  PowerShell : [Généraliser la machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI : [Étape 2 : Créer une image de machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API : [Virtual Machines - Generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize) (Machines virtuelles : généraliser)


## <a name="deploy-a-vm-from-a-user-vm-image"></a>Déployer une machine virtuelle à partir d’une image de machine virtuelle d’utilisateur

Vous allez déployer une machine virtuelle à partir d’une image de machine virtuelle d’utilisateur à l’aide de PowerShell ou du portail Azure.

<!-- TD: Recapture following hilited images and replace with red-box. -->

### <a name="deploy-a-vm-from-azure-portal"></a>Déployer une machine virtuelle à partir du portail Azure

Pour déployer votre machine virtuelle d’utilisateur à partir du portail Azure, procédez comme suit :

1.  Connectez-vous au [portail Azure](https://portal.azure.com).

2.  Cliquez sur **Nouveau**, recherchez **Déploiement de modèle**, puis sélectionnez **Build your own template in Editor** (Générez votre propre modèle dans l’éditeur).  <br/>
  ![Générer un modèle de déploiement de disque dur virtuel dans le portail Azure](./media/publishvm_021.png)

3. Copiez et collez ce [modèle JSON](./cpp-deploy-json-template.md) dans l’éditeur, puis cliquez sur **Enregistrer**. <br/>
  ![Enregistrer un modèle de déploiement de disque dur virtuel dans le portail Azure](./media/publishvm_022.png)

4. Indiquez les valeurs des paramètres pour les pages de propriétés **Déploiement personnalisé** affichées.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Paramètre**              |   **Description**                                                            |
   |  -------------              |   ---------------                                                            |
   | User Storage Account Name (Nom du compte de stockage de l’utilisateur)   | Nom du compte de stockage où se trouve le disque dur virtuel généralisé.                    |
   | User Storage Container Name (Nom du conteneur de stockage de l’utilisateur) | Nom du conteneur où se trouve le disque dur virtuel généralisé.                          |
   | DNS Name for Public IP (Nom DNS pour adresse IP publique)      | Nom DNS d’adresse IP publique                                                           |
   | Nom d’utilisateur administrateur             | Nom d’utilisateur du compte administrateur pour une nouvelle machine virtuelle                                  |
   | Mot de passe d’administrateur              | Mot de passe du compte administrateur pour une nouvelle machine virtuelle                                  |
   | Type de système d’exploitation                     | Système d’exploitation de machine virtuelle : `Windows` \| `Linux`                                    |
   | Identifiant d’abonnement             | Identificateur de l’abonnement sélectionné                                      |
   | Lieu                    | Emplacement géographique du déploiement                                        |
   | Taille de la machine virtuelle                     | [Taille de machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), par exemple `Standard_A2` |
   | Nom de l’adresse IP publique      | Nom de votre adresse IP publique                                               |
   | Nom de la machine virtuelle                     | Nom de la nouvelle machine virtuelle                                                           |
   | Nom du réseau virtuel        | Nom du réseau virtuel utilisé par la machine virtuelle                                   |
   | Nom de la carte réseau                    | Nom de la carte d’interface réseau exécutant le réseau virtuel               |
   | URL du disque dur virtuel                     | URL complète du disque dur virtuel du disque de système d’exploitation                                                     |
   |  |  |
            
5. Après avoir indiqué ces valeurs, cliquez sur **Achat**. 

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

<!-- TD: The following is a marketplace-publishing article and may be out-of-date.  TD: update and move topic.
For help with issues, see [Troubleshooting common issues encountered during VHD creation](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting) for additional assistance.
-->

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez déployé la machine virtuelle, vous pouvez [la configurer](./cpp-configure-vm.md).
