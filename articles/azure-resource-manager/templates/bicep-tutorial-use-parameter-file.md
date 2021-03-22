---
title: 'Tutoriel : Utiliser un fichier de paramètres pour déployer un fichier Bicep Azure Resource Manager'
description: Utilisez les fichiers de paramètres qui contiennent les valeurs à utiliser pour le déploiement de votre fichier Bicep.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 233bab26d659fef2da9a9f5a7080d4b3ecbce7d4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742847"
---
# <a name="tutorial-use-parameter-files-to-deploy-azure-resource-manager-bicep-file"></a>Tutoriel : Utiliser des fichiers de paramètres pour déployer un fichier Bicep Azure Resource Manager

Dans ce tutoriel, vous apprenez à utiliser des [fichiers de paramètres](parameter-files.md) pour stocker les valeurs que vous transmettez pendant le déploiement. Dans les tutoriels précédents, vous avez utilisé des paramètres incorporés à votre commande de déploiement. Cette approche fonctionnait pour tester votre fichier Bicep, mais, lors de l’automatisation des déploiements, il peut être plus facile de passer un ensemble de valeurs pour votre environnement. Les fichiers de paramètres facilitent la création de package des valeurs de paramètre pour un environnement spécifique. Vous utilisez le même fichier de paramètres JSON que pour le déploiement d’un modèle JSON. Dans ce tutoriel, vous allez créer des fichiers de paramètres pour les environnements de développement et de production. Comptez environ **12 minutes** pour suivre ce tutoriel.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prérequis

Nous vous recommandons de suivre le [tutoriel sur les étiquettes](bicep-tutorial-add-tags.md), mais ce n’est pas obligatoire.

Vous devez disposer de Visual Studio Code avec l’extension Bicep et, au choix, d’Azure PowerShell ou d’Azure CLI. Pour plus d’informations, consultez [Outils Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Vérifier le fichier Bicep

Votre fichier Bicep contient de nombreux paramètres que vous pouvez fournir pendant le déploiement. À la fin du tutoriel précédent, votre fichier Bicep ressemblait à ceci :

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Ce fichier Bicep fonctionne bien, mais vous souhaitez maintenant gérer sans peine les paramètres que vous transmettez pour le fichier Bicep.

## <a name="add-parameter-files"></a>Ajouter des fichiers de paramètres

Les fichiers de paramètres sont des fichiers JSON dont la structure est similaire à celle des modèles JSON. Dans le fichier, vous fournissez les valeurs de paramètre que vous souhaitez transmettre pendant le déploiement.

Dans le fichier de paramètres, vous fournissez des valeurs pour les paramètres de votre fichier Bicep. Le nom de chaque paramètre dans votre fichier de paramètres doit correspondre au nom d’un paramètre dans votre fichier Bicep. Le nom ne respecte pas la casse, mais, pour voir facilement les valeurs correspondantes, nous vous recommandons de faire correspondre la casse du fichier Bicep.

Vous n’avez pas besoin de fournir une valeur pour chaque paramètre. Si un paramètre non spécifié a une valeur par défaut, cette valeur est utilisée pendant le déploiement. Si un paramètre n’a pas de valeur par défaut et qu’il n’est pas spécifié dans le fichier de paramètres, vous êtes invité à fournir une valeur lors du déploiement.

Vous ne pouvez pas spécifier un nom de paramètre dans votre fichier de paramètres qui ne correspond pas à un nom de paramètre dans le fichier Bicep. Vous obtenez une erreur quand des paramètres inconnus sont fournis.

Dans Visual Studio Code, créez un fichier avec le contenu suivant. Enregistrez le fichier sous le nom _azuredeploy.parameters.dev.json_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Il s’agit de votre fichier de paramètres pour l’environnement de développement. Notez qu’il utilise **Standard_LRS** pour le compte de stockage, nomme les ressources avec un préfixe **dev** et définit l’étiquette `Environment` sur **Dev**.

Encore une fois, créez un fichier avec le contenu suivant. Enregistrez le fichier sous le nom _azuredeploy.parameters.prod.json_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Il s’agit de votre fichier de paramètres pour l’environnement de production. Notez qu’il utilise **Standard_GRS** pour le compte de stockage, nomme les ressources avec un préfixe **contoso** et définit l’étiquette `Environment` sur **Production**. Dans un environnement de production réel, vous pouvez également avoir envie d’utiliser un service d’application avec une référence SKU payante, cependant, pour les besoins de ce tutoriel, nous allons continuer à utiliser cette référence.

## <a name="deploy-bicep-file"></a>Déployer le fichier Bicep

Utilisez Azure CLI ou Azure PowerShell pour déployer le fichier Bicep.

Dans ce tutoriel, nous allons créer deux nouveaux groupes de ressources. L’un pour l’environnement de développement, et l’autre pour l’environnement de production.

Pour les variables de modèle et de paramètre, remplacez `{path-to-the-bicep-file}`, `{path-to-azuredeploy.parameters.dev.json}`, `{path-to-azuredeploy.parameters.prod.json}` et les accolades `{}` par les chemins à vos fichiers de paramètres et fichiers Bicep.

Tout d’abord, nous allons procéder au déploiement dans l’environnement de développement.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour exécuter cette cmdlet de déploiement, vous devez disposer de la [version la plus récente](/powershell/azure/install-az-ps) d’Azure PowerShell.

```azurepowershell
$bicepFile = "{path-to-the-bicep-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $bicepFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour exécuter cette commande de déploiement, vous devez disposer de la [dernière version](/cli/azure/install-azure-cli) d’Azure CLI.

```azurecli
bicepFile="{path-to-the-bicep-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $bicepFile \
  --parameters $devParameterFile
```

---

À présent, nous allons procéder au déploiement dans l’environnement de production.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> Si le déploiement a échoué, utilisez le commutateur `verbose` pour obtenir des informations sur les ressources en cours de création. Utilisez le commutateur `debug` pour obtenir des informations supplémentaires sur le débogage.

## <a name="verify-deployment"></a>Vérifier le déploiement

Vous pouvez vérifier le déploiement en explorant les groupes de ressources à partir du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.
1. Vous voyez les deux nouveaux groupes de ressources que vous avez déployés dans ce tutoriel.
1. Sélectionnez un groupe de ressources et affichez les ressources déployées. Remarquez qu’elles correspondent aux valeurs que vous avez spécifiées dans votre fichier de paramètres pour cet environnement.

## <a name="clean-up-resources"></a>Nettoyer les ressources

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous utilisez un fichier de paramètres pour déployer votre fichier Bicep. Dans le tutoriel suivant, vous apprendrez à modulariser vos fichiers Bicep.

> [!div class="nextstepaction"]
> [Ajouter des modules](./bicep-tutorial-add-modules.md)
