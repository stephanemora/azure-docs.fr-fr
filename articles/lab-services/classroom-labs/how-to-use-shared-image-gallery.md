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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: 8253b784a427ceb3842b6b4d585c52a2c30e0c4d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592421"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Utiliser une galerie d’images partagées dans Azure Lab Services
Cet article explique comment les enseignants/administrateurs de laboratoire peuvent enregistrer un modèle d’image de machine virtuelle dans une [galerie d’images partagées](../../virtual-machines/windows/shared-image-galleries.md) afin que d’autres personnes puissent l’utiliser pour créer des laboratoires. 

## <a name="scenarios"></a>Scénarios
Voici les deux scénarios que cette fonctionnalité prend en charge : 

- Un administrateur de compte lab joint une galerie d’images partagées au compte lab, puis charge une image dans la galerie d’images partagées en dehors du contexte d’un laboratoire. Ensuite, les créateurs de laboratoires peuvent utiliser cette image de la galerie d’images partagées pour créer des laboratoires. 
- Un administrateur de compte lab attache une galerie d’images partagées au compte lab. Un créateur de laboratoire (formateur) enregistre l’image personnalisée de son laboratoire dans la galerie d’images partagées. Ensuite, d’autres créateurs de laboratoires peuvent sélectionner cette image à partir de la galerie d’images partagées afin de créer un modèle pour leurs laboratoires. 

    Quand une image est enregistrée dans une galerie d’images partagées, Azure Lab Services réplique l’image enregistrée dans d’autres régions disponibles au sein de la même [zone géographique](https://azure.microsoft.com/global-infrastructure/geographies/). Ainsi, l’image est disponible pour les labos créés dans d’autres régions de la même zone géographique. L’enregistrement d’images dans une galerie d’images partagées entraîne un coût supplémentaire, qui comprend le coût de toutes les images répliquées. Ce coût est distinct du coût d’utilisation d’Azure Lab Services. Pour plus d’informations sur les tarifs liés à Shared Image Gallery, consultez [Vue d’ensemble de Shared Image Gallery - Facturation]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

    > [!NOTE]
    > Azure Lab Services prend en charge la création de modèles de machines virtuelles basés sur des images **généralisées** et **spécialisées** dans une galerie d’images partagées. 


## <a name="prerequisites"></a>Prérequis
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

    > [!NOTE]
    > Une fois l’image enregistrée dans la galerie d’images partagées, vous pouvez utiliser cette image à partir de la galerie lors de la création d’un autre laboratoire. 
    > 
    > Vous pouvez aussi charger une image dans la galerie d’images partagées en dehors du contexte d’un lab. Pour plus d’informations, consultez [Vue d’ensemble de la galerie d’images partagées](../../virtual-machines/windows/shared-images.md). 

    

## <a name="use-an-image-from-the-shared-image-gallery"></a>Utiliser une image de la galerie d’images partagées
Un enseignant peut choisir une image personnalisée disponible dans la galerie d’images partagées pour le modèle pendant la création d’un laboratoire.

![Utiliser une image de machine virtuelle de la galerie](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les galeries d’images partagées, consultez [Shared Image Gallery overview](../../virtual-machines/windows/shared-image-galleries.md) (Vue d’ensemble de la galerie d’images partagées).
