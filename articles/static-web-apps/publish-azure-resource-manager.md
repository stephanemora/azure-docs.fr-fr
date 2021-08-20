---
title: 'Tutoriel : Publier des applications Azure Static Web Apps en utilisant un modèle ARM'
description: Créer et déployer un modèle ARM pour les applications Static Web Apps
services: static-web-apps
author: petender
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 07/13/2021
ms.author: petender
ms.openlocfilehash: b7b75fcf6f7ef1d6f2444a8fe59421bdb64c1890
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113765862"
---
# <a name="tutorial-publish-azure-static-web-apps-using-an-arm-template"></a>Tutoriel : Publier des applications Azure Static Web Apps en utilisant un modèle ARM

Cet article explique comment déployer des [applications Azure Static Web Apps](./overview.md) en utilisant un [modèle Resource Manager](../azure-resource-manager/templates/overview.md) (modèle ARM).

Ce didacticiel vous apprend à effectuer les opérations suivantes :

> [!div class="checklist"]
> - Créer un modèle ARM pour Azure Static Web Apps
> - Déployer le modèle ARM pour créer une instance Azure Static Web Apps

## <a name="prerequisites"></a>Prérequis

- **Compte Azure actif :** si vous n’en avez pas, vous pouvez [créer un compte gratuitement](https://azure.microsoft.com/free/).
- **Compte GitHub :** si vous n’en avez pas, vous pouvez [créer un compte GitHub gratuitement](https://github.com).
- **Éditeur pour les modèles ARM :** l’examen et la modification des modèles nécessitent un éditeur JSON. Visual Studio Code avec l’[extension Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) convient pour modifier les modèles ARM. Pour obtenir des instructions sur l’installation et la configuration de Visual Studio Code, consultez [Démarrage rapide : Créer des modèles ARM avec Visual Studio Code](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md).

- **Azure CLI ou Azure PowerShell** : le déploiement de modèles ARM nécessite un outil en ligne de commande. Pour les instructions d’installation, consultez :
  - [Installer Azure CLI sur un système d’exploitation Windows](/cli/azure/install-azure-cli-windows)
  - [Installer Azure CLI sur un système d’exploitation Linux](/cli/azure/install-azure-cli-linux)
  - [Installer Azure CLI sur macOS](/cli/azure/install-azure-cli-macos)
  - [Installation d'Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="create-a-github-personal-access-token"></a>Créer un jeton d’accès personnel GitHub

L’un des paramètres du modèle ARM est `repositoryToken`, qui permet au processus de déploiement ARM d’interagir avec le dépôt GitHub contenant le code source du site statique. 

1. À partir de votre profil de compte GitHub (en haut à droite), sélectionnez **Settings**.

1. Sélectionnez **Developer settings**.

1. Sélectionnez **Personal access tokens**.

1. Sélectionnez **Generate new token**.

1. Donnez un nom à ce jeton dans le champ _Note_, par exemple *myfirstswadeployment*.

1. Spécifiez les *étendues* suivantes : **repo, workflow, write:packages**

1. Sélectionnez **Generate token**.

1. Copiez la valeur du jeton dans un éditeur de texte à des fins d’utilisation ultérieure.

> [!IMPORTANT]
> Veillez à copier ce jeton et à le stocker dans un emplacement sûr. Vous pouvez stocker ce jeton dans [Azure Key Vault](../azure-resource-manager/templates/template-tutorial-use-key-vault.md) et y accéder dans votre modèle ARM.

## <a name="create-a-github-repo"></a>Créer un dépôt GitHub

Cet article utilise un dépôt de modèles GitHub pour vous permettre de démarrer facilement. Le modèle comporte une application de démarrage utilisée pour le déploiement avec Azure Static Web Apps.

1. Accédez à l’emplacement suivant pour créer un nouveau dépôt :
    1. [https://github.com/staticwebdev/vanilla-basic/generate](https://github.com/login?return_to=/staticwebdev/vanilla-basic/generate)

1. Nommez votre dépôt **myfirstswadeployment**.

    > [!NOTE]
    > Azure Static Web Apps nécessite au moins un fichier HTML pour pouvoir créer une application web. Le référentiel que vous créez lors de cette étape comprend un seul _fichier index.html_.

1. Sélectionnez **Create repository from template** (Créer un dépôt à partir du modèle).

    :::image type="content" source="./media/getting-started/create-template.png" alt-text="Créer un référentiel à partir du modèle":::

## <a name="create-the-arm-template"></a>Créer le modèle ARM

Une fois les prérequis en place, l’étape suivante consiste à définir le fichier de modèle de déploiement ARM.

1. Créez un dossier destiné à contenir les modèles ARM.

1. Créez un fichier et nommez-le **azuredeploy.json**.

1. Collez l’extrait de modèle ARM suivant dans _azuredeploy.json_.

    ```json
        {
            "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "name": {
                    "type": "string"
                },
                "location": {
                    "type": "string"
                },
                "sku": {
                    "type": "string"
                },
                "skucode": {
                    "type": "string"
                },
                "repositoryUrl": {
                    "type": "string"
                },
                "branch": {
                    "type": "string"
                },
                "repositoryToken": {
                    "type": "securestring"
                },
                "appLocation": {
                    "type": "string"
                },
                "apiLocation": {
                    "type": "string"
                },
                "appArtifactLocation": {
                    "type": "string"
                },
                "resourceTags": {
                    "type": "object"
                },
                "appSettings": {
                    "type": "object"
                }
            },
            "resources": [
                {
                    "apiVersion": "2021-01-15",
                    "name": "[parameters('name')]",
                    "type": "Microsoft.Web/staticSites",
                    "location": "[parameters('location')]",
                    "tags": "[parameters('resourceTags')]",
                    "properties": {
                        "repositoryUrl": "[parameters('repositoryUrl')]",
                        "branch": "[parameters('branch')]",
                        "repositoryToken": "[parameters('repositoryToken')]",
                        "buildProperties": {
                            "appLocation": "[parameters('appLocation')]",
                            "apiLocation": "[parameters('apiLocation')]",
                            "appArtifactLocation": "[parameters('appArtifactLocation')]"
                        }
                    },
                    "sku": {
                        "Tier": "[parameters('sku')]",
                        "Name": "[parameters('skuCode')]"
                    },
                    "resources":[
                        {
                            "apiVersion": "2021-01-15",
                            "name": "appsettings",
                            "type": "config",
                            "location": "[parameters('location')]",
                            "properties": "[parameters('appSettings')]",
                            "dependsOn": [
                                "[resourceId('Microsoft.Web/staticSites', parameters('name'))]"
                            ]
                        }
                    ]
                }
            ]
        }

    ```

1. Créez un fichier et nommez-le **azuredeploy.parameters.json**.

1. Collez l’extrait de modèle ARM suivant dans _azuredeploy.parameters.json_.

    ```json
        {
            "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "name": {
                    "value": "myfirstswadeployment"
                },
                "location": { 
                    "value": "Central US"
                },   
                "sku": {
                    "value": "Free"
                },
                "skucode": {
                    "value": "Free"
                },
                "repositoryUrl": {
                    "value": "https://github.com/<YOUR-GITHUB-USER-NAME>/<YOUR-GITHUB-REPOSITORY-NAME>"
                },
                "branch": {
                    "value": "main"
                },
                "repositoryToken": {
                    "value": "<YOUR-GITHUB-PAT>" 
                },
                "appLocation": {
                    "value": "/"
                },
                "apiLocation": {
                    "value": ""
                },
                "appArtifactLocation": {
                    "value": "public"
                },
                "resourceTags": {
                    "value": {
                        "Environment": "Development",
                        "Project": "Testing SWA with ARM",
                        "ApplicationName": "myfirstswadeployment"
                    }
                },
                "appSettings": {
                    "value": {
                        "MY_APP_SETTING1": "value 1",
                        "MY_APP_SETTING2": "value 2"
                    }
                }
            }
        }
    ```

1. Mettez à jour les paramètres suivants.

    | Paramètre | Valeur attendue |
    | --- | --- |
    | `repositoryUrl` | Indiquez l’URL de votre dépôt GitHub Static Web Apps. |
    | `repositoryToken` | Indiquez le jeton PAT GitHub. |

1. Enregistrez les mises à jour avant d’exécuter le déploiement à l’étape suivante.

## <a name="running-the-deployment"></a>Exécution du déploiement

Vous avez besoin d’Azure CLI ou d’Azure PowerShell pour déployer le modèle.

### <a name="sign-in-to-azure"></a>Connexion à Azure

Pour déployer un modèle, connectez-vous à Azure CLI ou à Azure PowerShell.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

---

Si vous avez plusieurs abonnements Azure, sélectionnez celui que vous souhaitez utiliser. Remplacez `<SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>` par les informations de votre abonnement :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription <SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext <SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>
```

---

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Quand vous déployez un modèle, vous indiquez un groupe de ressources qui contient des ressources connexes. Avant d’exécuter la commande de déploiement, créez le groupe de ressources avec Azure CLI ou Azure PowerShell.

> [!NOTE]
> Les exemples CLI de cet article sont écrits pour l’interpréteur de commandes Bash.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
resourceGroupName="myfirstswadeployRG"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = "myfirstswadeployRG"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

---

## <a name="deploy-template"></a>Déployer un modèle

Utilisez l’une de ces options de déploiement pour déployer le modèle.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file <PATH-TO-AZUREDEPLOY.JSON> \
  --parameters <PATH-TO-AZUREDEPLOY.PARAMETERS.JSON> \
  --verbose
```

Pour en découvrir plus sur le déploiement de modèles en utilisant Azure CLI, consultez [Déployer des ressources à l’aide de modèles ARM et d’Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$templateparameterFile = Read-Host -Prompt "Enter the template parameter file path and file name"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -TemplateParameterFile $templateparameterfile `
  -verbose
```

Pour en découvrir plus sur le déploiement de modèles en utilisant Azure PowerShell, consultez [Déployer des ressources à l’aide de modèles ARM et d’Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md).

---

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

Nettoyez les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer votre application web statique](./configuration.md)