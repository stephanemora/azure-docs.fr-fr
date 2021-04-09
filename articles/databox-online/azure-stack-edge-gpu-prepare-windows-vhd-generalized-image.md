---
title: Créer des images de machine virtuelle à partir d’une image généralisée du disque dur virtuel Windows pour votre appareil Azure Stack Edge Pro GPU
description: Décrit comment créer des images de machine virtuelle à partir d’images généralisées à partir d’un disque dur virtuel Windows ou d’un VHDX. Utilisez cette image généralisée pour créer des images de machine virtuelle à utiliser avec les machines virtuelles déployées sur votre appareil Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: 978099accd57d15c750a27f77b2e220f569a2dd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962278"
---
# <a name="use-generalized-image-from-windows-vhd-to-create-a-vm-image-for-your-azure-stack-edge-pro-device"></a>Utiliser une image généralisée à partir d’un disque dur virtuel Windows pour créer une image de machine virtuelle pour votre appareil Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Pour déployer des machines virtuelles sur votre appareil Azure Stack Edge Pro, vous devez être capable de créer des images de machine virtuelle personnalisées que vous pouvez utiliser pour créer des machines virtuelles. Cet article décrit les étapes nécessaires pour préparer un disque dur virtuel Windows ou un VHDX pour créer une image généralisée. Cette image généralisée est ensuite utilisée pour créer une image de machine virtuelle pour votre appareil Azure Stack Edge Pro. 

## <a name="about-preparing-windows-vhd"></a>À propos de la préparation d’un disque dur virtuel Windows

Un disque dur virtuel Windows ou un VHDX peut être utilisé pour créer une image *généralisée* ou une image *spécialisée*. Le tableau suivant récapitule les principales différences entre les images *généralisées* et *spécialisées*.


|Type d’image  |Généralisée  |Spécialisée  |
|---------|---------|---------|
|Cible     |Déployée sur n’importe quel système         | Ciblée sur un système spécifique        |
|Installation après le démarrage     | Installation requise au premier démarrage de la machine virtuelle.          | Installation non nécessaire. <br> La plateforme active la machine virtuelle.        |
|Configuration     |Nom d’hôte, utilisateur admin et autres paramètres spécifiques à la machine virtuelle obligatoires.         |Entièrement préconfigurée.         |
|Utilisée lors de     |Création de plusieurs nouvelles machines virtuelles à partir de la même image.         |Migration d’une machine spécifique ou restauration d’une machine virtuelle à partir d’une sauvegarde précédente.         |


Cet article décrit les étapes nécessaires au déploiement à partir d’une image généralisée. Pour déployer à partir d’une image spécialisée, consultez [Utiliser un disque dur virtuel Windows spécialisé](azure-stack-edge-placeholder.md) pour votre appareil.

> [!IMPORTANT]
> Cette procédure ne couvre pas les cas où le disque dur virtuel source est configuré avec des configurations et des paramètres personnalisés. Par exemple, des actions supplémentaires peuvent être nécessaires pour généraliser un disque dur virtuel contenant des règles de pare-feu ou des paramètres de proxy personnalisés. Pour plus d’informations sur ces actions supplémentaires, consultez [Préparer un disque dur virtuel Windows à charger sur Azure - Machines virtuelles Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).


## <a name="vm-image-workflow"></a>Workflow d’image de machine virtuelle

Le workflow de niveau supérieur pour préparer un disque dur virtuel Windows en vue de l’utiliser comme une image généralisée comprend les étapes suivantes :

1. Convertissez le disque dur virtuel ou VHDX source en disque dur virtuel de taille fixe.
1. Créez une machine virtuelle dans Hyper-V à l’aide du disque dur virtuel fixe.
1. Connectez-vous à la machine virtuelle Hyper-V.
1. Généralisez le disque dur virtuel à l’aide de l’utilitaire *sysprep*. 
1. Copiez l’image généralisée dans le stockage d’objets BLOB.
1. Utilisez l’image généralisée pour déployer des machines virtuelles sur votre appareil. Pour plus d’informations, consultez comment [déployer une machine virtuelle via le portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) ou [déployer une machine virtuelle via PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).


## <a name="prerequisites"></a>Prérequis

Avant de préparer un disque dur virtuel Windows en vue de l’utiliser comme une image généralisée sur Azure Stack Edge, vérifiez que :

- Vous disposez d’un disque dur virtuel ou d’un VHDX contenant une version prise en charge de Windows. Consultez [Systèmes d’exploitation invités pris en charge]() pour votre Azure Stack Edge Pro. 
- Vous avez accès à un client Windows sur lequel le Gestionnaire Hyper-V est installé. 
- Vous avez accès à un compte de stockage d’objets BLOB Azure pour stocker votre disque dur virtuel une fois qu’il est préparé.

## <a name="prepare-a-generalized-windows-image-from-vhd"></a>Préparer une image Windows généralisée à partir d’un disque dur virtuel

## <a name="convert-to-a-fixed-vhd"></a>Convertir un disque dur virtuel fixe 

Pour votre appareil, vous avez besoin de disques durs virtuels de taille fixe pour créer des images de machine virtuelle. Vous devez convertir votre disque dur virtuel Windows ou VHDX source en disque dur virtuel fixe. Suivez les étapes ci-dessous :

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
Vous utiliserez ce disque dur virtuel fixe pour toutes les étapes suivantes de cet article.


## <a name="create-a-hyper-v-vm-from-fixed-vhd"></a>Créer une machine virtuelle Hyper-V à partir d’un disque dur virtuel fixe

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
     

## <a name="connect-to-the-hyper-v-vm"></a>Connexion à la machine virtuelle Hyper-V

La machine virtuelle apparaît dans la liste des machines virtuelles sur votre système client. 


1. Sélectionnez la machine virtuelle, puis cliquez avec le bouton droit et sélectionnez **Démarrer**. 

    ![Sélectionner une machine virtuelle et la démarrer](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-2.png)

2. La machine virtuelle apparaît comme étant **En cours d’exécution**. Sélectionnez la machine virtuelle, puis cliquez avec le bouton droit et sélectionnez **Se connecter**.

    ![Se connecter à une machine virtuelle](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-4.png)

Une fois connecté à la machine virtuelle, terminez l’Assistant d’Installation de la machine, puis connectez-vous à la machine virtuelle.


## <a name="generalize-the-vhd"></a>Généraliser le disque dur virtuel  

Utilisez l’utilitaire *sysprep* pour généraliser le disque dur virtuel. 

1. Dans la machine virtuelle, ouvrez une invite de commandes.
1. Exécutez la commande suivante pour généraliser le disque dur virtuel. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    Pour plus d’informations, consultez  [Vue d’ensemble de Sysprep (préparation du système)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).
1.  Une fois la commande terminée, la machine virtuelle s’arrête. **Ne redémarrez pas la machine virtuelle**.

## <a name="upload-the-vhd-to-azure-blob-storage"></a>Charger le disque dur virtuel dans le stockage d’objets BLOB

Vous pouvez maintenant utiliser votre disque dur virtuel pour créer une image généralisée sur Azure Stack Edge. 

1. Chargez le disque dur virtuel dans le stockage d’objets BLOB. Consultez les instructions détaillées dans [Charger un disque dur virtuel à l’aide de l’Explorateur Stockage Azure](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md).
1. Une fois le chargement terminé, vous pouvez utiliser l’image chargée pour créer des images de machine virtuelle et des machines virtuelles. 

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->



## <a name="next-steps"></a>Étapes suivantes

En fonction de la nature du déploiement, vous pouvez choisir l’une des procédures suivantes.

- [Déployer une machine virtuelle à partir d’une image généralisée via le portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Déployer une machine virtuelle à partir d’une image généralisée via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
