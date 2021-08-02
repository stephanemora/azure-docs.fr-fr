---
title: Azure Lab Services – Intégrer une image personnalisée Windows à Shared Image Gallery
description: Explique comment intégrer une image personnalisée Windows à Shared Image Gallery.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 87ae45819a19390b1776cc81e45b85b4ba1e2aee
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111967788"
---
# <a name="bring-a-windows-custom-image-to-shared-image-gallery"></a>Intégrer une image personnalisée Windows à Shared Image Gallery

Vous pouvez utiliser Shared Image Gallery pour apporter vos propres images personnalisées Windows et les utiliser lors de la création de labos dans Azure Lab Services.  Cet article explique comment apporter une image personnalisée depuis :

* Votre [environnement lab physique](upload-custom-image-shared-image-gallery.md#bring-a-windows-custom-image-from-a-physical-lab-environment).
* Une [machine virtuelle Azure](upload-custom-image-shared-image-gallery.md#bring-a-windows-custom-image-from-an-azure-virtual-machine).

Cette tâche est généralement assurée par le service informatique d’un établissement scolaire.

## <a name="bring-a-windows-custom-image-from-a-physical-lab-environment"></a>Apporter une image personnalisée Windows depuis un environnement lab physique

Les étapes de cette section montrent comment importer une image personnalisée qui démarre à partir de votre environnement lab physique.  Avec cette approche, vous créez un disque dur virtuel à partir de votre environnement physique, puis vous importez le disque dur virtuel dans Shared Image Gallery afin qu’il puisse être utilisé dans Lab Services.  Les principaux avantages de cette approche sont les suivants :

* Vous avez la possibilité de créer des images [généralisées ou spécialisées](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) à utiliser dans vos labos.  En revanche, si vous utilisez le [modèle de machine virtuelle d’un labo pour exporter une image](how-to-use-shared-image-gallery.md), l’image est toujours spécialisée.
* Vous pouvez charger des images créées à l’aide d’autres outils, tels que [Microsoft Endpoint Configuration Manager](/mem/configmgr/core/understand/introduction), pour ne pas avoir à configurer manuellement une image à l’aide d’un modèle de machine virtuelle de labo.

Pour les étapes de cette section, vous devez disposer de l’autorisation de créer un [disque managé](../virtual-machines/managed-disks-overview.md) dans l’abonnement Azure de votre établissement scolaire.

> [!IMPORTANT]
> Lors d’un déplacement d’images, d’un environnement lab physique vers Lab Services, vous devez restructurer chaque image afin qu’elle inclue uniquement les logiciels nécessaires à un cours de labo.  Pour plus d’informations, lisez le billet de blog sur le [Déplacement d’un labo physique vers Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

De nombreuses options existent pour créer un disque dur virtuel à partir d’un environnement de laboratoire physique. Les étapes ci-dessous montrent comment créer un disque dur virtuel à partir d’une machine virtuelle Windows Hyper-V à l’aide du Gestionnaire Hyper-V.

1. Commencez par une machine virtuelle Hyper-V dans votre environnement de laboratoire physique créé à partir de votre image.  Pour plus d’informations, lisez l’article [Guide pratique pour créer une machine virtuelle dans Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).
    1. La machine virtuelle doit être créée en tant que machine virtuelle de **Génération 1**.
    1. Le disque virtuel de la machine virtuelle doit être un disque dur virtuel de taille fixe.  La taille de disque ne doit *pas* dépasser 128 Go. Lorsque vous créez la machine virtuelle, entrez la taille du disque, comme indiqué dans l’image ci-dessous.
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Connecter un disque dur virtuel":::

    > [!IMPORTANT]
    > Les images dont la taille de disque dépasse 128 Go ne sont *pas* prises en charge par Lab Services. 
   
1. Connectez-vous à la machine virtuelle Hyper-V et [préparez-la pour Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) en suivant ces étapes :
    1. [Définissez les configurations Windows pour Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure).
    1. [Vérifiez les services Windows nécessaires pour garantir la connectivité des machines virtuelles](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services).
    1. [Mettez à jour les paramètres de Registre du Bureau à distance](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
    1. [Configurez les règles du Pare-feu Windows](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules).
    1. [Installez les mises à jour Windows](../virtual-machines/windows/prepare-for-upload-vhd-image.md).
    1. [Installer l’agent de machine virtuelle Azure et la configuration supplémentaire indiquée ici](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations) 
        
    Vous pouvez charger des images spécialisées ou généralisées sur Shared Image Gallery, et les utiliser pour créer des labos.  Les étapes ci-dessus créent une image spécialisée. Si vous devez créer plutôt une image généralisée, il vous faudra également [exécuter SysPrep](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep).  

    > [!IMPORTANT]
    > Créez une image spécialisée si vous souhaitez conserver les profils utilisateur et des informations propres à la machine.  Pour plus d’informations sur les différences existant entre les images généralisées et les images spécialisées, consultez [Images généralisées et spécialisées](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).

1. Étant donné que **Hyper-V** crée un fichier **VHDX** par défaut, vous devez le convertir en fichier VHD.
    1. Accédez à **Gestionnaire Hyper-V** -> **Action** -> **Modifier le disque**.
    1. Ensuite, choisissez de **Convertir** le disque d’un fichier VHDX en disque dur virtuel.  
     - Si vous augmentez la taille du disque, veillez à ne *pas* dépasser 128 Go.        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Choisir une action":::   

    Pour plus d’informations, lisez l’article qui explique comment [convertir le disque virtuel en disque dur virtuel de taille fixe](../virtual-machines/windows/prepare-for-upload-vhd-image.md#convert-the-virtual-disk-to-a-fixed-size-vhd).

1. Chargez le disque dur virtuel sur Azure pour créer un disque managé.
    1. Vous pouvez utiliser l’Explorateur Stockage ou AzCopy à partir de la ligne de commande, comme démontré dans l’article [Charger un disque dur virtuel sur Azure ou copier un disque managé dans une autre région](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md).        

    1. Après avoir chargé le disque dur virtuel, vous devriez désormais disposer d’un disque managé visible dans le portail Azure. 
    
    > [!WARNING]
    > Si votre ordinateur se met en veille ou se verrouille, le processus de chargement peut s’interrompre et échouer. 
    
    > [!IMPORTANT] 
    > L’onglet du portail Azure **Taille+Performances** pour le disque managé vous permet de modifier la taille de votre disque. Comme mentionné précédemment, la taille *ne doit pas* être supérieure à 128 Go.

1. Prenez un instantané du disque managé.
    Pour cela, vous pouvez utiliser PowerShell, le portail Azure ou l’Explorateur Stockage, comme démontré dans [Créer un instantané à l’aide du portail ou de PowerShell](../virtual-machines/windows/snapshot-copy-managed-disk.md).

1. Dans Shared Image Gallery, créez une définition d’image et une version d’image :
    1. [Créez une définition d’image](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition).  
     - Choisissez **Gen 1** pour la **génération de la machine virtuelle**.
     - Indiquez si vous créez une image **spécialisée** ou **généralisée** pour l’**État du système d’exploitation**.
     
    Pour plus d’informations sur les valeurs que vous pouvez spécifier pour une définition d’image, consultez [Définitions d’image](../virtual-machines/shared-image-galleries.md#image-definitions). 
    
    > [!NOTE] 
    > Vous pouvez également choisir d’utiliser une définition d’image existante, et créer une nouvelle version de votre image personnalisée.
    
1. [Créez une version d’image](../virtual-machines/windows/shared-images-portal.md#create-an-image-version).
    - La propriété **Numéro de version** utilise le format suivant : *VersionMajeure.VersionMineure.Patch*.   Lorsque vous utilisez Lab Services pour créer un labo et choisir une image personnalisée, la version la plus récente de l’image est automatiquement utilisée.  La version la plus récente est choisie en fonction de la valeur la plus élevée de VersionMajeure, puis de VersionMineure, et enfin de Patch.
    - Pour la **Source**, choisissez **Disques et/ou instantanés** dans la liste déroulante.
    - Pour la propriété **Disque de système d’exploitation**, choisissez l’instantané que vous avez créé lors des étapes précédentes.
    
    Pour plus d’informations sur les valeurs que vous pouvez spécifier dans une définition d’image, consultez [Versions d’image](../virtual-machines/shared-image-galleries.md#image-versions). 
   
1. [Créez le labo](tutorial-setup-classroom-lab.md) dans Lab Services et sélectionnez l’image personnalisée à partir de la galerie d’images partagées.

    Si vous avez augmenté la taille du disque *après* l’installation du système d’exploitation sur la machine virtuelle Hyper-V d’origine, vous devrez également étendre le lecteur C dans Windows pour utiliser l’espace de disque non alloué :      
    - Connectez-vous au modèle de machine virtuelle du labo et suivez les étapes similaires à celles présentées dans [Étendre un volume de base](/windows-server/storage/disk-management/extend-a-basic-volume). 

## <a name="bring-a-windows-custom-image-from-an-azure-virtual-machine"></a>Apporter une image personnalisée Windows à partir d’une machine virtuelle Azure
Une autre approche consiste à configurer votre image Windows à l’aide d’une [machine virtuelle Azure](../virtual-machines/windows/overview.md).  L’utilisation d’une machine virtuelle Azure vous donne la flexibilité de créer une image spécialisée ou généralisée à utiliser avec vos labos.  Le processus de préparation d’une image à partir d’une machine virtuelle Azure est plus simple comparé à la [création d’une image à partir d’un disque dur virtuel](upload-custom-image-shared-image-gallery.md#bring-a-windows-custom-image-from-a-physical-lab-environment), car celle-ci est déjà préparée pour s’exécuter dans Azure.

Pour effectuer les étapes ci-dessous, vous devez disposer de l’autorisation de créer une machine virtuelle Azure dans l’abonnement Azure de votre établissement scolaire :

1. Créez une machine virtuelle Azure à l’aide du [portail Azure](../virtual-machines/windows/quick-create-portal.md), de [PowerShell](../virtual-machines/windows/quick-create-powershell.md), de l’interface [Azure CLI](../virtual-machines/windows/quick-create-cli.md) ou d’un [modèle ARM](../virtual-machines/windows/quick-create-template.md).
    
    - Lorsque vous spécifiez les paramètres du disque, assurez-vous que la taille du disque n’est *pas* supérieure à 128 Go.
    
1. Installez les logiciels et apportez les modifications de configuration nécessaires à l’image de la machine virtuelle Azure.

1. Exécutez [SysPrep](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep) si vous devez créer une image généralisée.  Pour plus d’informations, consultez [Images généralisées et spécialisées](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).

1. Dans Shared Image Gallery, [créez une définition d’image](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition) ou choisissez une définition d’image existante.
     - Choisissez **Gen 1** pour la **génération de la machine virtuelle**.
     - Indiquez si vous créez une image **spécialisée** ou **généralisée** pour l’**État du système d’exploitation**.
    
1. [Créez une version d’image](../virtual-machines/windows/shared-images-portal.md#create-an-image-version).
    - La propriété **Numéro de version** utilise le format suivant : *VersionMajeure.VersionMineure.Patch*.   
    - Pour la **Source**, choisissez **Disques et/ou instantanés** dans la liste déroulante.
    - Pour la propriété **Disque de système d’exploitation**, choisissez le disque de votre machine virtuelle Azure que vous avez créé dans les étapes précédentes.

1. [Créez le labo](tutorial-setup-classroom-lab.md) dans Lab Services et sélectionnez l’image personnalisée à partir de la galerie d’images partagées.

Vous pouvez également importer votre image personnalisée à partir d’une machine virtuelle Azure dans Shared Image Gallery au moyen de PowerShell.  Pour plus d’informations, consultez le script suivant et le fichier Lisez-moi associé :
    
- [Apporter une image dans le script de Shared Image Gallery](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BringImageToSharedImageGallery/)

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de Shared Image Gallery](../virtual-machines/shared-image-galleries.md)
* [Attacher ou détacher une galerie d’images partagées](how-to-attach-detach-shared-image-gallery.md)
* [Utilisation de Shared Image Gallery](how-to-use-shared-image-gallery.md)