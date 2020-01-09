---
title: Simulation de déploiement de modèle (préversion)
description: Déterminez les modifications qui seront apportées à vos ressources avant de déployer un modèle Azure Resource Manager.
author: mumian
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jgao
ms.openlocfilehash: edb9f5e35008b1270031d8e2d5c8a5efa37cb554
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75476271"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Opération de simulation de déploiement de modèle Resource Manager (préversion)

Avant de déployer un modèle, vous souhaiterez peut-être obtenir un aperçu des modifications qui se produiront. Azure Resource Manager met à votre disposition l’opération de simulation, qui vous permet de voir comment les ressources changent si vous déployez le modèle. L’opération de simulation n’apporte aucune modification aux ressources existantes. Au lieu de cela, elle prédit les modifications si le modèle spécifié est déployé.

> [!NOTE]
> L’opération de simulation est disponible en préversion. Pour l’utiliser, vous devez [vous inscrire à la préversion](https://aka.ms/armtemplatepreviews). Dans la préversion, les résultats peuvent parfois indiquer qu’une ressource changera alors qu’aucune modification ne se produira. Nous nous efforçons de réduire ces problèmes, mais nous avons besoin de votre aide. Signalez ces problèmes à l’adresse [https://aka.ms/whatifissues](https://aka.ms/whatifissues).

Vous pouvez utiliser l’opération de simulation avec la commande PowerShell `New-AzDeploymentWhatIf` ou l’opération REST [Déploiement - Simulation](/rest/api/resources/deployments/whatif).

Dans PowerShell, la sortie ressemble à ceci :

![Opération de simulation de déploiement de modèle Resource Manager : charge utile de ressource complète et types de modification](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

## <a name="change-types"></a>Types de modification

L’opération de simulation liste six types différents de modifications :

- **Créer** : la ressource n’existe pas, mais elle est définie dans le modèle. La ressource est créée.

- **Supprimer** : ce type de modification s’applique uniquement lors de l’utilisation du [mode Complete](deployment-modes.md) pour le déploiement. la ressource existe, mais elle n’est pas définie dans le modèle. En mode Complete, la ressource est supprimée. Seules les ressources qui [prennent en charge la suppression en mode Complete](complete-mode-deletion.md) sont incluses dans ce type de modification.

- **Ignore** : la ressource existe, mais elle n’est pas définie dans le modèle. La ressource n’est pas déployée ou modifiée.

- **NoChange** : la ressource existe et est définie dans le modèle. La ressource est redéployée, mais ses propriétés ne changent pas. Ce type de modification est retourné quand [ResultFormat](#result-format) est défini sur `FullResourcePayloads`, qui est la valeur par défaut.

- **Modify** : la ressource existe et est définie dans le modèle. La ressource est redéployée et ses propriétés changent. Ce type de modification est retourné quand [ResultFormat](#result-format) est défini sur `FullResourcePayloads`, qui est la valeur par défaut.

- **Deploy** : la ressource existe et est définie dans le modèle. La ressource est redéployée. Il est possible que les propriétés de la ressource changent. L’opération retourne ce type de modification quand elle ne dispose pas de suffisamment d’informations pour déterminer si des propriétés changeront. Vous n’observez cette condition que si [ResultFormat](#result-format) est défini sur `ResourceIdOnly`.

## <a name="deployment-scope"></a>Étendue du déploiement

Vous pouvez utiliser l’opération de simulation pour les déploiements au niveau de l’abonnement ou du groupe de ressources. Vous définissez l’étendue de déploiement avec le paramètre `-ScopeType`. Les valeurs acceptées sont `Subscription` et `ResourceGroup`. Cet article présente les déploiements de groupes de ressources.

Pour découvrir les déploiements au niveau de l’abonnement, consultez [Créer des groupes de ressources et des ressources au niveau de l’abonnement](deploy-to-subscription.md#).

## <a name="result-format"></a>Format de résultat

Vous pouvez contrôler le niveau de détail retourné sur les modifications prédites. Définissez le paramètre `ResultFormat` sur `FullResourcePayloads` pour obtenir la liste des ressources qui changeront et les détails sur les propriétés qui changeront. Définissez le paramètre `ResultFormat` sur `ResourceIdOnly` pour obtenir la liste des ressources qui changeront . La valeur par défaut est `FullResourcePayloads`.  

Les captures d’écran suivantes illustrent les deux formats de sortie :

- Charges utiles de ressources complètes

    ![Opération de simulation de déploiement de modèle Resource Manager : sortie de fullresourcepayloads](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-fullresourcepayload.png)

- ID de ressource uniquement

    ![Opération de simulation de déploiement de modèle Resource Manager : sortie de resourceidonly](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-resourceidonly.png)

## <a name="run-what-if-operation"></a>Exécuter l’opération de simulation

### <a name="set-up-environment"></a>Configurer l’environnement

Pour voir comment fonctionne la simulation, nous allons exécuter des tests. Tout d’abord, déployez un modèle à partir de [Modèles de démarrage rapide Azure qui crée un compte de stockage](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json). Le type de compte de stockage par défaut est `Standard_LRS`. Vous utiliserez ce compte de stockage pour tester dans quelle mesure les modifications sont signalées par l’opération de simulation.

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

### <a name="test-modification"></a>Modification de test

Une fois le déploiement terminé, vous êtes prêt à tester l’opération de simulation. Exécutez la commande de simulation, mais modifiez le type de compte de stockage en `Standard_GRS`.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" `
  -storageAccountType Standard_GRS
```

Le résultat de l’opération de simulation ressemble à ce qui suit :

![Sortie de l’opération de simulation de déploiement de modèle Resource Manager](./media/template-deploy-what-if/resource-manager-deployment-whatif-output.png)

Notez que, en haut de la sortie, les couleurs sont définies pour indiquer le type de modifications.

En bas, la sortie indique que le nom de la référence SKU (type de compte de stockage) passera de **Standard_LRS** à **Standard_GRS**.

Certaines des propriétés répertoriées comme supprimées ne seront pas modifiées. Dans l’image précédente, ces propriétés sont accessTier, encryption.keySource et d’autres dans cette section. Les propriétés peuvent être incorrectement signalées comme supprimées lorsqu’elles ne sont pas dans le modèle, mais elles sont automatiquement définies comme valeurs par défaut lors du déploiement. Ce résultat est considéré comme du « bruit » dans la réponse de simulation. La ressource déployée finale aura les valeurs définies pour les propriétés. À mesure que l’opération de simulation évolue, ces propriétés sont exclues du résultat.

### <a name="test-deletion"></a>Suppression de test

L’opération de simulation prend en charge l’utilisation du [mode de déploiement](deployment-modes.md). En mode Complete, les ressources qui ne sont pas dans le modèle sont supprimées. L’exemple suivant déploie un [modèle qui n’a aucune ressource définie](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) en mode Complete.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Étant donné qu’aucune ressource n’est définie dans le modèle et que le mode de déploiement est défini sur Complete, le compte de stockage sera supprimé.

![Sortie de l’opération de simulation de déploiement de modèle Resource Manager : mode de déploiement Complete](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Gardez à l’esprit que l’opération de simulation n’apporte aucune modification réelle. Le compte de stockage existe toujours dans votre groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes

- Si vous constatez que la préversion de l’opération de simulation génère des résultats incorrects, signalez les problèmes à l’adresse [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Pour déployer des modèles avec Azure PowerShell, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](deploy-powershell.md).
- Pour déployer des modèles avec REST, consultez [Déployer des ressources à l’aide de modèles Resource Manager et de l’API REST Resource Manager](deploy-rest.md).
- Pour restaurer un déploiement réussi lorsque vous obtenez une erreur, consultez [Restaurer en cas d’erreur vers un déploiement réussi](rollback-on-error.md).
