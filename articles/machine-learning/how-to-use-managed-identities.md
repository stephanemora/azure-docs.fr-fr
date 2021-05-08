---
title: Utiliser des identités managées pour le contrôle d'accès (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser des identités managées pour contrôler l’accès aux ressources Azure à partir d’un espace de travail Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: 61f0f2a71240c0ade13f7cd20b6c411f3df7f278
ms.sourcegitcommit: 43be2ce9bf6d1186795609c99b6b8f6bb4676f47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108279151"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>Utiliser les identités managées avec Azure Machine Learning (préversion)

Les [identités managées](../active-directory/managed-identities-azure-resources/overview.md) vous permettent de configurer votre espace de travail avec les *autorisations minimales requises pour accéder aux ressources*. 

Pour configurer un espace de travail Azure Machine Learning de manière fiable, il est important de s’assurer que les différents services associés à l’espace de travail disposent du niveau d’accès correct. Par exemple, pendant le workflow Machine Learning, l’espace de travail doit accéder à Azure Container Registry (ACR) pour les images Docker et aux comptes de stockage pour les données de formation. 

En outre, les identités managées permettent un contrôle affiné des autorisations, par exemple, vous pouvez accorder à un registre ACR spécifique l’accès à des ressources de calcul particulières, ou le révoquer.

Cet article explique comment utiliser les identités managées pour :

 * Configurer et utiliser ACR pour votre espace de travail Azure Machine Learning sans avoir à activer l’accès utilisateur administrateur à ACR.
 * Accéder à un ACR privé externe à votre espace de travail, pour extraire des images de base à des fins de formation ou d’inférence.
 * Créer un espace de travail avec une identité managée affectée par l’utilisateur pour accéder aux ressources associées.

> [!IMPORTANT]
> L’utilisation d’identités managées pour contrôler l’accès aux ressources avec Azure Machine Learning est actuellement disponible en préversion. La fonctionnalité en préversion est fournie en l’état, sans garantie de support ni contrat de niveau de service. Pour plus d’informations, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="prerequisites"></a>Prérequis

- Un espace de travail Azure Machine Learning. Pour plus d’informations, voir la page [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).
- L’[extension Azure CLI pour le service Machine Learning](reference-azure-machine-learning-cli.md)
- Le [kit SDK Python d’Azure Machine Learning](/python/api/overview/azure/ml/intro).
- Pour attribuer des rôles, la connexion de votre abonnement Azure doit avoir le rôle [Opérateur d’identité managée](../role-based-access-control/built-in-roles.md#managed-identity-operator) ou un autre rôle qui accorde les actions requises (par exemple, __Propriétaire__).
- Vous devez être familiarisé avec la création et l’utilisation des [identités managées](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="configure-managed-identities"></a>Configurer des identités managées

Dans certains cas, il est nécessaire d’interdire l’accès utilisateur administrateur à Azure Container Registry. Par exemple, il se peut que le registre ACR soit partagé et que vous deviez interdire l’accès administrateur à d’autres utilisateurs. Ou encore, une stratégie de niveau d’abonnement n’autorise pas la création d’un ACR avec accès utilisateur administrateur activé.

> [!IMPORTANT]
> Lors de l’utilisation d’Azure Machine Learning pour l’inférence sur Azure Container Instance (ACI), l’accès utilisateur administrateur sur ACR est __requis__. Ne le désactivez pas si vous envisagez de déployer des modèles dans ACI pour l’inférence.

Lorsque vous créez un ACR sans activer l’accès utilisateur administrateur, les identités managées sont utilisées pour accéder à l’ACR afin de créer et d’extraire des images Docker.

Vous pouvez utiliser votre propre ACR avec accès utilisateur administrateur désactivé lorsque vous créez l’espace de travail. Vous pouvez également laisser Azure Machine Learning créer un ACR d’espace de travail et désactiver l’accès utilisateur administrateur par la suite.

### <a name="bring-your-own-acr"></a>Utiliser votre propre ACR

Si l’accès utilisateur administrateur pour ACR n’est pas autorisé par la stratégie d’abonnement, vous devez d’abord créer l’ACR sans accès utilisateur administrateur, puis l’associer à l’espace de travail. En outre, si vous avez déjà un ACR avec accès utilisateur administrateur désactivé, vous pouvez le joindre à l’espace de travail.

[Créez un ACR à partir d’Azure CLI](../container-registry/container-registry-get-started-azure-cli.md) sans définir d’argument ```--admin-enabled```, ou à partir du portail Azure sans activer l’accès utilisateur administrateur. Ensuite, lors de la création de l’espace de travail Azure Machine Learning, spécifiez l’ID de ressource Azure de l’ACR. L’exemple suivant illustre la création d’un espace de travail Azure ML qui utilise un ACR existant :

> [!TIP]
> Pour obtenir la valeur du paramètre `--container-registry` , utilisez la commande [az acr show](/cli/azure/acr#az_acr_show) pour afficher des informations pour votre ACR. Le champ `id` contient l’ID de ressource de votre ACR.

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>Laisser Azure Machine Learning Service créer un ACR d’espace de travail

Si vous n’utilisez pas votre propre ACR, le service Azure Machine Learning en crée un pour vous lorsque vous effectuez une opération qui en a besoin. Par exemple, soumettre un cycle de formation à Capacité de calcul Machine Learning, construire un environnement ou déployer un point de terminaison de service Web. L’ACR créé par l’espace de travail aura l’accès utilisateur administrateur activé et vous devrez le désactiver manuellement.

1. Créer un espace de travail

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. Effectuez une action qui requiert ACR. Par exemple, le [didacticiel sur l’apprentissage d’un modèle](tutorial-train-models-with-aml.md).

1. Faites créer le nom ACR par le cluster :

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    Cette commande retourne une valeur semblable au texte suivant. Vous souhaitez uniquement la dernière partie du texte, c’est-à-dire le nom de l’instance ACR :

    ```output
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. Mettez à jour l’ACR pour désactiver l’accès utilisateur administrateur :

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>Créez une instance de calcul avec une identité managée pour accéder aux images Docker pour la formation

Pour accéder à l’ACR d’espace de travail, créez un cluster de calcul Machine Learning avec l’identité managée attribuée par le système activée. Vous pouvez activer l’identité à partir du portail Azure ou de Studio lors de la création d’une instance de calcul, ou à partir d’Azure CLI en utilisant les informations ci-dessous. Pour plus d’informations, consultez [Utiliser l’identité managée avec des clusters de calcul](how-to-create-attach-compute-cluster.md#managed-identity).

# <a name="python"></a>[Python](#tab/python)

Lorsque vous créez un cluster de calcul avec [AmlComputeProvisioningConfiguration](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration), utilisez le paramètre `identity_type` pour définir le type d’identité managée.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

Pour plus d’informations sur la configuration d’une identité managée lors de la création d’un cluster de calcul dans Studio, consultez [Configurer une identité managée](how-to-create-attach-compute-studio.md#managed-identity).

---

Une identité managée reçoit automatiquement le rôle ACRPull dans l’ACR d’espace de travail pour lui permettre d’extraire des images Docker pour la formation.

> [!NOTE]
> Si vous créez d’abord l’instance de calcul, avant l’ACR d’espace de travail, vous devez attribuer le rôle ACRPull manuellement.

## <a name="access-base-images-from-private-acr"></a>Accéder aux images de base à partir d’un ACR privé

Par défaut, Azure Machine Learning utilise les images de base Docker qui proviennent d’un référentiel public géré par Microsoft. Il génère ensuite votre environnement de formation ou d’inférence sur ces images. Pour plus d’informations, consultez [Que sont les environnements ML ?](concept-environments.md).

Pour utiliser une image de base personnalisée interne à votre entreprise, vous pouvez utiliser des identités managées pour accéder à votre ACR privé. Il existe deux cas d’utilisation :

 * Utilisez l’image de base pour la formation en l’état.
 * Créez une image managée Azure Machine Learning avec une image personnalisée comme base.

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>Extrayez l’image de base Docker vers le cluster de calcul Machine Learning pour la formation en l’état

Créez un cluster de calcul Machine Learning avec l’identité managée attribuée par le système activée, comme décrit plus haut. Ensuite, déterminez l’ID du principal de l’identité managée.

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

Éventuellement, vous pouvez mettre à jour le cluster de calcul pour attribuer une identité managée attribuée par l’utilisateur :

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

Pour permettre au cluster de calcul d’extraire les images de base, accordez le rôle ACRPull de l’identité du service managé sur l’ACR privé

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

Enfin, lors de la soumission d’une exécution d’entraînement , spécifiez l’emplacement de l’image de base dans la [définition de l’environnement](how-to-use-environments.md#use-existing-environments).

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> Pour vous assurer que l’image de base est directement extraite dans la ressource de calcul, définissez `user_managed_dependencies = True` et ne spécifiez pas de fichier Dockerfile. Dans le cas contraire, Azure Machine Learning Service tentera de créer une nouvelle image Docker et échouera, car seul le cluster de calcul a accès à l’extraction de l’image de base à partir d’ACR.

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>Créer un environnement managé Azure Machine Learning dans l’image de base de l’ACR privé pour la formation ou l’inférence

Dans ce scénario, Azure Machine Learning Service crée l’environnement de formation ou d’inférence en plus d’une image de base que vous fournissez à partir d’un ACR privé. Étant donné que la tâche de création d’image s’effectue dans l’ACR d’espace de travail à l’aide d’ACR Tasks, vous devez effectuer des étapes supplémentaires pour autoriser l’accès.

1. Créez une __identité managée attribuée par l’utilisateur__ et accordez-lui l’accès ACRPull sur l’__ACR privé__.  
1. Accordez à l’__identité managée attribuée par le système__ de l’espace de travail un rôle Opérateur de l’identité managée sur l’__identité managée attribuée par l’utilisateur__  de l’étape précédente. Ce rôle permet à l’espace de travail d’attribuer l’identité managée attribuée par l’utilisateur à ACR Task pour la création de l’environnement managé. 

    1. Obtenez l’ID du principal de l’identité managée attribuée par le système de l’espace de travail :

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. Accordez le rôle Opérateur de l’identité managée :

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        L’ID de ressource UAI est l’ID de ressource Azure de l’identité attribuée par l’utilisateur, au format `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>`.

1. Spécifiez l’ID client et l’ACR externe de l’__identité managée attribuée par l’utilisateur__ dans les connexions de l’espace de travail à l’aide de la [méthode Workspace.set_connection](/python/api/azureml-core/azureml.core.workspace.workspace#set-connection-name--category--target--authtype--value-) :

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

Une fois la configuration terminée, vous pouvez utiliser les images de base de l’ACR privé lors de la création d’environnements pour la formation ou l’inférence. L’extrait de code suivant montre comment spécifier l’image de base ACR et le nom de l’image dans une définition d’environnement :

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

Vous pouvez éventuellement spécifier l’URL de ressource d’identité managée et l’ID de client dans la définition d’environnement proprement dite à l’aide de [RegistryIdentity](/python/api/azureml-core/azureml.core.container_registry.registryidentity). Si vous utilisez l’identité de registre de manière explicite, elle remplace toutes les connexions d’espace de travail spécifiées précédemment :

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="use-docker-images-for-inference"></a>Utiliser des Images Docker pour l’inférence

Une fois que vous avez configuré ACR sans accès utilisateur administrateur comme décrit plus haut, vous pouvez accéder aux images Docker pour l’inférence sans clés d’administration à partir de votre instance Azure Kubernetes Service (AKS). Lorsque vous créez ou joignez AKS à l’espace de travail, le principal de service du cluster se voit automatiquement attribuer l’accès ACRPull à l’ACR d’espace de travail.

> [!NOTE]
> Si vous utilisez votre propre cluster AKS, le cluster doit avoir le principal de service activé au lieu de l’identité managée.

## <a name="create-workspace-with-user-assigned-managed-identity"></a>Créer un espace de travail avec une identité managée affectée par l’utilisateur

Lorsque vous créez un espace de travail, vous pouvez spécifier une identité managée affectée par l’utilisateur qui sera utilisée pour accéder aux ressources associées : ACR, Key Vault, Stockage et App Insights.

Tout d’abord, [créez une identité managée affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md), puis notez l’ID de ressource ARM de l’identité managée.

Ensuite, utilisez Azure CLI ou le SDK Python pour créer l’espace de travail. Si vous utilisez l’interface CLI, spécifiez l’ID à l’aide du paramètre `--primary-user-assigned-identity`. Si vous utilisez le SDK, utilisez `primary_user_assigned_identity`. Voici des exemples d’utilisation d’Azure CLI et de Python en vue de créer un espace de travail à l’aide des paramètres suivants :

__Azure CLI__

```azurecli-interactive
az ml workspace create -w <workspace name> -g <resource group> --primary-user-assigned-identity <managed identity ARM ID>
```

__Python__

```python
from azureml.core import Workspace

ws = Workspace.create(name="workspace name", 
    subscription_id="subscription id", 
    resource_group="resource group name",
    primary_user_assigned_identity="managed identity ARM ID")
```

Vous pouvez également utiliser un [modèle ARM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced) pour créer un espace de travail avec une identité managée affectée par l’utilisateur.

> [!IMPORTANT]
> Si vous apportez vos propres ressources associées au lieu de les créer avec Azure Machine Learning Service, vous devez accorder les rôles d’identité managée à ces ressources. Utilisez le [modèle ARM d’attribution de rôle](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-dependencies-role-assignment) pour effectuer les attributions.

Pour un espace de travail disposant de (clés gérées par le client pour le chiffrement)[https://docs.microsoft.com/azure/machine-learning/concept-data-encryption ], vous pouvez passer une identité managée affectée par l’utilisateur pour vous authentifier auprès de Key Vault à partir du stockage. Utilisez l’argument __user-assigned-identity-for-cmk-encryption__ (CLI) ou l’argument __user_assigned_identity_for_cmk_encryption__ (SDK) pour passer l’identité managée. Cette identité managée peut être identique ou différente de celle de l’utilisateur principal de l’espace de travail.

Si vous disposez déjà d’un espace de travail, vous pouvez convertir l’identité managée affectée par le système en identité managée affectée par l’utilisateur à l’aide de la commande CLI ```az ml workspace update```, ou de la méthode ```Workspace.update``` du SDK Python.


## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur la [sécurité d'entreprise dans Azure Machine Learning](concept-enterprise-security.md).