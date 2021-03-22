---
title: 'Tutoriel : Ajouter des modules à un fichier Bicep Azure Resource Manager'
description: Utilisez des modules pour encapsuler des détails complexes de la déclaration de ressource brute.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: dfbf96f608a0cdf086f8b9eb5b8f1fc72932a772
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742857"
---
# <a name="tutorial-add-modules-to-azure-resource-manager-bicep-file"></a>Tutoriel : Ajouter des modules à un fichier Bicep Azure Resource Manager

Dans le [tutoriel précédent](bicep-tutorial-use-parameter-file.md), vous avez appris à utiliser un fichier de paramètres pour déployer votre fichier Bicep. Dans ce tutoriel, vous allez apprendre à utiliser des modules Bicep pour encapsuler des détails complexes de la déclaration de ressource brute. Les modules peuvent être partagés et réutilisés dans votre solution.  Comptez environ **12 minutes** pour suivre ce tutoriel.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prérequis

Nous vous recommandons de suivre le [tutoriel sur le fichier de paramètres](bicep-tutorial-use-parameter-file.md), mais ce n’est pas obligatoire.

Vous devez disposer de Visual Studio Code avec l’extension Bicep et, au choix, d’Azure PowerShell ou d’Azure CLI. Pour plus d’informations, consultez [Outils Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Vérifier le fichier Bicep

À la fin du précédent tutoriel, votre fichier Bicep présentait le contenu suivant :

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Ce fichier Bicep fonctionne bien. Toutefois, pour les solutions de grande taille, vous devez diviser votre fichier Bicep en plusieurs modules associés afin de pouvoir partager et réutiliser ces modules. Le fichier Bicep actuel déploie un compte de stockage, un plan App Service et un site web.  Nous allons séparer le compte de stockage dans un module.

## <a name="create-bicep-module"></a>Créer un module Bicep

Chaque fichier Bicep pouvant être consommé en tant que module, il n’existe aucune syntaxe spécifique pour la définition d’un module. Créez un fichier _storage.bicep_ avec le contenu suivant :

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/storage.bicep":::

Ce module contient la ressource du compte de stockage, ainsi que les paramètres et les variables associés. Les valeurs du paramètre _location_ et des paramètres _resourceTags_ ont été supprimées. Ces valeurs seront transmises à partir du fichier Bicep principal.

## <a name="consume-bicep-module"></a>Utiliser le module Bicep

Remplacez la définition de la ressource du compte de stockage dans le fichier _azuredeploy.bicep_ existant par le contenu Bicep suivant :

```bicep
module stg './storage.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: storagePrefix
    location: location
    resourceTags: resourceTags
  }
}
```

- **module** : mot clé.
- **nom symbolique** (stg) : il s’agit d’un identificateur pour le module.
- **fichier de module** : dans cet exemple, le chemin d’accès au module est spécifié à l’aide d’un chemin d’accès relatif (./storage.bicep). Tous les chemins d’accès dans du code Bicep doivent être spécifiés en utilisant le séparateur de répertoires de la barre oblique (/) pour garantir une compilation cohérente entre les plateformes. La barre oblique inverse (\)de Windows n’est pas pris en charge.

Pour récupérer le point de terminaison de stockage, mettez à jour la sortie dans le fichier _azuredeploy.bicep_ avec le code Bicep suivant :

```bicep
output storageEndpoint object = stg.outputs.storageEndpoint
```

Le fichier azuredeploy.bicep terminé présente le contenu suivant :

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/azuredeploy.bicep":::

## <a name="deploy-template"></a>Déployer un modèle

Utilisez Azure CLI ou Azure PowerShell pour déployer le modèle.

Si vous n’avez pas créé le groupe de ressources, consultez [Créer un groupe de ressources](bicep-tutorial-create-first-bicep.md#create-resource-group). L’exemple suppose que vous avez défini la variable `bicepFile` sur le chemin du fichier Bicep, comme indiqué dans le [premier tutoriel](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour exécuter cette cmdlet de déploiement, vous devez disposer de la [version la plus récente](/powershell/azure/install-az-ps) d’Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addmodule `
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
  --name addmodule \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
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
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**. Si vous avez terminé cette série, vous devez supprimer trois groupes de ressources : **myResourceGroup**, **myResourceGroupDev** et **myResourceGroupProd**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Félicitations, vous avez terminé cette présentation sur le déploiement de fichiers Bicep dans Azure. Faites-nous part de vos réflexions et de vos suggestions dans la section des commentaires. Merci !

La série de tutoriels suivante décrit plus en détail le déploiement de modèles.

> [!div class="nextstepaction"]
> [Ajouter des modules](./bicep-tutorial-add-modules.md)
