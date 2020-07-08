---
title: Déployer des ressources inter-abonnements et groupes de ressources
description: Montre comment cibler plusieurs groupes de ressources et des abonnements Azure pendant le déploiement.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 34de1d9df53d61d849ffbb81a57b468020bc3b65
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057379"
---
# <a name="deploy-azure-resources-across-subscriptions-or-resource-groups"></a>Déployer des ressources Azure entre des abonnements ou groupes de ressources

Resource Manager vous permet de déployer sur plusieurs groupes de ressources dans le cadre d’un seul déploiement. Vous utilisez des modèles imbriqués pour spécifier des groupes de ressources différents de celui dans l’opération de déploiement. Les groupes de ressources peuvent exister dans différents abonnements.

> [!NOTE]
> Vous pouvez déployer sur **800 groupes de ressources** en un seul déploiement. En règle générale, cette limitation signifie que vous pouvez déployer sur 1 groupe de ressources spécifié pour le modèle parent et jusqu’à 799 groupes de ressources dans les déploiements imbriqués ou liés. Toutefois, si votre modèle parent contient uniquement des modèles imbriqués ou liés et ne déploie lui-même aucune ressource, vous pouvez inclure jusqu’à 800 groupes de ressources dans les déploiements imbriqués ou liés.

## <a name="specify-subscription-and-resource-group"></a>Spécifier l’abonnement et le groupe de ressources

Pour cibler un groupe de ressources différent de celui du modèle parent, utilisez un modèle [imbriqué ou lié](linked-templates.md). Dans le type de ressource du déploiement, spécifiez des valeurs pour l’ID d’abonnement et le groupe de ressources sur lequel vous souhaitez déployer le modèle imbriqué.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Si vous ne spécifiez pas l’ID d’abonnement ni le groupe de ressources, ce sont l’abonnement et le groupe de ressources du modèle parent qui sont utilisés. Tous les groupes de ressources doivent exister avant l’exécution du déploiement.

Le compte qui déploie le modèle doit avoir l’autorisation de déployer vers l’ID d’abonnement spécifié. Si l’abonnement spécifié existe dans un autre abonné Azure Active Directory, vous devez [ajouter les utilisateurs invités à partir d’un autre répertoire](../../active-directory/b2b/what-is-b2b.md).

L’exemple suivant déploie deux comptes de stockage. Le premier compte de stockage est déployé sur le groupe de ressources spécifié lors de l’opération de déploiement. Le second compte de stockage est déployé sur le groupe de ressources spécifié dans les paramètres `secondResourceGroup` et `secondSubscriptionID` :

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Si vous définissez `resourceGroup`sur le nom d’un groupe de ressources qui n’existe pas, le déploiement échoue.

Pour tester le modèle précédent et voir les résultats, utilisez PowerShell ou Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Déployez deux comptes de stockage sur deux groupes de ressources dans le **même abonnement** en utilisant :

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Déployez deux comptes de stockage sur **deux abonnements** en utilisant :

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Déployez deux comptes de stockage sur deux groupes de ressources dans le **même abonnement** en utilisant :

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Déployez deux comptes de stockage sur **deux abonnements** en utilisant :

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>Utiliser les fonctions

Les fonctions [resourceGroup()](template-functions-resource.md#resourcegroup) et [subscription()](template-functions-resource.md#subscription) se résolvent différemment selon la façon dont vous spécifiez le modèle. Quand vous établissez un lien vers un modèle externe, les fonctions sont toujours résolues par rapport à la portée de ce modèle. Quand vous imbriquez un modèle dans un modèle parent, utilisez la propriété `expressionEvaluationOptions` pour indiquer si les fonctions sont résolues par rapport au groupe de ressources et à l’abonnement pour le modèle parent ou le modèle imbriqué. Affectez à la propriété la valeur `inner` pour effectuer la résolution par rapport à l’étendue du modèle imbriqué. Affectez à la propriété la valeur `outer` pour effectuer la résolution par rapport à l’étendue du modèle parent.

Le tableau suivant indique si les fonctions sont résolues par rapport à l’abonnement ou au groupe de ressources parent ou incorporé.

| Type de modèle | Étendue | Résolution |
| ------------- | ----- | ---------- |
| Imbriqué        | Externe (par défaut) | Groupe de ressources parent |
| Imbriqué        | interne | Sous-groupe de ressources |
| Lié        | N/A   | Sous-groupe de ressources |

L’[exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) suivant montre :

* Un modèle imbriqué avec l’étendue par défaut (externe)
* Un modèle imbriqué avec l’étendue interne
* Un modèle lié

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Pour tester le modèle précédent et voir les résultats, utilisez PowerShell ou Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

La sortie de l’exemple précédent est :

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

La sortie de l’exemple précédent est :

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Étapes suivantes

* Pour comprendre comment définir des paramètres dans votre modèle, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](template-syntax.md).
* Pour obtenir des conseils sur la résolution des erreurs courantes de déploiement, consultez la page [Résolution des erreurs courantes de déploiement Azure avec Azure Resource Manager](common-deployment-errors.md).
* Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton SAP, consultez [Déploiement d’un modèle privé avec un jeton SAP](secure-template-with-sas-token.md).
