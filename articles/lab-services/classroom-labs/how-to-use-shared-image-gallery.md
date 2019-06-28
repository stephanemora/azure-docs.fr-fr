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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 8d8b6fffe197d4180b091518dcd1615d0e0b9d19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65412849"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Utiliser une galerie d’images partagées dans Azure Lab Services
Cet article explique comment les administrateurs de laboratoire/enseignants peuvent enregistrer une image de modèle de machine virtuelle pour qu’elle puisse être réutilisée par d’autres personnes. Ces images sont enregistrées dans une [galerie d’images partagées](../../virtual-machines/windows/shared-image-galleries.md) Azure. Dans un premier temps, l’administrateur de laboratoire associe une galerie d’images partagées existante au compte de laboratoire. Une fois la galerie d’images partagées associée, les laboratoires créés dans le compte de laboratoire peuvent enregistrer des images dans la galerie d’images partagées. D’autres enseignants peuvent sélectionner cette image à partir de la galerie d’images partagées pour créer un modèle pour leurs classes. 

## <a name="prerequisites"></a>Prérequis
- Créez une galerie d’images partagées à l’aide [d’Azure PowerShell](../../virtual-machines/windows/shared-images.md) ou [d’Azure CLI](../../virtual-machines/linux/shared-images.md).
- Vous avez associé la galerie d’images partagées au compte de laboratoire. Pour obtenir des instructions détaillées, consultez [Attach or detach a shared image gallery in Azure Lab Services](how-to-attach-detach-shared-image-gallery.md) (Attacher ou détacher une galerie d’images partagées dans Azure Lab Services).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Enregistrer une image dans la galerie d’images partagées
Une fois qu’une galerie d’images partagées est associée, un enseignant peut enregistrer ou charger une image dans la galerie d’images partagées pour qu’elle puisse être réutilisée par d’autres professeurs. Pour obtenir des instructions sur le téléchargement d’une image dans la galerie d’images partagées, consultez [Create a shared image gallery with Azure PowerShell](../../virtual-machines/windows/shared-images.md) (Créer une galerie d’images partagées avec Azure PowerShell). 

> [!NOTE]
> Actuellement, l’interface utilisateur des laboratoires de salle de classe ne prend pas en charge l’enregistrement d’une image de laboratoire dans la galerie d’images partagées. 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Utiliser une image de la galerie d’images partagées
Un enseignant/professeur peut choisir une image personnalisée disponible dans la galerie d’images partagées pour le modèle pendant la création d’un laboratoire.

![Utiliser une image de machine virtuelle de la galerie](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les galeries d’images partagées, consultez [Shared Image Gallery overview](../../virtual-machines/windows/shared-image-galleries.md) (Vue d’ensemble de la galerie d’images partagées).
