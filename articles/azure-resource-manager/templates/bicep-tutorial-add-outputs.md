---
title: 'Tutoriel : Ajouter des sorties à un fichier Bicep Azure Resource Manager'
description: Ajoutez des sorties à votre fichier Bicep pour simplifier la syntaxe.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 4b7d7a1414091c516dba2c474e1681ba357b55a1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594306"
---
# <a name="tutorial-add-outputs-to-azure-resource-manager-bicep-file"></a>Tutoriel : Ajouter des sorties à un fichier Bicep Azure Resource Manager

Dans ce tutoriel, vous allez apprendre à renvoyer une valeur à partir de votre déploiement. Vous utilisez des sorties lorsque vous avez besoin d’une valeur provenant d’une ressource déployée. Ce tutoriel dure environ **7 minutes**.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prérequis

Nous vous recommandons de suivre le [tutoriel sur les variables](bicep-tutorial-add-variables.md), mais ce n’est pas obligatoire.

Vous devez disposer de Visual Studio Code avec l’extension Bicep et, au choix, d’Azure PowerShell ou d’Azure CLI. Pour plus d’informations, consultez [Outils Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Vérifier le fichier Bicep

À la fin du précédent tutoriel, votre fichier Bicep présentait le contenu suivant :

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep":::

Il déploie un compte de stockage, mais il ne retourne aucune information sur le compte de stockage. Vous aurez peut-être besoin de capturer les propriétés d’une nouvelle ressource afin de les avoir sous la main pour référence.

## <a name="add-outputs"></a>Ajouter des sorties

Vous pouvez utiliser des sorties pour renvoyer des valeurs à partir du déploiement. Par exemple, il peut être utile d’obtenir les points de terminaison pour votre nouveau compte de stockage.

L’exemple suivant montre la modification apportée à votre fichier Bicep pour ajouter une valeur de sortie. Copiez l’intégralité du fichier et remplacez votre fichier Bicep par son contenu.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep" range="1-33" highlight="33":::

Il y a quelques éléments importants à noter concernant la valeur de sortie que vous avez ajoutée.

Le type de valeur renvoyée est défini sur `object`, ce qui signifie qu’il retourne un objet de modèle.

Pour récupérer la propriété `primaryEndpoints` à partir du compte de stockage, vous devez utiliser le nom symbolique du compte de stockage. La fonctionnalité d’autocomplétion de Visual Studio Code présente la liste complète des propriétés :

   ![Propriétés d’objet de nom symbolique Bicep dans Visual Studio Code](./media/bicep-tutorial-add-outputs/visual-studio-code-bicep-output-properties.png)

## <a name="deploy-bicep-file"></a>Déployer le fichier Bicep

Vous êtes prêt à déployer le fichier Bicep et à examiner la valeur renvoyée.

Si vous n’avez pas créé le groupe de ressources, consultez [Créer un groupe de ressources](bicep-tutorial-create-first-bicep.md#create-resource-group). L’exemple suppose que vous avez défini la variable `bicepFile` sur le chemin du fichier Bicep, comme indiqué dans le [premier tutoriel](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour exécuter cette commande de déploiement, vous devez disposer de la [dernière version](/cli/azure/install-azure-cli) d’Azure CLI.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

Dans la sortie de la commande de déploiement, un objet semblable à l’exemple suivant s’affiche uniquement si la sortie est au format JSON :

```json
{
  "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
  "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
  "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
  "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
  "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
  "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> Si le déploiement a échoué, utilisez le commutateur `verbose` pour obtenir des informations sur les ressources en cours de création. Utilisez le commutateur `debug` pour obtenir des informations supplémentaires sur le débogage.

## <a name="review-your-work"></a>Passer en revue votre travail

Vous avez fait beaucoup de choses au cours des six derniers tutoriels. Prenons un moment pour regarder ensemble votre travail. Vous avez créé un fichier Bicep avec des paramètres qui sont faciles à fournir. Le fichier Bicep est réutilisable dans différents environnements parce qu’il peut être personnalisé et crée de façon dynamique les valeurs nécessaires. Il retourne également des informations sur le compte de stockage que vous pouvez utiliser dans votre script.

À présent, examinons le groupe de ressources et l’historique de déploiement.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources sur lequel vous avez effectué le déploiement.
1. Selon les étapes que vous avez effectuées, vous devez avoir au moins un compte de stockage, si ce n’est plusieurs, dans le groupe de ressources.
1. Vous devez également disposer de plusieurs déploiements réussis dans l’historique. Sélectionnez ce lien.

   ![Sélectionnez les déploiements.](./media/bicep-tutorial-add-outputs/select-deployments.png)

1. Vous voyez tous vos déploiements dans l’historique. Sélectionnez le déploiement nommé **addoutputs**.

   ![Affichage de l’historique des déploiements.](./media/bicep-tutorial-add-outputs/show-history.png)

1. Vous pouvez passer en revue les entrées.

   ![Affichage des entrées](./media/bicep-tutorial-add-outputs/show-inputs.png)

1. Vous pouvez examiner les sorties.

   ![Affichage des sorties](./media/bicep-tutorial-add-outputs/show-outputs.png)

1. Vous pouvez examiner le modèle JSON.

   ![Afficher le modèle](./media/bicep-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous passez au tutoriel suivant, vous n’avez pas besoin de supprimer le groupe de ressources.

Si vous arrêtez maintenant, vous pouvez nettoyer les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez ajouté une valeur renvoyée au fichier Bicep. Dans le tutoriel suivant, vous allez apprendre à exporter un modèle JSON et à utiliser des parties de ce modèle exporté dans votre fichier Bicep.

> [!div class="nextstepaction"]
> [Utiliser un modèle exporté](bicep-tutorial-export-template.md)
