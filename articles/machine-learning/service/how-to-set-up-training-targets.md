---
title: Cibles de calcul pour l’entraînement des modèles
titleSuffix: Azure Machine Learning service
description: Configurer les environnements d’entraînement (cibles de calcul) pour l’entraînement des modèles de machine learning. Vous pouvez facilement basculer entre différents environnements d’entraînement. Commencer l’entraînement en local. Si une montée en charge est nécessaire, basculez vers une cible de calcul basée sur le cloud.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 9037f6d7602f186bc30e55acbc050280bca134ee
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794462"
---
# <a name="set-up-compute-targets-for-model-training"></a>Configurer des cibles de calcul pour l’entraînement des modèles

Avec le service Azure Machine Learning, vous pouvez entraîner votre modèle sur différentes ressources de calcul. Ces ressources de calcul, appelées __cibles de calcul__, peuvent être locales ou dans le cloud. Dans cet article, vous allez découvrir les cibles de calcul prises en charge et comment les utiliser.

Une cible de calcul est une ressource où vous exécutez votre script d’entraînement, ou hébergez votre modèle quand il est déployé comme service web. Vous pouvez créer et gérer une cible de calcul avec le SDK Azure Machine Learning, Azure CLI ou le portail Azure. Si vous avez des cibles de calcul qui ont été créées via un autre service (par exemple un cluster Azure HDInsight), vous pouvez les utiliser en les attachant à votre espace de travail Azure Machine Learning service.

Il existe trois grandes catégories de cibles de calcul prises en charge par Azure Machine Learning :

* __Locales__ : Votre ordinateur local ou une machine virtuelle cloud que vous utilisez comme environnement de développement et d’expérience. 
* __Capacité de calcul managée__ : Capacité de calcul Azure Machine Learning est une offre de capacité de calcul gérée par Azure Machine Learning service. Elle vous permet de créer facilement des capacités de calcul à un ou plusieurs nœuds pour l’entraînement, le test et l’inférence par lot.
* __Capacité de calcul attachée__ : Vous pouvez également utiliser votre propre capacité de calcul Azure et l’attacher à Azure Machine Learning. Vous trouverez plus d’informations sur les types de capacités de calcul pris en charge et leur utilisation dans les sections suivantes.


## <a name="supported-compute-targets"></a>Cibles de calcul prises en charge

La prise en charge par le service Azure Machine Learning varie selon les différentes cibles de calcul. Un cycle de vie typique du développement d’un modèle commence par le déploiement et l’expérience sur une petite quantité de données. À ce stade, nous vous recommandons d’utiliser un environnement local tel que votre ordinateur local ou une machine virtuelle cloud. Quand vous effectuez une montée en puissance de votre entraînement sur des jeux de données plus volumineux ou que vous effectuez un entraînement distribué, utilisez un environnement Compute Azure Machine Learning pour créer un cluster avec un ou plusieurs nœuds qui se met à l’échelle automatiquement chaque fois que vous soumettez une exécution. Vous pouvez également attacher votre propre ressource de calcul, bien que la prise en charge des différents scénarios puisse varier comme indiqué dans le tableau ci-dessous :

|Cible de calcul| Accélération GPU | Automatisé<br/> optimisation des hyperparamètres | Automatisé</br> Apprentissage automatique | Pipelines faciles à utiliser|
|----|:----:|:----:|:----:|:----:|
|[Ordinateur local](#local)| Peut-être | &nbsp; | ✓ | &nbsp; |
|[Capacité de calcul Azure Machine Learning](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Machine virtuelle distante](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Service Analytique Azure Data Lake](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ _Azure Databricks et Azure Data Lake Analytics ne peuvent être utilisés que dans un pipeline._<br/>
> Pour plus d’informations sur les pipelines, consultez [Pipelines dans Azure Machine Learning](concept-ml-pipelines.md).
>
> L’environnement Compute Azure Machine Learning doit être créé depuis un espace de travail. Vous ne pouvez pas attacher des instances existantes à un espace de travail.
>
> Les autres cibles de calcul doivent être créées en dehors d’Azure Machine Learning, puis attachées à votre espace de travail.
>
> Lors de l’entraînement d’un modèle, certaines cibles de calcul s’appuient sur des images de conteneur Docker. L’image de base GPU doit être utilisée seulement sur les services Microsoft Azure. Pour l’entraînement des modèles, ces services sont :
> * Capacité de calcul Azure Machine Learning
> * Azure Kubernetes Service
> * Windows Data Science Virtual Machine (DSVM)

## <a name="workflow"></a>Workflow

Le workflow de développement et de déploiement de modèle avec Azure Machine Learning suit ces étapes :

1. Développez les scripts d’entraînement de machine learning avec Python.
1. Créez et configurez la cible de calcul ou attachez une cible de calcul existante.
1. Envoyer les scripts d’entraînement à la cible de calcul.
1. Inspecter les résultats afin d’identifier le meilleur modèle.
1. Inscrire le modèle dans le registre de modèles.
1. Déployer le modèle.

> [!NOTE]
> Votre script d’entraînement n’est pas lié à une cible de calcul spécifique. Vous pouvez effectuer l’entraînement initial sur votre ordinateur local, puis changer de cible de calcul sans avoir à réécrire le script d’entraînement.
> 
> Lorsque vous associez une cible de calcul à votre espace de travail, en créant une capacité de calcul managée ou en attachant une capacité de calcul existante, donnez un nom à votre capacité de calcul. Le nom doit contenir entre 2 et 16 caractères.

Pour basculer d’une cible de calcul à une autre, vous devez créer une [configuration d’exécution](concept-azure-machine-learning-architecture.md#run-configuration). La configuration d’exécution définit comment exécuter le script sur la cible de calcul.

## <a name="training-scripts"></a>Scripts d’entraînement

Quand vous démarrez une exécution d’entraînement, un instantané du répertoire qui contient vos scripts d’entraînement est créé et envoyé à la cible de calcul. Pour plus d’informations, consultez [Instantanés](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Ordinateur local

Lors de l’entraînement local, vous utilisez le SDK pour envoyer l’opération d’entraînement. Vous pouvez entraîner à l’aide d’un environnement géré par l’utilisateur ou par le système.

### <a name="user-managed-environment"></a>Environnement géré par l’utilisateur

Dans un environnement géré par l’utilisateur, vérifiez que tous les packages nécessaires sont disponibles dans l’environnement Python dans lequel vous exécutez le script. L’extrait de code suivant est un exemple de configuration de l’entraînement pour un environnement géré par l’utilisateur :

```python
from azureml.core.runconfig import RunConfiguration

# Edit a run configuration property on the fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# Choose a specific Python environment by pointing to a Python path. For example:
# run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>Environnement géré par le système

Les environnements gérés par le système s’appuient sur conda pour gérer les dépendances. Conda crée un fichier nommé **conda_dependencies.yml** qui contient une liste de dépendances. Vous pouvez demander au système de générer un nouvel environnement Conda et d’y exécuter vos scripts. Les environnements gérés par le système peuvent être réutilisés ultérieurement, à condition que le fichier conda_dependencies.yml reste inchangé. 

La configuration initiale d’un nouvel environnement peut prendre plusieurs minutes, en fonction de la taille des dépendances nécessaires. L’extrait de code suivant illustre la création d’un environnement géré par le système qui dépend de scikit-learn :

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify the conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Capacité de calcul Azure Machine Learning

Capacité de calcul Azure Machine Learning est une infrastructure de capacité de calcul managée qui permet à l’utilisateur de créer facilement une capacité de calcul à un ou plusieurs nœuds. La capacité de calcul est créée dans la région de votre espace de travail sous forme de ressource qui peut être partagée avec d’autres utilisateurs dans votre espace de travail. La capacité de calcul monte en puissance automatiquement quand un travail est soumis et peut être placée dans un réseau virtuel Azure. La capacité de calcul s’exécute dans un environnement conteneurisé et crée un package des dépendances de votre modèle dans un conteneur Docker.

Vous pouvez utiliser une capacité de calcul Azure Machine Learning pour distribuer le processus d’entraînement sur un cluster de nœuds de capacité de calcul de CPU ou de GPU dans le cloud. Pour plus d’informations sur les tailles de machine virtuelle qui incluent des GPU, consultez [Tailles de machine virtuelle à GPU optimisé](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

> [!NOTE]
> Capacité de calcul Azure Machine Learning comporte des limites par défaut, par exemple le nombre de cœurs qui peuvent être alloués. Pour plus d’informations, consultez [Gérer et demander des quotas pour les ressources Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

Vous pouvez créer un environnement Compute Azure Machine Learning à la demande quand vous planifiez une exécution ou comme ressource persistante.

### <a name="run-based-creation"></a>Création basée sur l’exécution

Vous pouvez créer un environnement Compute Azure Machine Learning comme cible de calcul au moment de l’exécution. La capacité de calcul est automatiquement créée pour votre exécution et ajustée au nombre **max_nodes** spécifié dans la configuration de votre exécution. La capacité de calcul est automatiquement supprimée une fois l’exécution terminée.

> [!IMPORTANT]
> La création d’un environnement Compute Azure Machine Learning basée sur l’exécution est actuellement en préversion. N’utilisez pas la création basée sur l’exécution si vous utilisez l’optimisation automatisée des hyperparamètres ou le machine learning automatisé. Pour utiliser l’optimisation des hyperparamètres ou le machine learning automatisé, créez l’environnement Compute Azure Machine Learning avant de soumettre une exécution.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

# First, list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# Create a new runconfig object
run_config = RunConfiguration()

# Signal that you want to use AmlCompute to execute the script
run_config.target = "amlcompute"

# AmlCompute is created in the same region as your workspace
# Set the VM size for AmlCompute from the list of supported_vmsizes
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>Capacité de calcul persistante : De base

Un environnement Compute Azure Machine Learning persistant peut être réutilisé par plusieurs travaux. Il peut être partagé avec d’autres utilisateurs dans l’espace de travail et conservé entre les travaux.

Pour créer une ressource d’environnement Compute Azure Machine Learning persistante, vous spécifiez les propriétés **vm_size** et **max_nodes**. Azure Machine Learning utilise ensuite des valeurs calculées par défaut pour les autres propriétés. La capacité de calcul se met à l’échelle automatiquement sur zéro nœud quand elle n’est pas utilisée et crée des machines virtuelles dédiées pour exécuter vos travaux en fonction des besoins. 

* **vm_size** : famille de machines virtuelles des nœuds créés par Capacité de calcul Azure Machine Learning.
* **max_nodes** : nombre maximal de nœuds pour la mise à l’échelle automatique lors de l’exécution d’un travail sur Capacité de calcul Azure Machine Learning.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>Capacité de calcul persistante : Avancé

Vous pouvez aussi configurer plusieurs propriétés avancées lors de la création d’un environnement Compute Azure Machine Learning. Ces propriétés vous permettent de créer un cluster persistant de taille fixe, ou au sein d’un réseau virtuel Azure existant dans votre abonnement.

En plus des propriétés **vm_size** et **max_nodes**, vous pouvez également utiliser les suivantes :

* **min_nodes** : nombre minimal de nœuds pour la mise à l’échelle lors de l’exécution d’un travail sur un environnement Compute Azure Machine Learning. La valeur minimale par défaut est zéro (0) nœud.
* **vm_priority** : Le type de machine virtuelle à utiliser lors de la création d’une ressource d’environnement Compute Azure Machine Learning. Choisissez une option entre **dédié** (par défaut) et **basse priorité**. Les machines virtuelles à basse priorité utilisent la capacité excédentaire dans Azure. Ces machines virtuelles sont moins chères, mais les exécutions peuvent être empêchées lorsque ces machines virtuelles sont utilisées.
* **idle_seconds_before_scaledown** : durée d’inactivité à attendre après une exécution et avant la mise à l’échelle automatique vers le nombre **min_nodes**. La durée d’inactivité par défaut est 120 secondes.
* **vnet_resourcegroup_name** : Le groupe de ressources du réseau virtuel __existant__. L’environnement Compute Azure Machine Learning est créé dans ce réseau virtuel.
* **vnet_name** : nom du réseau virtuel. Le réseau virtuel doit être dans la même région que votre espace de travail Azure Machine Learning.
* **subnet_name** : nom du sous-réseau au sein du réseau virtuel. Les ressources de l’environnement Compute Azure Machine Learning reçoivent des adresses IP de la plage de ce sous-réseau.

> [!TIP]
> Lorsque vous créez une ressource d’environnement Compute Azure Machine Learning persistante, vous pouvez mettre à jour les propriétés telles que les nombres **min_nodes** ou **max_nodes**. Pour ce faire, appelez la fonction `update()` pour la propriété en question.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist 
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           vm_priority='lowpriority',
                                                           min_nodes=2,
                                                           max_nodes=4,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```


## <a id="vm"></a>Machine virtuelle distante

Azure Machine Learning prend également en charge l’utilisation de votre propre ressource de calcul et son attachement à votre espace de travail. Un tel type de ressource est une machine virtuelle distante arbitraire tant qu’elle est accessible depuis Azure Machine Learning service. Il peut s’agir d’une machine virtuelle Azure, d’un serveur distant dans votre organisation, ou encore d’un serveur local. En particulier, avec l’adresse IP et les informations d’identification (nom d’utilisateur/mot de passe ou clé SSH), vous pouvez utiliser n’importe quelle machine virtuelle accessible pour les exécutions à distance.
Vous pouvez utiliser un environnement Conda intégré au système, un environnement Python existant ou un conteneur Docker. Lorsque vous exécutez à l’aide d’un conteneur Docker, vous devez exécuter un moteur Docker sur la machine virtuelle. La fonctionnalité de machine virtuelle distante est particulièrement utile quand vous voulez obtenir un environnement cloud de développement et expérience plus flexible que votre ordinateur local.

> [!TIP]
> Utilisez la DSVM comme machine virtuelle Azure pour ce scénario. Cette machine virtuelle est un environnement préconfiguré de développement de science des données et d’intelligence artificielle dans Azure. Elle offre un choix organisé d’outils et de frameworks pour le cycle de vie complet du développement Machine Learning. Pour plus d’informations sur l’utilisation de la DSVM avec Azure Machine Learning, consultez [Configurer un environnement de développement](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

> [!WARNING]
> Azure Machine Learning prend uniquement en charge les machines virtuelles exécutant Ubuntu. Lorsque vous créez une machine virtuelle ou en choisissez une existante, celle-ci doit utiliser Ubuntu.

Les étapes suivantes utilisent le SDK pour configurer une DSVM comme cible d’entraînement :

1. Pour attacher une machine virtuelle existante comme cible de calcul, vous devez fournir le nom de domaine complet, le nom d’utilisateur et le mot de passe de la machine virtuelle. Dans l’exemple, remplacez \<fqdn> par le nom de domaine complet public de la machine virtuelle, ou l’adresse IP publique. Remplacez \<username> et \<password> par le nom d’utilisateur SSH et le mot de passe de la machine virtuelle.

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create the compute config
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")

    # If you use SSH instead of a password, use this code:
    #                                                  ssh_port=22,
    #                                                  username='<username>',
    #                                                  password=None,
    #                                                  private_key_file="path-to-file",
    #                                                  private_key_passphrase="passphrase")

    # Attach the compute target
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. Créer une configuration pour la cible de calcul de DSVM. Docker et Conda sont utilisés pour créer et configurer l’environnement d’entraînement sur la DSVM.

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load into memory the cpu-dsvm.runconfig file created in the previous attach operation
    run_config = RunConfiguration(framework = "python")

    # Set the compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use the CPU base image
    # To use GPU in DSVM, you must also use the GPU base Docker image "azureml.core.runconfig.DEFAULT_GPU_IMAGE"
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask the system to provision a new conda environment based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when they're used for the first time
    run_config.prepare_environment = True

    # Specify the CondaDependencies object
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Azure Databricks

Azure Databricks est un environnement basé sur Apache Spark dans le cloud Azure. L’environnement peut être utilisé comme une cible de calcul lors de l’entraînement de modèles avec un pipeline Azure Machine Learning.

> [!IMPORTANT]
> Une cible de calcul Azure Databricks est utilisable uniquement dans un pipeline Machine Learning.
>
> Vous devez créer un espace de travail Azure Databricks avant de l’utiliser pour entraîner votre modèle. Pour créer ces ressources, consultez [Exécuter un travail Spark sur Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Pour attacher Azure Databricks comme cible de calcul, vous devez utiliser le Kit de développement logiciel Azure Machine Learning et fournir les informations suivantes :

* __Nom de la capacité de calcul__ : nom à attribuer à cette ressource de calcul.
* __Nom de l’espace de travail Databricks__ : nom de l’espace de travail Azure Databricks.
* __Jeton d’accès__ : jeton d’accès utilisé pour s’authentifier auprès d’Azure Databricks. Pour générer un jeton d’accès, consultez [Authentification](https://docs.azuredatabricks.net/api/latest/authentication.html).

Le code suivant montre comment attacher Azure Databricks comme cible de calcul :

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create the attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```

## <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics est une plateforme analytique de Big Data dans le cloud Azure. La plateforme peut être utilisée comme une cible de calcul lors de l’entraînement de modèles avec un pipeline Azure Machine Learning.

> [!IMPORTANT]
> Une cible de calcul Azure Data Lake Analytics est utilisable uniquement dans un pipeline Machine Learning.
>
> Vous devez créer un compte Azure Data Lake Analytics avant de l’utiliser pour entraîner votre modèle. Pour créer cette ressource, consultez [Prise en main d’Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Pour attacher Data Lake Analytics comme cible de calcul, vous devez utiliser le Kit de développement logiciel Azure Machine Learning et fournir les informations suivantes :

* __Nom de la capacité de calcul__ : nom à attribuer à cette ressource de calcul.
* __Groupe de ressources__ : groupe de ressources contenant le compte Data Lake Analytics.
* __Nom du compte__ : Le nom du compte Data Lake Analytics.

Le code suivant montre comment attacher Data Lake Analytics comme cible de calcul :

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    
    # Create the attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach the ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Les pipelines Azure Machine Learning fonctionnent uniquement avec les données stockées dans le magasin de données par défaut du compte Data Lake Analytics. Si les données dont vous avez besoin figurent dans un magasin non défini par défaut, vous pouvez utiliser une opération [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) pour copier les données avant d’entraîner le modèle.

## <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight est une plateforme populaire pour l’analytique de Big Data. Elle fournit Apache Spark, que vous pouvez utiliser pour entraîner votre modèle.

> [!IMPORTANT]
> Vous devez créer le cluster HDInsight avant de l’utiliser pour entraîner votre modèle. Pour créer un cluster Spark sur HDInsight, consultez [Créer un cluster Spark dans HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql).
>
> Lors de la création du cluster, vous devez spécifier un nom d’utilisateur SSH et un mot de passe. Notez ces valeurs, car vous en aurez besoin lors de l’utilisation de HDInsight comme cible de calcul.
>
> Une fois le cluster créé, il possède le nom de domaine complet \<clustername>.azurehdinsight.net, où \<clustername> est le nom que vous avez donné au cluster. Vous avez besoin de l’adresse du nom de domaine complet (ou de l’adresse IP publique du cluster) pour utiliser le cluster comme cible de calcul.

Pour configurer HDInsight comme cible de calcul, vous devez fournir le nom de domaine complet, le nom d’utilisateur et le mot de passe du cluster HDInsight. L’exemple suivant utilise le SDK pour attacher un cluster à votre espace de travail. Dans l’exemple, remplacez \<fqdn> par le nom de domaine complet public du cluster, ou l’adresse IP publique. Remplacez \<username> et \<password> par le nom d’utilisateur SSH et le mot de passe du cluster.

> [!NOTE]
> Pour trouver le nom de domaine complet de votre cluster, accédez au portail Azure et sélectionnez votre cluster HDInsight. Sous __Vue d'ensemble__, le nom de domaine complet apparaît dans l’entrée __URL__. Pour obtenir le nom de domaine complet, supprimez le préfixe https:\// du début de l’entrée.

![Obtenir le nom de domaine complet d’un cluster HDInsight dans le portail Azure](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attach an HDInsight cluster as a compute target
    attach_config = HDInsightCompute.attach_configuration(address = "fqdn-or-ipaddress",
                                                          ssh_port = 22,
                                                          username = "username",
                                                          password = None, #if using ssh key
                                                          private_key_file = "path-to-key-file",
                                                          private_key_phrase = "key-phrase")
    compute = ComputeTarget.attach(ws, "myhdi", attach_config)
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure the HDInsight run
# Load the runconfig object from the myhdi.runconfig file generated in the previous attach operation
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# Ask the system to prepare the conda environment automatically when it's used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-runs"></a>Soumettre des exécutions d’entraînement

Il existe deux façons de soumettre une exécution d’entraînement :

* Soumettre une exécution d’entraînement à l’aide d’un objet `ScriptRunConfig`.
* Soumettre une exécution d’entraînement à l’aide d’un objet `Pipeline`.

> [!IMPORTANT]
> Les cibles de calcul Azure Databricks et Azure Data Lake Analytics peuvent uniquement être utilisées dans un pipeline.
>
> La cible de calcul locale ne peut pas être utilisée dans un pipeline.

### <a name="scriptrunconfig-object"></a>Objet ScriptRunConfig

Le modèle de code pour soumettre une exécution d’entraînement avec l’objet `ScriptRunConfig` est le même pour tous les types de cibles de calcul :

1. Créez un objet `ScriptRunConfig` à l’aide de la configuration d’exécution de la cible de calcul.
1. Soumettez l’exécution.
1. Attendez la fin de l’exécution.

L’exemple suivant utilise la configuration de la cible de calcul locale gérée par le système créée plus haut :

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="pipeline-object"></a>Objet Pipeline

Le modèle de code pour soumettre une exécution d’entraînement avec un objet `Pipeline` est le même pour tous les types de cibles de calcul :

1. Ajoutez une étape à l’objet `Pipeline` pour la ressource de calcul.
1. Soumettez une exécution à l’aide du pipeline.
1. Attendez la fin de l’exécution.

L’exemple suivant utilise la cible de calcul Azure Databricks créée plus haut :

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    databricks_compute=databricks_compute,
    allow_reuse=False
)

# List of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

Pour plus d’informations sur les pipelines Machine Learning, consultez [Pipelines et Azure Machine Learning](concept-ml-pipelines.md).

Pour des exemples de notebooks Jupyter montrant comment entraîner un modèle à l’aide d’un pipeline, consultez [https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline).

## <a name="access-computes-in-the-azure-portal"></a>Accéder aux capacités de calcul dans le portail Azure

Vous pouvez accéder aux cibles de calcul associées à votre espace de travail à partir du portail Azure. 

### <a name="view-compute-targets"></a>Afficher les cibles de calcul

Pour consulter les cibles de calcul de votre espace de travail, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre espace de travail.
1. Sous __Applications__, sélectionnez __Capacité de calcul__.

    ![Afficher les cibles de calcul](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Créer une cible de calcul

Suivez la procédure ci-dessus pour afficher la liste des cibles de calcul. Puis procédez comme suit pour créer une cible de calcul :

1. Cliquez sur le signe Plus (+) pour ajouter une cible de calcul.

    ![Ajouter une cible de calcul](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Entrez un nom pour la cible de calcul.
1. Sélectionnez **Capacité de calcul Machine Learning** comme type de capacité de calcul à utiliser pour __Entraînement__.

    > [!IMPORTANT]
    > Vous ne pouvez créer qu’un environnement Compute Azure Machine Learning comme ressource de calcul managée pour l’entraînement.

1. Remplissez le formulaire. Indiquez une valeur pour les propriétés requises, notamment **Famille de machines virtuelles**, ainsi que le **nombre maximal de nœuds** à utiliser pour mettre en place la capacité de calcul. 
1. Sélectionnez __Créer__.
1. Pour afficher l’état de l’opération de création, sélectionnez la cible de calcul dans la liste :

    ![Sélectionnez une cible de calcul pour afficher l’état de l’opération de création](./media/how-to-set-up-training-targets/View_list.png)

1. Vous voyez alors les détails de la cible de calcul :

    ![Consultez les détails de la cible de calcul](./media/how-to-set-up-training-targets/compute-target-details.png)

Vous pouvez désormais soumettre une exécution sur les cibles de l’ordinateur comme décrit précédemment.


### <a name="reuse-existing-compute-targets"></a>Réutiliser des cibles de calcul existantes

Suivez la procédure décrite plus haut pour afficher la liste des cibles de calcul. Puis procédez comme suit pour réutiliser une cible de calcul :

1. Cliquez sur le signe Plus (+) pour ajouter une cible de calcul.
1. Entrez un nom pour la cible de calcul.
1. Sélectionnez le type de capacité de calcul à attacher pour __Entraînement__ :

    > [!IMPORTANT]
    > Il n’est pas possible d’attacher tous les types de capacités de calcul à l’aide du portail Azure.
    > Actuellement, les types qui peuvent être attachés pour l’entraînement sont les suivants :
    >
    > * Machine virtuelle distante
    > * Azure Databricks
    > * Service Analytique Azure Data Lake
    > * Azure HDInsight

1. Remplissez le formulaire et indiquez une valeur pour les propriétés requises.

    > [!NOTE]
    > Microsoft recommande d’utiliser des clés SSH, car elles sont plus sûres que les mots de passe. Les mots de passe sont vulnérables aux attaques en force brute. Les clés SSH utilisent des signatures de chiffrement. Pour plus d’informations sur la création de clés SSH pour une utilisation avec Machines virtuelles Azure, consultez les documents suivants :
    >
    > * [Créer et utiliser des clés SSH sur Linux ou macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Créer et utiliser des clés SSH sur Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Sélectionnez __Attacher__.
1. Pour afficher l’état de l’opération d’attachement, sélectionnez la cible de calcul dans la liste.

Vous pouvez désormais soumettre une exécution sur ces cibles de calcul comme décrit précédemment.

## <a name="notebook-examples"></a>Exemples de notebooks

Pour voir des exemples, consultez les notebooks dans les emplacements suivants :

* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Référence du SDK Azure Machine Learning](https://aka.ms/aml-sdk)
* [Tutoriel : Entraîner un modèle](tutorial-train-models-with-aml.md)
* [Où déployer les modèles](how-to-deploy-and-where.md)
* [Générer des pipelines de machine learning avec le service Azure Machine Learning](concept-ml-pipelines.md)
