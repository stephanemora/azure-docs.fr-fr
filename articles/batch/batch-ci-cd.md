---
title: Utiliser Azure Pipelines pour créer et déployer des solutions HPC
description: Découvrez comment déployer un pipeline de build/mise en production pour une application HPC en cours d’exécution sur Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/04/2021
ms.topic: how-to
ms.openlocfilehash: 8ea126637679974035d62cc415c4fd07f597d53f
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111961860"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Utiliser Azure Pipelines pour créer et déployer des solutions HPC

Les outils fournis par Azure DevOps peuvent entraîner la création et le test automatisés de solutions de calcul haute performance (HPC). [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) propose une gamme de processus d’intégration (CI) continue et de déploiement continu (CD) modernes pour la génération, le déploiement, le test et la surveillance des logiciels. Ces processus accélèrent la livraison des logiciels, ce qui vous permet de vous concentrer sur votre code plutôt que de prendre en charge l’infrastructure et les opérations.

Cet article explique comment configurer des processus CI/CD avec [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) pour les solutions HPC déployées sur Azure Batch.

## <a name="prerequisites"></a>Prérequis

Pour suivre la procédure décrite dans cet article, vous avez besoin d’une [organisation Azure DevOps](/azure/devops/organizations/accounts/create-organization). Vous devez également [créer un projet dans Azure DevOps](/azure/devops/organizations/projects/create-project).

Il est utile d’avoir une compréhension de base du [contrôle de code source](/azure/devops/user-guide/source-control) et de la [syntaxe des modèles Azure Resource Manager](../azure-resource-manager/templates/syntax.md) avant de commencer.

## <a name="create-an-azure-pipeline"></a>Créer un pipeline Azure

Dans cet exemple, vous allez créer un pipeline de build et de mise en production pour déployer une infrastructure Azure Batch et mettre un package d’application en production. En supposant que le code est développé localement, voici le flux de déploiement général :

![Diagramme montrant le flux de déploiement dans le pipeline.](media/batch-ci-cd/DeploymentFlow.png)

Cet exemple utilise plusieurs modèles Azure Resource Manager et des binaires existants. Vous pouvez copier ces exemples dans votre référentiel et les envoyer vers Azure DevOps.

### <a name="understand-the-azure-resource-manager-templates"></a>Présentation des modèles Azure Resource Manager

Cet exemple utilise plusieurs modèles Azure Resource Manager pour déployer la solution. Trois modèles de capacité (similaires aux unités et aux modules) sont utilisés pour implémenter une fonctionnalité spécifique. Un modèle de solution de bout en bout (deployment.json) est alors utilisé pour déployer ces modèles de capacité sous-jacents. Cette [structure de modèle liée](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) permet de tester individuellement chaque modèle de capacité et de le réutiliser pour plusieurs solutions.

![Diagramme montrant une structure de modèle liée utilisant des modèles Azure Resource Manager.](media/batch-ci-cd/ARMTemplateHierarchy.png)

Ce modèle définit un compte de stockage Azure, nécessaire pour déployer l’application sur le compte Batch. Pour plus d’informations, consultez le [Guide de référence des modèles Resource Manager pour les types de ressources Microsoft.Storage](/azure/templates/microsoft.storage/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Le modèle suivant définit un [compte Azure Batch](accounts.md). Le compte Azure Batch fonctionne comme une plateforme pour exécuter de nombreuses applications entre les [pools](nodes-and-pools.md#pools). Pour plus d’informations, consultez le [Guide de référence des modèles Resource Manager pour les types de ressources Microsoft.Batch](/azure/templates/microsoft.batch/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

Le modèle suivant crée un pool Batch dans le compte Batch. Pour plus d’informations, consultez le [Guide de référence des modèles Resource Manager pour les types de ressources Microsoft.Batch](/azure/templates/microsoft.batch/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Le modèle final fait office d’orchestrateur, déployant les trois modèles de capacité sous-jacents.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resource Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resource Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

### <a name="understand-the-hpc-solution"></a>Présentation de la solution HPC

Comme nous l’avons mentionné, cet exemple utilise plusieurs modèles Azure Resource Manager et des binaires existants. Vous pouvez copier ces exemples dans votre référentiel et les envoyer vers Azure DevOps.

Pour cette solution, le package d’application utilisé est ffmpeg. Vous pouvez [télécharger le package ffmpeg](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) si vous ne l’avez pas déjà fait.

![Capture d’écran de la structure du référentiel.](media/batch-ci-cd/git-repository.jpg)

Il existe quatre sections principales dans ce référentiel :

- Un dossier **arm-templates** contenant les modèles Azure Resource Manager
- Un dossier **hpc-application** contenant la version Windows 64 bits de [ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08)
- Un dossier **pipelines** contenant un fichier YAML qui définit le processus de pipeline de build
- Facultatif : un dossier **client-application**, qui est une copie de l’exemple [Traitement des fichiers Azure Batch .NET avec ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial). Cette application n’est pas nécessaire dans cet article.


> [!NOTE]
> Il s’agit d’un exemple d’une structure de codebase. Cette approche est utilisée pour démontrer que l’application, l’infrastructure et le code de pipeline sont stockés dans le même référentiel.

Maintenant que le code source est configuré, vous pouvez commencer le premier processus de build.

## <a name="continuous-integration"></a>Intégration continue

[Azure Pipelines](/azure/devops/pipelines/get-started/) vous permet, au sein d’Azure DevOps Services, d’implémenter un pipeline de build, de test et de déploiement pour vos applications.

Dans cette étape du pipeline, les tests sont généralement exécutés pour valider le code et générer les éléments appropriés du logiciel. Le nombre et les types de tests, ainsi que toutes les tâches supplémentaires que vous exécutez, dépendent du build le plus large et de la stratégie de publication.

## <a name="prepare-the-hpc-application"></a>Préparation de l’application HPC

Dans cette section, vous allez utiliser le dossier **hpc-application**. Ce dossier contient le logiciel (ffmpeg) qui sera exécuté dans le compte Azure Batch.

1. Accédez à la section Builds d’Azure Pipelines dans votre organisation Azure DevOps. Créez un **Nouveau pipeline**.

    ![Capture d’écran de l’écran Nouveau pipeline.](media/batch-ci-cd/new-build-pipeline.jpg)

1. Pour créer un pipeline de build, vous disposez de deux méthodes :

    a. [Utilisez le Concepteur visuel](/azure/devops/pipelines/get-started-designer). Pour cela, sélectionnez « Utiliser le Concepteur visuel » sur la page **Nouveau pipeline**.

    b. [Utilisez des builds YAML](/azure/devops/pipelines/get-started-yaml). Vous pouvez créer un pipeline YAML en cliquant sur l’option Azure Repos ou GitHub sur la page **Nouveau pipeline**. Vous pouvez également stocker l’exemple ci-dessous dans votre contrôle de code source et faire référence à un fichier YAML existant en sélectionnant le Concepteur visuel, puis en utilisant le modèle YAML.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Une fois la build configurée selon les besoins, sélectionnez **Enregistrer et mettre en file d’attente**. Si l’intégration continue avec activée (dans la section **Déclencheurs**), la build se déclenche automatiquement lorsqu’un nouveau commit est créé dans le référentiel, répondant aux conditions définies dans la build.

    ![Capture d’écran d’un pipeline de build existant.](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Affichez les mises à jour automatiques de la progression de votre build dans Azure DevOps en accédant à la section **Build** d’Azure Pipelines. Sélectionnez la build appropriée à partir de votre définition de build.

    ![Capture d’écran des sorties en direct du processus de build dans Azure DevOps.](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Si vous utilisez une application cliente pour exécuter votre solution HPC, vous devez créer une définition de build distincte pour cette application. Vous pouvez trouver différents guides pratiques dans la documentation [Azure Pipelines](/azure/devops/pipelines/get-started/index).

## <a name="continuous-deployment"></a>Déploiement continu

Azure Pipelines peut également être utilisé pour déployer votre application et l’infrastructure sous-jacente. Les [pipelines de mise en production](/azure/devops/pipelines/release) permettent un déploiement continu et automatisent le processus de mise en production.

### <a name="deploy-your-application-and-underlying-infrastructure"></a>Déploiement de l’application et de l’infrastructure sous-jacente

Il existe plusieurs étapes dans le processus de déploiement de l’infrastructure. Cette solution utilise des [modèles liés](../azure-resource-manager/templates/linked-templates.md), qui doivent donc être accessibles à partir d’un point de terminaison public (HTTP ou HTTPS). Il peut s’agir d’un référentiel sur GitHub, d’un compte de stockage d’objets Blob Azure ou d’un autre emplacement de stockage. Les artefacts du modèle téléchargé peuvent rester sécurisés, car ils peuvent être conservés en mode privé, tout en étant accessibles grâce à une sorte de jeton de signature d’accès partagé (SAS).

L’exemple suivant montre comment déployer une infrastructure avec des modèles à partir d’un objet blob Stockage Azure.

1. Créez une **Nouvelle définition de mise en production**, puis sélectionnez une définition vide. Renommez ensuite le nouvel environnement en lui donnant un nom pertinent par rapport à votre pipeline.

    ![Capture d’écran du pipeline de mise en production initial.](media/batch-ci-cd/Release-0.jpg)

1. Créez une dépendance vis-à-vis du pipeline de build pour obtenir la sortie de l’application HPC.

    > [!NOTE]
    > Notez la valeur **Alias Source**, qui sera nécessaire lors de la création de tâches à l’intérieur de la définition de mise en production.

    ![Capture d’écran montrant un lien d’artefact vers HPCApplicationPackage dans le pipeline de build approprié.](media/batch-ci-cd/Release-1.jpg)

1. Créez un lien vers un autre artefact, cette fois un référentiel Azure. Cette action est nécessaire pour accéder aux modèles Resource Manager stockés dans le référentiel. Comme les modèles Resource Manager ne nécessitent pas de compilation, vous n’avez pas besoin de les envoyer via un pipeline de build.

    > [!NOTE]
    > Une fois encore, notez la valeur **Alias Source**, qui sera nécessaire plus tard.

    ![Capture d’écran montrant un lien d’artefact vers Azure Repos.](media/batch-ci-cd/Release-2.jpg)

1. Accédez à la section **Variables**. Vous pouvez créer un certain nombre de variables dans votre pipeline afin de ne pas avoir à entrer les mêmes informations dans plusieurs tâches. Cet exemple utilise les variables suivantes :

   - **applicationStorageAccountName** : nom du compte de stockage qui contient les binaires de l’application HPC
   - **batchAccountApplicationName** : nom de l’application dans le compte Batch
   - **batchAccountName** : nom du compte Batch
   - **batchAccountPoolName** : Nom du pool de machines virtuelles qui exécutent le processus
   - **batchApplicationId** : ID unique de l’application Batch
   - **batchApplicationVersion** : version sémantique de l’application Batch (soit les binaires ffmpeg)
   - **location** : emplacement des ressources Azure à déployer
   - **resourceGroupName** : nom du groupe de ressources à créer, où les ressources seront déployées
   - **storageAccountName** : nom du compte de stockage qui contient les modèles Resource Manager liés

   ![Capture d’écran montrant l’ensemble de variables de la mise en production d’Azure Pipelines.](media/batch-ci-cd/Release-4.jpg)

1. Accédez aux tâches de l’environnement de développement. Dans la capture instantanée ci-dessous, vous pouvez voir six tâches. Ces tâches sont : télécharger les fichiers ffmpeg compressés, déployer un compte de stockage pour héberger les modèles Resource Manager imbriqués, copier ces modèles Resource Manager sur le compte de stockage, déployer le compte Batch et les dépendances requises, créer une application dans le compte Azure Batch et charger le package d’application dans le compte Azure Batch.

    ![Capture d’écran montrant les tâches utilisées pour mettre en production l’application HPC dans Azure Batch.](media/batch-ci-cd/Release-3.jpg)

1. Ajouter la tâche **Télécharger l’artefact de pipeline (préversion)** et définissez les propriétés suivantes :
    - **Nom d’affichage :** Télécharger ApplicationPackage sur l’agent
    - **Le nom de l’artefact à télécharger :** hpc-application
    - **Chemin d’accès pour télécharger** : $(System.DefaultWorkingDirectory)

1. Créez un compte de stockage pour stocker vos modèles Azure Resource Manager. Vous pouvez utiliser un compte de stockage existant de la solution. Cependant, dans le cadre de cet exemple autonome et de l’isolation du contenu, vous allez créer un compte de stockage dédié.

    Ajoutez la tâche **Déploiement de groupes de ressources Azure** et définissez les propriétés suivantes :
    - **Nom d’affichage :** Déployer un compte de stockage pour les modèles Resource Manager
    - **Abonnement Azure :** sélectionnez l’abonnement Azure correspondant.
    - **Action** : Créer ou mettre à jour un groupe de ressources
    - **Groupe de ressources** : $(resourceGroupName)
    - **Emplacement** : $(location)
    - **Modèle** : $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/storageAccount.json
    - **Remplacer les paramètres de modèle** : -accountName $(storageAccountName)

1. Chargez les artefacts du contrôle de code source vers le compte de stockage avec Azure Pipelines. Dans le cadre de cette tâche Azure Pipelines, l’URI du conteneur de compte de stockage et le jeton SAS peuvent être générés dans une variable dans Azure Pipelines, ce qui permet de les réutiliser tout au long de cette phase de l’agent.

    Ajoutez la tâche **Copie de fichiers Azure** et définissez les propriétés suivantes :
    - **Source :** $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/
    - **Type de connexion Azure** : Azure Resource Manager
    - **Abonnement Azure :** sélectionnez l’abonnement Azure correspondant.
    - **Type de destination** : Objets blob Azure
    - **Compte de stockage RM** : $(storageAccountName)
    - **Nom du conteneur** : modèles
    - **URI de conteneur de stockage** : templateContainerUri
    - **Jeton SAP du conteneur de stockage** : templateContainerSasToken

1. Déployez le modèle d’orchestrateur. Ce modèle comprend des paramètres d’URI du conteneur de compte de stockage et de jeton SAS. Les variables requises dans le modèle Resource Manager sont soit stockées dans la section Variables de la définition de mise en production, soit définies à partir d’une autre tâche Azure Pipelines (par exemple une partie de la tâche de copie d’objets blob Azure).

    Ajoutez la tâche **Déploiement de groupes de ressources Azure** et définissez les propriétés suivantes :
    - **Nom d’affichage :** Déployer Azure Batch
    - **Abonnement Azure :** sélectionnez l’abonnement Azure correspondant.
    - **Action** : Créer ou mettre à jour un groupe de ressources
    - **Groupe de ressources** : $(resourceGroupName)
    - **Emplacement** : $(location)
    - **Modèle** : $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/deployment.json
    - **Remplacer les paramètres du modèle** : `-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)`

   Une pratique courante consiste à utiliser les tâches Azure Key Vault. S’il dispose du bon ensemble de stratégies d’accès, le principal de service connecté à votre abonnement Azure peut télécharger des secrets à partir d’un coffre de clés Azure et être utilisé comme variables dans votre pipeline. Le nom de la clé secrète est défini avec la valeur associée. Par exemple, un secret sshPassword peut être référencé avec $(sshPassword) dans la définition de mise en production.

1. Les étapes suivantes appellent l’interface Azure CLI. La première sert à créer une application dans Azure Batch et à charger les packages associés.

    Ajoutez la tâche **Azure CLI** et définissez les propriétés suivantes :
    - **Nom d’affichage :** Créer une application dans un compte Azure Batch
    - **Abonnement Azure :** sélectionnez l’abonnement Azure correspondant.
    - **Emplacement du script** : Script en ligne
    - **Script en ligne** : `az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)`

1. La deuxième étape permet de charger les packages associés dans l’application (dans ce cas, les fichiers ffmpeg).

    Ajoutez la tâche **Azure CLI** et définissez les propriétés suivantes :
    - **Nom d’affichage :** Charger un package dans un compte Azure Batch
    - **Abonnement Azure :** sélectionnez l’abonnement Azure correspondant.
    - **Emplacement du script** : Script en ligne
    - **Script en ligne** : `az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip`

    > [!NOTE]
    > Le numéro de version du package d’application est défini sur une variable. Cela permet de remplacer les versions précédentes du package. Vous pouvez aussi contrôler manuellement le numéro de version du package envoyé (push) à Azure Batch.

1. Créez une nouvelle version en sélectionnant **Version > Créer une nouvelle version**. Une fois déclenchée, sélectionnez le lien vers la nouvelle version pour afficher l’état.

1. Affichez la sortie en direct de l’agent en sélectionnant le bouton **Journaux** situé au-dessous de votre environnement.

    ![Capture d’écran montrant l’état de la mise en production.](media/batch-ci-cd/Release-5.jpg)

## <a name="test-the-environment"></a>Test de l’environnement

Une fois l’environnement configuré, vérifiez que les tests suivants peuvent être effectués avec succès.

Connectez-vous au nouveau compte Azure Batch à l’aide de l’interface Azure CLI à partir d’une invite de commandes PowerShell.

- Connectez-vous à votre compte Azure avec `az login` et suivez les instructions pour vous effectuer l’authentification.
- Authentifiez maintenant le compte Batch : `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Répertorier les applications disponibles

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Vérifier que le pool est valide

```azurecli
az batch pool list
```

Notez la valeur de `currentDedicatedNodes` à partir de la sortie de cette commande. Cette valeur est ajustée dans le test suivant.

#### <a name="resize-the-pool"></a>Redimensionner le pool

Redimensionnez le pool afin que des nœuds de calcul soient disponibles pour les tests de tâches. Examinez la commande de liste de pool pour afficher l’état actuel jusqu’à ce que le redimensionnement soit terminé et les nœuds disponibles.

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment interagir avec un compte Batch par l’intermédiaire d’une application simple, consultez ces tutoriels.

- [Exécuter une charge de travail parallèle avec Azure Batch à l’aide de l’API Python](tutorial-parallel-python.md)
- [Exécuter une charge de travail parallèle avec Azure Batch à l’aide de l’API .NET](tutorial-parallel-dotnet.md)