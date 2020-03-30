---
title: Créer un groupe de placements de proximité à l’aide du portail
description: Découvrez comment créer un groupe de placements de proximité à l’aide du Portail Azure.
services: virtual-machines
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 8512d9b701242dc686d49bbe56e8a2059f14ee3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73180021"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Créer un groupe de placements de proximité à l’aide du portail

Pour que les machines virtuelles soient aussi proches que possible, avec la latence la plus faible possible, déployez-les dans un [groupe de placements de proximité](co-location.md#proximity-placement-groups).

Le groupe de placements de proximité est un regroupement logique utilisé pour s’assurer que les ressources de calcul Azure se trouvent proches les unes des autres. Les groupes de placements de proximité sont utiles pour les charges de travail où une latence faible est requise.


## <a name="create-the-proximity-placement-group"></a>Créer le groupe de placements de proximité

1. Tapez **groupe de placements de proximité** dans la recherche.
1. Sous **Services** dans les résultats de la recherche, sélectionnez **Groupes de placements de proximité**.
1. Dans la page **Groupes de placements de proximité**, sélectionnez **Ajouter**.
1. Sous l’onglet **De base**, sous **Détails du projet**, vérifiez que l’abonnement approprié est sélectionné.
1. Dans **Groupe de ressources**, sélectionnez un groupe de ressources existant dans la liste déroulante ou sélectionnez **Créer** pour créer un groupe de ressources.
1. Dans **Région**, sélectionnez l’emplacement où vous souhaitez créer le groupe de placements de proximité.
1. Dans **Groupe de placements de proximité**, tapez un nom, puis sélectionnez **Vérifier + créer**.
1. Après la validation, sélectionnez **Créer** pour créer le groupe de placements de proximité.

    ![Capture d’écran de la création d’un groupe de placements de proximité](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Lors de la création d’une machine virtuelle dans le portail, accédez à l’onglet **Avancé**. 
1. Dans la sélection **Groupe de placements de proximité**, choisissez le groupe de placements approprié. 

    ![Capture d’écran de la section de groupe de placements de proximité lors de la création d’une machine virtuelle dans le portail](./media/ppg/vm-ppg.png)

1. Lorsque vous avez terminé d’effectuer toutes les autres sélections requises, sélectionnez **Vérifier + créer**.
1. Après la validation, sélectionnez **Créer** pour déployer la machine virtuelle dans le groupe de placements.




## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également utiliser [Azure PowerShell](proximity-placement-groups.md) pour créer des groupes de placements de proximité.

