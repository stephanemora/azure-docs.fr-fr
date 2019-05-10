---
title: Attacher ou détacher une galerie d’images partagé dans Azure Lab Services | Microsoft Docs
description: Découvrez comment attacher une galerie d’images partagé à un laboratoire dans Azure Lab Services.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: de4e9fb4b15f4c346926fe46f23255c668204c2e
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413887"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Attacher ou détacher une galerie d’images partagé dans Azure Lab Services
Administrateur d’enseignants de lab peut enregistrer une image de machine virtuelle du modèle dans un Azure [Galerie d’images partagé](../../virtual-machines/windows/shared-image-galleries.md) pour qu’il puisse être réutilisé par d’autres utilisateurs. Dans un premier temps, l’administrateur de laboratoire attache une galerie d’images partagé existant pour le compte de laboratoire. Une fois que la galerie d’images partagé est attachée, laboratoires créés dans le compte de laboratoire peuvent enregistrer des images dans la galerie d’images partagé. Autres enseignants peuvent sélectionner cette image à partir de la galerie d’images partagé pour créer un modèle pour leurs classes. 

Cet article vous montre comment attacher ou détacher une galerie d’images partagé pour un compte de laboratoire. 

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurer au moment de la création de comptes de laboratoire
Lorsque vous créez un compte de laboratoire, vous pouvez attacher une galerie d’images partagé pour le compte de laboratoire. Vous pouvez sélectionner une galerie d’images partagé existant dans la liste déroulante ou créez-en un. Pour créer et attacher une galerie d’images partagé pour le compte de laboratoire, sélectionnez **créer**, entrez un nom pour la galerie, puis entrez **OK**. 

![Configurer la galerie d’images partagé au moment de la création de comptes de laboratoire](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configurer une fois créé le compte de laboratoire
Une fois que le compte de laboratoire est créé, vous pouvez effectuer les tâches suivantes :

- Créer et attacher une galerie d’images partagé
- Attacher une galerie d’images partagé pour le compte de laboratoire
- Détacher une galerie d’images partagé à partir du compte de laboratoire

## <a name="create-and-attach-a-shared-image-gallery"></a>Créer et attacher une galerie d’images partagé
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Tous les services**. Sélectionnez **Lab Services** dans la section **DEVOPS**. Si vous sélectionnez l’étoile (`*`) en regard de **Lab Services**, celui-ci est ajouté à la section **FAVORIS** dans le menu de gauche. Les fois suivantes, sélectionnez **Lab Services** sous **FAVORIS**.

    ![Tous les services -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Sélectionnez votre compte de laboratoire pour voir les **compte Lab** page. 
4. Sélectionnez **Galerie d’images partagé** sur le menu de gauche, puis sélectionnez **+ créer** sur la barre d’outils.  

    ![Créer le bouton de la galerie d’image partagée](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Dans le **créer une galerie image partagée** fenêtre, entrez un **nom** pour la galerie, puis entrez **OK**. 

    ![Créer la fenêtre de la galerie image partagée](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services crée la galerie d’images partagé et associé au compte de laboratoire. Tous les laboratoires créés dans ce compte de laboratoire ont accès à la galerie d’images partagé attaché. 

    ![Galerie d’image jointe](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    Dans le volet inférieur, vous consultez des images dans la galerie d’images partagé. Dans cette nouvelle galerie, il n’existe aucune image. Lorsque vous téléchargez des images à la galerie, il apparaît dans cette page.     

    Toutes les images dans la galerie d’images partagé attaché sont activées par défaut. Vous pouvez activer ou désactiver les images sélectionnées en les sélectionnant dans la liste et en utilisant le **activer les images sélectionnées** ou **désactiver les images sélectionnées** bouton.

## <a name="attach-an-existing-shared-image-gallery"></a>Attacher une galerie d’images partagé existant
La procédure suivante vous montre comment associer une galerie d’images partagé existant à un compte de laboratoire. 

1. Sur le **compte Lab** page, sélectionnez **Galerie d’images partagé** sur le menu de gauche, puis sélectionnez **attacher** sur la barre d’outils. 

    ![Partagé Galerie d’images - bouton Ajouter](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Sur le **attacher une galerie d’images partagé existant** page, sélectionnez votre galerie d’images partagé, puis **OK**.

    ![Sélectionnez une galerie existante](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Vous voyez l’écran suivant : 

    ![Ma galerie dans la liste](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Dans cet exemple, il n’y pas d’images dans la galerie d’images partagé encore.

    Identités de Azure Lab Services sont ajoutée en tant que contributeur à la galerie d’images partagé qui est attachée au laboratoire. Il permet aux enseignants / administrateur informatique chargé d’enregistrer la machine virtuelle d’images à la galerie d’images partagé. Tous les laboratoires créés dans ce compte de laboratoire ont accès à la galerie d’images partagé attaché. 

    Toutes les images dans la galerie d’images partagé attaché sont activées par défaut. Vous pouvez activer ou désactiver les images sélectionnées en les sélectionnant dans la liste et en utilisant le **activer les images sélectionnées** ou **désactiver les images sélectionnées** bouton. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Enregistrer une image dans la galerie d’images partagé
Une fois une galerie d’images partagé est attachée, un administrateur de compte de laboratoire ou un enseignant peut enregistrer ou charger une image dans la galerie d’images partagé afin qu’il peut être réutilisé par d’autres professeurs. Pour obtenir des instructions pour télécharger une image dans la galerie d’images partagé, consultez [vue d’ensemble de la galerie d’images partagé](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Actuellement, l’interface utilisateur de laboratoires de salle de classe (IU) ne prend en charge l’enregistrement d’une image de laboratoire dans la galerie d’images partagé. 

## <a name="detach-a-shared-image-gallery"></a>Détacher une galerie d’images partagé
Qu’une galerie d’images partagé peut être joint à un laboratoire. Si vous souhaitez attacher une autre galerie d’images partagé, détacher l’objet actuel avant d’attacher une nouvelle. Pour détacher une galerie d’images partagé à partir de votre laboratoire, sélectionnez **détachement** sur la barre d’outils, puis confirmez l’opération de détachement. 

![Détacher de la galerie d’images partagé à partir du compte de laboratoire](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment enregistrer une image de laboratoire dans la galerie d’images partagé ou utiliser une image à partir de la galerie d’images partagé pour créer une machine virtuelle, consultez [comment utiliser la galerie d’images partagé](how-to-use-shared-image-gallery.md).

Pour plus d’informations sur partagés galeries d’images en général, consultez [Galerie d’images partagé](../../virtual-machines/windows/shared-image-galleries.md).
