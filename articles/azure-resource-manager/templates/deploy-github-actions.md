---
title: Déployer des modèles Resource Manager à l’aide de GitHub Actions
description: Décrit comment déployer des modèles Azure Resource Manager à l’aide de GitHub Actions.
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: f2e0d73c838d16c161605972b87d6f07ef8869b9
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869181"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Déployer des modèles Azure Resource Manager à l’aide de GitHub Actions

[GitHub Actions](https://help.github.com/en/actions) vous permet de créer des workflows de cycle de vie de développement logiciel personnalisés directement dans votre référentiel GitHub où sont stockées vos modèles Azure Resource Manager (ARM). Un [workflow](https://help.github.com/actions/reference/workflow-syntax-for-github-actions) est défini par un fichier YAML. Les workflows comportent une ou plusieurs tâches dont chacune contient un ensemble d’étapes qui exécutent des tâches individuelles. Les étapes peuvent exécuter des commandes ou utiliser une action. Vous pouvez créer vos propres actions ou utiliser des actions partagées par la communauté [GitHub](https://github.com/marketplace?type=actions) et les personnaliser selon vos besoins. Cet article explique comment utiliser l’option [Azure CLI Action](https://github.com/marketplace/actions/azure-cli-action) pour déployer des modèles Resource Manager.

Azure CLI Action contient deux actions dépendantes :

- **[Validation](https://github.com/marketplace/actions/checkout)**  : Consultez votre référentiel pour que le workflow puisse accéder à n’importe quel modèle Resource Manager spécifié.
- **[Connexion à Azure](https://github.com/marketplace/actions/azure-login)**  : connectez-vous avec vos informations d’identification Azure

Un workflow de base pour le déploiement d’un modèle Resource Manager peut comporter trois étapes :

1. Validez un modèle de modèle.
2. Connectez-vous à Azure.
3. Déployez un modèle Azure Resource Manager

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’un référentiel GitHub pour stocker vos modèles Resource Manager et vos fichiers de workflow. Pour en créer un, consultez [Création d’un référentiel](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository).

## <a name="configure-deployment-credentials"></a>configurer les informations d’identification de déploiement

L’action de connexion Azure utilise un principal de service pour s’authentifier auprès d’Azure. Le principal d’un workflow CI/CD a généralement besoin du droit de collaborateur intégré pour déployer des ressources Azure.

Le script Azure CLI suivant montre comment générer un principal de service Azure avec des autorisations de collaborateur sur un groupe de ressources Azure. Ce groupe de ressources correspond à l’emplacement où le workflow déploie les ressources définies dans votre modèle Resource Manager.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

Personnalisez les valeurs **$projectName** et **$location** dans le script. Le nom du groupe de ressources est le nom du projet avec **rg** ajouté. Vous devez spécifier le nom du groupe de ressources dans votre workflow.

Le script génère un objet JSON similaire à ce qui suit :

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

Copiez la sortie JSON et stockez-la en tant que secret GitHub dans votre référentiel GitHub. Consultez la rubrique [Prérequis](#prerequisites) si vous n’avez pas encore de référentiel.

1. Dans votre référentiel GitHub, sélectionnez l’onglet **Paramètres**.
1. Sélectionnez **Secrets** dans le volet gauche.
1. Saisissez les valeurs suivantes :

    - **Name** : AZURE_CREDENTIALS
    - **Valeur** : (Coller la sortie JSON)
1. Sélectionnez **Ajouter un secret**.

Vous devez spécifier le nom du secret dans le workflow.

## <a name="add-resource-manager-template"></a>Ajouter un modèle Resource Manager

Ajoutez un modèle Resource Manager au référentiel GitHub. Si vous n’en avez pas, vous pouvez utiliser le modèle suivant. Le modèle crée un compte de stockage.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Vous pouvez placer le fichier n’importe où dans le référentiel. L’exemple de workflow dans la section suivante suppose que le fichier de modèle s’appelle **azuredeploy.json** et qu’il est stocké dans un dossier **modèles** à la racine de votre référentiel.

## <a name="create-workflow"></a>Créer un workflow

Le fichier de workflow doit être stocké dans le dossier **.github/workflow** à la racine de votre référentiel. L’extension du fichier de workflow peut être **.yml** ou **.yaml**.

Vous pouvez soit créer un fichier de workflow puis envoyer/télécharger le fichier vers le référentiel, soit utiliser la procédure suivante :

1. À partir de votre référentiel GitHub, sélectionnez **Actions** dans le menu supérieur.
1. Sélectionnez **Nouveau workflow**.
1. Sélectionnez **Configurer vous-même un workflow**.
1. Renommez le fichier de workflow si vous préférez utiliser un autre nom que **main.yml**. Par exemple : **deployStorageAccount.yml**.
1. Remplacez le contenu du fichier yml par ce qui suit :

    ```yml
    name: Deploy ARM Template

    on:
      push:
        branches:
          - master
        paths:
          - ".github/workflows/deployStorageAccount.yml"
          - "templates/azuredeploy.json"

    jobs:
      deploy-storage-account-template:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout source code
            uses: actions/checkout@master

          - name: Login to Azure
            uses: azure/login@v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}


          - name: Deploy ARM Template
            uses: azure/CLI@v1
            with:
              inlineScript: |
                az deployment group create --resource-group myResourceGroup --template-file ./templates/azuredeploy.json
    ```

    Le fichier de workflow comporte trois sections :

    - **nom** : Nom du workflow.
    - **on** : nom des événements GitHub qui déclenchent le workflow. Le workflow est déclenché lorsqu’il y a un événement push sur la branche maître, qui modifie au moins l’un des deux fichiers spécifiés. Les deux fichiers sont le fichier de workflow et le modèle de fichier.

        > [!IMPORTANT]
        > Vérifiez que les deux fichiers et leurs chemins d’accès correspondent aux vôtres.
    - **jobs** : une exécution de workflow se compose d’une ou plusieurs tâches. Il n’existe qu’une seule tâche appelée **deploy-storage-account-template**.  Cette tâche comporte trois étapes :

        - **Valider le code source**.
        - **Se connecter à Azure**.

            > [!IMPORTANT]
            > Vérifiez que le nom du secret correspond à ce que vous avez enregistré dans votre référentiel. Voir [Configurer les informations d’identification de déploiement](#configure-deployment-credentials).
        - **Déployer un modèle ARM**. Remplacez la valeur de **resourceGroupName**.  Si vous avez utilisé le script Azure CLI dans [Configurer les informations d’identification de déploiement](#configure-deployment-credentials), le nom du groupe de ressources généré correspond au nom du projet et se terminant par **rg**. Vérifiez la valeur de **templateLocation**.

1. Sélectionnez **Démarrer la validation**.
1. Sélectionnez **Valider directement sur la branche maître**.
1. Sélectionnez **Valider un nouveau fichier** (ou **Valider les modifications**).

Étant donné que le workflow est configuré pour être déclenché par le fichier de workflow ou le modèle de fichier mis à jour, le workflow démarre juste après la validation des modifications.

## <a name="check-workflow-status"></a>Vérifier l’état du workflow

1. Sélectionnez l’onglet **Actions**. Un workflow **Create deployStorageAccount.yml** devrait s’afficher. L’exécution du workflow prend 1 à 2 minutes.
1. Sélectionnez le workflow pour l’ouvrir.
1. Sélectionnez **deploy-storage-account-template** (nom de la tâche) dans le menu gauche. Le nom de la tâche est défini dans le worklow.
1. Sélectionnez **Déployer un modèle ARM** (nom de l’étape) pour le développer. Vous pouvez voir la réponse de l’API REST.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir un didacticiel pas à pas vous guidant tout au long du processus de création d’un modèle, consultez :[Totoriel : Créer et déployer votre premier modèle ARM](template-tutorial-create-first-template.md).
