---
title: 'Tutoriel : Points de terminaison en ligne managés pour accéder aux ressources'
description: Bénéficiez d’un accès sécurisé aux ressources Azure pour votre modèle de déploiement Machine Learning avec un point de terminaison en ligne managé et une identité managée affectée par le système.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 05/25/2021
ms.topic: tutorial
ms.custom: tutorial, devplatv2
ms.openlocfilehash: 78cbec0c8f4805794062b6fd525567f8aebcb2b6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114448833"
---
# <a name="tutorial-access-resources-with-managed-online-endpoints-and-identity-preview"></a>Tutoriel : Accéder aux ressources avec points de terminaison en ligne et identité managés (préversion)

Dans ce tutoriel, vous allez apprendre à accéder de façon sécurisée aux ressources Azure à partir de votre script de scoring avec un point de terminaison en ligne managé et une identité managée affectée par le système.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]
Ce tutoriel montre comment effectuer les actions suivantes avec Azure CLI et son extension ML :

> [!div class="checklist"]
> * Définir les valeurs par défaut de l’interface Azure CLI à utiliser
> * Configurer les variables à utiliser avec votre point de terminaison en ligne managé
> * Créer un compte Stockage Blob et un conteneur d’objets blob 
> * Créer un point de terminaison en ligne managé
> * Accorder à l’identité managée affectée par le système l’autorisation d’accéder au stockage
> * Créer un déploiement associé au point de terminaison managé


## <a name="prerequisites"></a>Prérequis

* Pour utiliser Azure Machine Learning, vous devez avoir un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/) dès aujourd’hui.

* Vous devez installer et configurer Azure CLI et l’extension ML. Pour plus d’informations, consultez [Installer, configurer et utiliser l’interface CLI 2.0 (préversion)](how-to-configure-cli.md). 

* Vous devez avoir un groupe de ressources Azure dans lequel vous (ou le principal de service que vous utilisez) devez disposer d’un accès `Contributor`. Vous disposez d’un tel groupe de ressources si vous avez configuré votre extension ML conformément à l’article ci-dessus. 

* Vous devez avoir un espace de travail Azure Machine Learning. Vous disposez d’un tel espace de travail si vous avez configuré votre extension ML conformément à l’article ci-dessus.

* Un modèle Machine Learning entraîné prêt pour le scoring et le déploiement.


## <a name="set-the-defaults-for-azure-cli"></a>Définir les paramètres par défaut pour Azure CLI

Pour vous assurer que les ressources appropriées sont utilisées tout au long de ce tutoriel, définissez les valeurs par défaut pour l’ID d’abonnement Azure, l’espace de travail Azure Machine Learning et le groupe de ressources que vous souhaitez utiliser. Cela vous permet d’éviter d’avoir à transmettre plusieurs fois les valeurs chaque fois que vous appelez une commande Azure CLI. 

> [!IMPORTANT]
> Vérifiez que votre compte d’utilisateur a le rôle « Administrateur de l’accès utilisateur » affecté au groupe de ressources. 

```azurecli
az account set --subscription <subscription id>
az configure --defaults workspace=<azureml workspace name> group=<resource group>
```

## <a name="define-the-configuration-yaml-file-for-your-deployment"></a>Définir le fichier YAML de configuration pour votre déploiement

Pour déployer un point de terminaison managé, vous devez d’abord définir la configuration pour votre point de terminaison dans un fichier YAML.

L’exemple de code suivant crée un point de terminaison managé, qui  
* Affiche les fichiers YAML du répertoire `endpoints/online/managed/managed-identities/`.
* Définit le nom avec lequel vous souhaitez faire référence au point de terminaison, `my-sai-endpoint`.
* Spécifie le type d’autorisation à utiliser pour accéder au point de terminaison, `auth-mode: key`.
* Spécifie que le type de point de terminaison que vous souhaitez créer est un point de terminaison `online`. 
* Indique que le point de terminaison est associé à un déploiement appelé `blue`.
* Configure les détails du déploiement, tels que le modèle à déployer ainsi que l’environnement et le script de scoring à utiliser.

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/2-sai-deployment.yml":::

Pour des informations de référence sur YAML, consultez [Informations de référence YAML sur les points de terminaison en ligne managés (préversion)](reference-online-endpoint-yaml.md).

## <a name="configure-variables-for-your-deployment"></a>Configurer des variables pour votre déploiement

Configurez les noms de variables pour l’espace de travail, l’emplacement de l’espace de travail et le point de terminaison que vous souhaitez créer. Le code suivant exporte ces valeurs en tant que variables d’environnement dans votre point de terminaison :

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="set_variables" :::

Ensuite, spécifiez les noms que vous voulez donner à vos compte Stockage Blob, conteneur d’objets blob et fichier. Ces noms de variables sont définis ici et sont référencés dans les commandes `az storage account create` et `az storage container create` dans la section suivante.

Le code suivant exporte ces valeurs en tant que variables d’environnement :

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="configure_storage_names" :::


Une fois ces variables exportées, créez un fichier texte localement. Lorsque le point de terminaison est déployé, le script de scoring accède à ce fichier texte à l’aide de l’identité managée affectée par le système qui est générée lors de la création du point de terminaison.

## <a name="create-blob-storage-and-container"></a>Créer un stockage et un conteneur d’objets blob

Pour cet exemple, vous allez créer un compte Stockage Blob et un conteneur d’objets blob, puis charger le fichier texte créé précédemment dans le conteneur d’objets blob. 

En premier lieu, créez un compte de stockage. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_account" :::

Ensuite, créez le conteneur d’objets blob dans le compte de stockage. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_container" :::

Chargez alors votre fichier texte dans le conteneur d’objets blob. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="upload_file_to_storage" :::

## <a name="create-a-managed-online-endpoint"></a>Créer un point de terminaison en ligne managé

Le code suivant crée un point de terminaison en ligne managé sans spécifier de déploiement. La création du déploiement est effectuée ultérieurement dans le tutoriel.

Lorsque vous créez un point de terminaison managé, une identité managée affectée par le système est créée par défaut pour le point de terminaison.

>[!IMPORTANT]
> Les identités managées affectées par le système sont immuables et ne peuvent pas être modifiées une fois créées.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_endpoint" :::

Vérifiez l’état du point de terminaison avec les éléments suivants. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_endpoint_Status" :::

En cas de problème, consultez [Résolution des problèmes de déploiement et de scoring de points de terminaison en ligne managés (préversion)](how-to-troubleshoot-managed-online-endpoints.md).

## <a name="give-storage-permission-to-system-assigned-managed-identity"></a>Accorder l’autorisation de stockage à l’identité managée affectée par le système

Vous pouvez autoriser le point de terminaison managé à accéder à votre stockage via son identité managée affectée par le système. 

Récupérez l’identité managée affectée par le système qui a été créée pour votre point de terminaison. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="get_system_identity" :::

À partir de là, vous pouvez accorder à l’identité managée affectée par le système l’autorisation d’accéder à votre stockage.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="give_permission_to_user_storage_account" :::

## <a name="scoring-script-to-access-azure-resource"></a>Script de scoring pour accéder à la ressource Azure

Reportez-vous au script de scoring suivant pour comprendre comment utiliser un jeton d’identité managée affectée par le système pour accéder aux ressources Azure. Dans ce scénario, la ressource Azure est le compte de stockage créé dans les sections précédentes. 

:::code language="python" source="~/azureml-examples-main/cli/endpoints/online/model-1/onlinescoring/score_managedidentity.py":::

## <a name="create-a-deployment-using-your-configuration"></a>Créer un déploiement à l’aide de votre configuration

Créez un déploiement associé au point de terminaison managé.

Ce déploiement peut prendre approximativement entre 8 et 14 minutes selon que l’environnement/l’image sous-jacent est généré pour la première fois ou non. Les déploiements ultérieurs avec le même environnement seront plus rapides.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="deploy" :::

Vérifiez l’état du déploiement. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deploy_Status" :::

> [!NOTE]
> La méthode init du script de scoring lit le fichier à partir de votre compte de stockage à l’aide du jeton d’identité managée affectée par le système. 

Pour vérifier la sortie de la méthode init, consultez le journal de déploiement avec le code suivant. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deployment_log" :::

Une fois cette commande terminée, vous aurez inscrit le modèle, l’environnement et le point de terminaison dans votre espace de travail Azure Machine Learning.

### <a name="confirm-your-endpoint-deployed-successfully"></a>Confirmer le déploiement correct de votre point de terminaison

Une fois votre point de terminaison déployé, confirmez son fonctionnement. Les détails de l’inférence varient d’un modèle à l’autre. Pour ce tutoriel, les paramètres de requête JSON se présentent comme suit : 

:::code language="json" source="~/azureml-examples-main/cli/endpoints/online/model-1/sample-request.json" :::

Pour appeler votre point de terminaison, exécutez : 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="test_endpoint" :::


## <a name="delete-the-endpoint-and-storage-account"></a>Supprimer le point de terminaison et le compte de stockage

Si vous n’envisagez pas de continuer à utiliser le point de terminaison déployé et le stockage, supprimez-les pour réduire les coûts. Lorsque vous supprimez le point de terminaison, tous ses déploiements associés sont également supprimés. 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_endpoint" :::
::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_storage_account" :::

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel sur Azure Machine Learning, vous avez utilisé l’interface CLI de machine learning pour les tâches suivantes :

> [!div class="checklist"]
> * Définir les valeurs par défaut de l’interface Azure CLI à utiliser
> * Configurer les variables à utiliser avec votre point de terminaison
> * Créer un compte Stockage Blob et un conteneur d’objets blob 
> * Créer un point de terminaison managé
> * Accorder à l’identité managée affectée par le système l’autorisation d’accéder au stockage
> * Créer un déploiement associé au point de terminaison managé

* Pour plus d’informations sur l’utilisation de l’interface CLI, consultez [Utiliser l’extension CLI pour Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Pour affiner les requêtes JSON afin de retourner uniquement des données spécifiques, consultez [Interroger la sortie de commande Azure CLI](/cli/azure/query-azure-cli).
* Pour plus d’informations sur le schéma YAML, consultez le document [Référence YAML sur les points de terminaison en ligne](reference-online-endpoint-yaml.md).
* Pour connaître les ressources de calcul que vous pouvez utiliser, consultez [Liste des références SKU pour les points de terminaison en ligne managés (préversion)](reference-managed-online-endpoints-vm-sku-list.md).
* Pour plus d’informations sur les coûts, consultez [Afficher les coûts d’un point de terminaison en ligne managé Azure Machine Learning (préversion)](how-to-view-online-endpoints-costs.md).
* Pour plus d’informations sur les déploiements, consultez [Déploiement sécurisé pour les points de terminaison en ligne (préversion)](how-to-safely-rollout-managed-endpoints.md).
* Pour plus d’informations sur la supervision des points de terminaison, consultez [Superviser des points de terminaison en ligne managés (préversion)](how-to-monitor-online-endpoints.md).
* Pour plus d’informations sur les limites pour les points de terminaison managés, consultez [Gérer et augmenter les quotas pour les ressources avec Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).
