---
title: Préparer une image généralisée à partir d’une image ISO pour déployer des machines virtuelles sur un appareil GPU Azure Stack Edge Pro
description: Décrit comment créer une image de machine virtuelle Windows généralisée à partir d’une image ISO. Utilisez cette image généralisée pour déployer des machines virtuelles sur votre appareil GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2021
ms.author: alkohli
ms.openlocfilehash: 22f9c86342428d3d011eb787745f74ac6ea026f8
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970320"
---
# <a name="prepare-generalized-image-from-iso-to-deploy-vms-on-azure-stack-edge-pro-gpu"></a>Préparer une image généralisée à partir d’une image ISO pour déployer des machines virtuelles sur un appareil GPU Azure Stack Edge Pro 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Pour déployer des machines virtuelles sur votre appareil GPU Azure Stack Edge Pro, vous devez être capable de créer des images de machines virtuelles personnalisées que vous pouvez utiliser pour créer des machines virtuelles. Cet article décrit comment préparer une image de machine virtuelle Windows à l’aide d’un support d’installation ISO, puis généraliser cette image afin de pouvoir l’utiliser pour déployer plusieurs nouvelles machines virtuelles sur votre appareil GPU Azure Stack Edge Pro.

Pour préparer une image généralisée créée à partir d’un disque dur virtuel (VHD) ou VHDX Windows, consultez [Préparer une image généralisée à partir d’un disque dur virtuel Windows pour déployer des machines virtuelles sur un appareil GPU Azure Stack Edge Pro](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md).

## <a name="about-vm-images"></a>À propos des images de machine virtuelle

Un disque dur virtuel Windows ou un VHDX peut être utilisé pour créer une image *spécialisée* ou une image *généralisée*. Le tableau suivant récapitule les principales différences entre les images *spécialisées* et *généralisées*.

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>Workflow 

Le workflow de haut niveau pour créer un disque dur virtuel Windows généralisé à l’aide d’une image ISO est le suivant :

1. Préparez la machine virtuelle source à l’aide d’une image ISO :
   1. Créez un nouveau disque dur virtuel de taille fixe vide dans le Gestionnaire Hyper-V.
   1. Utilisez ce disque dur virtuel pour créer une nouvelle machine virtuelle.
   1. Montez votre image ISO sur le lecteur de DVD de la nouvelle machine virtuelle.
1. Démarrez la machine virtuelle et installez le système d’exploitation Windows.
1. Généralisez le disque dur virtuel à l’aide de l’utilitaire *sysprep*.
1. Copiez l’image généralisée dans Stockage Blob Azure.

## <a name="prerequisites"></a>Configuration requise

Avant de pouvoir créer un disque dur virtuel Windows généralisé à l’aide d’une image ISO, vérifiez que :

- Vous disposez d’une image ISO pour la version Windows prise en charge que vous souhaitez convertir en disque dur virtuel généralisé. Les images ISO Windows peuvent être téléchargées à partir du [Centre d’évaluation Microsoft](https://www.microsoft.com/en-us/evalcenter/).

- Vous avez accès à un client Windows sur lequel le Gestionnaire Hyper-V est installé.

- Vous avez accès à un compte de stockage blob Azure pour stocker votre disque dur virtuel une fois qu’il est préparé.

## <a name="prepare-source-vm-using-an-iso"></a>Préparer la machine virtuelle source à l’aide d’une image ISO

Lorsque vous utilisez une image ISO pour installer le système d’exploitation sur votre image de machine virtuelle, vous commencez par créer un disque dur virtuel vide de taille fixe dans le Gestionnaire Hyper-V. Vous utilisez ensuite ce disque dur virtuel pour créer une machine virtuelle. Vous attachez ensuite l’image ISO à la machine virtuelle.

#### <a name="create-new-vhd-in-hyper-v-manager"></a>Créer un nouveau disque dur virtuel dans le Gestionnaire Hyper-V

La première étape consiste à créer un nouveau disque dur virtuel de Génération 1 dans le Gestionnaire Hyper-V, qui sera le disque dur virtuel source pour une nouvelle machine virtuelle.

Pour créer le disque dur virtuel, procédez comme suit :

1. Ouvrez le Gestionnaire Hyper-V sur votre système client. Dans le menu **Action**, sélectionnez **nouveau**, puis **Disque dur**.

   ![Sélectionner nouveau, puis Disque dur](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-01.png)

1. Sous **Choisir le format de disque**, sélectionnez **VHD**. Puis sélectionnez **Suivant >** . 

   ![Choisir le format de disque VHD](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-02.png)

2. Sous **Choisir le type de disque**, sélectionnez **Taille fixe**. Puis sélectionnez **Suivant >** .

   ![Choisir le type de disque Taille fixe](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-03.png)

3. Sous **Spécifier le nom et l’emplacement**, entrez un nom et un emplacement pour votre nouveau disque dur virtuel. Puis sélectionnez **Suivant >** .

   ![Entrer le nom et l’emplacement du disque dur virtuel](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-04.png)

4. Sous **Configurer le disque**, sélectionnez **Créer un nouveau disque dur virtuel vide**, puis entrez la taille du disque que vous souhaitez créer (généralement 20 Go ou plus pour Windows Server). Puis sélectionnez **Suivant >** .

   ![Paramètres pour la création d’un nouveau disque dur virtuel vide et spécification de la taille](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-05.png)

5. Sous **Résumé**, passez en revue vos sélections, puis sélectionnez **Terminer** pour créer le nouveau disque dur virtuel. Le processus prend cinq minutes ou plus en fonction de la taille du disque dur virtuel créé.

   ![Résumé des paramètres de disque dur virtuel](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-06.png)

#### <a name="create-hyper-v-vm-from-vhd"></a>Créer une machine virtuelle Hyper-V à partir d’un disque dur virtuel

Vous allez maintenant utiliser le disque dur virtuel que vous venez de créer pour créer un nouvelle machine virtuelle.

Pour créer votre nouvelle machine virtuelle, procédez comme suit :

1. Ouvrez le Gestionnaire Hyper-V sur votre client Windows.

2. Dans le volet **Actions**, sélectionnez **Nouveau**, puis **Machine virtuelle**.

   ![Sélectionnez Nouveau, puis Machine virtuelle dans le menu de droite.](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-07.png)

3. Dans l’Assistant Nouvelle machine virtuelle, spécifiez le nom et l’emplacement de votre machine virtuelle.

   ![Assistant Nouvelle machine virtuelle, Spécifier le nom et l’emplacement](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-08.png)

4. Sous **Spécifier la génération**, sélectionnez **Génération 1**. Puis sélectionnez **Suivant >** .

   ![Assistant Nouvelle machine virtuelle, Choisir la génération de la machine virtuelle à créer](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-09.png)

5. Sous **Attribuer de la mémoire**, attribuez la mémoire souhaitée à la machine virtuelle. Puis sélectionnez **Suivant >** .

   ![Assistant Nouvelle machine virtuelle, Attribuer de la mémoire](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-10.png)

6. Sous **Configurer la mise en réseau**, entrez la configuration de votre réseau. Puis sélectionnez **Suivant >** .

   ![Assistant Nouvelle machine virtuelle, Configurer la mise en réseau](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-11.png)

7. Sous **Connecter un disque dur virtuel**, sélectionnez **Utiliser un disque dur virtuel existant**, puis accédez au disque dur virtuel fixe créé dans la procédure précédente. Puis sélectionnez **Suivant >** . 

   ![Assistant Nouvelle machine virtuelle, Sélectionner un disque dur virtuel existant comme source](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-12.png)

8. Passez en revue le résumé, puis sélectionnez **Terminer** pour créer la machine virtuelle. 

#### <a name="mount-iso-image-on-dvd-drive-of-vm"></a>Monter une image ISO sur le lecteur de DVD d’une machine virtuelle

Après avoir créé la nouvelle machine virtuelle, procédez comme suit pour monter votre image ISO sur le lecteur de DVD de la machine virtuelle :

1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle que vous venez de créer, puis sélectionnez **Paramètres**.
 
   ![Dans le Gestionnaire Hyper-V, ouvrir Paramètres pour votre machine virtuelle](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-13.png)

2. Sous **BIOS**, assurez-vous que **CD** est en haut de la liste **Ordre de démarrage**.

   ![Dans les paramètres du BIOS, le premier élément sous Ordre de démarrage doit être CD](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-14.png)


3. Sous **Lecteur de DVD**, sélectionnez **Fichier image**, puis accédez à votre image ISO.  

   ![Dans les paramètres du lecteur de DVD, sélectionner le fichier image de votre disque dur virtuel](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-15.png)

4. Sélectionnez **OK** pour enregistrer les paramètres de votre machine virtuelle.

## <a name="start-vm-and-complete-os-installation"></a>Démarrer la machine virtuelle et terminer l’installation du système d’exploitation

Pour terminer la création de votre machine virtuelle, vous devez la démarrer, puis effectuer les étapes d’installation du système d’exploitation.

[!INCLUDE [Connect to Hyper-V VM](../../includes/azure-stack-edge-connect-to-hyperv-vm.md)]

> [!NOTE]
> Si vous avez installé le système d'exploitation Windows Server 2019 Standard sur votre machine virtuelle, vous devez définir le paramètre **BIOS** sur **IDE** avant de [généraliser le disque dur virtuel](#generalize-the-vhd). 

## <a name="generalize-the-vhd"></a>Généraliser le disque dur virtuel

Utilisez l’utilitaire *sysprep* pour généraliser le disque dur virtuel.

1. Si vous généralisez une machine virtuelle Windows Server 2019 Standard, avant de généraliser le disque dur virtuel, définissez le premier paramètre **BIOS** de la machine virtuelle sur IDE.

    1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle, puis sélectionnez **Paramètres**.
 
       ![Capture d’écran montrant comment ouvrir Paramètres pour une machine virtuelle sélectionnée dans le Gestionnaire Hyper-V](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-01.png)

     1. Sous **BIOS**, assurez-vous que **IDE** est en haut de la liste **Ordre de démarrage**. Puis sélectionnez **OK** pour enregistrer le paramètre.

        ![Capture d’écran montrant IDE en haut de l’ordre de démarrage dans les paramètres du BIOS pour une machine virtuelle dans le Gestionnaire Hyper-V](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-02.png)

1. Dans la machine virtuelle, ouvrez une invite de commandes.

1. Exécutez la commande suivante pour généraliser le disque dur virtuel. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    
    Pour plus d’informations, consultez [Vue d’ensemble de Sysprep (préparation du système)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

1.  Une fois la commande terminée, la machine virtuelle s’arrête. **Ne redémarrez pas la machine virtuelle**.

<!--[!INCLUDE [Generalize the VHD](../../includes/azure-stack-edge-generalize-vhd.md)]-->

Vous pouvez maintenant utiliser votre disque dur virtuel pour créer une image généralisée à utiliser sur un appareil GPU Azure Stack Edge Pro.

## <a name="upload-generalized-vhd-to-azure-blob-storage"></a>Charger le disque dur virtuel généralisé dans Stockage Blob Azure

[!INCLUDE [Upload VHD to Blob storage](../../includes/azure-stack-edge-upload-vhd-to-blob-storage.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Déployer une machine virtuelle à partir d’une image généralisée via le portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Préparer une image généralisée à partir d’un disque dur virtuel Windows pour déployer des machines virtuelles sur un appareil GPU Azure Stack Edge Pro](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [Préparer une image spécialisée et déployer des machines virtuelles à partir de l’image](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md) 
