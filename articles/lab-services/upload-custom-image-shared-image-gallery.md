---
title: Azure Lab Services - Guide pratique pour apporter une image Windows personnalisée depuis votre environnement lab physique
description: Explique comment apporter une image Windows personnalisée depuis votre environnement lab physique.
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: 8c33f9579da2a0b7e3a58bd00b99d392fce07d28
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532690"
---
# <a name="bring-a-windows-custom-image-from-a-physical-lab-environment"></a>Apporter une image personnalisée Windows depuis un environnement lab physique

Les étapes de cet article montrent comment importer une image personnalisée qui démarre à partir de votre environnement lab physique.  Avec cette approche, vous créez un disque dur virtuel à partir de votre environnement physique, puis vous importez le disque dur virtuel dans une galerie d’images partagées afin qu’il puisse être utilisé dans Lab Services.  Avant d’utiliser cette approche pour créer une image personnalisée, lisez l’article [Approches recommandées pour créer des images personnalisées](approaches-for-custom-image-creation.md) afin de déterminer la meilleure approche à votre scénario.

## <a name="prerequisites"></a>Configuration requise

Pour effectuer les étapes indiquées dans cet article, vous devez disposer de l’autorisation de créer un [disque managé Azure](../virtual-machines/managed-disks-overview.md) dans l’abonnement Azure de votre établissement scolaire.

Lors d’un déplacement d’images, d’un environnement lab physique vers Lab Services, vous devez restructurer chaque image afin qu’elle inclue uniquement les logiciels nécessaires à un cours de labo.  Pour plus d’informations, lisez le billet de blog sur le [Déplacement d’un labo physique vers Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

## <a name="prepare-a-custom-image-using-hyper-v-manager"></a>Préparer une image personnalisée avec le Gestionnaire Hyper-V

Les étapes suivantes montrent comment créer une image Windows à partir d’une machine virtuelle Windows Hyper-V avec le Gestionnaire Windows Hyper-V :

1. Commencez par une machine virtuelle Hyper-V dans votre environnement de laboratoire physique créé à partir de votre image.  Pour plus d’informations, lisez l’article [Guide pratique pour créer une machine virtuelle dans Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).
    -   La machine virtuelle doit être créée en tant que machine virtuelle de **Génération 1**.
    -   Utilisez l’option de configuration réseau **Commutateur par défaut** pour permettre à la machine virtuelle de se connecter à Internet.
    -   Le disque virtuel de la machine virtuelle doit être un disque dur virtuel de taille fixe.  La taille de disque ne doit *pas* dépasser 128 Go. Lorsque vous créez la machine virtuelle, entrez la taille du disque, comme indiqué dans l’image ci-dessous.
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Connecter un disque dur virtuel":::

    Les images dont la taille de disque dépasse 128 Go ne sont *pas* prises en charge par Lab Services. 
   
1. Connectez-vous à la machine virtuelle Hyper-V et [préparez-la pour Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) en suivant ces étapes :
    1. [Définissez les configurations Windows pour Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure).
    1. [Vérifiez les services Windows nécessaires pour garantir la connectivité des machines virtuelles](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services).
    1. [Mettez à jour les paramètres de Registre du Bureau à distance](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
    1. [Configurez les règles du Pare-feu Windows](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules).
    1. [Installez les mises à jour Windows](../virtual-machines/windows/prepare-for-upload-vhd-image.md).
    1. [Installer l’agent de machine virtuelle Azure et la configuration supplémentaire indiquée ici](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations) 
        
    Vous pouvez charger des images spécialisées ou généralisées sur une galerie d’images partagées et les utiliser pour créer des labos.  Les étapes ci-dessus créent une image spécialisée. Si vous devez créer plutôt une image généralisée, il vous faudra également [exécuter SysPrep](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep).  

    Créez une image spécialisée si vous souhaitez conserver les profils utilisateur et des informations propres à la machine.  Pour plus d’informations sur les différences existant entre les images généralisées et les images spécialisées, consultez [Images généralisées et spécialisées](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).

1. Étant donné que **Hyper-V** crée un fichier **VHDX** par défaut, vous devez le convertir en fichier VHD.
    1. Accédez à **Gestionnaire Hyper-V** -> **Action** -> **Modifier le disque**.
    1. Ensuite, choisissez de **Convertir** le disque d’un fichier VHDX en disque dur virtuel.  
     - Si vous augmentez la taille du disque, veillez à ne *pas* dépasser 128 Go.        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Choisir une action":::   

    Pour plus d’informations, lisez l’article qui explique comment [convertir le disque virtuel en disque dur virtuel de taille fixe](../virtual-machines/windows/prepare-for-upload-vhd-image.md#convert-the-virtual-disk-to-a-fixed-size-vhd).

Pour vous aider à redimensionner le disque dur virtuel et à le convertir en un disque VHDX, vous pouvez également utiliser les applets de commande PowerShell suivantes :
- [Resize-VHD](/powershell/module/hyper-v/resize-vhd?view=windowsserver2019-ps)
- [Convert-VHD](/powershell/module/hyper-v/convert-vhd?view=windowsserver2019-ps)

## <a name="upload-the-custom-image-to-a-shared-image-gallery"></a>Chargez l’image personnalisée sur une galerie d’images partagées

1. Chargez le disque dur virtuel sur Azure pour créer un disque managé.
    1. Vous pouvez utiliser l’Explorateur Stockage ou AzCopy à partir de la ligne de commande, comme démontré dans l’article [Charger un disque dur virtuel sur Azure ou copier un disque managé dans une autre région](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md).        

    1. Après avoir chargé le disque dur virtuel, vous devriez désormais disposer d’un disque managé visible dans le portail Azure. 
    
    Si votre ordinateur se met en veille ou se verrouille, le processus de chargement peut s’interrompre et échouer.  En outre, assurez-vous que, une fois AzCopy terminée, vous révoquez l’accès SAS au disque.  Dans le cas contraire, quand vous tentez de créer une image à partir du disque, vous obtenez une erreur : **L’opération « Créer l’image » n’est pas prise en charge avec le disque « nom de votre disque » dans l’état « Chargement actif ». Code d’erreur : OperationNotAllowed**
    
    L’onglet du portail Azure **Taille+Performances** pour le disque managé vous permet de modifier la taille de votre disque. Comme mentionné précédemment, la taille *ne doit pas* être supérieure à 128 Go.

1. Dans une galerie d’images partagées, créez une définition et une version d’image :
    1. [Créez une définition d’image](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition).  
     - Choisissez **Gen 1** pour la **génération de la machine virtuelle**.
     - Indiquez si vous créez une image **spécialisée** ou **généralisée** pour l’**État du système d’exploitation**.
     
    Pour plus d’informations sur les valeurs que vous pouvez spécifier pour une définition d’image, consultez [Définitions d’image](../virtual-machines/shared-image-galleries.md#image-definitions). 
    
    Vous pouvez également choisir d’utiliser une définition d’image existante, et créer une nouvelle version de votre image personnalisée.
    
1. [Créez une version d’image](../virtual-machines/windows/shared-images-portal.md#create-an-image-version).
    - La propriété **Numéro de version** utilise le format suivant : *VersionMajeure.VersionMineure.Patch*.   Lorsque vous utilisez Lab Services pour créer un labo et choisir une image personnalisée, la version la plus récente de l’image est automatiquement utilisée.  La version la plus récente est choisie en fonction de la valeur la plus élevée de VersionMajeure, puis de VersionMineure, et enfin de Patch.
    - Pour la **Source**, choisissez **Disques et/ou instantanés** dans la liste déroulante.
    - Pour la propriété **Disque de système d’exploitation**, choisissez le disque que vous avez créé lors des étapes précédentes.
    
    Pour plus d’informations sur les valeurs que vous pouvez spécifier dans une définition d’image, consultez [Versions d’image](../virtual-machines/shared-image-galleries.md#image-versions). 

## <a name="create-a-lab"></a>Création d’un laboratoire

1. [Créez le labo](tutorial-setup-classroom-lab.md) dans Lab Services et sélectionnez l’image personnalisée à partir de la galerie d’images partagées.

    Si vous avez augmenté la taille du disque *après* l’installation du système d’exploitation sur la machine virtuelle Hyper-V d’origine, vous pourrez également étendre le lecteur C dans Windows pour utiliser l’espace de disque non alloué :      
    - Connectez-vous au modèle de machine virtuelle du labo et suivez les étapes similaires à celles présentées dans [Étendre un volume de base](/windows-server/storage/disk-management/extend-a-basic-volume).

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de Shared Image Gallery](../virtual-machines/shared-image-galleries.md)
* [Attacher ou détacher une galerie d’images partagées](how-to-attach-detach-shared-image-gallery.md)
* [Utiliser une galerie d’images partagées](how-to-use-shared-image-gallery.md)