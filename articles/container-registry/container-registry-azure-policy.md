---
title: Conformité avec Azure Policy
description: Attribuer des stratégies intégrées dans Azure Policy pour auditer la conformité de vos registres de conteneurs Azure
ms.topic: article
ms.date: 03/01/2021
ms.openlocfilehash: 0fed0c4132043e1eaed7e634e1f45b27f7c6e933
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014296"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Auditer la conformité des registres de conteneurs Azure à l’aide d’Azure Policy

[Azure Policy](../governance/policy/overview.md) est un service d’Azure que vous utilisez pour créer, affecter et gérer des stratégies. Ces stratégies appliquent différentes règles et effets sur vos ressources, qui restent donc conformes aux normes et aux contrats de niveau de service de l’entreprise.

Cet article présente les stratégies intégrées pour Azure Container Registry. Utilisez ces stratégies pour auditer la conformité des registres nouveaux et existants.

L’utilisation d’Azure Policy est gratuite.

## <a name="built-in-policy-definitions"></a>Définitions de stratégie intégrées

Les définitions de stratégies intégrées suivantes sont spécifiques à Azure Container Registry :

[!INCLUDE [azure-policy-reference-rp-containerreg](../../includes/policy/reference/byrp/microsoft.containerregistry.md)]

## <a name="assign-policies"></a>Attribuer des stratégies

* Attribuez des stratégies à l’aide du [Portail Azure](../governance/policy/assign-policy-portal.md), d’[Azure CLI](../governance/policy/assign-policy-azurecli.md), d’un [modèle Resource Manager](../governance/policy/assign-policy-template.md) ou des Kits de développement logiciel (SDK) Azure Policy.
* Étendez une attribution de stratégie à un groupe de ressources, à un abonnement ou à un [groupe d’administration Azure](../governance/management-groups/overview.md). Les attributions de stratégies de registre de conteneurs s’appliquent aux registres de conteneurs existants et nouveaux au sein de l’étendue.
* Activez ou désactivez l’[application de la stratégie](../governance/policy/concepts/assignment-structure.md#enforcement-mode) à tout moment.

> [!NOTE]
> Une fois que vous avez attribué ou mis à jour une stratégie, l’application de l’attribution aux ressources de l’étendue définie prend un certain temps. Consultez les informations relatives aux [déclencheurs d’évaluation de la stratégie](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Vérifier la conformité de la stratégie

Accédez aux informations de conformité générées par vos affectations de stratégie à l’aide du Portail Azure, des outils en ligne de commande Azure ou des Kits de développement logiciel (SDK) Azure Policy. Pour plus d’informations, consultez [Obtenir les données de conformité des ressources Azure](../governance/policy/how-to/get-compliance-data.md).

De nombreuses raisons peuvent expliquer une ressource non conforme. Pour en déterminer la raison ou pour trouver la modification en cause, consultez [Déterminer une non-conformité](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Conformité de la stratégie dans le portail :

1. Sélectionnez **Tous les services** et recherchez **Stratégie**.
1. Sélectionnez **Conformité**.
1. Utilisez les filtres pour limiter les états de conformité ou pour rechercher des stratégies.

    ![Conformité de la stratégie dans le portail](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. Sélectionnez une stratégie pour passer en revue l’ensemble des détails et des événements relatifs à la conformité. Si vous le souhaitez, sélectionnez un registre spécifique pour la conformité des ressources.

### <a name="policy-compliance-in-the-azure-cli"></a>Conformité de la stratégie dans Azure CLI

Vous pouvez également utiliser l’interface de ligne de commande Azure pour accéder aux données de conformité. Par exemple, utilisez la commande [az policy assignment list](/cli/azure/policy/assignment#az-policy-assignment-list) dans l’interface CLI pour obtenir les ID des stratégies Azure Container Registry qui sont appliquées :

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Exemple de sortie :

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Exécutez ensuite [az policy state list](/cli/azure/policy/state#az-policy-state-list) pour retourner l’état de conformité au format JSON pour toutes les ressources sous un ID de stratégie spécifique :

```azurecli
az policy state list \
  --resource <policyID>
```

Ou exécutez [az policy state list](/cli/azure/policy/state#az-policy-state-list) pour retourner l’état de conformité au format JSON d’une ressource de registre spécifique, par exemple *myregistry* :

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [définitions](../governance/policy/concepts/definition-structure.md) et les [effets](../governance/policy/concepts/effects.md) Azure Policy.

* Créez une [définition de stratégie personnalisée](../governance/policy/tutorials/create-custom-policy-definition.md).

* En savoir plus sur les [capacités de gouvernance](../governance/index.yml) dans Azure.
