---
title: Définitions de stratégie pour le balisage des ressources
description: Décrit les définitions Azure Policy que vous pouvez attribuer pour garantir la conformité des étiquettes.
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: f7af799423b0aceac66dfa97e92c92d25c3916db
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456254"
---
# <a name="assign-policy-definitions-for-tag-compliance"></a>Affecter des définitions de stratégie pour la conformité des balises

Vous pouvez utiliser [Azure Policy](../../governance/policy/overview.md) pour appliquer des règles et conventions d’étiquetage. En créant une stratégie, vous pouvez éviter de déployer dans votre abonnement des ressources n’ayant pas les étiquettes attendues pour votre organisation. Au lieu d’appliquer des étiquettes ou de rechercher des ressources non conformes manuellement, vous pouvez créer une stratégie qui applique automatiquement les étiquettes nécessaires pendant le déploiement. Les étiquettes peuvent également être appliquées aux ressources existantes avec le nouvel effet [Modifier](../../governance/policy/concepts/effects.md#modify) et une [tâche de correction](../../governance/policy/how-to/remediate-resources.md). La section suivante présente des exemples de définitions de stratégie pour les étiquettes.

## <a name="policy-definitions"></a>Définitions de stratégies

[!INCLUDE [Tag policies](../../../includes/policy/reference/bycat/policies-tags.md)]

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la catégorisation des ressources, consultez [Organisation des ressources Azure à l’aide d’étiquettes](tag-resources.md).
* Les types de ressources ne prennent pas tous en charge les étiquettes. Pour déterminer si vous pouvez appliquer une étiquette à un type de ressource, consultez [Prise en charge des étiquettes pour les ressources Azure](tag-support.md).
