---
title: Utiliser des Pipelines d’Azure pour créer et déployer des solutions HPC - Azure Batch | Microsoft Docs
description: Découvrez comment déployer un pipeline de build/mise en production pour une application HPC en cours d’exécution sur Azure Batch.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.openlocfilehash: 5b7c44d3ea3394ff728adfb9d9fd72293138fb2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880856"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Utiliser des Pipelines d’Azure pour créer et déployer des solutions HPC

Les services de DevOps Azure proposent une gamme d’outils utilisé par les équipes de développement lors de la création d’une application personnalisée. Outils fournis par Azure DevOps peuvent se traduire automatisés de génération et de test de solutions de calcul hautes performances. Cet article montre comment configurer une intégration continue (CI) et le déploiement continu (CD) à l’aide de que pipelines d’Azure pour des performances élevées de calcul solution déployée sur Azure Batch.

Les Pipelines Azure fournit une gamme de processus de livraison continue modernes pour la création, de déploiement, de test et le logiciel de surveillance. Ces processus accélèrent la livraison de logiciels, ce qui vous permet de vous concentrer sur votre code plutôt que de prendre en charge d’infrastructure et les opérations.

## <a name="create-an-azure-pipeline"></a>Créer un Pipeline Azure

Dans cet exemple, nous créer une build et mise en production de pipeline pour déployer une infrastructure d’Azure Batch et de libérer un package d’application. En supposant que le code est développé localement, il s’agit du flux de déploiement général :

![Diagramme montrant le flux de déploiement dans notre Pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Paramétrage

Pour suivre les étapes décrites dans cet article, vous avez besoin d’une organisation Azure DevOps et un projet d’équipe.

* [Création d’une organisation Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Créez un projet dans Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Contrôle de code source pour votre environnement

Contrôle de code source permet aux équipes de suivre les modifications apportées à la base de code et inspecter les versions précédentes du code.

En règle générale, contrôle de code source est considérée de pair avec le code de logiciel. Que diriez-vous l’infrastructure sous-jacente ? Cela nous amène à l’Infrastructure en tant que Code, où nous allons utiliser des modèles Azure Resource Manager ou autres solutions open source pour définir de façon déclarative notre infrastructure sous-jacente.

Cet exemple s’appuie fortement sur un nombre de modèles Resource Manager (Documents JSON) et les fichiers binaires existants. Vous pouvez copier ces exemples dans votre référentiel et les envoient vers Azure DevOps.

La structure de base de code utilisée dans cet exemple ressemble à celui-ci ;

* Un **les modèles arm** dossier contenant un nombre de modèles Azure Resource Manager. Les modèles sont expliquées dans cet article.
* Un **application cliente** dossier, qui est une copie de la [Azure .NET fichier de traitement par lot avec ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) exemple. Cela n’est pas nécessaire pour cet article.
* Un **hpc-application** dossier, c'est-à-dire Windows 64 bits version de [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* Un **pipelines** dossier. Il contient un fichier YAML en mode plan de notre processus de génération. Ce sujet est abordé dans cet article.

Cette section suppose que vous êtes familiarisé avec les modèles de Resource Manager de conception et de contrôle de version. Si vous n’êtes pas familiarisé avec ces concepts, consultez les pages suivantes pour plus d’informations.

* [Qu’est le contrôle de code source ?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Modèles Microsoft Azure Resource Manager

Cet exemple s’appuie sur plusieurs modèles Resource Manager pour déployer notre solution. Pour ce faire, nous utilisons un nombre de modèles de fonctionnalité (semblables à unités ou modules) qui implémentent une partie spécifique des fonctionnalités. Nous utilisons également un modèle de solution de bout en bout qui est chargé de remettre les sous-jacent ensemble de fonctionnalités. Il existe quelques avantages de cette approche :

* Les modèles de fonctionnalité sous-jacent peuvent être individuellement de test unitaire.
* Les modèles de fonctionnalité sous-jacent peuvent être définis comme une norme à l’intérieur d’une organisation et être réutilisés dans plusieurs solutions.

Pour cet exemple, il existe un modèle de solution de bout en bout (Deployment.JSON ainsi) qui déploie les trois modèles. Les modèles sous-jacents sont des modèles de fonctionnalité, responsables du déploiement d’un aspect spécifique de la solution.

![Exemple de Structure de modèle lié à l’aide de modèles Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

Le premier modèle que nous allons examiner est pour un compte de stockage Azure. Notre solution requiert un compte de stockage pour déployer l’application sur notre compte Batch. Il est important de connaître le [guide de référence de modèle Resource Manager pour les types de ressources Microsoft.Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) lors de la création de modèles Resource Manager pour les comptes de stockage.

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

Ensuite, nous allons examiner le modèle de compte Azure Batch. Le compte de traitement par lots Azure agit comme une plateforme pour exécuter de nombreuses applications entre pools (regroupement d’ordinateurs). Il est important de connaître le [guide de référence de modèle Resource Manager pour les types de ressources Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) lors de la création de modèles Resource Manager pour les comptes Batch.

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

Le modèle suivant montre un exemple de création d’un Pool Azure Batch (les ordinateurs de serveur principal pour traiter de nos applications). Il est important de connaître le [guide de référence de modèle Resource Manager pour les types de ressources Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) lors de la création de modèles Resource Manager pour les Pools du compte Batch.

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

Enfin, nous proposons un modèle qui se comporte comme un orchestrateur. Ce modèle est responsable du déploiement des modèles de la fonctionnalité.

Vous pouvez également savoir plus sur [création de modèles Azure Resource Manager liés](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md) dans un article distinct.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
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

#### <a name="the-hpc-solution"></a>La Solution HPC

L’infrastructure et les logiciels peuvent définis en tant que code et résidant dans le même référentiel.

Pour cette solution, le ffmpeg est utilisé en tant que le package d’application. Le package ffmpeg peut être téléchargé [ici](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Exemple de Structure de référentiel Git](media/batch-ci-cd/git-repository.jpg)

Il existe quatre sections principales à ce référentiel :

* Le **les modèles arm** dossier qui stocke de notre Infrastructure en tant que Code
* Le **hpc-application** dossier qui contient les fichiers binaires pour ffmpeg
* Le **pipelines** dossier qui contient la définition pour notre pipeline de build.
* **Facultatif** : Le **application cliente** dossier qui serait de stocker le code d’application .NET. Nous n’utilisons pas cela dans l’exemple, mais dans votre propre projet, vous pouvez pour l’exécution de l’Application de traitement par lots HPC via une application cliente.

> [!NOTE]
> Il s’agit juste un exemple d’une structure à une base de code. Cette approche est utilisée pour les besoins de la démonstration que les applications, l’infrastructure et code de pipeline sont stockés dans le même référentiel.

Maintenant que le code source est configuré, nous pouvons commencer la première génération.

## <a name="continuous-integration"></a>Intégration continue

[Les Pipelines Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), au sein d’Azure DevOps Services, vous permet d’implémenter un pipeline de génération, de test et de déploiement pour vos applications.

Dans cette étape de votre pipeline, les tests sont généralement exécutés pour valider le code et générer les éléments appropriés du logiciel. Le nombre et les types de tests et toutes les tâches que vous exécutez dépend de votre build plus large et stratégie de version.

## <a name="preparing-the-hpc-application"></a>Préparation de l’application HPC

Dans cet exemple, nous allons nous concentrer sur la **hpc-application** dossier. Le **hpc-application** dossier est le logiciel de ffmpeg qui sera exécuté à partir du compte Azure Batch.

1. Accédez à la section de Builds de Pipelines d’Azure dans votre organisation Azure DevOps. Créer un **nouveau pipeline**.

    ![Créer un Pipeline de Build](media/batch-ci-cd/new-build-pipeline.jpg)

1. Vous avez deux options pour créer un pipeline de Build :

    a. [À l’aide du concepteur visuel](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Pour cela, cliquez sur « Utiliser le concepteur visuel » sur le **nouveau pipeline** page.

    b. [À l’aide de YAML génère](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Vous pouvez créer un pipeline YAML en cliquant sur l’option de GitHub sur la nouvelle page de pipeline ou de référentiels d’Azure. Vous pouvez également stocker l’exemple ci-dessous dans votre contrôle de code source et référencer un fichier YAML existant en cliquant sur le concepteur visuel, puis l’aide du modèle YAML.

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

1. Une fois la build est configurée en fonction des besoins, sélectionnez **Enregistrer & file d’attente**. Si vous avez activée l’intégration continue (dans le **déclencheurs** section), la build déclenche automatiquement lorsqu’une nouvelle validation dans le référentiel est effectuée, répondant aux conditions définie dans la build.

    ![Exemple d’un Pipeline de Build existant](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Afficher les mises à jour automatiques sur la progression de votre build dans Azure DevOps en accédant à la **Build** section des Pipelines d’Azure. Sélectionnez la build appropriée à partir de votre définition de build.

    ![Afficher les sorties en direct à partir de votre build](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Si vous utilisez une application cliente pour exécuter votre Application de traitement par lots HPC, vous devez créer une définition de build distinctes pour l’application. Vous pouvez trouver un nombre de guides de procédures dans les [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) documentation.

## <a name="continuous-deployment"></a>Déploiement continu

Les Pipelines Azure est également utilisé pour déployer votre application et l’infrastructure sous-jacente. [Mise en production de pipelines](https://docs.microsoft.com/azure/devops/pipelines/release/what-is-release-management?view=azure-devops) est le composant qui permet un déploiement continu et automatise votre processus de publication.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Déploiement de votre application et l’infrastructure sous-jacente

Il existe un nombre d’étapes impliquées dans le déploiement de l’infrastructure. Comme nous avons utilisé [modèles liés](../azure-resource-manager/resource-group-linked-templates.md), ces modèles devra être accessible à partir d’un point de terminaison public (HTTP ou HTTPS). Cela peut être un référentiel sur GitHub, ou un compte de stockage d’objets Blob Azure ou un autre emplacement de stockage. Les artefacts téléchargés modèle peuvent rester sécurisées, car elles peuvent être conservées dans un mode privé, mais accessibles à l’aide d’une forme de jeton de signature d’accès partagé. L’exemple suivant montre comment déployer une infrastructure avec des modèles à partir d’un objet blob Azure Storage.

1. Créer un **nouvelle définition de version**, puis sélectionnez une définition vide. Nous devons ensuite renommer l’environnement qui vient d’être créé en quelque chose d’utile pour notre pipeline.

    ![Pipeline de la version initiale](media/batch-ci-cd/Release-0.jpg)

1. Créez une dépendance sur le Pipeline de Build pour obtenir la sortie pour notre application HPC.

    > [!NOTE]
    > Une fois encore, notez le **Alias Source**, comme cela est nécessaire lorsque les tâches sont créées à l’intérieur de la définition de version.

    ![Créer un lien d’artefact vers le HPCApplicationPackage dans le pipeline de build appropriée](media/batch-ci-cd/Release-1.jpg)

1. Créer un lien vers un autre artefact, cette fois, un référentiel Azure. Cela est nécessaire pour accéder aux modèles Resource Manager stockés dans votre référentiel. Comme les modèles Resource Manager ne nécessitent pas de compilation, vous n’avez pas besoin de les transmettre via un pipeline de build.

    > [!NOTE]
    > Une fois encore, notez le **Alias Source**, comme cela est nécessaire lorsque les tâches sont créées à l’intérieur de la définition de version.

    ![Créer un lien d’artefact vers les dépôts de Azure](media/batch-ci-cd/Release-2.jpg)

1. Accédez à la **variables** section. Il est recommandé de créer un nombre de variables dans votre pipeline, donc vous n’êtes pas entrer les mêmes informations en plusieurs tâches. Voici les variables utilisées dans cet exemple, et leur impact sur le déploiement.

    * **applicationStorageAccountName**: Nom du compte de stockage pour contenir les fichiers binaires d’application HPC
    * **batchAccountApplicationName**: Nom de l’application dans le compte Azure Batch
    * **batchAccountName**: Nom du compte Azure Batch
    * **batchAccountPoolName**: Nom du pool de machines virtuelles qui effectue le traitement
    * **batchApplicationId**: ID unique pour l’application Azure Batch
    * **batchApplicationVersion**: Version sémantique de votre application de lot (autrement dit, les fichiers binaires ffmpeg)
    * **location** : Emplacement pour les ressources Azure à déployer
    * **resourceGroupName**: Nom du groupe de ressources doit être créé, et où vos ressources seront déployés
    * **storageAccountName** : Nom du compte de stockage pour contenir les modèles Resource Manager liés

    ![Exemple de variables définies pour la version de Pipelines d’Azure](media/batch-ci-cd/Release-4.jpg)

1. Accédez aux tâches pour l’environnement de développement. Dans le sous instantané, vous pouvez voir des six tâches. Ces tâches seront : télécharger les fichiers ZIP ffmpeg, de déployer un compte de stockage pour héberger les modèles Resource Manager imbriquées, copiez ces modèles Resource Manager pour le compte de stockage, déployer le compte batch et les dépendances requises, créez une application dans le compte Azure Batch et le téléchargement du package d’application au compte Azure Batch.

    ![Exemple de tâches utilisée pour libérer de l’Application HPC dans Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Ajouter le **télécharger Pipeline d’artefact (version préliminaire)** de tâches et définissez les propriétés suivantes :
    * **Nom complet :** Téléchargez ApplicationPackage à l’Agent
    * **Le nom de l’artefact à télécharger :** -application hpc
    * **Chemin d’accès pour télécharger sur**: $ (System.defaultworkingdirectory)

1. Créer un compte de stockage pour stocker vos artefacts. Un compte de stockage existant à partir de la solution peut être utilisé, mais pour l’exemple autonome et l’isolation du contenu, nous effectuons un compte de stockage dédié pour notre artefacts (en particulier les modèles Resource Manager).

    Ajouter le **déploiement de groupe de ressources Azure** de tâches et définissez les propriétés suivantes :
    * **Nom complet :** Déployer un compte de stockage pour les modèles Resource Manager
    * **Abonnement Azure :** Sélectionnez l’abonnement Azure approprié
    * **Action** : Créer ou mettre à jour un groupe de ressources
    * **Groupe de ressources**: $ (resourcegroupname)
    * **Emplacement**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **Remplacer les paramètres de modèle**: accountName - $(storageAccountName)

1. Téléchargez les artefacts à partir du contrôle de code Source dans le compte de stockage. Il existe une tâche Azure Pipeline pour effectuer cette. Dans le cadre de cette tâche, l’URL de conteneur du compte de stockage et le jeton SAP peuvent être générés à une variable dans les Pipelines d’Azure. Cela signifie qu’il peut être réutilisé tout au long de cette phase d’agent.

    Ajouter le **copie de fichiers Azure** de tâches et définissez les propriétés suivantes :
    * **Source:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **Type de connexion Azure**: Azure Resource Manager
    * **Abonnement Azure :** Sélectionnez l’abonnement Azure approprié
    * **Type de destination**: objet blob Azure
    * **Compte de stockage RM**: $(storageAccountName)
    * **Nom du conteneur**: modèles
    * **URI de conteneur de stockage**: templateContainerUri
    * **Jeton SAP de conteneur de stockage**: templateContainerSasToken

1. Déployez le modèle d’orchestrator. Rappelez-vous le modèle orchestrator précédemment, vous remarquerez que les paramètres pour l’URL de conteneur de compte de stockage, en plus du jeton SAS. Vous devriez remarquer que les variables nécessaires dans le modèle Resource Manager sont maintenus soit dans la section des variables de la définition de version, ou ont été définies à partir d’une autre tâche de Pipelines d’Azure (par exemple, la partie de la tâche de copie d’objets Blob Azure).

    Ajouter le **déploiement de groupe de ressources Azure** de tâches et définissez les propriétés suivantes :
    * **Nom complet :** Déployer Azure Batch
    * **Abonnement Azure :** Sélectionnez l’abonnement Azure approprié
    * **Action** : Créer ou mettre à jour un groupe de ressources
    * **Groupe de ressources**: $ (resourcegroupname)
    * **Emplacement**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **Remplacer les paramètres de modèle**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Une pratique courante consiste à utiliser les tâches d’Azure Key Vault. Si des stratégies d’accès appropriée est définie pour le Principal de Service (connexion à votre abonnement Azure), il peut télécharger les secrets à partir d’un coffre de clés Azure et être utilisé en tant que variables dans votre pipeline. Le nom de la clé secrète est défini avec la valeur associée. Par exemple, un secret de sshPassword peut faire référence avec $(sshPassword) dans la définition de version.

1. Les étapes suivantes appellent l’interface CLI. La première est utilisée pour créer une application dans Azure Batch. et télécharger les packages associés.

    Ajouter le **Azure CLI** de tâches et définissez les propriétés suivantes :
    * **Nom complet :** Créer l’application dans un compte Azure Batch
    * **Abonnement Azure :** Sélectionnez l’abonnement Azure approprié
    * **Emplacement du script**: Script inline
    * **Script inline**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. La deuxième étape est utilisée pour charger des packages associés à l’application. Dans notre cas, les fichiers ffmpeg.

    Ajouter le **Azure CLI** de tâches et définissez les propriétés suivantes :
    * **Nom complet :** Télécharger le package à un compte Azure Batch
    * **Abonnement Azure :** Sélectionnez l’abonnement Azure approprié
    * **Emplacement du script**: Script inline
    * **Script inline**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Le numéro de version du package d’application est défini à une variable. C’est pratique si vous en remplaçant les versions précédentes du package convient, et si vous souhaitez contrôler manuellement le numéro de version du package vers Azure Batch.

1. Créez une nouvelle version en sélectionnant **Release > créez une nouvelle version**. Une fois déclenché, sélectionnez le lien vers la nouvelle version pour afficher l’état.

1. Vous pouvez afficher la sortie en direct à partir de l’agent en sélectionnant le **journaux** bouton en dessous de votre environnement.

    ![Afficher l’état de votre version](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>L’environnement de test

Une fois que l’environnement est configuré, vérifiez que les tests suivants peuvent être effectués avec succès.

Se connecter pour le nouveau compte Azure Batch, à l’aide de l’interface CLI à partir d’une invite de commandes PowerShell.

* Connectez-vous à votre compte Azure avec `az login` et suivez les instructions pour l’authentification.
* Désormais s’authentifier le compte Batch : `az batch account login -g <resourceGroup> -n <batchAccount>`

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

Redimensionner le pool et n’est donc disponible pour le travail et le test de la tâche de nœuds de calcul, vérifiez auprès de la commande de liste de pool pour afficher l’état actuel jusqu'à ce que le redimensionnement est terminée et les nœuds disponibles

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Étapes suivantes

En plus de cet article, il existe deux didacticiels qui utilisent ffmpeg, à l’aide de .NET et Python. Consultez ces didacticiels pour plus d’informations sur la façon d’interagir avec un compte Batch via une application simple.

* [Exécuter une charge de travail parallèle avec Azure Batch à l’aide de l’API Python](tutorial-parallel-python.md)
* [Exécuter une charge de travail parallèle avec Azure Batch à l’aide de l’API .NET](tutorial-parallel-dotnet.md)
