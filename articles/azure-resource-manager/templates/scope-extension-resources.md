---
title: Étendue sur les types de ressources d’extension
description: Décrit comment utiliser la propriété scope lors du déploiement de types de ressources d’extension.
ms.topic: conceptual
ms.date: 01/13/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 0f24c37c69394358e87407245f5db41ce8b6de80
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109752046"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>Définition de l’étendue pour les ressources d’extension dans les modèles Resource Manager

Une ressource d’extension est une ressource qui modifie une autre ressource. Par exemple, vous pouvez attribuer un rôle à une ressource. L’attribution de rôle est un type de ressource d’extension.

Pour obtenir la liste complète des types de ressources d’extension, consultez [Types de ressources qui étendent les fonctionnalités d’autres ressources](../management/extension-resource-types.md).

Cet article explique comment définir l’étendue d’un type de ressource d’extension lors du déploiement avec un modèle Azure Resource Manager. Il décrit la propriété scope qui est disponible pour les ressources d’extension lorsqu’elles s’appliquent à une ressource.

> [!NOTE]
> La propriété scope est uniquement disponible pour les types de ressources d’extension. Pour spécifier une étendue différente pour un type de ressource qui n’est pas un type d’extension, utilisez un déploiement imbriqué ou lié. Pour plus d’informations, consultez [Déploiements de groupes de ressources](deploy-to-resource-group.md), [Déploiements d’abonnements](deploy-to-subscription.md), [Déploiements de groupes d’administration](deploy-to-management-group.md) et [Déploiements de locataires](deploy-to-tenant.md).

## <a name="apply-at-deployment-scope"></a>Appliquer au niveau de l’étendue du déploiement

Pour appliquer un type de ressource d’extension au niveau de l’étendue du déploiement, vous ajoutez la ressource à votre modèle, comme avec n’importe quel type de ressource. Les étendues disponibles sont un [groupe de ressources](deploy-to-resource-group.md), un [abonnement](deploy-to-subscription.md), un [groupe d’administration](deploy-to-management-group.md) et un [locataire](deploy-to-tenant.md). L’étendue de déploiement doit prendre en charge le type de ressource.

Le modèle suivant déploie un verrou.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

Lorsqu’il est déployé sur un groupe de ressources, il verrouille le groupe de ressources.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
 New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

---

L’exemple suivant attribue un rôle.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

Lorsqu’il est déployé sur un abonnement, il attribue le rôle à l’abonnement.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

---

## <a name="apply-to-resource"></a>Appliquer à la ressource

Pour appliquer une ressource d’extension à une ressource, utilisez la propriété `scope`. Définissez la propriété scope sur le nom de la ressource à laquelle vous ajoutez l’extension. La propriété scope est une propriété racine pour le type de ressource d’extension.

L’exemple suivant crée un compte de stockage et lui applique un rôle.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>Étapes suivantes

* Pour comprendre comment définir des paramètres dans votre modèle, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](template-syntax.md).
* Pour obtenir des conseils sur la résolution des erreurs courantes de déploiement, consultez la page [Résolution des erreurs courantes de déploiement Azure avec Azure Resource Manager](common-deployment-errors.md).
* Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton SAP, consultez [Déployer un modèle ARM privé avec un jeton SAP](secure-template-with-sas-token.md).
