---
title: Vue d’ensemble de l’exemple de blueprint DoD Impact Level 5
description: Vue d’ensemble de l’exemple DoD Impact Level 5. Cet exemple de blueprint permet aux clients d’évaluer des contrôles DoD Impact Level 5 spécifiques.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 5b2d018cf6c986c38cbe5a1a8ea18bf4a290a4df
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035068"
---
# <a name="overview-of-the-dod-impact-level-5-blueprint-sample"></a>Vue d’ensemble de l’exemple de blueprint DoD Impact Level 5

L’exemple de blueprint Department of Defense Impact Level 5 (DoD IL5) fournit des garde-fous de gouvernance avec [Azure Policy](../../../policy/overview.md), qui vous permettent d’évaluer des contrôles DoD Impact Level 5 spécifiques. Avec ce blueprint, les clients peuvent déployer un ensemble de stratégies pour toute architecture déployée par Azure devant implémenter des contrôles DoD Impact Level 5. Pour obtenir les dernières informations sur les clouds et services Azure qui satisfont l’autorisation DoD Impact Level 5, consultez [Services Azure par étendue d’audit du FedRAMP et du DoD CC SRG](../../../../azure-government/compliance/azure-services-in-fedramp-auditscope.md).

> [!NOTE]
> Cet exemple de blueprint est disponible dans Azure Government.

## <a name="control-mapping"></a>Correspondance des contrôles

La section sur la correspondance des contrôles fournit des détails sur les stratégies incluses dans ce blueprint et décrit comment ces stratégies permettent de répondre aux différents contrôles dans DoD Impact Level 5. Quand des ressources sont attribuées à une architecture, elles sont évaluées par Azure Policy pour savoir si elles sont en conformité avec les stratégies affectées. Pour plus d’informations, consultez [Présentation d’Azure Policy](../../../policy/overview.md).

## <a name="next-steps"></a>Étapes suivantes

Vous venez de parcourir une vue d’ensemble de l’exemple de blueprint DoD Impact Level 5. Continuez avec les articles suivants pour découvrir les correspondances de contrôles et la manière de déployer cet exemple :

> [!div class="nextstepaction"]
> [Blueprint DoD Impact Level 5 - Correspondance des contrôles](./control-mapping.md)
> [Blueprint DoD Impact Level 5 - Déployer les étapes](./deploy.md)

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).