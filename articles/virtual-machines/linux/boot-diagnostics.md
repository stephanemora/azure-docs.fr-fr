---
title: Diagnostics de démarrage pour les machines virtuelles Linux dans Azure | Microsoft Docs
description: Vue d’ensemble des deux fonctionnalités de débogage pour les machines virtuelles Linux dans Azure
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: delhan
ms.openlocfilehash: 38cc806cb77af60cda10f3aeac2e5ed13b445b8c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941851"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-linux-virtual-machines-in-azure"></a>Comment utiliser les diagnostics de démarrage pour résoudre les problèmes des machines virtuelles Linux dans Azure

La prise en charge de deux fonctionnalités de débogage est désormais disponible dans Azure : les supports de Console Output et Screenshot pour le modèle de déploiement Azure Virtual Machines Resource Manager. 

Lorsque vous importez votre propre image dans Azure, ou même lorsque vous démarrez une des images de plateforme, il peut y avoir de nombreuses raisons pour lesquelles une Machine Virtuelle passe en état non démarrable. Ces fonctionnalités vous permettent de facilement diagnostiquer des échecs de démarrage et de récupérer vos Machines virtuelles.

Dans le cas des Machines Virtuelles Linux, vous pouvez facilement afficher la sortie de votre journal de console à partir du Portail :

![Portail Azure](./media/boot-diagnostics/screenshot1.png)
 
Toutefois, pour les Machines Virtuelles Windows et Linux, Azure vous permet également de voir une capture d’écran de la machine virtuelle à partir de l’hyperviseur :

![Error](./media/boot-diagnostics/screenshot2.png)

Ces deux fonctionnalités sont prises en charge par les Machines Virtuelles Azure dans toutes les régions. Notez que des captures d’écran ainsi que des sorties peuvent prendre jusqu'à 10 minutes pour apparaître dans votre compte de stockage.

## <a name="common-boot-errors"></a>Erreurs de démarrage courantes

- [Problèmes de système de fichiers](https://support.microsoft.com/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 
- [Problèmes de noyau](https://support.microsoft.com/help/4091524/how-recovery-azure-linux-vm-from-kernel-related-boot-related-issues/) 
- [Erreurs FSTAB](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Activer la fonction de diagnostic sur une nouvelle machine virtuelle
1. Lorsque vous créez une Machine virtuelle à partir de la préversion du Portail, sélectionnez **Azure Resource Manager** dans la liste déroulante du modèle de déploiement :
 
    ![Gestionnaire de ressources](./media/boot-diagnostics/screenshot3.jpg)

2. Dans **Paramètres**, activez la fonctionnalité **Diagnostics de démarrage**, puis sélectionnez le compte de stockage dans lequel vous souhaitez placer ces fichiers de diagnostic.
 
    ![Créer une machine virtuelle](./media/boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > La fonctionnalité Diagnostics de démarrage ne prend pas en charge le compte de stockage Premium. Si vous utilisez le compte de stockage Premium pour la fonctionnalité Diagnostics de démarrage, vous pouvez recevoir l’erreur StorageAccountTypeNotSupported lorsque vous démarrez la machine virtuelle. 
    >
    > 

3. Si vous déployez à partir d’un modèle Azure Resource Manager, accédez à votre ressource de machine virtuelle et ajoutez la section de profil des diagnostics. Pensez à utiliser l’en-tête de version d’API « 2015-06-15 ».

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. Le profil de diagnostics vous permet de sélectionner le compte de stockage dans lequel vous souhaitez placer ces journaux.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

Pour déployer un exemple de machine virtuelle alors que les diagnostics de démarrage sont activés, consultez notre référentiel ici.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Activer la fonctionnalité Diagnostics de démarrage sur la machine virtuelle existante 

Pour activer la fonctionnalité Diagnostics de démarrage sur une machine virtuelle existante, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis sélectionnez la machine virtuelle.
2. Dans **Support et dépannage**, sélectionnez **Diagnostics de démarrage** > **Paramètres**, définissez l’état sur **Activé**, puis sélectionnez un compte de stockage. 
4. Assurez-vous que l’option Diagnostics de démarrage est sélectionnée, puis enregistrez la modification.

    ![Mettre à jour une machine virtuelle existante](./media/boot-diagnostics/enable-for-existing-vm.png)

3. Redémarrez la machine virtuelle pour appliquer les modifications.

## <a name="next-steps"></a>Étapes suivantes

Si vous voyez une erreur indiquant l’impossibilité d’obtenir le contenu du journal lorsque vous utilisez les diagnostics de démarrage sur la machine virtuelle, voir l’article sur l’[échec d’obtention du contenu de l’erreur journalisée dans les diagnostics de démarrage sur la machine virtuelle](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur).