---
title: Intégration et livraison continues dans Azure Synapse Analytics
description: Découvrez comment utiliser l’intégration continue et la livraison continue (CI/CD) pour déployer des modifications entre des environnements dans un espace de travail Azure Synapse Analytics.
author: liudan66
ms.service: synapse-analytics
ms.subservice: cicd
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 3269bfed98264574d5b44759cceae6e1613eb5f8
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729244"
---
# <a name="continuous-integration-and-delivery-for-an-azure-synapse-analytics-workspace"></a>Intégration et livraison continues pour un espace de travail Azure Synapse Analytics

L’intégration continue (CI) est le processus d’automatisation de création et de test du code chaque fois qu’un membre de l’équipe valide une modification apportée à la gestion de version. La livraison continue (CD) est le processus de création, de test, de configuration et de déploiement de plusieurs environnements de test ou intermédiaires vers un environnement de production.

Dans un espace de travail Azure Synapse Analytics, la CI/CD déplace toutes les entités d’un environnement (développement, test, production) vers un autre environnement. La promotion de votre espace de travail vers un autre espace de travail est un processus en deux parties. Tout d’abord, utilisez un [modèle Azure Resource Manager (modèle ARM)](../../azure-resource-manager/templates/overview.md) pour créer ou mettre à jour des ressources d’espace de travail (pools et espace de travail). Ensuite, migrez les artefacts tels que les scripts et notebooks SQL, les définitions de tâche Spark, les pipelines, les jeux de données et les flux de données en utilisant les outils CI/CD Azure Synapse dans Azure DevOps ou sur GitHub. 

Cet article décrit comment utiliser un pipeline de mise en production Azure DevOps et GitHub Actions pour automatiser le déploiement d’un espace de travail Azure Synapse dans plusieurs environnements.

## <a name="prerequisites"></a>Prérequis

Pour automatiser le déploiement d’un espace de travail Azure Synapse dans plusieurs environnements, les conditions préalables et les configurations suivantes doivent être en place.

### <a name="azure-devops"></a>Azure DevOps

- Préparez un projet Azure DevOps pour l’exécution du pipeline de mise en production.
- [Accordez à tous les utilisateurs qui enregistreront le code un accès De base au niveau de l’organisation](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page&preserve-view=true), afin qu’ils puissent voir le référentiel.
- Accordez l’autorisation Propriétaire au référentiel Azure Synapse.
- Assurez-vous d’avoir créé un agent de machine virtuelle Azure DevOps auto-hébergé ou d’utiliser un agent hébergé Azure DevOps.
- Accordez les autorisations permettant de [créer une connexion au service Azure Resource Manager pour le groupe de ressources](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml&preserve-view=true).
- Un administrateur Azure Active Directory (Azure AD) doit [installer l’extension de l’agent de déploiement d’espace de travail Synapse Azure DevOps dans l’organisation Azure DevOps](/azure/devops/marketplace/install-extension).
- Créez ou nommez un compte de service pour l’exécution du pipeline. Vous pouvez utiliser un jeton d’accès personnel au lieu d’un compte de service, mais vos pipelines ne fonctionneront pas une fois le compte d’utilisateur supprimé.

### <a name="github"></a>GitHub

- Créez un référentiel GitHub qui contient les artefacts de l’espace de travail Azure Synapse et le modèle d’espace de travail. 
- Assurez-vous d’avoir créé un exécuteur auto-hébergé, ou utilisez un exécuteur hébergé par GitHub.

### <a name="azure-active-directory"></a>Azure Active Directory

- Si vous utilisez un principal de service, dans Azure AD, créez un principal de service à utiliser pour le déploiement. 
- Si vous utilisez une identité managée, activez l’identité managée affectée par le système sur votre machine virtuelle dans Azure en tant qu’agent ou exécuteur, puis ajoutez-la à Azure Synapse Studio en tant qu’administrateur Synapse.
- Utilisez le rôle d’administrateur Azure AD pour effectuer ces actions.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> Vous pouvez automatiser et déployer ces conditions préalables en utilisant le même pipeline, un modèle ARM ou Azure CLI, mais ces processus ne sont pas décrits dans cet article.

- L’espace de travail « source » utilisé pour le développement doit être configuré avec un référentiel Git dans Azure Synapse Studio. Pour plus d’informations, consultez [Contrôle de code source dans Azure Synapse Studio](source-control.md#configuration-method-2-manage-hub).

- Configurez un espace de travail vierge pour le déploiement :

  1. Créez un nouvel espace de travail Azure Synapse.
  1. Accordez l’autorisation Contributeur de l’agent de machine virtuelle et du principal de service au groupe de ressources dans lequel le nouvel espace de travail est hébergé.
  1. Dans l’espace de travail, ne configurez pas la connexion au référentiel Git.
  1. Dans le portail Azure, recherchez le nouvel espace de travail Azure Synapse, puis accordez l’autorisation Propriétaire à vous-même et à l’utilisateur qui exécutera le pipeline Azure DevOps sur l’espace de travail Azure Synapse. 
  1. Ajoutez l’agent de machine virtuelle Azure DevOps et le principal de service au rôle Contributeur de l’espace de travail. (Le rôle devrait avoir été hérité, mais vérifiez que c’est le cas.)
  1. Dans l’espace de travail Azure Synapse, accédez à **Studio** > **Gérer** > **Contrôle d’accès**. Ajoutez l’agent de machine virtuelle Azure DevOps et le principal de service au groupe d’administrateurs de l’espace de travail.
  1. Ouvrez le compte de stockage utilisé pour l’espace de travail. Dans le volet **Gestion des identités et des accès**, ajoutez l’agent de machine virtuelle et le principal de service au rôle Contributeur aux données Blob du stockage.
  1. Créez un coffre de clés dans l’abonnement au support et assurez-vous que l’espace de travail existant et le nouvel espace de travail disposent au moins des autorisations GET et LIST sur le coffre.
  1. Pour que le déploiement automatisé fonctionne, assurez-vous que toutes les chaînes de connexion spécifiées dans vos services liés se trouvent dans le coffre de clés.

### <a name="other-prerequisites"></a>Autres conditions préalables
 
- Les pools Spark et les runtimes d’intégration auto-hébergés ne sont pas créés dans une tâche de déploiement de l’espace de travail. Si vous avez un service lié qui utilise un runtime d’intégration auto-hébergé, créez manuellement le runtime dans le nouvel espace de travail.
- Si les éléments de l’espace de travail de développement sont attachés à des pools spécifiques, assurez-vous de créer ou de paramétrer les mêmes noms pour les pools de l’espace de travail cible dans le fichier de paramètres.  
- Si vos pools SQL approvisionnés sont suspendus lorsque vous tentez d’effectuer le déploiement, ce dernier peut échouer.

Pour plus d’informations, consultez [CI/CD dans Azure Synapse Analytics, partie 4 : Le pipeline de mise en production](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434). 


## <a name="set-up-a-release-pipeline-in-azure-devops"></a>Créer un pipeline de mise en production dans Azure DevOps

Dans cette section, vous apprendrez à déployer un espace de travail Azure Synapse dans Azure DevOps. 

1. Dans [Azure DevOps](https://dev.azure.com/), ouvrez le projet que vous avez créé pour la mise en production.

1. Dans le menu de gauche, sélectionnez **Pipelines** > **Mises en production**.

    :::image type="content" source="media/create-release-pipeline.png" alt-text="Capture d’écran montrant la sélection de Pipelines, puis de Mises en production dans le menu Azure DevOps.":::        
 
1. Sélectionnez **Nouveau pipeline**. Si vous avez des pipelines existants, sélectionnez **Nouveau** > **Nouveau pipeline de mise en version**.

1. Sélectionnez le modèle **Tâche vide**.

    :::image type="content" source="media/create-release-select-empty.png" alt-text="Capture d’écran montrant la sélection du modèle Tâche vide.":::

1. Dans **Nom de la phase**, entrez le nom de votre environnement.

1. Sélectionnez **Ajouter un artefact**, puis choisissez le référentiel Git qui est configuré avec Azure Synapse Studio dans votre environnement de développement. Sélectionnez le référentiel Git dans lequel vous gérez vos pools et votre modèle ARM d’espace de travail. Si vous utilisez GitHub comme source, créez une connexion de service pour votre compte GitHub et les référentiels d’extraction. Pour plus d’informations, consultez [Connexions de service](/azure/devops/pipelines/library/service-endpoints).

    :::image type="content" source="media/release-creation-github.png" alt-text="Capture d’écran montrant la sélection de GitHub pour ajouter une branche de publication pour un nouvel artefact.":::

1. Sélectionnez la branche du modèle ARM de ressource. Pour **Version par défaut**, sélectionnez **La dernière de la branche par défaut**.

    :::image type="content" source="media/release-creation-arm-template-branch.png" lightbox="media/release-creation-arm-template-branch.png" alt-text="Capture d’écran montrant la configuration de la branche du modèle ARM de ressource.":::

1. Pour la **branche par défaut** des artefacts, sélectionnez la [branche de publication](source-control.md#configure-publishing-settings) du référentiel. Par défaut, la branche de publication est `workspace_publish`. Pour **Version par défaut**, sélectionnez **La dernière de la branche par défaut**.

    :::image type="content" source="media/release-creation-publish-branch.png" alt-text="Capture d’écran montrant la configuration de la branche des artefacts.":::

### <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-a-resource"></a>Configurer une tâche intermédiaire pour un modèle ARM afin de créer et mettre à jour une ressource 

Si vous avez un modèle ARM qui déploie une ressource, telle qu’un espace de travail Azure Synapse, un pool Spark et SQL ou un coffre de clés, ajoutez une tâche de déploiement Azure Resource Manager pour créer ou mettre à jour ces ressources :

1. Dans la vue des phases, sélectionnez **Afficher les tâches de phase**.

    :::image type="content" source="media/release-creation-stage-view.png" alt-text="Capture d’écran montrant la configuration de la vue des phases.":::

1. Créer une tâche. Recherchez **Déploiement de modèles ARM**, puis sélectionnez **Ajouter**.

1. Dans l’onglet **Tâches** du déploiement, sélectionnez l’abonnement, le groupe de ressources et l’emplacement de l’espace de travail. Fournissez les informations d’identification si nécessaire.

1. Pour **Action**, sélectionnez **Créer ou mettre à jour un groupe de ressources**.

1. Pour **Modèle**, sélectionnez les points de suspension ( **…** ). Accédez au modèle ARM de l’espace de travail.

1. Pour **Paramètres du modèle**, sélectionnez le bouton **…** pour choisir le fichier de paramètres.

    :::image type="content" source="media/pools-resource-deploy.png" lightbox="media/pools-resource-deploy.png" alt-text="Capture d’écran montrant le déploiement de l’espace de travail et des pools.":::

1. Pour **Remplacer les paramètres du modèle**, sélectionnez le bouton **…** , puis entrez les valeurs de paramètres que vous souhaitez utiliser pour l’espace de travail. 

1. Pour **Mode de déploiement**, sélectionnez **Incrémentiel**.

1. (Facultatif) Ajoutez **Azure PowerShell** pour l’octroi et la mise à jour de l’attribution de rôle de l’espace de travail. Si vous utilisez un pipeline de mise en production pour créer un espace de travail Azure Synapse, le principal de service du pipeline est ajouté comme administrateur de l’espace de travail par défaut. Vous pouvez exécuter PowerShell pour accorder à d’autres comptes l’accès à l’espace de travail. 

    :::image type="content" source="media/release-creation-grant-permission.png" lightbox="media/release-creation-grant-permission.png" alt-text="Capture d’écran montrant l’exécution d’un script PowerShell pour accorder des autorisations.":::

> [!WARNING]
> En mode de déploiement complet, les ressources du groupe de ressources qui ne sont pas spécifiées dans le nouveau modèle ARM sont *supprimées*. Pour plus d’informations, consultez [Modes de déploiement Azure Resource Manager](../../azure-resource-manager/templates/deployment-modes.md).

### <a name="set-up-a-stage-task-for-azure-synapse-artifacts-deployment"></a>Configurer une tâche intermédiaire pour le déploiement d’artefacts Azure Synapse 

Utilisez l’extension de [déploiement de l’espace de travail Synapse](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) pour déployer d’autres éléments dans votre espace de travail Azure Synapse. Les éléments que vous pouvez déployer comprennent des jeux de données, des scripts et des notebooks SQL, une définition de tâche Spark, un flux de données, un pipeline, un service lié, des informations d’identification et un runtime d’intégration.  

1. Recherchez et obtenez l’extension à partir de [Visual Studio Marketplace](https://marketplace.visualstudio.com/azuredevops).

    :::image type="content" source="media/get-extension-marketplace.png" alt-text="Capture d’écran montrant l’extension de déploiement de l’espace de travail Synapse telle qu’elle apparaît dans Visual Studio Marketplace.":::

1. Sélectionnez l’organisation Azure DevOps dans laquelle vous souhaitez installer l’extension. 

    :::image type="content" source="media/install-extension.png" alt-text="Capture d’écran montrant la sélection d’une organisation dans laquelle installer l’extension de déploiement de l’espace de travail Synapse.":::

1. Assurez-vous que le principal de service du pipeline Azure DevOps a reçu l’autorisation Abonnement et qu’il est désigné comme administrateur de l’espace de travail Synapse pour l’espace de travail. 

1. Pour créer une nouvelle tâche, recherchez **Déploiement de l’espace de travail Synapse**, puis sélectionnez **Ajouter**.

    :::image type="content" source="media/add-extension-task.png" alt-text="Capture d’écran montrant la recherche de Déploiement de l’espace de travail Synapse pour créer une tâche.":::

1. Dans la tâche, à côté de **Modèle**, sélectionnez le bouton **…** pour choisir le fichier du modèle.

1. À côté de **Paramètres du modèle**, sélectionnez le bouton **…**  pour choisir le fichier de paramètres.

1. Sélectionnez une connexion, un groupe de ressources et un nom pour l’espace de travail. 

1. À côté de **Remplacer les paramètres du modèle**, sélectionnez le bouton **…** . Entrez les valeurs de paramètre que vous souhaitez utiliser pour l’espace de travail, notamment les chaînes de connexion et les clés de compte qui sont utilisées dans vos services liés. Pour plus d’informations, consultez l’article [CI/CD dans Azure Synapse Analytics](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434).

    :::image type="content" source="media/create-release-artifacts-deployment.png" lightbox="media/create-release-artifacts-deployment.png" alt-text="Capture d’écran montrant la configuration de la tâche de déploiement Synapse pour l’espace de travail.":::

> [!IMPORTANT]
> Dans les scénarios de CI/CD, le type de runtime d’intégration doit être le même dans les différents environnements. Par exemple, si vous avez un runtime d’intégration auto-hébergé dans l’environnement de développement, le même runtime d’intégration doit être auto-hébergé dans les autres environnements, tels que les environnements de test et de production. De même, si vous partagez des runtimes d’intégration entre plusieurs phases, les runtimes d’intégration doivent être liés et auto-hébergés dans tous les environnements, tels que les environnements de développement, de test et de production.

### <a name="create-a-release-for-deployment"></a>Créer une mise en production pour le déploiement 

Après avoir enregistré toutes les modifications, vous pouvez sélectionner **Créer une mise en production** pour créer manuellement une mise en production. Pour savoir comment automatiser la création d’une mise en production, consultez les [déclencheurs de mise en production Azure DevOps](/azure/devops/pipelines/release/triggers).

:::image type="content" source="media/release-creation-manually.png" lightbox="media/release-creation-manually.png" alt-text="Capture d’écran montrant le volet Nouveau pipeline de mise en production, avec l’option Créer une mise en production en évidence.":::

## <a name="set-up-a-release-in-github-actions"></a>Configurer une mise en production dans GitHub Actions 

Dans cette section, vous apprendrez à créer des workflows GitHub en utilisant GitHub Actions pour le déploiement d’un espace de travail Azure Synapse.

Vous pouvez utiliser le [modèle GitHub Actions pour Azure Resource Manager](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) pour automatiser le déploiement d’un modèle ARM sur Azure pour l’espace de travail et les pools de calcul.

### <a name="workflow-file"></a>Fichier de workflow

Définissez un workflow GitHub Actions dans un fichier YAML (.yml) sous le chemin */.github/workflows/* de votre référentiel. La définition contient les étapes et les paramètres qui composent le workflow.

Le fichier .yml comporte deux sections :

|Section  |Tâches  |
|---------|---------|
|**Authentification** | 1. Définissez un principal de service. <br /> 2. Créez un secret GitHub. |
|**Déployer** | Déployez les artefacts d’espace de travail. |

### <a name="configure-github-actions-secrets"></a>Configurer des secrets GitHub Actions

Les secrets GitHub Actions sont des variables d’environnement qui sont chiffrées. Toute personne disposant d’une autorisation Collaborateur sur ce référentiel peut utiliser ces secrets pour interagir avec Actions dans le référentiel.

1. Dans le référentiel GitHub, sélectionnez l’onglet **Paramètres**, puis sélectionnez **Secrets** > **Nouveau secret de référentiel**.

    :::image type="content" source="media/create-secret-new.png" lightbox="media/create-secret-new.png" alt-text="Capture d’écran montrant les éléments GitHub à sélectionner pour créer un nouveau un nouveau secret de référentiel.":::

1. Ajoutez un nouveau secret pour l’ID client, et ajoutez une nouvelle clé secrète client si vous utilisez le principal de service pour le déploiement. Vous pouvez également choisir d’enregistrer l’ID d’abonnement et l’ID de locataire en tant que secrets. 

### <a name="add-your-workflow"></a>Ajouter votre workflow

Dans votre référentiel GitHub, accédez à **Actions**. 

1. Sélectionnez **Configurer vous-même un workflow**.
1. Dans le fichier de workflow, supprimez tout ce qui se trouve après la section `on:`. Par exemple, votre workflow restant pourrait ressembler à cet exemple : 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Renommez votre workflow. Dans l’onglet **Marketplace**, recherchez l’action de déploiement de l’espace de travail Synapse, puis ajoutez l’action. 

    :::image type="content" source="media/search-action.png" lightbox="media/search-action.png" alt-text="Capture d’écran montrant la recherche de la tâche de déploiement de l’espace de travail Synapse sous l’onglet Marketplace.":::

1. Définissez les valeurs requises et le modèle d’espace de travail :

    ```yaml
    name: workspace deployment

    on:
        push:
            branches: [ publish_branch ]
    jobs:
        release:
            # You also can use the self-hosted runners.
            runs-on: windows-latest
            steps:
            # Checks out your repository under $GITHUB_WORKSPACE, so your job can access it.
            - uses: actions/checkout@v2
            - uses: azure/synapse-workspace-deployment@release-1.0
            with:
              TargetWorkspaceName: 'target workspace name'
              TemplateFile: './path of the TemplateForWorkspace.json'
              ParametersFile: './path of the TemplateParametersForWorkspace.json'
              OverrideArmParameters: './path of the parameters.yaml'
              environment: 'Azure Public'
              resourceGroup: 'target workspace resource group'
              clientId: ${{secrets.CLIENTID}}
              clientSecret:  ${{secrets.CLIENTSECRET}}
              subscriptionId: 'subscriptionId of the target workspace'
              tenantId: 'tenantId'
              DeleteArtifactsNotInTemplate: 'true'
              managedIdentity: 'False'
    ``` 

1. Vous êtes prêt à valider vos modifications. Sélectionnez **Start commit** (Démarrer la validation), entrez le titre, puis ajoutez une description (facultatif). Ensuite, sélectionnez **Commit new file** (Valider le nouveau fichier).

    :::image type="content" source="media/commit-workflow.png" lightbox="media/commit-workflow.png" alt-text="Capture d’écran montrant la validation du workflow dans GitHub.":::

   Le fichier apparaît dans le dossier *.github/workflows* de votre référentiel.

   > [!NOTE]
   > L’identité managée est pris en charge uniquement avec les machines virtuelles auto-hébergées dans Azure. Veillez à définir l’exécuteur sur auto-hébergé. Activez l’identité managée affectée par le système pour votre machine virtuelle, puis ajoutez-la à Azure Synapse Studio en tant qu’administrateur Synapse.

### <a name="review-your-deployment"></a>Vérifier votre déploiement

1. Dans votre référentiel GitHub, accédez à **Actions**.
1. Pour voir les journaux détaillés de l’exécution de votre workflow, ouvrez le premier résultat :

    :::image type="content" source="media/review-deploy-status.png" lightbox="media/review-deploy-status.png" alt-text="Capture d’écran montrant la sélection du journal de déploiement de l’espace de travail dans le référentiel Actions dans GitHub.":::

## <a name="create-custom-parameters-in-the-workspace-template"></a>Créer des paramètres personnalisés dans le modèle d’espace de travail 

Si vous utilisez la CI/CD automatisée et que vous souhaitez modifier certaines propriétés pendant le déploiement, mais que ces propriétés ne sont pas paramétrisées par défaut, vous pouvez remplacer le modèle de paramètres par défaut.

Pour remplacer le modèle de paramètres par défaut, créez un modèle de paramètres personnalisé nommé *template-parameters-definition.json* dans le dossier racine de votre branche de collaboration Git. Vous devez utiliser ce nom de fichier exact. Lorsque l’espace de travail Azure Synapse publie à partir de la branche de collaboration, il lit ce fichier et utilise sa configuration pour générer les paramètres. Si l’espace de travail Azure Synapse ne trouve pas ce fichier, il utilise le modèle de paramètres par défaut.

### <a name="custom-parameter-syntax"></a>Syntaxe de paramètre personnalisé

Vous pouvez utiliser les instructions suivantes pour créer un fichier de paramètres personnalisés :

* Entrez le chemin d’accès de propriété sous le type d’entité correspondant.
* Définir un nom de propriété sur `*` indique que vous souhaitez paramétrer toutes les propriétés sous la propriété en question (uniquement jusqu’au premier niveau, pas de manière récursive). Vous pouvez définir des exceptions à cette configuration.
* Définir la valeur d’une propriété sous forme de chaîne indique que vous souhaitez paramétrer la propriété. Utilisez le format `<action>:<name>:<stype>`.
   *  `<action>` peut être l’un des caractères suivants :
      * `=` permet de conserver la valeur actuelle en tant que valeur par défaut pour le paramètre.
      * `-` permet de ne pas conserver la valeur par défaut pour le paramètre.
      * `|` est un cas particulier pour les secrets Azure Key Vault pour les chaînes de connexion ou les clés.
   * `<name>` correspond au nom du paramètre. S’il est vide, il prend le nom du Si la valeur commence par un caractère `-`, le nom est abrégé. Par exemple, `AzureStorage1_properties_typeProperties_connectionString` serait abrégé en `AzureStorage1_connectionString`.
   * `<stype>` correspond au type de paramètre. Si `<stype>` est vide, le type par défaut est `string`. Valeurs prises en charge : `string`, `securestring`, `int`, `bool`, `object`, `secureobject` et `array`.
* La spécification d’un tableau dans le fichier indique que la propriété correspondante dans le modèle est un tableau. Azure Synapse itère au sein de tous les objets du tableau à l’aide de la définition spécifiée. Le second objet, une chaîne, correspond alors au nom de la propriété et sert de nom au paramètre pour chaque itération.
* Une définition ne peut pas être spécifique à une instance de ressource. Toute définition s’applique à toutes les ressources de ce type.
* Par défaut, toutes les chaînes sécurisées (telles que les secrets Key Vault) et les chaînes sécurisées (telles que les chaînes de connexion, les clés et les jetons) sont paramétrisées.

### <a name="parameter-template-definition-example"></a>Exemple de définition de modèle de paramètres 

Voici un exemple de définition de modèle de paramètres :

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

Voici une explication de la façon dont le modèle précédent est construit, par type de ressource.

**`notebooks`**

- Toute propriété du chemin d’accès `properties/bigDataPool/referenceName` est paramétrisée avec sa valeur par défaut. Vous pouvez paramétriser le pool Spark attaché pour chaque fichier de notebook. 

**`sqlscripts`**

- Dans le chemin d’accès `properties/content/currentConnection`, les propriétés `poolName` et `databaseName` sont paramétrisées en tant que chaînes sans les valeurs par défaut du modèle. 

**`pipelines`**

- Toute propriété du chemin d’accès `activities/typeProperties/waitTimeInSeconds` est paramétrisée. Toute activité dans un pipeline qui a une propriété au niveau du code nommée `waitTimeInSeconds` (par exemple, l’activité `Wait`) est paramétrable en tant que nombre, avec un nom par défaut. La propriété n’aura pas de valeur par défaut dans le modèle Resource Manager. Au lieu de cela, l’entrée de la propriété devra être saisie pendant le déploiement Resource Manager.
- La propriété `headers` (par exemple, dans une activité `Web`) est paramétrisée avec le type `object` (Objet). La propriété `headers` a une valeur par défaut qui est la même valeur que la fabrique source.

**`integrationRuntimes`**

- Toutes les propriétés du chemin `typeProperties` sont paramétrisées avec leurs valeurs par défaut respectives. Par exemple, deux propriétés existent sous les propriétés de type `IntegrationRuntimes` : `computeProperties` et `ssisProperties`. Les deux types de propriété sont créés avec leurs valeurs et types (objet) par défaut respectifs.

**`triggers`**

- Sous `typeProperties`, deux propriétés sont paramétrisées :
  - La propriété `maxConcurrency` a une valeur par défaut et est de type `string`. Le nom du paramètre par défaut de la propriété `maxConcurrency` est `<entityName>_properties_typeProperties_maxConcurrency`.
  - La propriété `recurrence` est également paramétrable. Toutes les propriétés sous la propriété `recurrence` sont définies pour être paramétrisées en tant que chaînes, avec des valeurs par défaut et des noms de paramètres. La propriété `interval` est une exception, qui est paramétrisée en tant que type `int`. Le nom du paramètre a pour suffixe `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. De même, la propriété `freq` est une chaîne et peut être paramétrée en tant que chaîne. La propriété `freq` est toutefois paramétrable sans valeur par défaut. Le nom est abrégé et suivi d’un suffixe, tel que `<entityName>_freq`.

**`linkedServices`**

- Les services liés sont uniques. Étant donné que les services liés et les jeux de données sont de types différents, vous pouvez fournir une personnalisation spécifique au type. Dans l’exemple précédent, pour tous les services liés de type `AzureDataLakeStore`, un modèle spécifique est appliqué. Pour tous les autres (identifiés par l’utilisation du caractère `*`), un modèle différent est appliqué.
- La propriété `connectionString` est paramétrisée en tant que valeur `securestring`. Elle n’a pas de valeur par défaut. Le nom du paramètre est abrégé et suivi du suffixe `connectionString`.
- La propriété `secretAccessKey` est paramétrisée en tant que valeur `AzureKeyVaultSecret` (par exemple, dans un service lié Amazon S3). La propriété est paramétrisée automatiquement en tant que secret Azure Key Vault et extraite du coffre de clés configuré. Vous pouvez également paramétriser le coffre de clés proprement dit.

**`datasets`**

- Bien que vous puissiez personnaliser les types dans les jeux de données, une configuration explicite au niveau \* n’est pas nécessaire. Dans l’exemple précédent, toutes les propriétés du jeu de données sous `typeProperties` sont paramétrables.

## <a name="best-practices-for-cicd"></a>Meilleures pratiques pour CI/CD

Si vous utilisez l’intégration de Git à votre espace de travail Azure Synapse et que vous disposez d’un pipeline CI/CD qui déplace vos modifications de l’environnement de développement à celui de test, puis à celui de production, nous vous recommandons les meilleures pratiques suivantes :

-   **Intégrez uniquement l’espace de travail de développement à Git**. Si vous utilisez l’intégration de Git, intégrez uniquement votre espace de travail Azure Synapse *de développement* à Git. Les modifications au niveau des espaces de travail de test et de production sont déployées via CI/CD et ne nécessitent pas d’intégration Git.
-   **Préparez les pools avant de migrer les artefacts**. Si vous disposez d’un script ou notebook SQL attaché à des pools dans l’espace de travail de développement, utilisez les mêmes noms de pools dans différents environnements. 
-   **Synchronisez le contrôle de version dans les scénarios « infrastructure as code »** . Pour gérer l’infrastructure (réseaux, machines virtuelles, équilibreurs de charge et topologie de connexion) dans un modèle descriptif, utilisez le même contrôle de version que celui que l’équipe DevOps utilise pour le code source. 
-   **Passez en revue les meilleures pratiques d’Azure Data Factory**. Si vous utilisez Data Factory, consultez les [meilleures pratiques relatives aux artefacts Data Factory](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd).

## <a name="troubleshoot-artifacts-deployment"></a>Résoudre les problèmes de déploiement d’artefacts 

### <a name="use-the-synapse-workspace-deployment-task"></a>Utiliser la tâche de déploiement de l’espace de travail Synapse

Dans Azure Synapse, contrairement à Data Factory, certains artefacts ne sont pas des ressources Resource Manager. Vous ne pouvez pas utiliser la tâche de déploiement d’un modèle ARM pour déployer des artefacts Azure Synapse. Utilisez plutôt la tâche de déploiement de l’espace de travail Synapse.
 
### <a name="unexpected-token-error-in-release"></a>Erreur de jeton inattendu dans la mise en production

Si votre fichier de paramètres contient des valeurs de paramètres qui ne sont pas échappées, le pipeline de mise en production ne parvient pas à analyser le fichier et génère une erreur `unexpected token`. Nous vous suggérons de remplacer les paramètres ou d’utiliser Key Vault pour récupérer les valeurs des paramètres. Vous pouvez également utiliser des caractères d’échappement doubles pour résoudre le problème.
