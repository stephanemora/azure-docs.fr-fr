---
title: Comment déployer des modèles dans Azure Kubernetes Service
titleSuffix: Azure Machine Learning service
description: Découvrez comment déployer vos modèles Azure Machine Learning service en tant que service web à l’aide d’Azure Kubernetes Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: 26f965169f1fd01676efd3356534a8ac9f2121f7
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036084"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Déployer un modèle sur un cluster Azure Kubernetes Service

Découvrez comment utiliser Azure Machine Learning service pour déployer un modèle en tant que service web sur Azure Kubernetes service (AKS). Azure Kubernetes Service est idéal pour les déploiements de production à grande échelle. Utilisez Azure Kubernetes Service si vous avez besoin d’une ou de plusieurs des fonctionnalités suivantes :

- __Temps de réponse rapide__.
- __Mise à l’échelle automatique__ du service déployé.
- Options d’__accélération matérielle__, telles que le GPU et les FPGA (Field-Programmable Gate Array).

> [!IMPORTANT]
> La mise à l’échelle du cluster n’est pas fournie par le Kit de développement logiciel (SDK) Azure Machine Learning. Pour plus d’informations sur la mise à l’échelle des nœuds dans un cluster AKS, voir [Mettre le nombre de nœuds à l’échelle dans un cluster Azure Kubernetes Service (AKS)](../../aks/scale-cluster.md).

Lors d’un déploiement sur Azure Kubernetes Service, vous déployez sur un cluster AKS qui est __connecté à votre espace de travail__. Il existe deux façons de connecter un cluster AKS à votre espace de travail :

* Créez le cluster AKS à l’aide du Kit de développement logiciel (SDK) Azure Machine Learning service, de l’interface de ligne de commande Machine Learning ou du Portail Azure. Ce processus connecte automatiquement le cluster à l’espace de travail.
* Attachez un cluster AKS existant à votre espace de travail Azure Machine Learning service. Un cluster peut être attaché au moyen du Kit de développement logiciel (SDK) Azure Machine Learning service, de l’interface de ligne de commande Machine Learning ou du Portail Azure.

> [!IMPORTANT]
> Le processus de création ou d’attachement est une tâche unique. Une fois qu’un cluster AKS est connecté à l’espace de travail, vous pouvez l’utiliser pour les déploiements. Vous pouvez détacher ou supprimer le cluster AKS si vous n’en avez plus besoin. Une fois détaché ou supprimé, vous ne pourrez plus déployer sur le cluster.

## <a name="prerequisites"></a>Prérequis

- Un espace de travail de service Microsoft Azure Machine Learning. Pour plus d’informations, consultez [Créer un espace de travail Azure Machine Learning service](how-to-manage-workspace.md).

- Un modèle Machine Learning inscrit dans votre espace de travail. Si vous n’avez pas de modèle inscrit, consultez la section [Comment et où déployer des modèles](how-to-deploy-and-where.md).

- L’[extension Azure CLI pour Machine Learning service](reference-azure-machine-learning-cli.md), le [SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk) ou l’[extension Azure Machine Learning pour Visual Studio Code](how-to-vscode-tools.md).

- Les extraits de code __Python__ de cet article partent du principe que les variables suivantes sont définies :

    * `ws` – Sur votre espace de travail.
    * `model` – Sur votre modèle inscrit.
    * `inference_config` – Sur la configuration d’inférence pour le modèle.

    Pour plus d’informations sur le paramétrage de ces variables, consultez la section [Comment et où déployer des modèles ?](how-to-deploy-and-where.md)

- Les extraits de code __CLI__ de cet article partent du principe que vous avez créé un document `inferenceconfig.json`. Pour plus d’informations sur la création de ce document, consultez la section [Comment et où déployer des modèles ?](how-to-deploy-and-where.md)

## <a name="create-a-new-aks-cluster"></a>Créer un cluster AKS

**Durée estimée** : environ 20 minutes.

La création ou l’attachement d’un cluster AKS est un processus à effectuer une seule fois pour votre espace de travail. Vous pouvez le réutiliser pour vos autres déploiements. Si vous supprimez le cluster ou le groupe de ressources dans lequel il se trouve, vous devrez recréer un cluster lors du prochain déploiement. Vous pouvez avoir plusieurs clusters AKS attachés à votre espace de travail.

> [!TIP]
> Si vous souhaitez sécuriser votre cluster AKS à l’aide d’un réseau virtuel Azure, vous devez commencer par créer le réseau virtuel. Pour plus d’informations, voir [Sécuriser l’expérimentation et l’inférence avec un réseau virtuel Microsoft Azure](how-to-enable-virtual-network.md#aksvnet).

Si vous souhaitez créer un cluster AKS à des fins de __développement__, de __validation__ et de __test__ au lieu de production, vous pouvez spécifier l’__objet du cluster__ sur __dev test__.

> [!WARNING]
> Si vous définissez sur `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, le cluster créé n’est pas approprié pour le trafic de production et peut augmenter les temps d’inférence. Par ailleurs, les clusters de développement/test ne garantissent pas une tolérance de panne. Nous vous recommandons d’utiliser au moins 2 processeurs virtuels pour les clusters de développement/test.

Les exemples suivants montrent comment créer un cluster AKS à l’aide du Kit de développement logiciel (SDK) et de l’interface de ligne de commande :

**Avec le kit SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Pour [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), si vous choisissez des valeurs personnalisées pour `agent_count` et `vm_size` que `cluster_purpose` n’est pas `DEV_TEST`, vous devez vous assurer que `agent_count` multiplié par `vm_size` est supérieur ou égal à 12 processeurs virtuels. Par exemple, si vous définissez une `vm_size` sur « Standard_D3_v2 », qui comporte 4 processeurs virtuels, vous devez en définir `agent_count` sur 3 ou plus.
>
> Le kit de développement logiciel (SDK) Azure Machine Learning ne prend pas en charge la mise à l’échelle d'un cluster AKS. Pour mettre à l'échelle les nœuds du cluster, utilisez l’interface utilisateur de votre cluster AKS dans le portail Azure. Vous pouvez modifier le nombre de nœuds, mais pas la taille de machine virtuelle du cluster.

Pour plus d’informations sur les classes, les méthodes et les paramètres utilisés dans cet exemple, consultez les documents de référence suivants :

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Avec l’interface CLI**

```azurecli
az ml computetarget create aks -n myaks
```

Pour plus d’informations, consultez la référence [az ml computetarget create ask](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks).

## <a name="attach-an-existing-aks-cluster"></a>Attacher un cluster AKS existant

**Durée estimée** : 5 minutes environ.

Si vous avez déjà un cluster AKS version 1.12.## dans votre abonnement Azure, vous pouvez l’utiliser pour déployer votre image.

> [!TIP]
> Le cluster AKS existant peut se trouver dans une autre région Azure que celle de votre espace de travail Azure Machine Learning service.
>
> Si vous souhaitez sécuriser votre cluster AKS à l’aide d’un réseau virtuel Azure, vous devez commencer par créer le réseau virtuel. Pour plus d’informations, voir [Sécuriser l’expérimentation et l’inférence avec un réseau virtuel Microsoft Azure](how-to-enable-virtual-network.md#aksvnet).

> [!WARNING]
> Quand vous attachez un cluster AKS à un espace de travail, vous pouvez spécifier la façon dont vous allez l’utiliser en définissant le paramètre `cluster_purpose`.
>
> Si vous ne définissez pas le paramètre `cluster_purpose`, ou `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, le cluster doit avoir au moins 12 processeurs virtuels disponibles.
>
> Si vous définissez `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, le cluster n’a pas besoin d’autant de processeurs virtuels. Nous vous recommandons d’utiliser au moins 2 processeurs virtuels pour le développement/test. Toutefois, un cluster configuré pour le développement/test n’est pas approprié pour le trafic de production et peut augmenter les temps d’inférence. Par ailleurs, les clusters de développement/test ne garantissent pas une tolérance de panne.

Pour plus d’informations sur la création d’un cluster AKS à l’aide de l’interface de ligne de commande Azure ou du portail, consultez les articles suivants :

* [Créer un cluster AKS (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Créer un cluster AKS (portail)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Les exemples suivants montrent comment attacher un cluster AKS 1.12.## existant à votre espace de travail :

**Avec le kit SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

Pour plus d’informations sur les classes, les méthodes et les paramètres utilisés dans cet exemple, consultez les documents de référence suivants :

* [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Avec l’interface CLI**

Pour attacher un cluster existant à l’aide de l’interface de ligne de commande, vous devez récupérer l’ID de ressource du cluster existant. Pour obtenir cette valeur, utilisez la commande suivante. Remplacez `myexistingcluster` par le nom de votre cluster AKS. Remplacez `myresourcegroup` par le groupe de ressources qui contient le cluster :

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Cette commande retourne une valeur semblable au texte suivant :

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Pour attacher le cluster existant à votre espace de travail, utilisez la commande suivante. Remplacez `aksresourceid` par la valeur retournée par la commande précédente. Remplacez `myresourcegroup` par le groupe de ressources qui contient votre espace de travail. Remplacez `myworkspace` par le nom de votre espace de travail.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Pour plus d’informations, consultez la référence [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks).

## <a name="deploy-to-aks"></a>Déployer sur AKS

Pour déployer un modèle sur Azure Kubernetes Service, créez une __configuration de déploiement__ décrivant les ressources de calcul nécessaires. Par exemple, le nombre de cœurs et la mémoire. Vous avez également besoin d’une __configuration d’inférence__ décrivant l’environnement nécessaire pour héberger le modèle et le service Web. Pour plus d’informations sur la création de la configuration d’inférence, consultez la section [Comment et où déployer des modèles ?](how-to-deploy-and-where.md)

### <a name="using-the-sdk"></a>Utilisation du kit de développement logiciel

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Pour plus d’informations sur les classes, les méthodes et les paramètres utilisés dans cet exemple, consultez les documents de référence suivants :

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Utilisation de l’interface CLI

Pour déployer à l’aide de la CLI, utilisez la commande suivante. Remplacez `myaks` par le nom de la cible de calcul AKS. Remplacez `mymodel:1` par le nom et la version du modèle inscrit. Remplacez `myservice` par le nom à attribuer à ce service :

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

Pour plus d’informations, consultez les informations de référence sur [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy). 

### <a name="using-vs-code"></a>Avec VS Code

Pour plus d’informations sur l’utilisation de VS Code, consultez [déployer sur AKS via l’extension VS Code](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT] 
> Le déploiement via VS Code nécessite que le cluster AKS soit créé ou attaché à votre espace de travail à l’avance.

## <a name="web-service-authentication"></a>Authentification de service web

Lors du déploiement sur Azure Kubernetes Service, l’authentification __basée sur les clés__ est activée par défaut. Vous pouvez également activer l'authentification __par jeton__. L'authentification par jeton exige que les clients utilisent un compte Azure Active Directory pour demander un jeton d'authentification, qui est utilisé pour adresser des requêtes au service déployé.

Pour __désactiver__ l’authentification, définissez le paramètre `auth_enabled=False` lors de la création de la configuration de déploiement. L’exemple suivant désactive l’authentification à l’aide du kit de développement logiciel (SDK) :

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Pour plus d’informations sur l'authentification à partir d’une application cliente, consultez [Utiliser un modèle Azure Machine Learning déployé en tant que service web](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Authentification avec des clés

Si l’authentification par clé est activée, vous pouvez utiliser la méthode `get_keys` pour récupérer des clés d’authentification primaire et secondaire :

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Si vous devez regénérer une clé, utilisez [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Authentification avec des jetons

Pour activer l’authentification par jeton, utilisez le paramètre `token_auth_enabled=True` lorsque vous créez ou mettez à jour un déploiement. L’exemple suivant active l’authentification par jeton à l’aide du kit de développement logiciel (SDK) :

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Si l’authentification par jeton est activée, vous pouvez utiliser la méthode `get_token` pour récupérer un jeton JWT et le délai d’expiration du jeton :

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Vous devrez demander un nouveau jeton après l’heure de `refresh_by` du jeton.
>
> Microsoft recommande vivement de créer votre espace de travail Azure Machine Learning dans la même région que celle de votre Azure Kubernetes Service. Pour s’authentifier avec un jeton, le service web appelle la région dans laquelle votre espace de travail Azure Machine Learning est créé. Si la région de votre espace de travail est indisponible, vous ne pouvez pas extraire de jeton pour votre service web, même si votre cluster se trouve dans une région différente de celle de votre espace de travail. Cela a pour effet d'empêcher l'authentification par jeton tant que la région de votre espace de travail n'est pas disponible. Par ailleurs, plus la distance entre la région de votre cluster et celle de votre espace de travail est élevée, plus l’extraction de jeton prend de temps.

## <a name="update-the-web-service"></a>Mise à jour du service web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser l’expérimentation et l’inférence avec un réseau virtuel Microsoft Azure](how-to-enable-virtual-network.md)
* [Guide pratique pour déployer un modèle à l’aide d’une image Docker personnalisée](how-to-deploy-custom-docker-image.md)
* [Résolution des problèmes liés au déploiement](how-to-troubleshoot-deployment.md)
* [Sécuriser les services web Azure Machine Learning avec SSL](how-to-secure-web-service.md)
* [Utiliser un modèle ML déployé en tant que service web](how-to-consume-web-service.md)
* [Superviser vos modèles Azure Machine Learning avec Application Insights](how-to-enable-app-insights.md)
* [Collecter des données pour des modèles en production](how-to-enable-data-collection.md)
