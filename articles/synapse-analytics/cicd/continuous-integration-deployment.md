---
title: Intégration et livraison continues pour l’espace de travail Synapse
description: Découvrez comment utiliser l’intégration et la livraison continues pour déployer les changements dans un espace de travail d’un environnement (développement, test, production) à un autre.
author: liudan66
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 833478d956560c981bd6cc3ba03b48bb602f563c
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739671"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Intégration et livraison continues pour l’espace de travail Azure Synapse

## <a name="overview"></a>Vue d’ensemble

L’intégration continue (CI) est le processus d’automatisation de création et de test du code chaque fois qu’un membre de l’équipe valide les modifications apportées au contrôle de version. Le déploiement continu (CD) est le processus de création, de test, de configuration et de déploiement de plusieurs environnements de test ou intermédiaires dans un environnement de production.

Dans un espace de travail Microsoft Azure Synapse Analytics, l’intégration continue et la livraison continue (CI/CD) déplacent toutes les entités d’un environnement (développement, test, production) à un autre. Pour promouvoir votre espace de travail vers un autre espace de travail, il y a deux parties. Tout d’abord, utilisez un [modèle Azure Resource Manager (modèle ARM)](../../azure-resource-manager/templates/overview.md) pour créer ou mettre à jour des ressources d’espace de travail (pools et espace de travail). Ensuite, migrez les artefacts (scripts SQL, notebook, définition de tâche Spark, pipelines, jeux de données, flux de données, etc.) dans Azure DevOps avec les outils CI/CD d’Azure Synapse Analytics. 

Cet article décrit comment utiliser un pipeline de mise en production Azure DevOps pour automatiser le déploiement d’un espace de travail Azure Synapse Analytics dans plusieurs environnements.

## <a name="prerequisites"></a>Prérequis

Ces prérequis et configurations doivent être en place pour automatiser le déploiement d’un espace de travail Azure Synapse Analytics dans plusieurs environnements.

### <a name="azure-devops"></a>Azure DevOps

- Un projet Azure DevOps a été préparé pour exécuter le pipeline de mise en production.
- [Accordez à tous les utilisateurs qui enregistreront le code un accès « de base » au niveau de l’organisation](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page), afin qu’ils puissent voir le référentiel.
- Accordez des droits de propriétaire au référentiel Azure Synapse Analytics.
- Assurez-vous d’avoir créé un agent de machine virtuelle Azure DevOps auto-hébergé ou d’utiliser un agent hébergé Azure DevOps.
- Assurez-vous d’avoir les autorisations permettant de [créer une connexion au service Azure Resource Manager pour le groupe de ressources](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml).
- Un administrateur Azure Active Directory (Azure AD) doit [installer l’extension de l’agent de déploiement d’espace de travail Synapse Azure DevOps dans l’organisation Azure DevOps](/azure/devops/marketplace/install-extension).
- Créez ou nommez un compte de service pour l’exécution du pipeline. Vous pouvez utiliser un jeton d’accès personnel au lieu d’un compte de service, mais vos pipelines ne fonctionneront pas une fois le compte d’utilisateur supprimé.

### <a name="azure-active-directory"></a>Azure Active Directory

- Dans Azure AD, créez un principal de service à utiliser pour le déploiement. La tâche de déploiement de l’espace de travail Synapse ne prend pas en charge l’utilisation d’une identité managée dans les versions 1* et antérieures.
- Des droits d’administrateur Azure AD sont nécessaires pour cette action.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> Vous pouvez automatiser et déployer ces composants requis à l’aide du même pipeline, d’un modèle ARM ou de l’interface de ligne de commande Azure, mais le processus n’est pas décrit dans cet article.

- L’espace de travail « source » utilisé pour le développement doit être configuré avec un référentiel Git dans Synapse Studio. Pour plus d’informations, consultez [Contrôle de code source dans Synapse Studio](source-control.md#configuration-method-2-manage-hub).

- Un espace de travail vide vers lequel effectuer le déploiement. Pour configurer l’espace de travail vide :

  1. Créez un espace de travail Azure Synapse Analytics.
  1. Accordez les droits de contributeur de l’agent de machine virtuelle et du principal de service au groupe de ressources dans lequel le nouvel espace de travail est hébergé.
  1. Dans le nouvel espace de travail, ne configurez pas la connexion au référentiel Git.
  1. Dans le portail Azure, recherchez le nouvel espace de travail Azure Synapse Analytics et accordez à vous-même et à toute personne qui exécutera le pipeline Azure DevOps les droits de propriétaire sur l’espace de travail Azure Synapse Analytics. 
  1. Ajoutez l’agent de machine virtuelle Azure DevOps et le principal du service au rôle Contributeur de l’espace de travail (cela devrait avoir été hérité, mais vérifiez que c’est le cas).
  1. Dans l’espace de travail Azure Synapse Analytics, accédez à **Studio** > **Gérer** > **Gestion des identités et des accès**. Ajoutez l’agent de machine virtuelle Azure DevOps et le principal du service au groupe d’administrateurs de l’espace de travail.
  1. Ouvrez le compte de stockage utilisé pour l’espace de travail. Dans Gestion des identités et des accès, ajoutez l’agent de machine virtuelle et le principal du service au rôle Contributeur aux données Blob du stockage.
  1. Créez un coffre de clés dans l’abonnement au support et assurez-vous que l’espace de travail existant et le nouvel espace de travail disposent au moins des autorisations GET et LIST sur le coffre.
  1. Pour que le déploiement automatisé fonctionne, assurez-vous que toutes les chaînes de connexion spécifiées dans vos services liés se trouvent dans le coffre de clés.

### <a name="additional-prerequisites"></a>Autres composants requis
 
 - Les pools Spark et les runtimes d’intégration auto-hébergé ne sont pas créés dans un pipeline. Si vous avez un service lié qui utilise un runtime d’intégration auto-hébergé, créez-le manuellement dans le nouvel espace de travail.
 - Si vous développez des notebooks et qu’ils sont connectés à un pool Spark, recréez le pool Spark dans l’espace de travail.
 - Les notebooks qui sont liés à un pool Spark qui n’existe pas dans un environnement ne seront pas déployés.
 - Les noms des pools Spark doivent être les mêmes dans les deux espaces de travail.
 - Nommez toutes les bases de données, tous les pools SQL et toutes les autres ressources de la même façon dans les deux espaces de travail.
 - Si vos pools SQL approvisionnés sont suspendus lorsque vous tentez d’effectuer le déploiement, ce dernier peut échouer.

Pour plus d’informations, consultez [CI/CD dans Azure Synapse Analytics, partie 4 : Le pipeline de mise en production](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434). 


## <a name="set-up-a-release-pipeline"></a>Configurer un pipeline de mise en production

1.  Dans [Azure DevOps](https://dev.azure.com/), ouvrez le projet créé pour la mise en production.

1.  Sur le côté gauche de la page, sélectionnez **Pipelines**, puis sélectionnez **Versions**.

    ![Sélectionner Pipelines, Versions](media/create-release-1.png)

1.  Sélectionnez **Nouveau pipeline** ou, si vous avez des pipelines existants, sélectionnez **Nouveau** puis **Nouveau pipeline de mise en production**.

1.  Sélectionnez le modèle **Tâche vide**.

    ![Sélectionner Projet vide](media/create-release-select-empty.png)

1.  Dans la zone **Nom de la phase**, entrez le nom de votre environnement.

1.  Sélectionnez **Ajouter un artefact**, puis choisissez le dépôt Git configuré avec votre instance Synapse Studio de développement. Sélectionnez le référentiel Git que vous avez utilisé pour gérer le modèle ARM de pools et l’espace de travail. Si vous utilisez GitHub en tant que source, vous devez créer une connexion de service pour votre compte GitHub et les référentiels d’extraction. Pour plus d’informations sur la [connexion de service](/azure/devops/pipelines/library/service-endpoints) 

    ![Ajouter une branche de publication](media/release-creation-github.png)

1.  Sélectionnez la branche du modèle ARM pour la mise à jour des ressources. Pour **Version par défaut**, sélectionnez **La dernière de la branche par défaut**.

    ![Ajouter un modèle ARM](media/release-creation-arm-branch.png)

1.  Sélectionnez la [branche de publication](source-control.md#configure-publishing-settings) du dépôt comme **branche par défaut**. Par défaut, cette branche de publication est `workspace_publish`. Pour **Version par défaut**, sélectionnez **La dernière de la branche par défaut**.

    ![Ajouter un artefact](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-resource"></a>Configurer une tâche intermédiaire pour un modèle ARM afin de créer et mettre à jour une ressource 

Si vous disposez d’un modèle ARM pour déployer une ressource, comme un espace de travail Azure Synapse Analytics, des pools Spark et SQL ou un coffre de clés, ajoutez une tâche de déploiement Azure Resource Manager pour créer ou mettre à jour ces ressources :

1. Dans la vue des phases, sélectionnez **Afficher les tâches de phase**.

    ![Vue des phases](media/release-creation-stage-view.png)

1. Créer une tâche. Recherchez **Déploiement de modèles ARM**, puis sélectionnez **Ajouter**.

1. Dans la tâche Déploiement, sélectionnez l’abonnement, le groupe de ressources et l’emplacement de l’espace de travail cible. Fournissez les informations d’identification si nécessaire.

1. Dans la liste **Actions**, sélectionnez **Créer ou mettre à jour un groupe de ressources**.

1. Sélectionnez le bouton de sélection ( **…** ) en regard de la zone **Modèle**. Rechercher le modèle Azure Resource Manager de votre espace de travail cible

1. Sélectionnez **…** en regard de la zone **Paramètres du modèle** pour choisir le fichier de paramètres.

1. Sélectionnez **…** en regard de la zone **Remplacer les paramètres du modèle** et entrez les valeurs de paramètre souhaitées pour l’espace de travail cible. 

1. Sélectionnez **Incrémentiel** comme **Mode de déploiement**.
    
    ![déploiement de l’espace de travail et des pools](media/pools-resource-deploy.png)

1. (Facultatif) Ajoutez **Azure PowerShell** pour l’octroi et la mise à jour de l’attribution de rôle de l’espace de travail. Si vous utilisez un pipeline de mise en production pour créer un espace de travail Synapse, le principal du service du pipeline est ajouté en tant qu’administrateur de l’espace de travail par défaut. Vous pouvez exécuter PowerShell pour accorder à d’autres comptes l’accès à l’espace de travail. 
    
    ![accorder l’autorisation](media/release-creation-grant-permission.png)

 > [!WARNING]
> En mode de déploiement complet, les ressources présentes dans le groupe de ressources mais pas spécifiées dans le modèle Resource Manager sont **supprimées**. Pour plus d’informations, consultez [Modes de déploiement Azure Resource Manager](../../azure-resource-manager/templates/deployment-modes.md).

## <a name="set-up-a-stage-task-for-synapse-artifacts-deployment"></a>Configurer une tâche intermédiaire pour le déploiement d’artefacts Synapse 

Utilisez l’extension de [déploiement d’espace de travail Synapse](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) pour déployer d’autres éléments dans l’espace de travail Synapse, comme un jeu de données, un script SQL, un notebook, une définition de travail Spark, un flux de données, un pipeline, un service lié, des informations d’identification et un runtime d'intégration.  

1. Recherchez et récupérez l’extension à partir de la **Place de marché Azure DevOps**(https://marketplace.visualstudio.com/azuredevops) 

     ![Obtenir une extension](media/get-extension-from-market.png)

1. Sélectionnez une organisation pour l’installation de l’extension. 

     ![Installer l’extension](media/install-extension.png)

1. Assurez-vous que le principal du service du pipeline Azure DevOps a reçu l’autorisation d’abonnement et a également été affecté en tant qu’administrateur d’espace de travail pour l’espace de travail cible. 

1. Créer une tâche. Recherchez **Déploiement d’espace de travail Synapse**, puis sélectionnez **Ajouter**.

     ![Ajouter une extension](media/add-extension-task.png)

1.  Dans la tâche, sélectionnez **…** à côté de la zone **Modèle** pour choisir le fichier de modèle.

1. Sélectionnez **…** en regard de la zone **Paramètres du modèle** pour choisir le fichier de paramètres.

1. Sélectionnez la connexion, le groupe de ressources et le nom de l’espace de travail cible. 

1. Sélectionnez **…** à côté de la case **Remplacer les paramètres du modèle**, entrez les valeurs de paramètre souhaitées pour l’espace de travail cible, notamment les chaînes de connexion et les clés de compte qui sont utilisées dans vos services liés. [Cliquez ici pour plus d’informations.](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)

    ![Déployer un espace de travail Synapse](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> Dans les scénarios d’intégration et de livraison continues, le type de runtime d’intégration (IR) doit être le même dans les différents environnements. Par exemple, si vous avez un runtime d’intégration auto-hébergé dans l’environnement de développement, le même runtime d’intégration doit aussi être de type auto-hébergé dans les autres environnements (test, production). De même, si vous partagez des runtimes d’intégration dans plusieurs phases, vous devez les configurer comme étant liés et auto-hébergés dans tous les environnements (développement, test, production).

## <a name="create-release-for-deployment"></a>Créer une mise en production pour le déploiement 

Après avoir enregistré toutes les modifications, vous pouvez sélectionner **Créer une version** pour créer manuellement une mise en production. Pour automatiser la création des mises en production, consultez les [déclencheurs de mise en production Azure DevOps](/azure/devops/pipelines/release/triggers).

   ![Sélectionner Créer une mise en production](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>Utiliser des paramètres personnalisés du modèle d’espace de travail 

Vous utilisez CI/CD automatisé et souhaitez modifier certaines propriétés pendant le déploiement mais celles-ci ne sont pas paramétrables par défaut. Dans ce cas, vous pouvez remplacer le modèle de paramètres par défaut.

Pour remplacer le modèle de paramètres par défaut, vous devez créer un modèle de paramètres personnalisé, un fichier nommé **template-parameters-definition.json** dans le dossier racine de votre branche de collaboration git. Vous devez utiliser ce nom de fichier exact. Lors de la publication à partir de la branche de collaboration, l’espace de travail Synapse lit ce fichier et utilise sa configuration pour générer les paramètres. Si aucun fichier n’est trouvé, le modèle de paramètres par défaut est utilisé.

### <a name="custom-parameter-syntax"></a>Syntaxe de paramètre personnalisé

Voici quelques instructions pour créer le fichier de paramètres personnalisés :

* Entrez le chemin d’accès de propriété sous le type d’entité correspondant.
* Définir un nom de propriété sur `*` indique que vous souhaitez paramétrer toutes les propriétés dans celle-ci (uniquement jusqu’au premier niveau, pas de manière récursive). Vous pouvez également fournir des exceptions à cette configuration.
* Définir la valeur d’une propriété sous forme de chaîne indique que vous souhaitez paramétrer la propriété. Utilisez le format `<action>:<name>:<stype>`.
   *  `<action>` peut être l’un des caractères suivants :
      * `=` permet de conserver la valeur actuelle en tant que valeur par défaut pour le paramètre.
      * `-` permet de ne pas conserver la valeur par défaut pour le paramètre.
      * `|` est un cas particulier pour les secrets Azure Key Vault pour les chaînes de connexion ou les clés.
   * `<name>` correspond au nom du paramètre. S’il est vide, il prend le nom du Si la valeur commence par un caractère `-`, le nom est abrégé. Par exemple, `AzureStorage1_properties_typeProperties_connectionString` serait abrégé en `AzureStorage1_connectionString`.
   * `<stype>` correspond au type de paramètre. Si `<stype>` est vide, le type par défaut est `string`. Valeurs prises en charge : `string`, `securestring`, `int`, `bool`, `object`, `secureobject` et `array`.
* La spécification d’un tableau dans le fichier indique que la propriété correspondante dans le modèle est un tableau. Synapse itère au sein de tous les objets du tableau à l’aide de la définition spécifiée. Le second objet, une chaîne, correspond alors au nom de la propriété et sert de nom au paramètre pour chaque itération.
* Une définition ne peut pas être spécifique à une instance de ressource. Toute définition s’applique à toutes les ressources de ce type.
* Par défaut, toutes les chaînes sécurisées, telles que les secrets Key Vault, et les chaînes sécurisées, telles que les chaînes de connexion, les clés et les jetons, sont paramétrables.

### <a name="parameter-template-definition-samples"></a>Exemples de définition de modèle de paramètres 

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

Voici une explication de la façon dont le modèle précédent est construit, décomposé par type de ressource.

#### <a name="notebooks"></a>Notebooks 

* Toute propriété du chemin d’accès `properties/bigDataPool/referenceName` est paramétrable avec sa valeur par défaut. Vous pouvez paramétrer le pool Spark attaché pour chaque fichier de bloc-notes. 

#### <a name="sql-scripts"></a>Scripts SQL 

* Les propriétés (poolName et databaseName) dans le chemin d’accès `properties/content/currentConnection` sont paramétrables en tant que chaînes sans les valeurs par défaut dans le modèle. 

#### <a name="pipelines"></a>Pipelines

* Toute propriété du chemin `activities/typeProperties/waitTimeInSeconds` est paramétrable. Toute activité dans un pipeline qui a une propriété au niveau du code nommée `waitTimeInSeconds` (par exemple, l’activité `Wait`) est paramétrable en tant que nombre, avec un nom par défaut. Mais elle n’a pas de valeur par défaut dans le modèle Resource Manager. Il s’agit d’une entrée obligatoire lors du déploiement de Resource Manager.
* De même, une propriété appelée `headers` (par exemple, dans une activité `Web`) est paramétrable avec le type `object` (Objet). Elle a une valeur par défaut, qui est la même que celle de la fabrique source.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Toutes les propriétés, sous le chemin `typeProperties` sont paramétrables avec des valeurs par défaut respectives. Par exemple, deux propriétés existent sous les propriétés de type `IntegrationRuntimes` : `computeProperties` et `ssisProperties`. Les deux types de propriété sont créés avec leurs valeurs et types (objet) par défaut respectifs.

#### <a name="triggers"></a>Déclencheurs

* Sous `typeProperties`, deux propriétés sont paramétrables. La première est `maxConcurrency`, qui est spécifiée pour avoir une valeur par défaut et est de type `string`. Elle porte le nom de paramètre par défaut `<entityName>_properties_typeProperties_maxConcurrency`.
* La propriété `recurrence` est également paramétrable. Sous celle-ci, toutes les propriétés à ce niveau sont spécifiées pour être paramétrables sous forme de chaînes, avec des valeurs et noms de paramètres par défaut. La propriété `interval` est une exception, qui est paramétrée en tant que type `int`. Le nom du paramètre a pour suffixe `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. De même, la propriété `freq` est une chaîne et peut être paramétrée en tant que chaîne. La propriété `freq` est toutefois paramétrable sans valeur par défaut. Le nom est abrégé et suivi d’un suffixe. Par exemple : `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Les services liés sont uniques. Étant donné que les services liés et les jeux de données sont de types différents, vous pouvez fournir une personnalisation spécifique au type. Dans cet exemple, pour tous les services liés de type `AzureDataLakeStore`, un modèle spécifique est appliqué. Pour tous les autres (via `*`), un autre modèle est appliqué.
* La propriété `connectionString` est paramétrée en tant que valeur `securestring`. Elle n’a pas de valeur par défaut. Elle a un nom de paramètre raccourci doté du suffixe `connectionString`.
* La propriété `secretAccessKey` se trouve être un `AzureKeyVaultSecret` (par exemple, dans un service lié Amazon S3). Elle est paramétrable automatiquement en tant que secret Azure Key Vault et extraite du coffre de clés configuré. Vous pouvez également paramétrer le coffre de clés proprement dit.

#### <a name="datasets"></a>Groupes de données

* La personnalisation spécifique au type est disponible pour les jeux de données, mais vous pouvez fournir une configuration sans avoir explicitement de configuration au niveau \*. Dans l’exemple précédent, toutes les propriétés du jeu de données sous `typeProperties` sont paramétrables.


## <a name="best-practices-for-cicd"></a>Meilleures pratiques pour CI/CD

Si vous utilisez une intégration Git avec votre espace de travail Azure Synapse Analytics et que vous disposez d’un pipeline CI/CD qui déplace vos modifications de l’environnement de développement à celui de test, puis à celui de production, nous vous recommandons les meilleures pratiques suivantes :

-   **Intégration Git**. Configurez uniquement votre espace de travail Azure Synapse Analytics de développement avec l’intégration Git. Les modifications au niveau des espaces de travail de test et de production sont déployées via CI/CD et ne nécessitent pas d’intégration Git.
-   **Préparez les pools avant la migration des artefacts**. Si vous disposez d’un script SQL ou d’un notebook attaché à des pools dans l’espace de travail de développement, le même nom de pools dans différents environnements est attendu. 
-   **Infrastructure en tant que code (IaC)** . La gestion de l'infrastructure (réseaux, machines virtuelles, équilibreurs de charge et topologie de connexion) dans un modèle descriptif utilise le même contrôle de version que celui utilisé par l'équipe DevOps pour le code source. 
-   **Autres**. Consultez les [meilleures pratiques pour les artefacts ADF](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>Résolution des problèmes de déploiement d’artefacts 

### <a name="use-the-azure-synapse-analytics-workspace-deployment-task"></a>Utiliser la tâche de déploiement de l’espace de travail Azure Synapse Analytics

Dans Azure Synapse Analytics, il existe un certain nombre d’artefacts qui ne sont pas des ressources ARM. Cela diffère d’Azure Data Factory. La tâche de déploiement d’un modèle ARM ne fonctionnera pas correctement pour déployer des artefacts Azure Synapse Analytics.
 
### <a name="unexpected-token-error-in-release"></a>Erreur de jeton inattendu dans la mise en production

Lorsque votre fichier de paramètres contient des valeurs de paramètre qui ne sont pas placées dans une séquence d’échappement, le pipeline de mise en production ne parvient pas à analyser le fichier et génère l’erreur « Jeton inattendu ». Nous vous suggérons de remplacer les paramètres ou d’utiliser Azure Key Vault pour récupérer les valeurs des paramètres. Vous pouvez également utiliser des caractères d’échappement doubles comme solution de contournement.
