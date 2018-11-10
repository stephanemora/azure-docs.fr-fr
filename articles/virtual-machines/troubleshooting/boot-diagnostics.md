---
title: Diagnostics de démarrage pour les machines virtuelles dans Azure | Microsoft Docs
description: Vue d’ensemble des deux fonctionnalités de débogage pour les machines virtuelles dans Azure
services: virtual-machines
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 9341458336e4c95b84590eadbc86073e7dbf09a0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419552"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Comment utiliser les diagnostics de démarrage pour résoudre les problèmes des machines virtuelles dans Azure

La prise en charge de deux fonctionnalités de débogage est désormais disponible dans Azure : Console Output et Screenshot sont pris en charge pour le modèle de déploiement Azure Virtual Machines Resource Manager. 

Lorsque vous importez votre propre image dans Azure ou même lorsque vous démarrez l’une des images de plateforme, de nombreuses raisons peuvent expliquer pourquoi une machine virtuelle passe à l’état non démarrable. Ces fonctionnalités vous permettent de diagnostiquer facilement des échecs de démarrage et de récupérer vos machines virtuelles.

Dans le cas des machines virtuelles Linux, vous pouvez facilement afficher la sortie de votre journal de console à partir du portail. Pour les machines virtuelles Windows et Linux, Azure vous permet également de voir une capture d’écran de la machine virtuelle à partir de l’hyperviseur. Ces deux fonctionnalités sont prises en charge par les machines virtuelles Azure dans toutes les régions. Notez que des captures d’écran ainsi que des sorties peuvent prendre jusqu'à 10 minutes pour apparaître dans votre compte de stockage.

Vous pouvez sélectionner l’option **Diagnostics de démarrage** pour afficher le journal et la capture d’écran.

![Gestionnaire de ressources](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

## <a name="common-boot-errors"></a>Erreurs de démarrage courantes

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Système d’exploitation introuvable.](https://support.microsoft.com/help/4010142)
- [Échec de démarrage ou INACCESSIBLE_BOOT_DEVICE.](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Activer la fonction de diagnostic sur une nouvelle machine virtuelle
1. Lorsque vous créez une machine virtuelle à partir du portail Azure, sélectionnez **Azure Resource Manager** dans la liste déroulante des modèles de déploiement :
 
    ![Gestionnaire de ressources](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. Dans **Paramètres**, activez la fonctionnalité **Diagnostics de démarrage**, puis sélectionnez le compte de stockage dans lequel vous souhaitez placer ces fichiers de diagnostic.
 
    ![Créer une machine virtuelle](./media/virtual-machines-common-boot-diagnostics/create-storage-account.png)

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
                "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
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

    ![Mettre à jour une machine virtuelle existante](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

3. Redémarrez la machine virtuelle pour appliquer les modifications.


