---
title: Utiliser une galerie d’images partagées dans Azure Lab Services | Microsoft Docs
description: Découvrez comment configurer un compte de laboratoire pour utiliser une galerie d’images partagées, afin qu’un utilisateur puisse partager une image avec d’autres personnes et que ces autres personnes puissent utiliser l’image pour créer un modèle de machine virtuelle dans le laboratoire.
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
ms.date: 02/24/2020
ms.author: spelluru
ms.openlocfilehash: c611ecdb5a2534f7368e533e3e19e6e3f96de57f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190447"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Utiliser une galerie d’images partagées dans Azure Lab Services
Cet article explique comment les administrateurs de laboratoire/enseignants peuvent enregistrer une image de modèle de machine virtuelle pour qu’elle puisse être réutilisée par d’autres personnes. Ces images sont enregistrées dans une [galerie d’images partagées](../../virtual-machines/windows/shared-image-galleries.md) Azure. Dans un premier temps, l’administrateur de laboratoire associe une galerie d’images partagées existante au compte de laboratoire. Une fois la galerie d’images partagées associée, les laboratoires créés dans le compte de laboratoire peuvent enregistrer des images dans la galerie d’images partagées. D’autres enseignants peuvent sélectionner cette image à partir de la galerie d’images partagées pour créer un modèle pour leurs classes. 

> [!NOTE]
> Actuellement, Azure Lab Services prend en charge la création de modèles de machines virtuelles basés uniquement sur des images de machine virtuelle **généralisées** (et non sur des images spécialisées) d’une galerie d’images partagées. 

## <a name="prerequisites"></a>Conditions préalables requises
- Créez une galerie d’images partagées à l’aide [d’Azure PowerShell](../../virtual-machines/windows/shared-images.md) ou [d’Azure CLI](../../virtual-machines/linux/shared-images.md).
- Vous avez associé la galerie d’images partagées au compte de laboratoire. Pour obtenir des instructions détaillées, consultez [Attach or detach a shared image gallery in Azure Lab Services](how-to-attach-detach-shared-image-gallery.md) (Attacher ou détacher une galerie d’images partagées dans Azure Lab Services).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Enregistrer une image dans la galerie d’images partagées
Une fois qu’une galerie d’images partagées est associée, un administrateur de compte lab ou un enseignant peut enregistrer une image dans la galerie d’images partagées, afin qu’elle puisse être réutilisée par d’autres enseignants. 

1. Dans la page **Modèle** du laboratoire, sélectionnez **Exporter vers Shared Image Gallery** dans la barre d’outils.

    ![Bouton Enregistrer l’image](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Dans la boîte de dialogue **Exporter vers Shared Image Gallery**, entrez un **nom pour l’image**, puis sélectionnez **Exporter**. 

    ![Boîte de dialogue Exporter vers Shared Image Gallery](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Vous pouvez voir la progression de cette opération sur la page **Modèle**. Cette opération peut prendre un certain temps. 

    ![Exportation en cours](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Une fois l’opération d’exportation réussie, le message suivant s’affiche :

    ![Exportation terminée](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

Vous pouvez aussi charger une image dans la galerie d’images partagées en dehors du contexte d’un lab. Pour plus d’informations, consultez [Vue d’ensemble de la galerie d’images partagées](../../virtual-machines/windows/shared-images.md). 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Utiliser une image de la galerie d’images partagées
Un enseignant/professeur peut choisir une image personnalisée disponible dans la galerie d’images partagées pour le modèle pendant la création d’un laboratoire.

![Utiliser une image de machine virtuelle de la galerie](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les galeries d’images partagées, consultez [Shared Image Gallery overview](../../virtual-machines/windows/shared-image-galleries.md) (Vue d’ensemble de la galerie d’images partagées).
