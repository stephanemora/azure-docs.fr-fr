---
title: 'Tutoriel : Ajouter des étiquettes aux ressources dans un fichier Bicep Azure Resource Manager'
description: Ajoutez des étiquettes aux ressources que vous déployez dans vos fichiers Bicep. Les étiquettes vous permettent d’organiser logiquement les ressources.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 05bb68609705ac4c13ad77d2c74db7c105ba23fb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742841"
---
# <a name="tutorial-add-tags-in-azure-resource-manager-bicep-files"></a>Tutoriel : Ajouter des étiquettes dans des fichiers Bicep Azure Resource Manager

Dans ce tutoriel, vous apprenez à ajouter des étiquettes aux ressources dans votre fichier Bicep. Les [étiquettes](../management/tag-resources.md) vous aident à organiser logiquement vos ressources. Les valeurs des étiquettes apparaissent dans les rapports financiers. Ce tutoriel dure environ **8 minutes**.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prérequis

Nous vous recommandons de suivre le [tutoriel sur les modèles de démarrage rapide](bicep-tutorial-quickstart-template.md), mais il n’est pas obligatoire.

Vous devez disposer de Visual Studio Code avec l’extension Bicep et, au choix, d’Azure PowerShell ou d’Azure CLI. Pour plus d’informations, consultez [Outils Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Vérifier le fichier Bicep

Votre fichier Bicep précédent a déployé un compte de stockage, un plan App Service et une application web.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep":::

Après avoir déployé ces ressources, vous pouvez avoir besoin d’effectuer un suivi des coûts, et de rechercher les ressources qui appartiennent à une catégorie. La possibilité d’ajouter des étiquettes vous permet de résoudre ces problèmes.

## <a name="add-tags"></a>Ajouter des étiquettes

Vous étiquetez des ressources pour ajouter des valeurs qui vous aident à identifier leur utilisation. Par exemple, vous pouvez ajouter des étiquettes qui listent l’environnement et le projet. Vous pouvez ajouter des étiquettes qui identifient un centre de coûts ou l’équipe propriétaire de la ressource. Ajoutez des valeurs significatives pour votre organisation.

L’exemple suivant met en évidence les modifications apportées au fichier Bicep. Copiez l’intégralité du fichier et remplacez votre fichier Bicep par son contenu.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep" range="1-81" highlight="27-30,38,51,71":::

## <a name="deploy-bicep-file"></a>Déployer le fichier Bicep

Le moment est venu de déployer le fichier Bicep et d’examiner les résultats.

Si vous n’avez pas créé le groupe de ressources, consultez [Créer un groupe de ressources](bicep-tutorial-create-first-bicep.md#create-resource-group). L’exemple suppose que vous avez défini la variable `bicepFile` sur le chemin du fichier Bicep, comme indiqué dans le [premier tutoriel](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour exécuter cette cmdlet de déploiement, vous devez disposer de la [version la plus récente](/powershell/azure/install-az-ps) d’Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour exécuter cette commande de déploiement, vous devez disposer de la [dernière version](/cli/azure/install-azure-cli) d’Azure CLI.

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Si le déploiement a échoué, utilisez le commutateur `verbose` pour obtenir des informations sur les ressources en cours de création. Utilisez le commutateur `debug` pour obtenir des informations supplémentaires sur le débogage.

## <a name="verify-deployment"></a>Vérifier le déploiement

Vous pouvez vérifier le déploiement en explorant le groupe de ressources à partir du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources sur lequel vous avez effectué le déploiement.
1. Sélectionnez l’une des ressources, par exemple la ressource de compte de stockage. Vous voyez qu’elle contient désormais des étiquettes.

   ![Afficher les mots clés](./media/bicep-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous passez au tutoriel suivant, vous n’avez pas besoin de supprimer le groupe de ressources.

Si vous arrêtez maintenant, vous pouvez nettoyer les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez ajouté des étiquettes aux ressources. Dans le tutoriel suivant, vous allez apprendre à utiliser des fichiers de paramètres pour simplifier la transmission de valeurs au déploiement.

> [!div class="nextstepaction"]
> [Utiliser un fichier de paramètres](bicep-tutorial-use-parameter-file.md)
