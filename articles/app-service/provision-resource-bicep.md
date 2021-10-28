---
title: Créer une application App Service à l’aide d’Azure Bicep
description: Créez votre première application pour Azure App Service en quelques secondes en utilisant Azure Bicep, qui est l’un des nombreux modes de déploiement sur App Service.
author: seligj95
ms.author: msangapu
ms.topic: article
ms.date: 8/26/2021
ms.openlocfilehash: 764a44a79ce6f892f0715dde6d657755a3048acf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257192"
---
# <a name="create-app-service-app-using-bicep"></a>Créer une application App Service à l’aide de Bicep

Commencez à utiliser [Azure App Service](overview.md) en déployant une application sur le cloud à l’aide d’un fichier [Bicep](../azure-resource-manager/bicep/index.yml) et d’[Azure CLI](/cli/azure/get-started-with-azure-cli) dans Cloud Shell. Comme vous utilisez un niveau App Service gratuit, vous pouvez suivre ce guide de démarrage rapide gratuitement.

Bicep est un langage spécifique à un domaine (DSL) qui utilise la syntaxe déclarative pour déployer des ressources Azure. Il fournit une syntaxe concise, une cohérence des types fiable et une prise en charge de la réutilisation du code. Vous pouvez utiliser Bicep au lieu de JSON pour développer vos modèles Azure Resource Manager ([modèles ARM](../azure-resource-manager/templates/overview.md)). La syntaxe JSON pour la création d’un modèle ARM peut être détaillée et nécessiter une expression complexe. La syntaxe Bicep réduit cette complexité et améliore l’expérience de développement. Bicep est une abstraction transparente sur un modèle ARM JSON et ne perd aucune des fonctionnalités de modèle JSON. Pendant le déploiement, Bicep CLI transforme un fichier Bicep en JSON de modèle ARM.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Pour créer efficacement des ressources avec Bicep, vous devez configurer un [environnement de développement](../azure-resource-manager/bicep/install.md) Bicep. L’extension Bicep pour [Visual Studio Code](https://code.visualstudio.com/) fournit la prise en charge du langage et la saisie semi-automatique des ressources. L’extension vous aide à créer et à valider des fichiers Bicep. Elle est recommandée pour les développeurs appelés à créer des ressources à l’aide de Bicep après avoir pris connaissance de ce guide de démarrage rapide.

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce guide de démarrage rapide est montré ci-dessous. Il déploie un plan de App Service et une application App Service sur Linux et un exemple d’application de Node.js « Hello World » à partir du référentiel d’[exemples Azure](https://github.com/Azure-Samples).

```bicep
param webAppName string = uniqueString(resourceGroup().id) // Generate unique String for web app name
param sku string = 'F1' // The SKU of App Service Plan
param linuxFxVersion string = 'node|14-lts' // The runtime stack of web app
param location string = resourceGroup().location // Location for all resources
param repositoryUrl string = 'https://github.com/Azure-Samples/nodejs-docs-hello-world'
param branch string = 'master'
var appServicePlanName = toLower('AppServicePlan-${webAppName}')
var webSiteName = toLower('wapp-${webAppName}')
resource appServicePlan 'Microsoft.Web/serverfarms@2020-06-01' = {
  name: appServicePlanName
  location: location
  properties: {
    reserved: true
  }
  sku: {
    name: sku
  }
  kind: 'linux'
}
resource appService 'Microsoft.Web/sites@2020-06-01' = {
  name: webSiteName
  location: location
  properties: {
    serverFarmId: appServicePlan.id
    siteConfig: {
      linuxFxVersion: linuxFxVersion
    }
  }
}
resource srcControls 'Microsoft.Web/sites/sourcecontrols@2021-01-01' = {
  name: '${appService.name}/web'
  properties: {
    repoUrl: repositoryUrl
    branch: branch
    isManualIntegration: true
  }
}
```

Trois ressources Azure sont définies dans le modèle :

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms) : créer un plan App Service.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites) : créer une application App Service.
* [**Microsoft.Web/sites/sourcecontrols**](/azure/templates/microsoft.web/sites/sourcecontrols) : créer une configuration de déploiement git externe.

Ce modèle contient plusieurs paramètres prédéfinis par souci pratique. Consultez le tableau ci-dessous pour connaître les valeurs par défaut des paramètres et leurs descriptions :

| Paramètres | Type    | Valeur par défaut                | Description |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nom de l’application |
| location   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Région de l’application |
| sku        | string  | "F1"                         | Taille de l’instance  |
| linuxFxVersion   | string  | "NODE&#124;14-LTS"       | Pile du langage de programmation &#124; Version |
| repositoryUrl    | string  | "https://github.com/Azure-Samples/nodejs-docs-hello-world"    | Dépôt Git externe (facultatif) |
| branche    | string  | « master »    | Branche par défaut pour l’exemple de code |

---

## <a name="deploy-the-template"></a>Déployer le modèle

Copiez et collez le modèle dans votre éditeur/IDE préféré, puis enregistrez le fichier dans votre répertoire de travail local.

Azure CLI est utilisé ici pour déployer le modèle. Vous pouvez également utiliser le portail Azure, Azure PowerShell ou l’API REST. Pour découvrir d’autres méthodes de déploiement, consultez [Commandes de déploiement Bicep](../azure-resource-manager/bicep/deploy-cli.md).

Le code suivant crée un groupe de ressources, un plan App Service et une application web. Un groupe de ressources, un plan App Service et une localisation par défaut ont été définis pour vous. Remplacez `<app-name>` par un nom d’application global unique (les caractères valides sont `a-z`, `0-9` et `-`).

Ouvrez un terminal sur lequel le Azure CLI est installé, puis exécutez le code ci-dessous pour créer une application Node.js sur Linux.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --template-file <path-to-template>
```

Pour déployer une autre pile de langage, mettez à jour `linuxFxVersion` avec les valeurs appropriées. Des exemples sont présentés ci-dessous. Pour afficher les versions actuelles, exécutez la commande suivante dans Cloud Shell : `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Langage    | Exemple :                                               |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.JS** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---

## <a name="validate-the-deployment"></a>Valider le déploiement

Accédez à `http://<app_name>.azurewebsites.net/` et vérifiez qu’il a été créé.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand il n’est plus nécessaire, [supprimez le groupe de ressources](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation Bicep](../azure-resource-manager/bicep/index.yml)
> [!div class="nextstepaction"]
> [Exemples Bicep pour Azure App Service](./samples-bicep.md)