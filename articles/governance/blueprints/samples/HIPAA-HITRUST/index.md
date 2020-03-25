---
title: Vue d’ensemble de l’exemple de blueprint HIPAA HITRUST
description: Vue d’ensemble de l’exemple de blueprint HIPAA HITRUST. Cet exemple de blueprint aide les clients à évaluer des contrôles HIPAA HITRUST spécifiques.
ms.date: 12/3/2019
ms.topic: sample
ms.openlocfilehash: ebe907053b37ebe0ff99248aa181a480c045d533
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2020
ms.locfileid: "75470064"
---
# <a name="overview-of-the-hipaa-hitrust-blueprint-sample"></a>Vue d’ensemble de l’exemple de blueprint HIPAA HITRUST

L’exemple de blueprint HIPAA HITRUST fournit un ensemble de garde-fous de gouvernance avec [Azure Policy](../../../policy/overview.md) visant à tendre vers une attestation [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html). 

## <a name="blueprint-sample"></a>Exemple de blueprint

L’exemple de blueprint aide les clients à déployer un ensemble de stratégies pour toutes les architectures déployées sur Azure nécessitant une accréditation ou une conformité au framework HIPAA HITRUST. La section sur la [correspondance des contrôles](./control-mapping.md) fournit des détails sur les stratégies incluses dans cette initiative et explique comment ces stratégies permettent de répondre aux différents contrôles définis par le framework HIPAA HITRUST. Quand des ressources sont attribuées à une architecture, Azure Policy évalue leur conformité aux stratégies attribuées.

## <a name="next-steps"></a>Étapes suivantes

Vous venez de parcourir une vue d’ensemble de l’exemple de blueprint HIPAA HITRUST. Continuez avec les articles suivants pour découvrir les correspondances de contrôles et la manière de déployer cet exemple :

> [!div class="nextstepaction"]
> [Blueprint HIPAA HITRUST - Correspondance des contrôles](./control-mapping.md)
> [Blueprint HIPAA HITRUST - Déployer les étapes](./deploy.md)

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).