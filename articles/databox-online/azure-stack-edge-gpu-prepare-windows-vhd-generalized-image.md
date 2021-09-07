---
title: Préparer une image généralisée à partir d’un disque dur virtuel Windows pour déployer des machines virtuelles sur un appareil GPU Azure Stack Edge Pro
description: Décrit comment créer une image de machine virtuelle généralisée à partir d’un disque dur virtuel Windows (de format VHD ou VHDX). Utilisez cette image de machine virtuelle généralisée pour déployer des machines virtuelles sur votre appareil GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/18/2021
ms.author: alkohli
ms.openlocfilehash: e60f7e7195ef81b3bf17a85c5662824df945ef91
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968398"
---
# <a name="prepare-generalized-image-from-windows-vhd-to-deploy-vms-on-azure-stack-edge-pro-gpu"></a>Préparer une image généralisée à partir d’un disque dur virtuel Windows pour déployer des machines virtuelles sur un appareil GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Pour déployer des machines virtuelles sur votre appareil GPU Azure Stack Edge Pro, vous devez être capable de créer des images de machine virtuelle personnalisées utilisables pour créer des machines virtuelles. Cet article explique comment préparer une image généralisée à partir d’un disque dur virtuel Windows (de format VHD ou VHDX), image que vous pourrez ensuite utiliser pour déployer des machines virtuelles sur votre appareil GPU Azure Stack Edge Pro.

Pour préparer une image de machine virtuelle généralisée en utilisant une image ISO, consultez [Préparer une image généralisée à partir d’une image ISO pour déployer des machines virtuelles sur un appareil GPU Azure Stack Edge Pro](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md).

## <a name="about-vm-images"></a>À propos des images de machine virtuelle

Un disque dur virtuel Windows ou un VHDX peut être utilisé pour créer une image *spécialisée* ou une image *généralisée*. Le tableau suivant récapitule les principales différences entre les images *spécialisées* et *généralisées*.

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>Workflow

Le workflow général de préparation d’un disque dur virtuel Windows à utiliser comme image généralisée, à partir du disque VHD ou VHDX d’une machine virtuelle existante, comporte les étapes suivantes :

1. Préparez la machine virtuelle source à partir d’un disque dur virtuel Windows :
   1. Convertissez le disque VHD ou VHDX source en disque dur virtuel de taille fixe.
   1. Utilisez ce disque dur virtuel pour créer une autre machine virtuelle.<!--Can this procedure be generalized and moved to an include file?-->
1. Démarrez la machine virtuelle et installez le système d’exploitation Windows.
1. Généralisez le disque dur virtuel à l’aide de l’utilitaire *sysprep*.
1. Copiez l’image généralisée dans le stockage d’objets BLOB.

## <a name="prerequisites"></a>Configuration requise

Avant de préparer un disque dur virtuel Windows en vue de l’utiliser comme image généralisée sur un appareil GPU Azure Stack Edge Pro, vérifiez que :

- Vous disposez d’un disque dur virtuel ou d’un VHDX contenant une version prise en charge de Windows. 
- Vous avez accès à un client Windows sur lequel le Gestionnaire Hyper-V est installé. 
- Vous avez accès à un compte de stockage d’objets BLOB Azure pour stocker votre disque dur virtuel une fois qu’il est préparé.

## <a name="prepare-source-vm-from-windows-vhd"></a>Préparer la machine virtuelle source à partir d’un disque dur virtuel Windows

Si votre machine virtuelle source est un disque dur virtuel Windows de format VHD ou VHDX, vous devez commencer par convertir ce disque dur virtuel Windows en disque dur virtuel de taille fixe. Vous utiliserez ce disque dur virtuel de taille fixe pour créer une machine virtuelle.

> [!IMPORTANT]
> Ces procédures ne couvrent pas les cas où le disque dur virtuel source est configuré avec des configurations et des paramètres personnalisés. Par exemple, des actions supplémentaires peuvent être nécessaires pour généraliser un disque dur virtuel contenant des règles de pare-feu ou des paramètres de proxy personnalisés. Pour plus d’informations sur ces actions supplémentaires, consultez [Préparer un disque dur virtuel Windows à charger sur Azure - Machines virtuelles Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

#### <a name="convert-source-vhd-to-a-fixed-size-vhd"></a>Convertir le disque virtuel source en disque dur virtuel de taille fixe

Pour votre appareil, vous avez besoin de disques durs virtuels de taille fixe pour créer des images de machine virtuelle. Vous devez convertir votre disque dur virtuel Windows ou VHDX source en disque dur virtuel fixe. 

Suivez les étapes ci-dessous :

1. Ouvrez le Gestionnaire Hyper-V sur votre système client. Accédez à **Modifier le disque**.

    ![Ouvrir le Gestionnaire Hyper-V](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. Dans la page **Avant de commencer**, sélectionnez **Suivant>** .

1. Dans la page **Rechercher un disque dur virtuel**, accédez à l’emplacement du disque dur virtuel Windows ou VHDX source que vous souhaitez convertir. Sélectionnez **Suivant>** .

    ![Page Rechercher un disque dur virtuel](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. Dans la page **Choisir une action**, sélectionnez **Convertir**, puis sélectionnez **Suivant>** .

    ![Page Choisir une action](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. Dans la page **Choisir le format de disque**, sélectionnez le format **VHD**, puis sélectionnez **Suivant>** .

   ![Page Choisir le format de disque](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)

1. Dans la page **Choisir le type de disque**, sélectionnez **Taille fixe**, puis sélectionnez **Suivant>** .

   ![Page Choisir le type de disque](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)

1. Dans la page **Configurer le disque**, accédez à l’emplacement et spécifiez un nom pour le disque dur virtuel de taille fixe. Sélectionnez **Suivant>** .

   ![Page Configurer le disque](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)

1. Vérifiez le résumé et sélectionnez **Terminer**. La conversion du disque dur virtuel ou du VHDX prend quelques minutes. La durée de la conversion dépend de la taille du disque source.

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
Vous utiliserez ce disque dur virtuel de taille fixe dans toutes les prochaines étapes décrites dans cet article.

#### <a name="create-hyper-v-vm-from-the-fixed-size-vhd"></a>Créer une machine virtuelle Hyper-V à partir du disque dur virtuel de taille fixe

1. Dans le **Gestionnaire Hyper-V**, cliquez avec le bouton droit dans le volet d’étendue sur le nœud de votre système pour ouvrir le menu contextuel, puis sélectionnez **Nouveau** > **Machine virtuelle**.

    ![Sélectionner une nouvelle machine virtuelle dans le volet d’étendue](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. Dans la page **Avant de commencer** de l’Assistant Nouvelle machine virtuelle, sélectionnez **Suivant**.

1. Dans la page **Spécifier le nom et l’emplacement**, indiquez le **Nom** et l’**Emplacement** de votre machine virtuelle. Sélectionnez **Suivant**.

    ![Spécifier le nom et l’emplacement de votre machine virtuelle](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. Dans la page **Spécifier la génération**, choisissez **Génération 1** pour le type d’image d’appareil .vhd, puis sélectionnez **Suivant**.    

    ![Spécifier la génération](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. Attribuez les configurations de mémoire et réseau souhaitées.

1. Dans la page **Connecter un disque dur virtuel**, choisissez **Utiliser un disque dur virtuel existant**, spécifiez l’emplacement du disque dur virtuel fixe Windows que vous avez créé précédemment, puis sélectionnez **Suivant**.

    ![Page Connecter un disque dur virtuel](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. Passez en revue le **Sommaire**, puis sélectionnez **Terminer** pour créer la machine virtuelle.

La création de la machine virtuelle prend plusieurs minutes.

La machine virtuelle apparaît dans la liste des machines virtuelles sur votre système client.

## <a name="start-vm-and-install-operating-system"></a>Démarrer la machine virtuelle et installer le système d’exploitation

Pour terminer la création de votre machine virtuelle, vous devez la démarrer, puis effectuez les étapes d’installation du système d’exploitation.

[!INCLUDE [Connect to Hyper-V VM](../../includes/azure-stack-edge-connect-to-hyperv-vm.md)]

Une fois que vous êtes connecté à la machine virtuelle, terminez l’Assistant Installation de la machine, puis ouvrez une session sur la machine virtuelle.<!--It's not clear what they are doing here. Where does the Machine setup wizard come in?-->

## <a name="generalize-the-vhd"></a>Généraliser le disque dur virtuel

Utilisez l’utilitaire *sysprep* pour généraliser le disque dur virtuel. 

[!INCLUDE [Generalize the VHD](../../includes/azure-stack-edge-generalize-vhd.md)]

Vous pouvez maintenant utiliser votre disque dur virtuel pour créer une image généralisée à utiliser sur un appareil GPU Azure Stack Edge Pro.

## <a name="upload-generalized-vhd-to-azure-blob-storage"></a>Charger le disque dur virtuel généralisé dans le Stockage Blob Azure

[!INCLUDE [Upload VHD to Blob storage](../../includes/azure-stack-edge-upload-vhd-to-blob-storage.md)]

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->

## <a name="next-steps"></a>Étapes suivantes

En fonction de la nature du déploiement, vous pouvez choisir l’une des procédures suivantes.

- [Déployer une machine virtuelle à partir d’une image généralisée via le portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Préparer une image généralisée à partir d’une image ISO pour déployer des machines virtuelles sur un appareil GPU Azure Stack Edge Pro](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
- [Préparer une image spécialisée et déployer des machines virtuelles à partir de l’image](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md) 
