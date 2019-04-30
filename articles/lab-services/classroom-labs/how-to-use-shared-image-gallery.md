---
title: Utilisez une galerie d’images partagé dans Azure Lab Services | Microsoft Docs
description: Découvrez comment configurer un compte de laboratoire pour utiliser une galerie d’images partagé afin qu’un utilisateur peut partager une image avec d’autres et un autre utilisateur peut utiliser l’image pour créer un modèle de machine virtuelle dans le laboratoire.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 93136c7d685bd9fc8ec4bcdea3a900b28029059b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695165"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Utilisez une galerie d’images partagé dans Azure Lab Services
Cet article explique comment enseignants de lab admin peut enregistrer une image de machine virtuelle de modèle pour qu’il puisse être réutilisé par d’autres. Ces images sont enregistrées dans un Azure [Galerie d’images partagé](../../virtual-machines/windows/shared-image-galleries.md). Dans un premier temps, l’administrateur de laboratoire attache une galerie d’images partagé existant pour le compte de laboratoire. Une fois que la galerie d’images partagé est attachée, laboratoires créés dans le compte de laboratoire peuvent enregistrer des images dans la galerie d’images partagé. Autres enseignants peuvent sélectionner cette image à partir de la galerie d’images partagé pour créer un modèle pour leurs classes. 

## <a name="prerequisites"></a>Conditions préalables
Créer une galerie d’images partagé à l’aide [Azure PowerShell](../../virtual-machines/windows/shared-images.md) ou [Azure CLI](../../virtual-machines/linux/shared-images.md).

## <a name="attach-a-shared-image-gallery-to-a-lab-account"></a>Attacher une galerie d’images partagé à un compte de laboratoire
La procédure suivante vous montre comment associer une galerie d’images partagé à un compte de laboratoire. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Tous les services**. Sélectionnez **Lab Services** dans la section **DEVOPS**. Si vous sélectionnez l’étoile (`*`) en regard de **Lab Services**, celui-ci est ajouté à la section **FAVORIS** dans le menu de gauche. Les fois suivantes, sélectionnez **Lab Services** sous **FAVORIS**.

    ![Tous les services -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Sélectionnez votre compte de laboratoire pour voir les **compte Lab** page. 
4. Sélectionnez **Galerie d’images partagé** sur le menu de gauche, puis sélectionnez **attacher** sur la barre d’outils. 

    ![Partagé Galerie d’images - bouton Ajouter](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Sur le **attacher une galerie d’images partagé existant** page, sélectionnez votre galerie d’images partagé, puis **OK**.

    ![Sélectionnez une galerie existante](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Vous voyez l’écran suivant : 

    ![Ma galerie dans la liste](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Dans cet exemple, il n’y pas d’images dans la galerie d’images partagé encore.

Identités de Azure Lab Services sont ajoutée en tant que contributeur à la galerie d’images partagé qui est attachée au laboratoire. Il permet aux enseignants / administrateur informatique chargé d’enregistrer la machine virtuelle d’images à la galerie d’images partagé. Tous les laboratoires créés dans ce compte de laboratoire ont accès à la galerie d’images partagé attaché. 

Toutes les images dans la galerie d’images partagé attaché sont activées par défaut. Vous pouvez activer ou désactiver les images sélectionnées en les sélectionnant dans la liste et en utilisant le **activer les images sélectionnées** ou **désactiver les images sélectionnées** bouton. 

## <a name="detach-a-shared-image-gallery"></a>Détacher une galerie d’images partagé
Qu’une galerie d’images partagé peut être joint à un laboratoire. Si vous souhaitez attacher une autre galerie d’images partagé, détacher l’objet actuel avant d’attacher une nouvelle. Pour détacher une galerie d’images partagé à partir de votre laboratoire, sélectionnez **détachement** sur la barre d’outils, puis confirmez l’opération de détachement. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Enregistrer une image dans la galerie d’images partagé
Une fois une galerie d’images partagé est attachée, un enseignant peut enregistrer une image de modèle à la galerie d’images partagé afin qu’il peut être réutilisé par d’autres professeurs.

![Enregistrer l’image de machine virtuelle dans la galerie](../media/how-to-use-shared-image-gallery/save-virtual-machine.png)

## <a name="use-an-image-from-the-shared-image-gallery"></a>Utiliser une image de la galerie d’images partagé
Un enseignant peut choisir une image personnalisée disponible dans la galerie d’images partagé pour le modèle pendant la création de laboratoire.

![Utiliser l’image de machine virtuelle à partir de la galerie](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les galeries d’images partagé, consultez [Galerie d’images partagé](../../virtual-machines/windows/shared-image-galleries.md).
