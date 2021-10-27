---
title: Plans de mise à l’échelle d’Azure Virtual Desktop pour les pools d’hôtes (préversion)
description: Comment attribuer des plans de mise à l’échelle à des pools d’hôtes nouveaux ou existants dans votre déploiement.
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b61869e09003456f55a6d69cae01356a06bc633d
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181685"
---
# <a name="enable-scaling-plans-for-existing-and-new-host-pools-preview"></a>Activer des plans de mise à l’échelle pour des pools d’hôtes existants et nouveaux (préversion)

> [!IMPORTANT]
> La fonctionnalité de mise à l'échelle automatique est actuellement disponible en version préliminaire.
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez activer des plans de mise à l’échelle pour tous les pools d’hôtes existants dans votre déploiement. Lorsque vous appliquez votre plan de mise à l’échelle au pool d’hôtes, il s’applique également à tous les hôtes de session de ce pool. La mise à l’échelle s’applique aussi automatiquement à tous les nouveaux hôtes de session que vous créez dans le pool d’hôtes qui vous est attribué.

Si vous désactivez un plan de mise à l’échelle, toutes les ressources concernées resteront dans l’état de mise à l’échelle dans lequel elles se trouvaient au moment où vous l’avez désactivé.

## <a name="assign-a-scaling-plan-to-an-existing-host-pool"></a>Attribuer un plan de mise à l’échelle à un pool d’hôtes existant

Pour attribuer un plan de mise à l’échelle à un pool d’hôtes existant :

1. Ouvrez le [portail Azure](https://portal.azure.com).

2. Accédez à **Windows Virtual Desktop**.

3. Sélectionnez **Pools d’hôtes**, puis allez dans **Plan de mise à l’échelle** et sélectionnez **Nouveau**.

4. Sélectionnez **Plan de mise à l’échelle**, puis sélectionnez **+ Attribuer** pour attribuer un plan de mise à l’échelle à un pool d’hôtes non affecté, comme illustré dans la capture d’écran suivante.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la fenêtre du plan de mise à l’échelle. Le bouton + Ajouter dans le coin supérieur gauche est mis en évidence par un encadré rouge.](media/assign-scaling-plan.png)

    Si vous avez activé le plan de mise à l’échelle pendant le déploiement, vous aurez également la possibilité de désactiver le plan pour le pool d’hôtes sélectionné dans le menu **Plan de mise à l’échelle** en décochant la case **Activer le plan de mise à l’échelle**, comme le montre la capture d’écran suivante.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la fenêtre du plan de mise à l’échelle. La case à cocher « Activer la mise à l’échelle automatique » est sélectionnée et mise en évidence par un encadré rouge.](media/enable-autoscale.png)

## <a name="edit-an-existing-scaling-plan"></a>Modifier un plan de mise à l’échelle existant

Pour modifier un plan de mise à l’échelle existant :

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/).

2. Accédez à **Azure Virtual Desktop**.

3. Sélectionnez **Plan de mise à l’échelle**, puis sélectionnez le nom du plan de mise à l’échelle que vous souhaitez modifier. La fenêtre des paramètres doit s’ouvrir.

4. Pour modifier le nom d’affichage, la description, le fuseau horaire ou les balises d’exclusion du plan, accédez à l’onglet **Propriétés**.

5. Pour attribuer des pools d’hôtes ou modifier des planifications, accédez à l’onglet **Gérer**.

## <a name="next-steps"></a>Étapes suivantes

- Consultez la marche à suivre pour créer un plan de mise à l’échelle dans [Mise à l’échelle automatique (préversion) d’hôtes de session Azure Virtual Desktop](autoscale-new-existing-host-pool.md).
- Découvrez comment résoudre les problèmes liés à votre plan de mise à l’échelle dans [Activer les diagnostics pour votre plan de mise à l’échelle](autoscale-diagnostics.md).