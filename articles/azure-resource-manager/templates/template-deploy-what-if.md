---
title: Simulation de déploiement de modèle (préversion)
description: Déterminez les modifications qui seront apportées à vos ressources avant de déployer un modèle Azure Resource Manager.
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: b9d4150779842614a5dc284a2b3a489593fabfe1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388516"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Opération de simulation de déploiement de modèle Resource Manager (préversion)

Avant de déployer un modèle, vous souhaiterez peut-être obtenir un aperçu des modifications qui se produiront. Azure Resource Manager met à votre disposition l’opération de simulation, qui vous permet de voir comment les ressources changent si vous déployez le modèle. L’opération de simulation n’apporte aucune modification aux ressources existantes. Au lieu de cela, elle prédit les modifications si le modèle spécifié est déployé.

> [!NOTE]
> L’opération de simulation est disponible en préversion. Pour l’utiliser, vous devez [vous inscrire à la préversion](https://aka.ms/armtemplatepreviews). Dans la préversion, les résultats peuvent parfois indiquer qu’une ressource changera alors qu’aucune modification ne se produira. Nous nous efforçons de réduire ces problèmes, mais nous avons besoin de votre aide. Signalez ces problèmes à l’adresse [https://aka.ms/whatifissues](https://aka.ms/whatifissues).

Vous pouvez utiliser l’opération de simulation avec les commandes PowerShell ou des opérations de l’API REST.

Dans PowerShell, la sortie comprend des résultats à code de couleurs qui vous permettent de voir les différents types de modifications.

![Opération de simulation de déploiement de modèle Resource Manager : charge utile de ressource complète et types de modification](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

La sortie de texte est :

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

## <a name="what-if-commands"></a>Commandes de simulation

Vous pouvez utiliser Azure PowerShell ou l’API REST Azure pour l’opération de simulation.

### <a name="azure-powershell"></a>Azure PowerShell

Pour afficher un aperçu des modifications avant de déployer un modèle, ajoutez le paramètre de commutateur `-Whatif` à la commande de déploiement.

* `New-AzResourceGroupDeployment -Whatif` pour des déploiements de groupes de ressources
* `New-AzSubscriptionDeployment -Whatif` et `New-AzDeployment -Whatif` pour des déploiements au niveau de l’abonnement

Vous pouvez utiliser le paramètre de commutateur `-Confirm` pour afficher un aperçu des modifications et être invité à poursuivre le déploiement.

* `New-AzResourceGroupDeployment -Confirm` pour des déploiements de groupes de ressources
* `New-AzSubscriptionDeployment -Confirm` et `New-AzDeployment -Confirm` pour des déploiements au niveau de l’abonnement

Les commandes précédentes retournent un résumé sous forme de texte que vous pouvez inspecter manuellement. Pour obtenir un objet que vous pouvez inspecter par programme pour y détecter des modifications, utilisez :

* `$results = Get-AzResourceGroupDeploymentWhatIf` pour des déploiements de groupes de ressources
* `$results = Get-AzSubscriptionDeploymentWhatIf` ou `$results = Get-AzDeploymentWhatIf` pour des déploiements au niveau de l’abonnement

> [!NOTE]
> Avant la mise en production de la version 2.0.1-alpha5, vous utilisiez la commande `New-AzDeploymentWhatIf`. Cette commande a été remplacée par les commandes `Get-AzDeploymentWhatIf`, `Get-AzResourceGroupDeploymentWhatIf` et `Get-AzSubscriptionDeploymentWhatIf`. Si vous avez utilisé une version antérieure, vous devez mettre à jour cette syntaxe. Le paramètre `-ScopeType` a été supprimé.

### <a name="azure-rest-api"></a>API REST Azure

Pour l’API REST, utilisez :

* [Déploiements – Simulation](/rest/api/resources/deployments/whatif) pour les déploiements de groupes de ressources
* [Déploiements – Simulation au niveau de l’étendue d’abonnement](/rest/api/resources/deployments/whatifatsubscriptionscope) pour les déploiements au niveau de l’abonnement

## <a name="change-types"></a>Types de modification

L’opération de simulation liste six types différents de modifications :

- **Créer** : la ressource n’existe pas, mais elle est définie dans le modèle. La ressource est créée.

- **Supprimer** : ce type de modification s’applique uniquement lors de l’utilisation du [mode Complete](deployment-modes.md) pour le déploiement. la ressource existe, mais elle n’est pas définie dans le modèle. En mode Complete, la ressource est supprimée. Seules les ressources qui [prennent en charge la suppression en mode Complete](complete-mode-deletion.md) sont incluses dans ce type de modification.

- **Ignore** : la ressource existe, mais elle n’est pas définie dans le modèle. La ressource n’est pas déployée ou modifiée.

- **NoChange** : la ressource existe et est définie dans le modèle. La ressource est redéployée, mais ses propriétés ne changent pas. Ce type de modification est retourné quand [ResultFormat](#result-format) est défini sur `FullResourcePayloads`, qui est la valeur par défaut.

- **Modify** : la ressource existe et est définie dans le modèle. La ressource est redéployée et ses propriétés changent. Ce type de modification est retourné quand [ResultFormat](#result-format) est défini sur `FullResourcePayloads`, qui est la valeur par défaut.

- **Deploy** : la ressource existe et est définie dans le modèle. La ressource est redéployée. Il est possible que les propriétés de la ressource changent. L’opération retourne ce type de modification quand elle ne dispose pas de suffisamment d’informations pour déterminer si des propriétés changeront. Vous n’observez cette condition que si [ResultFormat](#result-format) est défini sur `ResourceIdOnly`.

## <a name="result-format"></a>Format de résultat

Vous pouvez contrôler le niveau de détail retourné sur les modifications prédites. Dans les commandes de déploiement (`New-Az*Deployment`), utilisez le paramètre **-WhatIfResultFormat**. Dans les commandes d’objet programmatique (`Get-Az*DeploymentWhatIf`), utilisez le paramètre **ResultFormat**.

Définissez le paramètre de format sur **FullResourcePayloads** pour obtenir la liste des ressources qui changeront et des détails sur les propriétés qui changeront. Définissez le paramètre de format sur **ResourceIdOnly** pour obtenir la liste des ressources qui changeront. La valeur par défaut est **FullResourcePayloads**.  

Les résultats suivants illustrent les deux formats de sortie :

- Charges utiles de ressources complètes

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- ID de ressource uniquement

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Exécuter l’opération de simulation

### <a name="set-up-environment"></a>Configurer l’environnement

Pour voir comment fonctionne la simulation, nous allons exécuter des tests. Tout d’abord, déployez un [modèle de qui crée un réseau virtuel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Vous utiliserez ce réseau virtuel pour tester dans quelle mesure les modifications sont signalées par l’opération de simulation.

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Modification de test

Une fois le déploiement terminé, vous êtes prêt à tester l’opération de simulation. Cette fois, déployez un [modèle qui modifie le réseau virtuel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Il manque une des balises d’origine, un sous-réseau a été supprimé et le préfixe de l’adresse a changé.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

La sortie de simulation ressemble à ceci :

![Sortie de l’opération de simulation de déploiement de modèle Resource Manager](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

La sortie de texte est :

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Notez que, en haut de la sortie, les couleurs sont définies pour indiquer le type de modifications.

Au bas de la sortie apparaît la balise indiquant que le propriétaire a été supprimé. Le préfixe d’adresse est passé de 10.0.0.0/16 à 10.0.0.0/15. Le sous-réseau nommé subnet001 a été supprimé. N’oubliez pas que ces modifications n’ont pas été réellement déployées. Vous voyez un aperçu des modifications qui se produiront si vous déployez le modèle.

Certaines des propriétés répertoriées comme supprimées ne seront pas modifiées. Les propriétés peuvent être incorrectement signalées comme supprimées lorsqu’elles ne sont pas dans le modèle, mais elles sont automatiquement définies comme valeurs par défaut lors du déploiement. Ce résultat est considéré comme du « bruit » dans la réponse de simulation. La ressource déployée finale aura les valeurs définies pour les propriétés. À mesure que l’opération de simulation évolue, ces propriétés sont exclues du résultat.

## <a name="programmatically-evaluate-what-if-results"></a>Évaluer par programmation les résultats de l’opération de simulation

À présent, nous allons évaluer par programmation les résultats de l’opération de simulation en définissant la commande sur une variable.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Vous pouvez voir un résumé de chaque modification.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>Confirmer la suppression

L’opération de simulation prend en charge l’utilisation du [mode de déploiement](deployment-modes.md). En mode Complete, les ressources qui ne sont pas dans le modèle sont supprimées. L’exemple suivant déploie un [modèle qui n’a aucune ressource définie](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) en mode Complete.

Pour afficher un aperçu des modifications avant de déployer un modèle, utilisez le paramètre de commutateur `-Confirm` avec la commande de déploiement. Si les modifications sont bien celles que vous attendiez, confirmez que vous souhaitez que le déploiement s’accomplisse.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Étant donné qu’aucune ressource n’est définie dans le modèle et que le mode de déploiement est défini sur Complet, le réseau virtuel sera supprimé.

![Sortie de l’opération de simulation de déploiement de modèle Resource Manager : mode de déploiement Complete](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

La sortie de texte est :

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Vous voyez les modifications attendues et pouvez confirmer que vous souhaitez que le déploiement s’exécute.

## <a name="next-steps"></a>Étapes suivantes

- Si vous constatez que la préversion de l’opération de simulation génère des résultats incorrects, signalez les problèmes à l’adresse [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Pour déployer des modèles avec Azure PowerShell, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](deploy-powershell.md).
- Pour déployer des modèles avec REST, consultez [Déployer des ressources à l’aide de modèles Resource Manager et de l’API REST Resource Manager](deploy-rest.md).
