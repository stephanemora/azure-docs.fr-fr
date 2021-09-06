---
title: Azure Lab Services - Comment récupérer une image Windows personnalisée à partir d’une machine virtuelle Azure
description: Décrit comment récupérer une image Windows personnalisée à partir d’une machine virtuelle Azure.
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: ab79f4acb0b61a46e4a8f1cebe243e6df43702bf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524345"
---
# <a name="bring-a-windows-custom-image-from-an-azure-virtual-machine"></a>Apporter une image personnalisée Windows à partir d’une machine virtuelle Azure

Les étapes de cet article montrent comment importer une image personnalisée à partir d’une [machine virtuelle Azure](https://azure.microsoft.com/services/virtual-machines/).  Avec cette approche, vous configurez une image sur une machine virtuelle Azure et vous importez l’image dans une galerie d’images partagées afin qu’elle puisse être utilisée dans Lab Services.  Avant d’utiliser cette approche pour créer une image personnalisée, lisez l’article [Approches recommandées pour créer des images personnalisées](approaches-for-custom-image-creation.md) afin de déterminer la meilleure approche à votre scénario.

## <a name="prerequisites"></a>Configuration requise

Pour effectuer les étapes indiquées dans cet article, vous devez disposer de l’autorisation de créer une machine virtuelle Azure dans l’abonnement Azure de votre établissement scolaire.

## <a name="prepare-a-custom-image-on-an-azure-vm"></a>Préparer une image personnalisée sur une machine virtuelle Azure

1. Créez une machine virtuelle Azure à l’aide du [portail Azure](../virtual-machines/windows/quick-create-portal.md), de [PowerShell](../virtual-machines/windows/quick-create-powershell.md), de l’interface [Azure CLI](../virtual-machines/windows/quick-create-cli.md) ou d’un [modèle ARM](../virtual-machines/windows/quick-create-template.md).
    
    - Lorsque vous spécifiez les paramètres du disque, assurez-vous que la taille du disque n’est *pas* supérieure à 128 Go.
    
1. Installez les logiciels et apportez les modifications de configuration nécessaires à l’image de la machine virtuelle Azure.

1. Si vous le souhaitez, vous pouvez généraliser l’image. Exécutez [SysPrep](../virtual-machines/generalize.md#windows) si vous devez créer une image généralisée.  Dans le cas contraire, si vous créez une image spécialisée, vous pouvez passer à l’étape suivante.

    Créez une image spécialisée si vous souhaitez conserver les profils utilisateur et des informations propres à la machine.  Pour plus d’informations sur les différences existant entre les images généralisées et les images spécialisées, consultez [Images généralisées et spécialisées](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).

## <a name="import-the-custom-image-into-a-shared-image-gallery"></a>Importer l’image personnalisée dans une galerie d’images partagées

1. Dans une galerie d’images partagées, [créez une définition d’image](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition) ou choisissez une définition d’image existante.
     - Choisissez **Gen 1** pour la **génération de la machine virtuelle**.
     - Indiquez si vous créez une image **spécialisée** ou **généralisée** pour l’**État du système d’exploitation**.

    Pour plus d’informations sur les valeurs que vous pouvez spécifier pour une définition d’image, consultez [Définitions d’image](../virtual-machines/shared-image-galleries.md#image-definitions). 
    
    Vous pouvez également choisir d’utiliser une définition d’image existante, et créer une nouvelle version de votre image personnalisée.
    
1. [Créez une version d’image](../virtual-machines/windows/shared-images-portal.md#create-an-image-version).
    - La propriété **Numéro de version** utilise le format suivant : *VersionMajeure.VersionMineure.Patch*.   
    - Pour la **Source**, choisissez **Disques et/ou instantanés** dans la liste déroulante.
    - Pour la propriété **Disque de système d’exploitation**, choisissez le disque de votre machine virtuelle Azure que vous avez créé dans les étapes précédentes.

    Vous pouvez également importer votre image personnalisée à partir d’une machine virtuelle Azure dans Shared Image Gallery au moyen de PowerShell.  Pour plus d’informations, consultez le script suivant et le fichier Lisez-moi associé :
    - [Apporter une image dans le script de Shared Image Gallery](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BringImageToSharedImageGallery/)

## <a name="create-a-lab"></a>Création d’un laboratoire

1. [Créez le labo](tutorial-setup-classroom-lab.md) dans Lab Services et sélectionnez l’image personnalisée à partir de la galerie d’images partagées.

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de Shared Image Gallery](../virtual-machines/shared-image-galleries.md)
* [Attacher ou détacher une galerie d’images partagées](how-to-attach-detach-shared-image-gallery.md)
* [Utiliser une galerie d’images partagées](how-to-use-shared-image-gallery.md)