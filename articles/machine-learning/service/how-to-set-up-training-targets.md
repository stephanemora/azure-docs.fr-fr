---
title: Cibles de calcul pour l’entraînement des modèles
titleSuffix: Azure Machine Learning service
description: Configurer les environnements d’entraînement (cibles de calcul) pour l’entraînement des modèles de machine learning. Vous pouvez facilement basculer entre des environnements d’entraînement. Démarrez l’entraînement localement et, si une montée en charge est nécessaire, basculez vers une cible de calcul basée sur le cloud. Databricks
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
ms.openlocfilehash: 664d56daf3e70e2e5699d0c07331c466c60e06c5
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338856"
---
# <a name="set-up-compute-targets-for-model-training"></a>Configurer des cibles de calcul pour l’entraînement des modèles

Avec le service Azure Machine Learning, vous pouvez entraîner votre modèle sur différentes ressources de calcul. Ces ressources de calcul, appelées __cibles de calcul__, peuvent être locales ou dans le cloud. Dans ce document, vous allez découvrir les cibles de calcul prises en charge et comment les utiliser.

Une cible de calcul est une ressource où votre script d’entraînement est exécutée, ou bien où votre modèle est hébergé quand il est déployé comme service web. Vous pouvez créer et gérer une cible de calcul avec le SDK Azure Machine Learning, Azure CLI ou le portail Azure. Si vous avez des cibles de calcul qui ont été créées via un autre service (par exemple un cluster HDInsight), vous pouvez les utiliser en les attachant à votre espace de travail du service Azure Machine Learning.

Il existe trois grandes catégories de cibles de calcul prises en charge par Azure Machine Learning :

* __Locales__ : Votre ordinateur local ou une machine virtuelle cloud que vous utilisez comme environnement de développement/expérience. 

* __Capacité de calcul managée__ : Capacité de calcul Azure Machine Learning est une offre de capacité de calcul gérée par le service Azure Machine Learning. Il vous permet de créer facilement des capacités de calcul à un ou plusieurs nœuds pour l’entraînement, le test et l’inférence par lot.

* __Capacité de calcul attachée__ : Vous pouvez également utiliser votre propre capacité de calcul Azure et l’attacher à Azure Machine Learning. Vous trouverez plus d’informations ci-dessous sur les types de capacité de calcul pris en charge et comment les utiliser.


## <a name="supported-compute-targets"></a>Cibles de calcul prises en charge

La prise en charge par le service Azure Machine Learning varie selon les différentes cibles de calcul. Un cycle de vie typique du développement d’un modèle commence par le développement/l’expérience sur une petite quantité de données. À ce stade, nous recommandons d’utiliser un environnement local. Par exemple, votre ordinateur local ou une machine virtuelle basée cloud. Quand vous effectuez un scale-up de votre entraînement sur des jeux de données plus grands ou que vous faites un entraînement distribué, nous recommandons d’utiliser Capacité de calcul Azure Machine Learning pour créer un cluster avec un ou plusieurs nœuds qui se met à l’échelle automatiquement chaque fois que vous lancez une exécution. Vous pouvez également attacher votre propre ressource de calcul, bien que la prise en charge des différents scénarios puisse varier comme indiqué ci-dessous :

|Cible de calcul| Accélération GPU | Réglage automatisé des hyperparamètres | Machine learning automatisé | Pipelines faciles à utiliser|
|----|:----:|:----:|:----:|:----:|
|[Ordinateur local](#local)| Peut-être | &nbsp; | ✓ | &nbsp; |
|[Capacité de calcul Azure Machine Learning](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Machine virtuelle distante](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Service Analytique Azure Data Lake](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ Azure Databricks et Azure Data Lake Analytics peuvent être utilisés __seulement__ dans un pipeline. Pour plus d’informations sur les pipelines, consultez le document [Pipelines dans Azure Machine Learning](concept-ml-pipelines.md).

> [!IMPORTANT]
> Capacité de calcul Azure Machine Learning doit être créé depuis un espace de travail. Vous ne pouvez pas attacher des instances existantes à un espace de travail.
>
> Les autres cibles de calcul doivent être créées en dehors d’Azure Machine Learning, puis attachées à votre espace de travail.

> [!NOTE]
> Certaines cibles de calcul s’appuient sur des images de conteneur Docker lors de l’entraînement d’un modèle. L’image de base GPU doit être utilisée seulement sur les services Microsoft Azure. Pour l’entraînement des modèles, ces services sont :
>
> * Capacité de calcul Azure Machine Learning
> * Azure Kubernetes Service
> * Data Science Virtual Machine

## <a name="workflow"></a>Workflow

Le workflow de développement et de déploiement de modèle avec Azure Machine Learning suit ces étapes :

1. Développer des scripts d’entraînement de machine learning avec Python.
1. Créer et configurer ou attacher une cible de calcul existante.
1. Envoyer les scripts d’entraînement à la cible de calcul.
1. Inspecter les résultats afin d’identifier le meilleur modèle.
1. Inscrire le modèle dans le registre de modèles.
1. Déployer le modèle.

> [!IMPORTANT]
> Votre script d’entraînement n’est pas lié à une cible de calcul spécifique. Vous pouvez effectuer l’entraînement initial sur votre ordinateur local, puis changer de cible de calcul sans avoir à réécrire le script d’entraînement.

> [!TIP]
> Chaque fois que vous associez une cible de calcul à votre espace de travail, en créant une capacité de calcul managée ou en attachant une capacité de calcul existante, vous devez spécifier un nom pour votre capacité de calcul. Il doit comprendre entre 2 et 16 caractères.

Le basculement d’une cible de calcul à une autre implique la création d’une [configuration d’exécution](concept-azure-machine-learning-architecture.md#run-configuration). La configuration d’exécution définit comment exécuter le script sur la cible de calcul.

## <a name="training-scripts"></a>Scripts d’entraînement

Quand vous démarrez une exécution d’entraînement, un instantané du répertoire qui contient vos scripts d’entraînement est créé et envoyé à la cible de calcul. Pour plus d’informations, consultez [Instantanés](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Ordinateur local

Lors de l’entraînement local, vous utilisez le SDK pour envoyer l’opération d’entraînement. Vous pouvez entraîner à l’aide d’un environnement géré par l’utilisateur ou par le système.

### <a name="user-managed-environment"></a>Environnement géré par l’utilisateur

Dans un environnement géré par l’utilisateur, vous devez vous assurer que tous les packages nécessaires sont disponibles dans l’environnement Python dans lequel vous choisissez d’exécuter le script. L’extrait de code suivant est un exemple de configuration de l’entraînement pour un environnement géré par l’utilisateur :

```python
from azureml.core.runconfig import RunConfiguration

# Editing a run configuration property on-fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>Environnement géré par le système

Les environnements gérés par le système s’appuient sur conda pour gérer les dépendances. Conda crée un fichier nommé `conda_dependencies.yml` qui contient une liste de dépendances. Vous pouvez ensuite demander au système de générer un nouvel environnement conda et d’y exécuter vos scripts. Les environnements gérés par le système peuvent être réutilisés ultérieurement, à condition que les fichiers `conda_dependencies.yml` restent inchangés. 

La configuration initiale d’un nouvel environnement peut prendre plusieurs minutes, en fonction de la taille des dépendances nécessaires. L’extrait de code suivant illustre la création d’un environnement géré par le système qui dépend de scikit-learn :

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Capacité de calcul Azure Machine Learning

Capacité de calcul Machine Learning est une infrastructure de capacité de calcul managée qui permet à l’utilisateur de créer facilement une capacité de calcul à un ou plusieurs nœuds. Il est créé __dans la région de votre espace de travail__ et il s’agit d’une ressource qui peut être partagée avec d’autres utilisateurs dans votre espace de travail. Il fait l’objet d’un scale-up automatique quand un travail est soumis et peut être placé dans un réseau virtuel Azure. Il s’exécute dans un __environnement conteneurisé__, avec un empaquetage des dépendances de votre modèle dans un conteneur Docker.

Vous pouvez utiliser une capacité de calcul Azure Machine Learning pour distribuer le processus d’entraînement sur un cluster de nœuds de capacité de calcul de CPU ou de GPU dans le cloud. Pour plus d’informations sur les tailles de machine virtuelle qui incluent des GPU, consultez la documentation [Tailles de machine virtuelle à GPU optimisé](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

> [!NOTE]
> Capacité de calcul Azure Machine Learning a des limites par défaut sur des éléments comme le nombre de cœurs qui peuvent être alloués. Pour plus d’informations, consultez le document [Gérer et demander des quotas pour les ressources Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

Vous pouvez créer une Capacité de calcul Azure Machine Learning à la demande quand vous planifiez une exécution ou comme ressource persistante.

### <a name="run-based-creation"></a>Création basée sur l’exécution

Vous pouvez créer une Capacité de calcul Azure Machine Learning comme cible de calcul au moment de l’exécution. Dans ce cas, la capacité de calcul est créée automatiquement pour votre exécution, fait l’objet d’un scale-up jusqu’à la valeur de max_nodes que vous spécifiez dans la configuration de votre exécution, puis elle est __supprimée automatiquement__ après l’exécution.

> [!IMPORTANT]
> La création de la Capacité de calcul Azure Machine Learning basée sur l’exécution est actuellement en préversion. N’utilisez pas la création basée sur l’exécution si vous utilisez l’optimisation des hyperparamètres ou le machine learning automatisé. Si vous avez besoin d’utiliser l’optimisation des hyperparamètres ou le machine learning automatisé, créez la Capacité de calcul Azure Machine Learning avant de soumettre une exécution.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

#Let us first list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use AmlCompute to execute script.
run_config.target = "amlcompute"

# AmlCompute will be created in the same region as workspace. Set vm size for AmlCompute from the list returned above
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>Capacité de calcul persistante (De base)

Une Capacité de calcul Azure Machine Learning persistante peut être réutilisée par plusieurs travaux. Il peut être partagé avec d’autres utilisateurs dans l’espace de travail et conservé entre les travaux.

Pour créer une ressource Capacité de calcul Azure Machine Learning persistante, vous spécifiez les paramètres `vm_size` et `max_nodes`. Azure Machine Learning utilise ensuite des valeurs calculées par défaut pour le reste des paramètres.  Par exemple, la capacité de calcul est définie de façon à se mettre à l’échelle automatiquement avec zéro nœud quand elle n’est pas utilisée et à créer des machines virtuelles dédiées pour exécuter vos travaux en fonction des besoins. 

* **vm_size** : famille de machines virtuelles des nœuds créés par Capacité de calcul Azure Machine Learning.
* **max_nodes** : nombre maximal de nœuds pour la mise à l’échelle automatique lors de l’exécution d’un travail sur Capacité de calcul Azure Machine Learning.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>Capacité de calcul persistante (Avancé)

Vous pouvez aussi configurer plusieurs propriétés avancées lors de la création d’une Capacité de calcul Azure Machine Learning.  Ces propriétés vous permettent de créer un cluster persistant de taille fixe, ou au sein d’un réseau virtuel Azure existant dans votre abonnement.

En plus de `vm_size` et `max_nodes`, vous pouvez utiliser les propriétés suivantes :

* **min_nodes** : nombre minimal de nœuds (0 nœud par défaut) pour la mise à l’échelle décroissante lors de l’exécution d’un travail sur Capacité de calcul Azure Machine Learning.
* **vm_priority** : choisissez entre des machines virtuelles « dédiées » (valeur par défaut) et « lowpriority » (Basse priorité) lors de la création de la Capacité de calcul Azure Machine Learning. Les machines virtuelles basse priorité utilisent les capacités excédentaires d’Azure : elles sont par conséquent moins chères, mais risquent de faire passer votre exécution après d’autres travaux.
* **idle_seconds_before_scaledown** : durée d’attente d’inactivité (120 secondes par défaut) après l’achèvement de l’exécution avant la mise à l’échelle vers min_nodes.
* **vnet_resourcegroup_name** : groupe de ressources du réseau virtuel __existant__. La Capacité de calcul Azure Machine Learning est créée dans ce réseau virtuel.
* **vnet_name** : nom du réseau virtuel. Le réseau virtuel doit être dans la même région que votre espace de travail Azure Machine Learning.
* **subnet_name** : nom du sous-réseau au sein du réseau virtuel. Les ressources de la Capacité de calcul Azure Machine Learning recevront des adresses IP de la plage de ce sous-réseau.

> [!TIP]
> Quand vous créez une ressource de Capacité de calcul Azure Machine Learning persistante vous avez également la possibilité de mettre à jour ses propriétés, comme min_nodes ou max_nodes. Appelez simplement la fonction `update()` pour celle-ci.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
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

Azure Machine Learning prend également en charge l’utilisation de votre propre ressource de calcul et son attachement à votre espace de travail. Un tel type de ressource est une machine virtuelle distante arbitraire tant qu’elle est accessible depuis le service Azure Machine Learning. Il peut s’agir d’une machine virtuelle Azure ou d’un serveur distant dans votre organisation, ou encore d’un serveur local. En particulier, avec l’adresse IP et les informations d’identification (nom d’utilisateur/mot de passe ou clé SSH), vous pouvez utiliser n’importe quelle machine virtuelle accessible pour les exécutions à distance.
Vous pouvez utiliser un environnement Conda intégré au système, un environnement Python déjà existant ou un conteneur Docker. L’exécution avec un conteneur Docker nécessite que le moteur Docker soit en cours d’exécution sur la machine virtuelle. Cette fonctionnalité est particulièrement pratique quand vous voulez obtenir un environnement cloud de développement/expérience plus flexible que votre ordinateur local.

> [!TIP]
> Nous recommandons l’utilisation de Data Science Virtual Machine comme choix de machine virtuelle Azure pour ce scénario. Il s’agit d’un environnement préconfiguré de développement de science des données et d’intelligence artificielle dans Azure, avec un choix organisé d’outils et de frameworks pour le cycle de vie complet du développement ML. Pour plus d’informations sur l’utilisation de Data Science Virtual Machine avec Azure Machine Learning, consultez le document [Configurer un environnement de développement](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

> [!WARNING]
> Azure Machine Learning prend uniquement en charge les machines virtuelles exécutant Ubuntu. Quand vous créez une machine virtuelle ou que vous sélectionnez une machine existante, vous devez en sélectionner une qui utilise Ubuntu.

Les étapes suivantes utilisent le SDK pour configurer une DSVM comme cible d’entraînement :

1. Pour attacher une machine virtuelle existante comme cible de calcul, vous devez fournir le nom de domaine complet, le nom de connexion et le mot de passe de la machine virtuelle.  Dans l’exemple, remplacez ```<fqdn>``` par le nom de domaine complet public de la machine virtuelle, ou l’adresse IP publique. Remplacez ```<username>``` et ```<password>``` par l’utilisateur SSH et le mot de passe de la machine virtuelle :

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create compute config.
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")
    # If using SSH instead of a password, use this:
    #                                                  ssh_port=22,
    #                                                   username='<username>',
    #                                                   password=None,
    #                                                   private_key_file="path-to-file",
    #                                                   private_key_passphrase="passphrase")

    # Attach the compute
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. Créer une configuration pour la cible de calcul de DSVM. Docker et Conda sont utilisés pour créer et configurer l’environnement d’entraînement sur DSVM :

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image
    # If you want to use GPU in DSVM, you must also use GPU base Docker image azureml.core.runconfig.DEFAULT_GPU_IMAGE
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Azure Databricks

Azure Databricks est un environnement basé sur Apache Spark dans le cloud Azure. Il peut être utilisé comme une cible de calcul lors de l’apprentissage des modèles avec un pipeline Azure Machine Learning.

> [!IMPORTANT]
> Une cible de calcul Azure Databricks est utilisable uniquement dans un pipeline Machine Learning.
>
> Vous devez créer un espace de travail Azure Databricks avant de l’utiliser pour former votre modèle. Pour créer ces ressources, consultez le document [Exécuter un travail Spark sur Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Pour attacher Azure Databricks comme cible de calcul, vous devez utiliser le Kit de développement logiciel Azure Machine Learning et fournir les informations suivantes :

* __Nom de la capacité de calcul__ : nom que vous voulez affecter à cette ressource de calcul.
* __Nom de l’espace de travail Databricks__ : nom de l’espace de travail Azure Databricks.
* __Jeton d’accès__ : jeton d’accès utilisé pour s’authentifier auprès d’Azure Databricks. Pour générer un jeton d’accès, consultez le document [Authentification](https://docs.azuredatabricks.net/api/latest/authentication.html).

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

    # Create attach config
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

Azure Data Lake Analytics est une plateforme analytique de Big Data dans le cloud Azure. Il peut être utilisé comme une cible de calcul lors de l’apprentissage des modèles avec un pipeline Azure Machine Learning.

> [!IMPORTANT]
> Une cible de calcul Azure Data Lake Analytics est utilisable uniquement dans un pipeline Machine Learning.
>
> Vous devez créer un compte Azure Data Lake Analytics avant de l’utiliser pour former votre modèle. Pour créer cette ressource, consultez le document [Prise en main d’Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Pour attacher Data Lake Analytics comme cible de calcul, vous devez utiliser le Kit de développement logiciel Azure Machine Learning et fournir les informations suivantes :

* __Nom de la capacité de calcul__ : nom que vous voulez affecter à cette ressource de calcul.
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
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Les pipelines Azure Machine Learning peuvent uniquement fonctionner avec les données stockées dans le magasin de données par défaut du compte Data Lake Analytics. Si les données dont vous avez besoin se trouvent dans un magasin non défini par défaut, vous pouvez utiliser un [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) pour copier les données avant l’apprentissage.

## <a id="hdinsight"></a>Azure HDInsight 

HDInsight est une plateforme populaire pour l’analytique de Big Data. Elle fournit Apache Spark, que vous pouvez utiliser pour entraîner votre modèle.

> [!IMPORTANT]
> Vous devez créer le cluster HDInsight avant de l’utiliser pour entraîner votre modèle. Pour créer un cluster Spark sur HDInsight, consultez le document [Créer un cluster Spark dans HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql).
>
> Lors de la création du cluster, vous devez spécifier un nom d’utilisateur SSH et un mot de passe. Notez ces valeurs, car vous en aurez besoin lors de l’utilisation de HDInsight comme cible de calcul.
>
> Une fois le cluster créé, son nom de domaine complet est `<clustername>.azurehdinsight.net`, où `<clustername>` est le nom que vous avez fourni pour le cluster. Vous avez besoin de cette adresse (ou de l’adresse IP du cluster) pour l’utiliser comme cible de calcul

Pour configurer HDInsight comme cible de calcul, vous devez fournir le nom de domaine complet, le nom de connexion et le mot de passe du cluster HDInsight. L’exemple suivant utilise le SDK pour attacher un cluster à votre espace de travail. Dans l’exemple, remplacez `<fqdn>` par le nom de domaine complet public du cluster, ou l’adresse IP publique. Remplacez `<username>` et `<password>` par l’utilisateur SSH et le mot de passe du cluster :

> [!NOTE]
> Pour trouver le nom de domaine complet de votre cluster, accédez au portail Azure et sélectionnez votre cluster HDInsight. Dans la section __Vue d’ensemble__, le nom de domaine complet fait partie de l’entrée __URL__. Supprimez simplement `https://` du début de la valeur.
>
> ![Capture d’écran de la vue d’ensemble du cluster HDInsight avec l’entrée d’URL mise en surbrillance](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attaches a HDInsight cluster as a compute target.
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

# Configure HDInsight run
# load the runconfig object from the "myhdi.runconfig" file generated by the attach operaton above.
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# ask system to prepare the conda environment automatically when used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-run"></a>Soumettre une exécution d’entraînement

Il existe deux façons de soumettre une exécution d’entraînement :

* Soumettre un objet `ScriptRunConfig`.
* Soumettre un objet `Pipeline`.

> [!IMPORTANT]
> Les cibles de calcul Azure Databricks et Azure Data Lake Analytics peuvent uniquement être utilisées dans un pipeline.
> La cible de calcul locale ne peut pas être utilisée dans un pipeline.

### <a name="submit-using-scriptrunconfig"></a>Soumettre en utilisant `ScriptRunConfig`

Le modèle de code permettant de soumettre une exécution d’entraînement à l’aide de `ScriptRunConfig` est le même quelle que soit la cible de calcul :

* Créez un objet `ScriptRunConfig` à l’aide de la configuration d’exécution de la cible de calcul.
* Soumettez l’exécution.
* Attendez la fin de l’exécution.

L’exemple suivant utilise la configuration de la cible de calcul locale gérée par le système créée plus haut dans ce document :

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="submit-using-a-pipeline"></a>Soumettre à l’aide d’un pipeline

Le modèle de code permettant de soumettre une exécution d’entraînement à l’aide d’un pipeline est le même, quelle que soit la cible de calcul :

* Ajoutez une étape au pipeline pour la ressource de calcul.
* Soumettez une exécution à l’aide du pipeline.
* Attendez la fin de l’exécution.

L’exemple suivant utilise la cible de calcul Azure Databricks créée plus haut dans ce document :

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
# list of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

Pour plus d’informations sur les pipelines Machine Learning, consultez le document [Pipelines et Azure Machine Learning](concept-ml-pipelines.md).

Pour des exemples Blocs-notes Jupyter montrant l’apprentissage à l’aide d’un pipeline, consultez [https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline).

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Afficher et configurer la cible de calcul à l’aide du portail Azure

Vous pouvez afficher les cibles de calcul qui sont associées à votre espace de travail à partir du portail Azure. Pour accéder à la liste, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre espace de travail.
2. Cliquez sur le lien __Calcul__ situé sous la section __Applications__.

    ![Onglet Calcul](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Créer une cible de calcul

Suivez les étapes ci-dessus pour afficher la liste des cibles de calcul, puis procédez comme suit pour créer une cible de calcul :

1. Cliquez sur le signe __+__ pour ajouter une cible de calcul.

    ![Ajouter une cible de calcul ](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Entrez un nom pour la cible de calcul.
1. Sélectionnez **Capacité de calcul Machine Learning** comme type de capacité de calcul à utiliser pour __Entraînement__

    > [!IMPORTANT]
    > Vous pouvez créer une capacité de calcul Azure Machine Learning seulement comme capacité de calcul managée pour l’entraînement

1. Remplissez le formulaire demandé, en particulier la famille de machines virtuelles et le nombre maximal de nœuds à utiliser pour configurer la capacité de calcul 
1. Sélectionnez __Créer__
1. Vous pouvez afficher l’état de l’opération de création en sélectionnant la cible de calcul dans la liste

    ![Afficher la liste des capacités de calcul](./media/how-to-set-up-training-targets/View_list.png)

1. Vous voyez alors les détails de la cible de calcul.

    ![Afficher les détails](./media/how-to-set-up-training-targets/compute-target-details.png)

1. Vous pouvez maintenant soumettre une exécution sur ces cibles, comme indiqué ci-dessus.


### <a name="reuse-existing-compute-in-your-workspace"></a>Réutiliser une cible de calcul existante dans votre espace de travail

Suivez les étapes ci-dessus pour afficher la liste des cibles de calcul, puis procédez comme suit pour réutiliser une cible de calcul :

1. Cliquez sur le signe **+** pour ajouter une cible de calcul.
2. Entrez un nom pour la cible de calcul.
3. Sélectionnez le type de capacité de calcul à attacher pour __Entraînement__.

    > [!IMPORTANT]
    > Il n’est pas possible d’attacher tous les types de calcul à l’aide du portail.
    > Actuellement, les types qui peuvent être attachés pour l’apprentissage sont les suivants :
    > 
    > * Machine virtuelle distante
    > * Databricks
    > * Data Lake Analytics
    > * HDInsight

1. Remplir le formulaire demandé

    > [!NOTE]
    > Microsoft recommande d’utiliser des clés SSH, car elles sont plus sûres que les mots de passe. Les mots de passe sont vulnérables aux attaques par force brute, alors que les clés SSH s’appuient sur des signatures de chiffrement. Pour plus d’informations sur la création de clés SSH pour une utilisation avec Machines virtuelles Azure, consultez les documents suivants :
    >
    > * [Créer et utiliser des clés SSH sur Linux ou macOS]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Créer et utiliser des clés SSH sur Windows]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Sélectionner Attacher
1. Vous pouvez afficher l’état de l’opération d’attachement en sélectionnant la cible de calcul dans la liste
1. Vous pouvez maintenant soumettre une exécution sur ces cibles, comme indiqué ci-dessus.

## <a name="examples"></a>Exemples
Reportez-vous aux notebooks dans les emplacements suivants :
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)

* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Référence du SDK Azure Machine Learning](https://aka.ms/aml-sdk)
* [Tutoriel : Entraîner un modèle](tutorial-train-models-with-aml.md)
* [Où déployer les modèles](how-to-deploy-and-where.md)
* [Générer des pipelines de machine learning avec le service Azure Machine Learning](concept-ml-pipelines.md)
