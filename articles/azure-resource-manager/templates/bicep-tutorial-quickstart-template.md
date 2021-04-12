---
title: 'Tutoriel : Utiliser des modèles de démarrage rapide pour le développement de code Bicep Azure Resource Manager'
description: Découvrez comment utiliser les modèles de démarrage rapide Azure pour terminer le développement de votre code Bicep.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: cf655885e01fe6bca99a82c82d6bbbc4c1a080b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632428"
---
# <a name="tutorial-use-azure-quickstart-templates-for-azure-resource-manager-bicep-development"></a>Tutoriel : Utiliser des modèles de démarrage rapide Azure pour le développement de code Bicep Azure Resource Manager

Les [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/) constituent un référentiel de modèles JSON fournis par la communauté. Vous pouvez utiliser les exemples de modèles dans le développement de votre code Bicep. Dans ce tutoriel, vous trouverez une définition de ressource de site web, vous la décompilerez en Bicep et vous l’ajouterez à votre fichier Bicep. Comptez environ **12 minutes** pour suivre ce tutoriel.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prérequis

Nous vous recommandons de suivre le [tutoriel sur les modèles exportés](bicep-tutorial-export-template.md), mais ce n’est pas obligatoire.

Vous devez disposer de Visual Studio Code avec l’extension Bicep et, au choix, d’Azure PowerShell ou d’Azure CLI. Pour plus d’informations, consultez [Outils Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Vérifier le fichier Bicep

À la fin du précédent tutoriel, votre fichier Bicep présentait le contenu suivant :

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep":::

Ce fichier Bicep fonctionne pour le déploiement de comptes de stockage et de plans App Service, mais vous aimeriez peut-être y ajouter un site web. Vous pouvez utiliser des modèles prédéfinis pour découvrir rapidement le code JSON nécessaire au déploiement d’une ressource. Avant que les modèles de démarrage rapide Azure proposent des exemples de code Bicep, vous pouvez utiliser des outils de conversion pour convertir des modèles JSON en fichiers Bicep.

## <a name="find-template"></a>Rechercher un modèle

Actuellement, les modèles de démarrage rapide Azure fournissent uniquement des modèles JSON. Il existe des outils que vous pouvez utiliser pour décompiler des modèles JSON en modèles Bicep.

1. Ouvrez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/).
1. Dans **Rechercher**, entrez _deploy linux web app_ (déployer une application web Linux).
1. Sélectionnez la vignette intitulée **Deploy a basic Linux web app** (Déployer une application web Linux de base). Si vous rencontrez des difficultés pour la trouver, voici le [lien direct](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Sélectionnez **Rechercher sur GitHub**.
1. Sélectionnez _azuredeploy.json_. Il s’agit du modèle que vous pouvez utiliser.
1. Sélectionnez **Raw** (Brut), puis faites une copie de l’URL.
1. Accédez à **https://bicepdemo.z22.web.core.windows.net/** , sélectionnez **Decompile** (Décompiler), puis indiquez l’URL du modèle brut.
1. Vérifiez le modèle Bicep. Recherchez en particulier la ressource `Microsoft.Web/sites`.

    ![Modèle Resource Manager - Site web de démarrage rapide](./media/bicep-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

    Si vous avez travaillé sur des modèles JSON, quelques fonctionnalités importantes de Bicep sont à noter dans cette nouvelle ressource.

    Dans les modèles JSON ARM, vous devez spécifier manuellement les dépendances des ressources avec la propriété _dependsOn_. La ressource de site web dépend de la ressource du plan App service. Avec Bicep, si vous référencez une propriété de la ressource en utilisant le nom symbolique, la propriété dependsOn est automatiquement ajoutée.

    Vous pouvez facilement référencer l’ID de ressource à partir du nom symbolique du plan App Service (appServicePlanName.id) qui sera traduit par la fonction resourceId(...) dans le modèle JSON compilé.

## <a name="revise-existing-bicep-file"></a>Réviser le fichier Bicep existant

Fusionnez le modèle de démarrage rapide décompilé avec le fichier Bicep existant. Comme vous l’avez fait dans le tutoriel précédent, mettez à jour le nom symbolique et le nom de la ressource pour qu’ils correspondent à votre convention d’affectation de noms.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep" range="1-73" highlight="20-25,28,61-71":::

Le nom de l’application web doit être unique dans Azure. Pour éviter d’avoir des noms en double, la variable `webAppPortalName` a été mise à jour en passant de `var webAppPortalName_var = '${webAppName}-webapp'` à `var webAppPortalName = '${webAppName}${uniqueString(resourceGroup().id)}'` .

## <a name="deploy-bicep-file"></a>Déployer le fichier Bicep

Utilisez Azure CLI ou Azure PowerShell pour déployer un modèle Bicep.

Si vous n’avez pas créé le groupe de ressources, consultez [Créer un groupe de ressources](bicep-tutorial-create-first-bicep.md#create-resource-group). L’exemple suppose que vous avez défini la variable **bicepFile** sur le chemin du fichier Bicep, comme indiqué dans le [premier tutoriel](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour exécuter cette cmdlet de déploiement, vous devez disposer de la [version la plus récente](/powershell/azure/install-az-ps) d’Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
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
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Si le déploiement a échoué, utilisez le commutateur `verbose` pour obtenir des informations sur les ressources en cours de création. Utilisez le commutateur `debug` pour obtenir des informations supplémentaires sur le débogage.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous passez au tutoriel suivant, vous n’avez pas besoin de supprimer le groupe de ressources.

Si vous arrêtez maintenant, vous pouvez nettoyer les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à utiliser un modèle de démarrage rapide pour le développement de votre code Bicep. Dans le prochain tutoriel, vous allez ajouter des étiquettes aux ressources.

> [!div class="nextstepaction"]
> [Ajouter des étiquettes](bicep-tutorial-add-tags.md)
