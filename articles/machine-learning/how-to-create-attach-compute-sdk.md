---
title: Créer des ressources de calcul avec le kit de développement logiciel (SDK) Python
titleSuffix: Azure Machine Learning
description: Utilisez le kit de développement logiciel (SDK) Python Azure Machine Learning pour créer des ressources de calcul d’entraînement et de déploiement (cibles de calcul) pour le Machine Learning.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 96aa6839fe51bb8a8c26f411c1a1f9df6b8c5a7f
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147230"
---
# <a name="create-compute-targets-for-model-training-and-deployment-with-python-sdk"></a>Créer des cibles de calcul pour l’entraînement et le déploiement de modèle avec le kit SDK Python

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez utiliser le kit SDK Python Azure Machine Learning pour créer et gérer des cibles de calcul. Vous pouvez également créer et gérer des cibles de calcul avec :
* [Azure Machine Learning studio](how-to-create-attach-compute-studio.md). 
* L’[Eetension CLI](reference-azure-machine-learning-cli.md#resource-management) pour Azure Machine Learning.
* L’[extension VS Code](how-to-manage-resources-vscode.md#compute-clusters) pour Azure Machine Learning.


## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui
* Le [kit de développement logiciel (SDK) Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>Qu’est-ce qu’une cible de calcul ?

Azure Machine Learning vous permet de faire l’apprentissage de votre modèle sur une variété de ressources ou d’environnements, appelés collectivement [__cibles de calcul__](concept-azure-machine-learning-architecture.md#compute-targets). Une cible de calcul peut être un ordinateur local ou une ressource cloud, comme une capacité de calcul Azure Machine Learning, Azure HDInsight ou une machine virtuelle distante.  Vous pouvez également créer des cibles de calcul pour le déploiement de modèle, comme décrit dans [« Déployer des modèles avec le service Azure Machine Learning »](how-to-deploy-and-where.md).

## <a name="compute-targets-for-training"></a>Cibles de calcul pour l’entraînement

La prise en charge d’Azure Machine Learning varie selon les cibles de calcul. Un cycle de vie typique du développement d’un modèle commence par le développement/l’expérience sur une petite quantité de données. À ce stade, nous recommandons d’utiliser un environnement local. Par exemple, votre ordinateur local ou une machine virtuelle basée cloud. Quand vous effectuez un scale-up de votre entraînement sur des jeux de données plus grands ou que vous faites un entraînement distribué, nous recommandons d’utiliser la Capacité de calcul Machine Learning Azure pour créer un cluster avec un ou plusieurs nœuds qui se met à l’échelle automatiquement chaque fois que vous lancez une exécution. Vous pouvez également attacher votre propre ressource de calcul, bien que la prise en charge des différents scénarios puisse varier comme indiqué ci-dessous :

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

> [!NOTE]
> Les clusters Capacité de calcul Azure Machine Learning peuvent être créés en tant que ressource persistante ou créés dynamiquement lorsque vous demandez une exécution. La création basée sur l'exécution supprime la cible de calcul au terme de la formation. Vous ne pouvez donc pas réutiliser les cibles de calcul créées de cette façon.

Reportez-vous aux sections ci-dessous pour configurer ces cibles de calcul :

* [Ordinateur local](#local)
* [Cluster de calcul Azure Machine Learning](#amlcompute)
* [Instance de calcul Azure Machine Learning](#instance)
* [Machines virtuelles distantes](#vm)
* [Azure HDInsight](#hdinsight)


## <a name="local-computer"></a><a id="local"></a>Ordinateur local

Lorsque vous utilisez votre ordinateur local pour l’entraînement, il n’est pas nécessaire de créer une cible de calcul.  Il vous suffit de [soumettre l’exécution d’entraînement](how-to-set-up-training-targets.md) à partir de votre ordinateur local.

## <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Cluster de calcul Azure Machine Learning

Le cluster de calcul Azure Machine Learning est une infrastructure de capacité de calcul managée qui vous permet de créer facilement une capacité de calcul à un ou plusieurs nœuds. La capacité de calcul est créée dans la région de votre espace de travail sous forme de ressource qui peut être partagée avec d’autres utilisateurs dans votre espace de travail. La cible de calcul monte en puissance automatiquement quand un travail est soumis, et peut être placée dans un réseau virtuel Azure. La cible de calcul s’exécute dans un environnement conteneurisé et empaquète les dépendances de votre modèle dans un [conteneur Docker](https://www.docker.com/why-docker).

Vous pouvez utiliser une capacité de calcul Azure Machine Learning pour distribuer le processus d’entraînement sur un cluster de nœuds de capacité de calcul de CPU ou de GPU dans le cloud. Pour plus d’informations sur les tailles de machine virtuelle qui incluent des GPU, consultez [Tailles de machine virtuelle à GPU optimisé](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu). 

Capacité de calcul Azure Machine Learning comporte des limites par défaut, par exemple le nombre de cœurs qui peuvent être alloués. Pour plus d’informations, consultez [Gérer et demander des quotas pour les ressources Azure](how-to-manage-quotas.md).

> [!TIP]
> Les clusters peuvent généralement effectuer un scale-up jusqu’à 100 nœuds tant que vous disposez d’un quota suffisant pour le nombre de cœurs requis. Par défaut, les clusters sont configurés de manière à permettre la communication entre les nœuds du cluster, pour prendre en charge les travaux MPI par exemple. Toutefois, vous pouvez mettre à l’échelle vos clusters sur des milliers de nœuds simplement en [soumettant un ticket de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) et en demandant à ajouter votre abonnement, un espace de travail ou un cluster spécifique à une liste verte pour désactiver la communication entre nœuds. 

Une Capacité de calcul Azure Machine Learning peut être réutilisée pour plusieurs exécutions. Le calcul peut être partagé avec d’autres utilisateurs dans l’espace de travail et est conservé entre les exécutions, ce qui permet d’augmenter ou de diminuer automatiquement le nombre de nœuds en fonction du nombre d’exécutions soumises et du paramètre max_nodes défini sur votre cluster. Le paramètre min_nodes contrôle le nombre minimal de nœuds disponibles.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **Créer et attacher** : Pour créer une ressource de capacité de calcul Azure Machine Learning persistante dans Python, spécifiez les propriétés **vm_size** et **max_nodes**. Azure Machine Learning utilise ensuite des valeurs calculées par défaut pour les autres propriétés. La capacité de calcul diminue en puissance, et passe automatiquement à zéro nœud quand elle n’est pas utilisée.   Des machines virtuelles dédiées sont créées pour exécuter vos travaux en fonction des besoins.
    
    * **vm_size** : famille de machines virtuelles des nœuds créés par Capacité de calcul Azure Machine Learning.
    * **max_nodes** : nombre maximal de nœuds pour la mise à l’échelle automatique lors de l’exécution d’un travail sur une capacité de calcul Azure Machine Learning.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Vous pouvez aussi configurer plusieurs propriétés avancées lors de la création d’une capacité de calcul Azure Machine Learning. Ces propriétés vous permettent de créer un cluster persistant de taille fixe, ou au sein d’un réseau virtuel Azure existant dans votre abonnement.  Pour plus de détails, voir la [classe AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ).

    Vous pouvez également créer et attacher une ressource de capacité de calcul Azure Machine Learning persistante dans [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#portal-create).

Une fois que vous avez attaché le calcul, l’étape suivante consiste à [soumettre l’exécution d’entraînement](how-to-set-up-training-targets.md).

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Réduire le coût de votre cluster de calcul

Vous pouvez également choisir d’utiliser [des machines virtuelles de faible priorité](concept-plan-manage-cost.md#low-pri-vm) pour exécuter une partie ou la totalité de vos charges de travail. Ces machines virtuelles n’ont pas de disponibilité garantie et peuvent être anticipées en cours d’utilisation. Une tâche anticipée est redémarrée, pas reprise. 

Utilisez l’une des méthodes suivantes pour spécifier une machine virtuelle de faible priorité :
    
* Dans Studio, choisissez **Basse priorité** lorsque vous créez une machine virtuelle.
    
* Avec le kit SDK Python, définissez l’attribut `vm_priority` dans votre configuration de provisionnement.  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* Dans l’interface de ligne de commande, définissez `vm-priority` :
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a>Configurer une identité managée

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Configurez l’identité managée dans votre configuration de provisionnement :  
    
* Identité managée affectée par le système :
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )
    ```

* Identité managée affectée par l’utilisateur : 

    ```python
    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

Ajoutez une identité managée à un cluster de calcul existant :  
    
* Identité managée affectée par le système :

    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")
    ````

* Identité managée affectée par l’utilisateur :

    ```python
    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

#### <a name="managed-identity-usage"></a>Utilisation de l’identité managée

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]


## <a name="azure-machine-learning-compute-instance"></a><a id="instance"></a>Instance de calcul Azure Machine Learning

L’[instance de calcul Azure Machine Learning](concept-compute-instance.md) est une infrastructure de capacité de calcul managée qui vous permet de créer facilement une machine virtuelle unique. La capacité de calcul est créée dans la région de votre espace de travail, mais contrairement à un cluster de calcul, une instance ne peut pas être partagée avec d’autres utilisateurs dans votre espace de travail. En outre, l’instance n’effectue pas automatiquement de scale-down.  Vous devez arrêter la ressource pour empêcher les frais en cours.

Une instance de calcul peut exécuter plusieurs travaux en parallèle et dispose d’une file d’attente de travaux. 

Les instances de calcul peuvent exécuter des travaux en toute sécurité dans un [environnement de réseau virtuel](how-to-enable-virtual-network.md#compute-instance), sans qu’il soit nécessaire pour les entreprises d’ouvrir des ports SSH. Le travail s’exécute dans un environnement conteneurisé et empaquette les dépendances de votre modèle dans un conteneur Docker. 

1. **Créer et attacher** : 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

À présent que vous avez attaché la cible de calcul et configuré votre série de tests, l’étape suivante consiste à [soumettre la série de tests d’apprentissage](how-to-set-up-training-targets.md).


## <a name="remote-virtual-machines"></a><a id="vm"></a>Machines virtuelles distantes

Azure Machine Learning prend également en charge l’utilisation de votre propre ressource de calcul et son attachement à votre espace de travail. Un tel type de ressource est une machine virtuelle distante arbitraire tant qu’elle est accessible depuis Azure Machine Learning. Il peut s’agir d’une machine virtuelle Azure, d’un serveur distant dans votre organisation, ou encore d’un serveur local. En particulier, avec l’adresse IP et les informations d’identification (nom d’utilisateur/mot de passe ou clé SSH), vous pouvez utiliser n’importe quelle machine virtuelle accessible pour les exécutions à distance.

Vous pouvez utiliser un environnement Conda intégré au système, un environnement Python déjà existant ou un conteneur Docker. Pour exécuter sur un conteneur Docker, vous devez disposer d’un moteur Docker en cours d’exécution sur la machine virtuelle. Cette fonctionnalité est particulièrement pratique quand vous voulez obtenir un environnement cloud de développement/expérience plus flexible que votre ordinateur local.

Pour ce scénario, utilisez Azure Data Science Virtual Machine (DSVM) en tant que machine virtuelle Azure. Cette machine virtuelle est un environnement de science des données et de développement d’intelligence artificielle préconfiguré dans Azure. La machine virtuelle offre un choix organisé d’outils et d’infrastructures pour le développement de l’apprentissage automatique en cycle de vie complet. Pour plus d’informations sur l’utilisation de la DSVM avec Azure Machine Learning, consultez [Configurer un environnement de développement](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Créer** : Créez une Data Science Virtual Machine (DSVM) à utiliser pour former votre modèle. Pour savoir comment créer cette ressource, voir [Approvisionner une machine virtuelle pour la science des données pour Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning prend uniquement en charge les machines virtuelles exécutant **Ubuntu**. Lorsque vous créez une machine virtuelle ou en choisissez une existante, celle-ci doit utiliser Ubuntu.
    > 
    > Azure Machine Learning nécessite également que la machine virtuelle dispose d’une __adresse IP publique__.

1. **Attacher** : Pour attacher une machine virtuelle existante en tant que cible de calcul, vous devez fournir l'ID de ressource, le nom d'utilisateur et le mot de passe de la machine virtuelle. L'ID de ressource de la machine virtuelle peut être construit à l'aide de l'ID d’abonnement, du nom du groupe de ressources et du nom de la machine virtuelle en utilisant le format de chaîne suivant : `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Vous pouvez également attacher la machine DSVM (Data Science Virtual Machine) à votre espace de travail [à l’aide d’Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

1. **Configurer** : Créez une configuration de série de tests pour la cible de calcul Data Science Virtual Machine (DSVM). Docker et Conda sont utilisés pour créer et configurer l’environnement d’entraînement sur la DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


À présent que vous avez attaché la cible de calcul et configuré votre série de tests, l’étape suivante consiste à [soumettre la série de tests d’apprentissage](how-to-set-up-training-targets.md).

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight est une plateforme populaire pour l’analytique de Big Data. Elle fournit Apache Spark, que vous pouvez utiliser pour entraîner votre modèle.

1. **Créer** :  Créez le cluster HDInsight à utiliser pour former votre modèle. Pour créer un cluster Spark sur HDInsight, consultez [Créer un cluster Spark dans HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    > [!WARNING]
    > Azure Machine Learning nécessite également que le cluster HDInsight dispose d’une __adresse IP publique__.

    Lors de la création du cluster, vous devez spécifier un nom d’utilisateur SSH et un mot de passe. Notez ces valeurs, car vous en aurez besoin lors de l’utilisation de HDInsight comme cible de calcul.
    
    Une fois le cluster créé, connectez-le au nom d’hôte \<clustername>-ssh.azurehdinsight.net, où \<clustername>est le nom que vous avez donné au cluster. 

1. **Attacher** : Pour attacher un cluster HDInsight en tant que cible de calcul, vous devez fournir l'ID de ressource, le nom d'utilisateur et le mot de passe du cluster HDInsight. L'ID de ressource du cluster HDInsight peut être construit à l'aide de l'ID d’abonnement, du nom du groupe de ressources et du nom du cluster HDInsight en utilisant le format de chaîne suivant : `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Vous pouvez également attacher le cluster HDInsight à votre espace de travail [à l’aide d’Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

1. **Configurer** : Créez une configuration de série de tests pour la cible de calcul HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


À présent que vous avez attaché la cible de calcul et configuré votre série de tests, l’étape suivante consiste à [soumettre la série de tests d’apprentissage](how-to-set-up-training-targets.md).

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch sert à exécuter efficacement des applications de calcul haute performance (HPC) en parallèle et à grande échelle dans le cloud. AzureBatchStep peut être utilisé dans un pipeline Azure Machine Learning pour envoyer des travaux à un pool de machines Azure Batch.

Pour attacher Azure Batch comme cible de calcul, vous devez utiliser le Kit de développement logiciel Azure Machine Learning et fournir les informations suivantes :

-    **Nom de calcul Azure Batch** : Nom convivial à utiliser pour le calcul au sein de l’espace de travail
-    **Nom du compte Azure Batch** : Nom du compte Azure Batch
-    **Groupe de ressources** : Groupe de ressources qui contient le compte Azure Batch.

Le code suivant montre comment attacher Azure Batch comme cible de calcul :

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks est un environnement basé sur Apache Spark dans le cloud Azure. Il peut être utilisé comme cible de calcul avec un pipeline Azure Machine Learning.

Créez un espace de travail Azure Databricks avant de l’utiliser. Pour créer une ressource d’espace de travail, consultez le document [Exécuter un travail Spark sur Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Pour joindre Azure Databricks comme cible de calcul, fournissez les informations suivantes :

* __Nom de la capacité de calcul Databricks__ : nom que vous voulez affecter à cette ressource de calcul.
* __Nom de l’espace de travail Databricks__ : nom de l’espace de travail Azure Databricks.
* __Jeton d’accès Databricks__ : jeton d’accès utilisé pour s’authentifier auprès d’Azure Databricks. Pour générer un jeton d’accès, consultez le document [Authentification](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html).

Le code suivant montre comment joindre Azure Databricks en tant que cible de calcul avec le kit de développement logiciel (SDK) Azure Machine Learning (__l’espace de travail Databricks doit être présent dans le même abonnement que votre espace de travail AML__) :

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Si vous souhaitez obtenir un exemple plus détaillé, veuillez consulter un [exemple de notebook](https://aka.ms/pl-databricks) sur GitHub.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics est une plateforme analytique de Big Data dans le cloud Azure. Il peut être utilisé comme cible de calcul avec un pipeline Azure Machine Learning.

Créez un compte Azure Data Lake Analytics avant de l’utiliser. Pour créer cette ressource, consultez le document [Prise en main d’Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Pour attacher Data Lake Analytics comme cible de calcul, vous devez utiliser le Kit de développement logiciel Azure Machine Learning et fournir les informations suivantes :

* __Nom de la capacité de calcul__ : nom que vous voulez affecter à cette ressource de calcul.
* __Groupe de ressources__ : groupe de ressources contenant le compte Data Lake Analytics.
* __Nom du compte__ : Le nom du compte Data Lake Analytics.

Le code suivant montre comment attacher Data Lake Analytics comme cible de calcul :

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Si vous souhaitez obtenir un exemple plus détaillé, veuillez consulter un [exemple de notebook](https://aka.ms/pl-adla) sur GitHub.

> [!TIP]
> Les pipelines Azure Machine Learning peuvent uniquement fonctionner avec les données stockées dans le magasin de données par défaut du compte Data Lake Analytics. Si les données dont vous avez besoin se trouvent dans un magasin non défini par défaut, vous pouvez utiliser un [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) pour copier les données avant l’apprentissage.

## <a name="notebook-examples"></a>Exemples de notebooks

Pour des exemples d’apprentissage avec différentes cibles de calcul, voir les blocs-notes suivants :
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

* Utilisez la ressource de calcul pour [soumettre une exécution d’entraînement](how-to-set-up-training-targets.md).
* [Tutoriel : Former un modèle](tutorial-train-models-with-aml.md) utilise une cible de calcul gérée pour former un modèle.
* Découvrez comment [optimiser efficacement les hyperparamètres](how-to-tune-hyperparameters.md) afin de générer des modèles plus efficaces.
* Une fois le modèle formé, découvrez [comment et où déployer les modèles](how-to-deploy-and-where.md).
* [Utiliser Azure Machine Learning avec des réseaux virtuels Azure](how-to-enable-virtual-network.md)
