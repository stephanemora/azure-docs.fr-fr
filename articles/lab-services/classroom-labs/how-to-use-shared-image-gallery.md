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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 8d8b6fffe197d4180b091518dcd1615d0e0b9d19
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412849"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Utilisez une galerie d’images partagé dans Azure Lab Services
Cet article explique comment enseignants de lab admin peut enregistrer une image de machine virtuelle de modèle pour qu’il puisse être réutilisé par d’autres. Ces images sont enregistrées dans un Azure [Galerie d’images partagé](../../virtual-machines/windows/shared-image-galleries.md). Dans un premier temps, l’administrateur de laboratoire attache une galerie d’images partagé existant pour le compte de laboratoire. Une fois que la galerie d’images partagé est attachée, laboratoires créés dans le compte de laboratoire peuvent enregistrer des images dans la galerie d’images partagé. Autres enseignants peuvent sélectionner cette image à partir de la galerie d’images partagé pour créer un modèle pour leurs classes. 

## <a name="prerequisites"></a>Conditions préalables
- Créer une galerie d’images partagé à l’aide [Azure PowerShell](../../virtual-machines/windows/shared-images.md) ou [Azure CLI](../../virtual-machines/linux/shared-images.md).
- Vous avez attaché à la galerie d’images partagé pour le compte de laboratoire. Pour obtenir des instructions détaillées, consultez [comment attacher ou détacher partagé Galerie d’images](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Enregistrer une image dans la galerie d’images partagé
Une fois une galerie d’images partagé est attachée, un enseignant peut enregistrer ou charger une image dans la galerie d’images partagé afin qu’il peut être réutilisé par d’autres professeurs. Pour obtenir des instructions pour télécharger une image dans la galerie d’images partagé, consultez [vue d’ensemble de la galerie d’images partagé](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Actuellement, l’interface utilisateur de laboratoires de salle de classe (IU) ne prend en charge l’enregistrement d’une image de laboratoire dans la galerie d’images partagé. 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Utiliser une image de la galerie d’images partagé
Un enseignant peut choisir une image personnalisée disponible dans la galerie d’images partagé pour le modèle pendant la création de laboratoire.

![Utiliser l’image de machine virtuelle à partir de la galerie](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les galeries d’images partagé, consultez [Galerie d’images partagé](../../virtual-machines/windows/shared-image-galleries.md).
