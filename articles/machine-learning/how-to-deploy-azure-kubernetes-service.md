---
title: Déployer des modèles Machine Learning dans le service Kubernetes
titleSuffix: Azure Machine Learning
description: Découvrez comment déployer vos modèles Azure Machine Learning en tant que service web à l’aide d’Azure Kubernetes Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: ad34195e003e0ca2d73000d3482cc79c3dbe3ee0
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372108"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Déployer un modèle sur un cluster Azure Kubernetes Service
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Découvrez comment utiliser Azure Machine Learning pour déployer un modèle en tant que service web sur Azure Kubernetes service (AKS). Azure Kubernetes Service est idéal pour les déploiements de production à grande échelle. Utilisez Azure Kubernetes Service si vous avez besoin d’une ou de plusieurs des fonctionnalités suivantes :

- __Temps de réponse rapide__.
- __Mise à l’échelle automatique__ du service déployé.
- Options d’__accélération matérielle__, telles que le GPU et les FPGA (Field-Programmable Gate Array).

> [!IMPORTANT]
> La mise à l’échelle du cluster n’est pas fournie par le kit SDK Azure Machine Learning. Pour plus d’informations sur la mise à l’échelle des nœuds dans un cluster AKS, voir [Mettre le nombre de nœuds à l’échelle dans un cluster Azure Kubernetes Service (AKS)](../aks/scale-cluster.md).

Lors d’un déploiement sur Azure Kubernetes Service, vous déployez sur un cluster AKS qui est __connecté à votre espace de travail__. Il existe deux façons de connecter un cluster AKS à votre espace de travail :

* Créez le cluster AKS à l’aide du Kit de développement logiciel (SDK) Azure Machine Learning, de l’interface de ligne de commande Machine Learning ou d’[Azure Machine Learning Studio](https://ml.azure.com). Ce processus connecte automatiquement le cluster à l’espace de travail.
* Attachez un cluster AKS existant à votre espace de travail Azure Machine Learning. Un cluster peut être attaché au moyen du Kit de développement logiciel (SDK) Azure Machine Learning, de l’interface de ligne de commande Machine Learning ou d’Azure Machine Learning Studio.

Le cluster AKS et l’espace de travail AML peuvent se trouver dans des groupes de ressources différents.

> [!IMPORTANT]
> Le processus de création ou d’attachement est une tâche unique. Une fois qu’un cluster AKS est connecté à l’espace de travail, vous pouvez l’utiliser pour les déploiements. Vous pouvez détacher ou supprimer le cluster AKS si vous n’en avez plus besoin. Une fois qu’il est détaché ou supprimé, vous ne pourrez plus déployer sur le cluster.

> [!IMPORTANT]
> Il est fortement recommandé d’effectuer un débogage local avant de procéder au déploiement sur le service web. Pour plus d’informations, consultez [Débogage local](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally).
>
> Vous pouvez également vous reporter à Azure Machine Learning – [Déploiement sur un notebook local](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local).

## <a name="prerequisites"></a>Prérequis

- Un espace de travail Azure Machine Learning. Pour plus d’informations, voir la page [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

- Un modèle Machine Learning inscrit dans votre espace de travail. Si vous n’avez pas de modèle inscrit, consultez la section [Comment et où déployer des modèles](how-to-deploy-and-where.md).

- L’[extension Azure CLI pour Machine Learning service](reference-azure-machine-learning-cli.md), le [SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou l’[extension Azure Machine Learning pour Visual Studio Code](tutorial-setup-vscode-extension.md).

- Les extraits de code __Python__ de cet article partent du principe que les variables suivantes sont définies :

    * `ws` – Sur votre espace de travail.
    * `model` – Sur votre modèle inscrit.
    * `inference_config` – Sur la configuration d’inférence pour le modèle.

    Pour plus d’informations sur le paramétrage de ces variables, consultez la section [Comment et où déployer des modèles ?](how-to-deploy-and-where.md)

- Les extraits de code __CLI__ de cet article partent du principe que vous avez créé un document `inferenceconfig.json`. Pour plus d’informations sur la création de ce document, consultez la section [Comment et où déployer des modèles ?](how-to-deploy-and-where.md)

- Si vous attachez un cluster AKS pour lequel [une plage d’adresses IP autorisées a accès au serveur d’API](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), activez les plages d’adresses IP du plan de contrôle AML pour le cluster AKS. Le plan de contrôle AML est déployé sur les régions jumelées et déploie des pods d’inférence sur le cluster AKS. Le déploiement des pods d’inférence n’est pas possible sans accès au serveur d’API. Utilisez les [plages d’adresses IP](https://www.microsoft.com/en-us/download/confirmation.aspx?id=56519) des deux [régions jumelées]( https://docs.microsoft.com/azure/best-practices-availability-paired-regions) lors de l’activation des plages d’adresses IP dans un cluster AKS.
 
 - Le nom de calcul DOIT être unique dans un espace de travail.
   - Le nom est obligatoire et doit comprendre entre 3 et 24 caractères.
   - Les caractères valides sont les lettres majuscules et minuscules, les chiffres et le caractère -.
   - Le nom doit commencer par une lettre.
   - Le nom doit être unique parmi tous les calculs existants au sein d’une région Azure. Si le nom que vous choisissez n’est pas unique, une alerte s’affiche.
   
 - Si vous souhaitez déployer des modèles sur des nœuds GPU ou FPGA (ou sur une référence SKU spécifique), vous devez créer un cluster de la référence SKU en question. Il n’est pas possible de créer un pool de nœuds secondaire dans un cluster existant et de déployer des modèles dans le pool de nœuds secondaire.
 
 - Si vous devez déployer un équilibreur de charge SLB (Standard Load Balancer) au lieu d’un équilibreur de charge BLB (Basic Load Balancer) dans votre cluster, créez un cluster sur le portail AKS/l’interface CLI/le kit SDK, puis attachez-le à l’espace de travail AML. 



## <a name="create-a-new-aks-cluster"></a>Créer un cluster AKS

**Durée estimée** : environ 10 minutes.

La création ou l’attachement d’un cluster AKS est un processus à effectuer une seule fois pour votre espace de travail. Vous pouvez le réutiliser pour vos autres déploiements. Si vous supprimez le cluster ou le groupe de ressources dans lequel il se trouve, vous devrez recréer un cluster lors du prochain déploiement. Vous pouvez avoir plusieurs clusters AKS attachés à votre espace de travail.
 
Azure Machine Learning prend maintenant en charge l’utilisation d’une instance Azure Kubernetes Service avec activation de la liaison privée.
Pour créer un cluster AKS privé, suivez [ces documents](https://docs.microsoft.com/azure/aks/private-clusters).

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
> Le kit de développement logiciel (SDK) Azure Machine Learning ne prend pas en charge la mise à l’échelle d'un cluster AKS. Pour mettre à l’échelle les nœuds du cluster, utilisez l’interface utilisateur de votre cluster AKS dans Azure Machine Learning Studio. Vous pouvez modifier le nombre de nœuds, mais pas la taille de machine virtuelle du cluster.

Pour plus d’informations sur les classes, les méthodes et les paramètres utilisés dans cet exemple, consultez les documents de référence suivants :

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Avec l’interface CLI**

```azurecli
az ml computetarget create aks -n myaks
```

Pour plus d’informations, consultez la référence [AZ ml computetarget Create AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks).

## <a name="attach-an-existing-aks-cluster"></a>Attacher un cluster AKS existant

**Durée estimée** : 5 minutes environ.

Si vous avez déjà un cluster AKS dans votre abonnement Azure et qu’il s’agit d’une version 1,17 ou antérieure, vous pouvez l’utiliser pour déployer votre image.

> [!TIP]
> Le cluster AKS existant peut se trouver dans une autre région Azure que celle de votre espace de travail Azure Machine Learning.
>
> Si vous souhaitez sécuriser votre cluster AKS à l’aide d’un réseau virtuel Azure, vous devez commencer par créer le réseau virtuel. Pour plus d’informations, voir [Sécuriser l’expérimentation et l’inférence avec un réseau virtuel Microsoft Azure](how-to-enable-virtual-network.md#aksvnet).

Quand vous attachez un cluster AKS à un espace de travail, vous pouvez spécifier la façon dont vous allez l’utiliser en définissant le paramètre `cluster_purpose`.

Si vous ne définissez pas le paramètre `cluster_purpose`, ou `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, le cluster doit avoir au moins 12 processeurs virtuels disponibles.

Si vous définissez `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, le cluster n’a pas besoin d’autant de processeurs virtuels. Nous vous recommandons d’utiliser au moins 2 processeurs virtuels pour le développement/test. Toutefois, un cluster configuré pour le développement/test n’est pas approprié pour le trafic de production et peut augmenter les temps d’inférence. Par ailleurs, les clusters de développement/test ne garantissent pas une tolérance de panne.

> [!WARNING]
> Ne créez pas plusieurs attachements en même temps dans le même cluster AKS depuis votre espace de travail. Par exemple, l’attachement d’un cluster AKS à un espace de travail en utilisant deux noms différents. Chaque nouvel attachement va supprimer le ou les attachements précédents.
>
> Si vous voulez réattacher un cluster AKS, par exemple pour changer le protocole TLS ou un autre paramètre de configuration du cluster, vous devez d’abord supprimer l’attachement existant en utilisant [AksCompute.detach()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Pour plus d’informations sur la création d’un cluster AKS à l’aide de l’interface de ligne de commande Azure ou du portail, consultez les articles suivants :

* [Créer un cluster AKS (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Créer un cluster AKS (portail)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)
* [Créer un cluster AKS (modèle ARM sur les modèles de démarrage rapide Azure)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

Les exemples suivants montrent comment attacher un cluster AKS existant à votre espace de travail :

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

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
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
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Utilisation de l’interface CLI

Pour déployer à l’aide de la CLI, utilisez la commande suivante. Remplacez `myaks` par le nom de la cible de calcul AKS. Remplacez `mymodel:1` par le nom et la version du modèle inscrit. Remplacez `myservice` par le nom à attribuer à ce service :

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Pour plus d’informations, consultez les informations de référence sur [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

### <a name="using-vs-code"></a>Avec VS Code

Pour plus d’informations sur l’utilisation de VS Code, consultez [déployer sur AKS via l’extension VS Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Le déploiement via VS Code nécessite que le cluster AKS soit créé ou attaché à votre espace de travail à l’avance.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Déployer des modèles sur AKS à l’aide d’un déploiement contrôlé (préversion)

Analyser et promouvoir des versions de modèle de manière contrôlée à l’aide de points de terminaison. Vous pouvez déployer jusqu’à six versions derrière un seul point de terminaison. Les points de terminaison offrent les capacités suivantes :

* Configurez le __pourcentage du trafic de scoring envoyé à chaque point de terminaison__. Par exemple, acheminez 20 % du trafic vers le point de terminaison « test » et 80 % vers « production ».

    > [!NOTE]
    > Si 100 % du trafic n’est pas utilisé, tout pourcentage restant est acheminé vers la version __par défaut__ du point de terminaison. Par exemple, si vous configurez que 10 % du trafic ira à la version du point de terminaison « test » et 30 % à « prod », les 60 % restants sont envoyés à la version par défaut du point de terminaison.
    >
    > La première version créée du point de terminaison est automatiquement configurée comme version par défaut. Vous pouvez modifier cela en définissant `is_default=True` lors de la création ou de la mise à jour d’une version de point de terminaison.
     
* Marquez une version de point de terminaison comme __contrôle__ ou __traitement__. Par exemple, la version actuelle du point de terminaison de production peut être le contrôle, tandis que les éventuels nouveaux modèles sont déployés en tant que versions de traitement. Après évaluation des performances des versions de traitement, si l’une d’elles obtient de meilleures performances que le contrôle actuel, elle peut être promue comme nouvelle version de production/contrôle.

    > [!NOTE]
    > Vous ne pouvez avoir qu’__un seul__ contrôle. Vous pouvez avoir plusieurs traitements.

Vous pouvez activer App Insights pour afficher les métriques opérationnelles des points de terminaison et des versions déployées.

### <a name="create-an-endpoint"></a>Créer un point de terminaison
Une fois que vous êtes prêt à déployer vos modèles, créez un point de terminaison de notation et déployez votre première version. L’exemple suivant montre comment déployer et créer le point de terminaison à l’aide du Kit de développement logiciel (SDK). Le premier déploiement sera défini comme la version par défaut, ce qui signifie que tout percentile de trafic non spécifié dans l’ensemble des versions ira à la version par défaut.  

> [!TIP]
> Dans l’exemple suivant, la configuration définit que la version initiale du point de terminaison traite 20 % du trafic. Étant donné qu’il s’agit du premier point de terminaison, il s’agit également de la version par défaut. Et puisque nous n’avons pas d’autres versions pour recevoir les 80 % restants du trafic, ce trafic sera également acheminé vers la version par défaut. Tant que d’autres versions recevant un pourcentage du trafic n’ont pas été déployées, celle-ci reçoit de fait 100 % du trafic.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Mettre à jour un point de terminaison et y ajouter des versions

Ajoutez une autre version à votre point de terminaison et configurez le centile de trafic de notation passant à la version. Il existe deux types de versions : version de contrôle et version de traitement. Il peut y avoir plusieurs versions de traitement pour faciliter la comparaison avec une version de contrôle unique.

> [!TIP]
> La deuxième version, créée par l’extrait de code suivant, accepte 10 % du trafic. La première version est configurée pour 20 %, donc seulement 30 % du trafic est configuré pour des versions spécifiques. Les 70 % restants sont envoyés à la première version du point de terminaison, car il s’agit également de la version par défaut.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

Mettez à jour les versions existantes ou supprimez-les dans un point de terminaison. Vous pouvez modifier le type par défaut, le type de contrôle et le centile de trafic de la version. Dans l’exemple suivant, la deuxième version augmente son part de trafic à 40 % et est désormais la valeur par défaut.

> [!TIP]
> Après l’extrait de code suivant, la deuxième version est désormais la version par défaut. Elle est maintenant configurée pour 40 %, tandis que la version d’origine est toujours configurée pour 20 %. Cela signifie que 40 % du trafic n’est pas utilisé pour les configurations de version. Le trafic restant est acheminé vers la deuxième version, car il s’agit désormais de la version par défaut. Elle reçoit de fait 80 % du trafic.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


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
>
> Pour récupérer un jeton, vous devez utiliser le Kit de développement logiciel (SDK) Azure Machine Learning ou la commande [az ml service obten-access-token](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token).

## <a name="update-the-web-service"></a>Mise à jour du service web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser l’expérimentation et l’inférence avec un réseau virtuel Microsoft Azure](how-to-enable-virtual-network.md)
* [Guide pratique pour déployer un modèle à l’aide d’une image Docker personnalisée](how-to-deploy-custom-docker-image.md)
* [Résolution des problèmes liés au déploiement](how-to-troubleshoot-deployment.md)
* [Utiliser TLS pour sécuriser un service web par le biais d’Azure Machine Learning](how-to-secure-web-service.md)
* [Utiliser un modèle ML déployé en tant que service web](how-to-consume-web-service.md)
* [Superviser vos modèles Azure Machine Learning avec Application Insights](how-to-enable-app-insights.md)
* [Collecter des données pour des modèles en production](how-to-enable-data-collection.md)
