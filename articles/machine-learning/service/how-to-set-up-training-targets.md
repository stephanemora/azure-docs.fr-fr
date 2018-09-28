---
title: Configurer des cibles de calcul pour l’entraînement de modèle avec le service Azure Machine Learning | Microsoft Docs
description: Découvrez comment sélectionner et configurer les environnements d’entraînement (cibles de calcul) servant à entraîner vos modèles Machine Learning. Avec le service Azure Machine Learning, il est très facile de basculer d’un environnement d’entraînement à un autre. Démarrez l’entraînement localement et, si une montée en charge est nécessaire, basculez vers une cible de calcul basée sur le cloud.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 4af2e570b498e496e80b6aeee2b8aeae23c582cc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952407"
---
# <a name="select-and-use-a-compute-target-to-train-your-model"></a>Sélectionner et utiliser une cible de calcul pour entraîner votre modèle

Avec le service Azure Machine Learning, vous pouvez entraîner votre modèle dans plusieurs environnements. Ces environnements, appelés __cibles de calcul__, peuvent être locaux ou dans le cloud. Dans ce document, vous allez découvrir les cibles de calcul prises en charge et comment les utiliser.

Une cible de calcul est la ressource qui exécute votre script d’entraînement ou qui héberge votre modèle quand il est déployé en tant que service web. Vous pouvez les créer et les gérer à l’aide du SDK Azure Machine Learning ou de l’interface CLI. Si vous avez des cibles de calcul qui ont été créées par un autre processus (par exemple le portail Azure ou Azure CLI), vous pouvez les utiliser en les attachant à votre espace de travail du service Azure Machine Learning.

Vous pouvez commencer par des exécutions locales sur votre ordinateur, puis monter en puissance et mettre à l’échelle vers d’autres environnements tels que des images DSVM distantes avec GPU ou Azure Batch AI. 

## <a name="supported-compute-targets"></a>Cibles de calcul prises en charge

Azure Machine Learning prend en charge les cibles de calcul suivantes :

|Cible de calcul| Accélération GPU | Réglage automatisé des hyperparamètres | Sélection automatisée du modèle | Peut être utilisé dans les pipelines|
|----|:----:|:----:|:----:|:----:|
|[Ordinateur local](#local)| Peut-être | &nbsp; | ✓ | &nbsp; |
|[Data Science Virtual Machine (DSVM)](#dsvm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Batch AI](#batch)| ✓ | ✓ | ✓ | ✓ | ✓ |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

__[Azure Container Instances (ACI)](#aci)__  peut également être utilisé pour entraîner des modèles. Il s’agit d’une offre de cloud serverless peu coûteuse et facile à créer et à utiliser. ACI ne prend pas en charge l’accélération GPU, le réglage automatisé des hyperparamètres, ni la sélection automatisée du modèle. De plus, vous ne pouvez pas l’utiliser dans un pipeline.

Les principales différences entre les cibles de calcul sont les suivantes :
* __Accélération GPU__ : les GPU sont disponibles avec Data Science Virtual Machine et Azure Batch AI. Vous pouvez avoir accès à un GPU sur votre ordinateur local, en fonction du matériel, des pilotes et des frameworks installés.
* __Réglage automatisé des hyperparamètres__ : l’optimisation automatisée des hyperparamètres Azure Machine Learning vous aide à trouver les meilleures hyperparamètres pour votre modèle.
* __Sélection automatisée du modèle__ : Azure Machine Learning peut recommander de manière intelligente une sélection d’algorithmes et d’hyperparamètres lors de la génération d’un modèle. La sélection automatisée du modèle vous aide à obtenir un modèle de haute qualité plus rapidement que si vous deviez essayer manuellement différentes combinaisons. Pour plus d’informations, consultez le [Tutoriel : Entraîner automatiquement un modèle de classification avec le machine learning automatisé dans Azure Machine Learning](tutorial-auto-train-models.md).
* __Pipelines__ : Azure Machine Learning vous permet de combiner différentes tâches telles que l’entraînement et le déploiement dans un pipeline. Les pipelines peuvent être exécutés en parallèle ou séquentiellement, et ils procurent un mécanisme d’automation fiable. Pour plus d’informations, consultez le document [Générer des pipelines de machine learning avec le service Azure Machine Learning](concept-ml-pipelines.md).

Vous pouvez utiliser le SDK Azure Machine Learning, Azure CLI ou le portail Azure pour créer des cibles de calcul. Vous pouvez également utiliser des cibles de calcul existantes en les ajoutant (attachant) à votre espace de travail.

> [!IMPORTANT]
> Vous ne pouvez pas attacher une instance ACI existante à votre espace de travail. Au lieu de cela, vous devez créer une nouvelle instance.
>
> Vous ne pouvez pas créer un cluster Azure HDInsight dans un espace de travail. Au lieu de cela, vous devez attacher un cluster existant.

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

Le basculement d’une cible de calcul à une autre implique la création d’une [configuration d’exécution](concept-azure-machine-learning-architecture.md#run-configuration). La configuration d’exécution définit comment exécuter le script sur la cible de calcul.

## <a name="training-scripts"></a>Scripts d’entraînement

Quand vous démarrez une exécution d’entraînement, l’ensemble du répertoire qui contient vos scripts d’entraînement est envoyé. Un instantané est créé et envoyé à la cible de calcul. Pour plus d’informations, consultez [Instantanés](concept-azure-machine-learning-architecture.md#snapshot).

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
run_config_system_managed.prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```
## <a id="dsvm"></a>Data Science Virtual Machine

Il se peut que votre ordinateur local ne dispose pas des ressources de calcul ou GPU nécessaires pour entraîner le modèle. Dans ce cas, vous pouvez effectuer une montée en charge ou en puissance du processus d’entraînement en ajoutant des cibles de calcul telles que des images DSVM (Data Science Virtual Machines).

> [!WARNING]
> Azure Machine Learning prend uniquement en charge les machines virtuelles exécutant Ubuntu. Quand vous créez une machine virtuelle ou que vous sélectionnez une machine existante, vous devez en sélectionner une qui utilise Ubuntu.

Les étapes suivantes utilisent le SDK pour configurer une DSVM comme cible d’entraînement :

1. Créer ou attacher une machine virtuelle
    
    * Avant de créer une DSVM, vérifiez tout d’abord si vous avez une DSVM portant le même nom. Si ce n’est pas le cas, créez une machine virtuelle :
    
        ```python
        from azureml.core.compute import DsvmCompute
        from azureml.core.compute_target import ComputeTargetException

        compute_target_name = 'mydsvm'

        try:
            dsvm_compute = DsvmCompute(workspace = ws, name = compute_target_name)
            print('found existing:', dsvm_compute.name)
        except ComputeTargetException:
            print('creating new.')
            dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
            dsvm_compute = DsvmCompute.create(ws, name = compute_target_name, provisioning_configuration = dsvm_config)
            dsvm_compute.wait_for_completion(show_output = True)
        ```
    * Pour attacher une machine virtuelle existante comme cible de calcul, vous devez fournir le nom de domaine complet, le nom de connexion et le mot de passe de la machine virtuelle.  Dans l’exemple, remplacez ```<fqdn>``` par le nom de domaine complet public de la machine virtuelle, ou l’adresse IP publique. Remplacez ```<username>``` et ```<password>``` par l’utilisateur SSH et le mot de passe de la machine virtuelle :

        ```python
        from azureml.core.compute import RemoteCompute

        dsvm_compute = RemoteCompute.attach(ws,
                                        name="attach-dsvm",
                                        username='<username>',
                                        address="<fqdn>",
                                        ssh_port=22,
                                        password="<password>")

        dsvm_compute.wait_for_completion(show_output=True)
    
   It takes around 5 minutes to create the DSVM instance.

1. Create a configuration for the DSVM compute target. Docker and conda are used to create and configure the training environment on DSVM:

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image from DockerHub
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

1. Pour supprimer les ressources de calcul quand vous avez terminé, utilisez le code suivant :

    ```python
    dsvm_compute.delete()
    ```

## <a id="batch"></a>Azure Batch AI

Si l’entraînement du modèle prend beaucoup de temps, vous pouvez utiliser Azure Batch AI pour répartir la charge d’entraînement sur un cluster de ressources de calcul dans le cloud. Batch AI peut également être configuré pour activer une ressource GPU.

L’exemple suivant recherche un cluster Batch AI existant par nom. S’il n’en trouve aucun, il en crée un :

```python
from azureml.core.compute import BatchAiCompute
from azureml.core.compute import ComputeTarget

# choose a name for your cluster
batchai_cluster_name = ws.name + "cpu"

found = False
# see if this compute target already exists in the workspace
for ct in ws.compute_targets():
    print(ct.name, ct.type)
    if (ct.name == batchai_cluster_name and ct.type == 'BatchAI'):
        found = True
        print('found compute target. just use it.')
        compute_target = ct
        break
        
if not found:
    print('creating a new compute target...')
    provisioning_config = BatchAiCompute.provisioning_configuration(vm_size = "STANDARD_D2_V2", # for GPU, use "STANDARD_NC6"
                                                                #vm_priority = 'lowpriority', # optional
                                                                autoscale_enabled = True,
                                                                cluster_min_nodes = 1, 
                                                                cluster_max_nodes = 4)

    # create the cluster
    compute_target = ComputeTarget.create(ws,batchai_cluster_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current BatchAI cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

Pour attacher un cluster Batch AI existant comme cible de calcul, vous devez fournir l’ID de ressource Azure. Pour obtenir l’ID de ressource à partir du portail Azure, vous devez :
1. Recherchez le service `Batch AI` sous **Tous les Services**.
1. Cliquer sur le nom de l’espace de travail auquel appartient votre cluster.
1. Sélectionner le cluster.
1. Cliquer sur **Propriétés**.
1. Copier l’**ID**.

L’exemple suivant utilise le SDK pour attacher un cluster à votre espace de travail. Dans l’exemple, remplacez `<name>` par n’importe quel nom pour la cible de calcul. Il ne doit pas obligatoirement correspondre au nom du cluster. Remplacez `<resource-id>` par l’ID de ressource Azure détaillée plus haut :

```python
from azureml.core.compute import BatchAiCompute
BatchAiCompute.attach(workspace=ws,
                      name=<name>,
                      resource_id=<resource-id>)
```

Vous pouvez également vérifier l’état des travaux et du cluster Batch AI à l’aide des commandes Azure CLI suivantes :

- Vérifiez l’état du cluster. Vous pouvez voir combien de nœuds sont en cours d’exécution à l’aide de `az batchai cluster list`.
- Vérifiez l’état des travaux. Vous pouvez voir combien de travaux sont en cours d’exécution à l’aide de `az batchai job list`.

La création du cluster Batch AI prend environ cinq minutes.

## <a name='aci'></a>Azure Container Instance (ACI)

Les instances ACI sont des conteneurs isolés qui offrent des temps de démarrage plus courts et n’exigent pas la gestion de machines virtuelles par l’utilisateur. Le service Azure Machine Learning utilise des conteneurs Linux, qui sont disponibles dans les régions westus, eastus, westeurope, northeurope, westus2 et southeastasia. Pour plus d’informations, consultez [Disponibilité des régions](https://docs.microsoft.com/azure/container-instances/container-instances-quotas#region-availability). 

L’exemple suivant montre comment utiliser le SDK pour créer une cible de calcul ACI et l’utiliser pour entraîner un modèle : 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use ACI to run script.
run_config.target = "containerinstance"

# ACI container group is only supported in certain regions, which can be different than the region the Workspace is in.
run_config.container_instance.region = 'eastus'

# set the ACI CPU and Memory 
run_config.container_instance.cpu_cores = 1
run_config.container_instance.memory_gb = 2

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
#run_config.environment.docker.base_image = 'microsoft/mmlspark:plus-0.9.9'

# use conda_dependencies.yml to create a conda environment in the Docker image
run_config.environment.python.user_managed_dependencies = False

# auto-prepare the Docker image when used for the first time (if it is not already prepared)
run_config.auto_prepare_environment = True

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

La création d’une cible de calcul ACI peut prendre de quelques secondes à quelques minutes.

## <a id="hdinsight"></a>Attacher un cluster HDInsight 

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
from azureml.core.compute import HDInsightCompute

try:
    # Attaches a HDInsight cluster as a compute target.
    HDInsightCompute.attach(ws,name = "myhdi",
                            address = "<fqdn>",
                            username = "<username>",
                            password = "<password>")
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
    
Le code permettant de soumettre une exécution d’entraînement est le même quelle que soit la cible de calcul :

* Créez un objet `ScriptRunConfig` à l’aide de la configuration d’exécution de la cible de calcul.
* Soumettez l’exécution.
* Attendez la fin de l’exécution.

L’exemple suivant utilise la configuration de la cible de calcul locale gérée par le système créée plus haut dans ce document :

```pyghon
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Afficher et configurer la cible de calcul à l’aide du portail Azure

Vous pouvez afficher les cibles de calcul qui sont associées à votre espace de travail à partir du portail Azure. Pour accéder à la liste, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre espace de travail.
2. Cliquez sur le lien __Calcul__ situé sous la section __Applications__.

    ![Onglet Calcul](./media/how-to-set-up-training-targets/compute_tab.png)

### <a name="create-a-compute-target"></a>Créer une cible de calcul

Suivez les étapes ci-dessus pour afficher la liste des cibles de calcul, puis procédez comme suit pour créer une cible de calcul :

1. Cliquez sur le signe __+__ pour ajouter une cible de calcul.

    ![Ajouter une cible de calcul ](./media/how-to-set-up-training-targets/add_compute.png)

1. Entrez un nom pour la cible de calcul.
1. Sélectionnez le type de cible de calcul à attacher pour l’__Entraînement__. 
1. Sélectionnez __Créer nouveau__ et remplissez le formulaire. 
1. Sélectionnez __Créer__
1. Vous pouvez afficher l’état de l’opération de création en sélectionnant la cible de calcul dans la liste.

    ![Afficher la liste des cibles de calcul](./media/how-to-set-up-training-targets/View_list.png) Vous verrez alors les détails de cette cible de calcul.
    ![Afficher les détails](./media/how-to-set-up-training-targets/vm_view.PNG)
1. Vous pouvez maintenant soumettre une exécution par rapport à ces cibles comme indiqué ci-dessus.

### <a name="reuse-existing-compute-in-your-workspace"></a>Réutiliser une cible de calcul existante dans votre espace de travail

Suivez les étapes ci-dessus pour afficher la liste des cibles de calcul, puis procédez comme suit pour réutiliser une cible de calcul :

1. Cliquez sur le signe **+** pour ajouter une cible de calcul.
2. Entrez un nom pour la cible de calcul.
3. Sélectionnez le type de cible de calcul à attacher pour l’Entraînement. Batch AI et Machines virtuelles sont actuellement pris en charge dans le portail pour l’entraînement.
4. Sélectionnez « Utiliser l’existant ».
    - Lors de l’attachement de clusters Batch AI, sélectionnez la cible de calcul dans la liste déroulante, sélectionnez l’espace de travail Batch AI et le cluster Batch AI, puis cliquez sur **Créer**.
    - Lors de l’attachement d’une machine virtuelle, entrez l’adresse IP, la combinaison nom d’utilisateur/mot de passe, les clés publique/privée et le port, puis cliquez sur Créer.

    > [!NOTE]
    > Microsoft recommande d’utiliser des clés SSH, car elles sont plus sûres que les mots de passe. Les mots de passe sont vulnérables aux attaques par force brute, alors que les clés SSH s’appuient sur des signatures de chiffrement. Pour plus d’informations sur la création de clés SSH pour une utilisation avec Machines virtuelles Azure, consultez les documents suivants :
    >
    > * [Créer et utiliser des clés SSH sur Linux ou macOS]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Créer et utiliser des clés SSH sur Windows]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

5. Vous pouvez afficher l’état du provisionnement en sélectionnant la cible de calcul dans la liste.
6. Vous pouvez maintenant soumettre une exécution par rapport à ces cibles.

## <a name="examples"></a>Exemples
Les blocs-notes suivants illustrent les concepts de cet article :
* `01.getting-started/02.train-on-local/02.train-on-local.ipynb`
* `01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb`
* `01.getting-started/05.train-in-spark/05.train-in-spark.ipynb`
* `01.getting-started/07.hyperdrive-with-sklearn/07.hyperdrive-with-sklearn.ipynb`

Consultez ces blocs-notes : [!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Référence du SDK Azure Machine Learning](http://aka.ms/aml-sdk)
* [Tutoriel : Entraîner un modèle](tutorial-train-models-with-aml.md)
* [Où déployer les modèles](how-to-deploy-and-where.md)
* [Générer des pipelines de machine learning avec le service Azure Machine Learning](concept-ml-pipelines.md)
