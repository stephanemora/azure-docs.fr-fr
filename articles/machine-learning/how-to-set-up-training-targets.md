---
title: Utiliser des cibles de calcul pour la formation des modèles
titleSuffix: Azure Machine Learning
description: Configurer les environnements d’entraînement (cibles de calcul) pour l’entraînement des modèles de machine learning. Vous pouvez facilement basculer entre différents environnements d’entraînement. Commencer l’entraînement en local. Si un scale-out est nécessaire, basculez vers une cible de calcul basée sur le cloud.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/13/2020
ms.custom: seodec18
ms.openlocfilehash: 7fccd60ef0312748287d4103d8754bce944d4df6
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266455"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Configurer et utiliser des cibles de calcul pour effectuer l’apprentissage du modèle 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning vous permet de faire l’apprentissage de votre modèle sur une variété de ressources ou d’environnements, appelés collectivement [__cibles de calcul__](concept-azure-machine-learning-architecture.md#compute-targets). Une cible de calcul peut être un ordinateur local ou une ressource cloud, comme une capacité de calcul Azure Machine Learning, Azure HDInsight ou une machine virtuelle distante.  Vous pouvez également créer des cibles de calcul pour le déploiement de modèle, comme décrit dans [« Déployer des modèles avec le service Azure Machine Learning »](how-to-deploy-and-where.md).

Vous pouvez créer et gérer une cible de calcul avec le SDK Azure Machine Learning, Azure Machine Learning Studio, Azure CLI ou l’extension Azure Machine Learning VS Code. Si vous avez des cibles de calcul qui ont été créées via un autre service (par exemple un cluster HDInsight), vous pouvez les utiliser en les attachant à votre espace de travail Azure Machine Learning.
 
Cet article explique comment utiliser les différentes cibles de calcul pour l’entraînement des modèles.  Pour toutes les cibles de calcul, le flux de travail est identique :
1. __Créez__ une cible de calcul si vous n’en avez pas encore.
2. __Attachez__ la cible de calcul à votre espace de travail.
3. __Configurez__ la cible de calcul pour qu’elle contienne les dépendances d’environnement et de package Python requises par votre script.


>[!NOTE]
> Le code présenté dans cet article a été testé avec le Kit de développement logiciel (SDK) Azure Machine Learning version 1.0.74.

## <a name="compute-targets-for-training"></a>Cibles de calcul pour l’entraînement

La prise en charge d’Azure Machine Learning varie selon les cibles de calcul. Un cycle de vie typique du développement d’un modèle commence par le développement/l’expérience sur une petite quantité de données. À ce stade, nous recommandons d’utiliser un environnement local. Par exemple, votre ordinateur local ou une machine virtuelle basée cloud. Quand vous effectuez un scale-up de votre entraînement sur des jeux de données plus grands ou que vous faites un entraînement distribué, nous recommandons d’utiliser la Capacité de calcul Machine Learning Azure pour créer un cluster avec un ou plusieurs nœuds qui se met à l’échelle automatiquement chaque fois que vous lancez une exécution. Vous pouvez également attacher votre propre ressource de calcul, bien que la prise en charge des différents scénarios puisse varier comme indiqué ci-dessous :

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> La Capacité de calcul Azure Machine Learning peut être créée en tant que ressource persistante ou créée dynamiquement lorsque vous demandez une exécution. La création basée sur l'exécution supprime la cible de calcul au terme de la formation. Vous ne pouvez donc pas réutiliser les cibles de calcul créées de cette façon.

## <a name="whats-a-run-configuration"></a>Qu’est une configuration de série de tests ?

Lors de l’apprentissage, il est courant de commencer par exécuter le script d’apprentissage sur l’ordinateur local, avant de l’exécuter sur une autre cible de calcul. Avec Azure Machine Learning, vous pouvez exécuter votre script sur différentes cibles de calcul sans avoir à le modifier.

Il vous suffit de définir l’environnement pour chaque cible de calcul dans une **configuration de série de tests**.  Ensuite, lorsque vous souhaitez exécuter votre expérience de formation sur une autre cible de calcul, spécifiez la configuration de série de tests pour celle-ci. Pour plus d’informations sur la spécification d’un environnement et la liaison de celui-ci à une configuration d’exécution, consultez [Créer et gérer des environnements pour l’entraînement et le déploiement](how-to-use-environments.md).

Pour en savoir plus, voir [Envoi d’expériences](#submit) à la fin de cet article.

## <a name="whats-an-estimator"></a>Est-ce qu’un estimateur ?

Pour faciliter une formation de modèles à l’aide d’infrastructures populaires, le kit de développement logiciel (SDK) Python d’Azure Machine Learning fournit une alternative d’abstraction de plus haut niveau, la classe d’estimateur.  Cette classe vous permet de construire facilement des configurations de série de tests. Vous pouvez créer et utiliser un [estimateur](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) générique pour envoyer des scripts d’apprentissage qui utilisent toute infrastructure de formation que vous choisissez (comme scikit-Learn). Nous vous recommandons d’utiliser un estimateur pour l’apprentissage, car celui-ci crée automatiquement des objets incorporés, tels que des objets Environment ou RunConfiguration. Si vous souhaitez mieux contrôler la façon dont ces objets sont créés et spécifier les packages à installer pour l’exécution de votre expérience, suivez [ces étapes](#amlcompute) pour envoyer vos expériences d’apprentissage à l’aide d’un objet RunConfiguration sur une capacité de calcul Azure Machine Learning.

Azure Machine Learning fournit des estimateurs spécifiques pour [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) et [Ray RLlib](how-to-use-reinforcement-learning.md).

Pour plus d’informations, consultez [Former des modèles ML avec estimateurs](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Qu’est-ce qu’un pipeline ML ?

Avec les pipelines ML, vous pouvez optimiser votre flux de travail en profitant de ces avantages : simplicité, rapidité, portabilité et réutilisation. Le fait de créer des pipelines avec Azure Machine Learning vous permet de vous concentrer sur votre domaine d’expertise, le Machine Learning, plutôt que sur l’infrastructure et l’automatisation.

Les pipelines ML sont construits à partir de plusieurs **étapes**, qui sont des unités de calcul distinctes dans le pipeline. Chaque étape peut s’exécuter indépendamment et utiliser des ressources de calcul isolées. Cette approche permet à plusieurs scientifiques de données de travailler sur le même pipeline en même temps sans surcharge des ressources de calcul, et cela facilite également l’utilisation de différents types/tailles de calcul pour chaque étape.

> [!TIP]
> Les pipelines ML peuvent utiliser la configuration de série de tests ou des estimateurs lors de l’apprentissage des modèles.

Alors que des pipelines ML peuvent former des modèles, ils peuvent également préparer des données avant de former et déployer des modèles après l’apprentissage. L’un des principaux cas d’utilisation pour les pipelines est la notation par lots. Pour plus d’informations, consultez [Pipelines : optimiser les workflows Machine Learning](concept-ml-pipelines.md).

## <a name="set-up-in-python"></a>Configurer dans Python

Reportez-vous aux sections ci-dessous pour configurer ces cibles de calcul :

* [Ordinateur local](#local)
* [Capacité de calcul Azure Machine Learning](#amlcompute)
* [Machines virtuelles distantes](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>Ordinateur local

1. **Créer et attacher** : Il n’est pas nécessaire de créer ou d’attacher une cible de calcul pour utiliser votre ordinateur local en tant qu’environnement d’apprentissage.  

1. **Configurer** :  Lorsque vous utilisez votre ordinateur local comme cible de calcul, le code d’apprentissage est exécuté dans votre [environnement de développement](how-to-configure-environment.md).  Si celui-ci comprend déjà les packages Python nécessaires, utilisez l’environnement géré par l’utilisateur.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

À présent que vous avez attaché la cible de calcul et configuré votre série de tests, l’étape suivante consiste à [soumettre la série de tests d’apprentissage](#submit).

### <a name="azure-machine-learning-compute"></a><a id="amlcompute"></a>Capacité de calcul Azure Machine Learning

Capacité de calcul Azure Machine Learning est une infrastructure de capacité de calcul managée qui permet à l’utilisateur de créer facilement une capacité de calcul à un ou plusieurs nœuds. La capacité de calcul est créée dans la région de votre espace de travail sous forme de ressource qui peut être partagée avec d’autres utilisateurs dans votre espace de travail. La cible de calcul monte en puissance automatiquement quand un travail est soumis, et peut être placée dans un réseau virtuel Azure. La cible de calcul s’exécute dans un environnement conteneurisé et empaquète les dépendances de votre modèle dans un [conteneur Docker](https://www.docker.com/why-docker).

Vous pouvez utiliser une capacité de calcul Azure Machine Learning pour distribuer le processus d’entraînement sur un cluster de nœuds de capacité de calcul de CPU ou de GPU dans le cloud. Pour plus d’informations sur les tailles de machine virtuelle qui incluent des GPU, consultez [Tailles de machine virtuelle à GPU optimisé](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu). 

Capacité de calcul Azure Machine Learning comporte des limites par défaut, par exemple le nombre de cœurs qui peuvent être alloués. Pour plus d’informations, consultez [Gérer et demander des quotas pour les ressources Azure](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).

Vous pouvez également choisir d’utiliser des machines virtuelles de faible priorité pour exécuter une partie ou la totalité de vos charges de travail. Ces machines virtuelles n’ont pas de disponibilité garantie et peuvent être anticipées en cours d’utilisation. Une tâche anticipée est redémarrée, pas reprise.  Les machines virtuelles de faible priorité sont proposées à des tarifs réduits par rapport à des machines virtuelles normales. Consultez [Prévoir et gérer les coûts](https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost).

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
    
   Vous pouvez également créer et attacher une ressource de capacité de calcul Azure Machine Learning persistante dans [Azure Machine Learning Studio](#portal-create).

1. **Configurer** : Créez une configuration de série de tests pour la cible de calcul persistante.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

À présent que vous avez attaché la cible de calcul et configuré votre série de tests, l’étape suivante consiste à [soumettre la série de tests d’apprentissage](#submit).


### <a name="remote-virtual-machines"></a><a id="vm"></a>Machines virtuelles distantes

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

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Vous pouvez également attacher la machine DSVM (Data Science Virtual Machine) à votre espace de travail [à l’aide d’Azure Machine Learning Studio](#portal-reuse).

1. **Configurer** : Créez une configuration de série de tests pour la cible de calcul Data Science Virtual Machine (DSVM). Docker et Conda sont utilisés pour créer et configurer l’environnement d’entraînement sur la DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


À présent que vous avez attaché la cible de calcul et configuré votre série de tests, l’étape suivante consiste à [soumettre la série de tests d’apprentissage](#submit).

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

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

   Vous pouvez également attacher le cluster HDInsight à votre espace de travail [à l’aide d’Azure Machine Learning Studio](#portal-reuse).

1. **Configurer** : Créez une configuration de série de tests pour la cible de calcul HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


À présent que vous avez attaché la cible de calcul et configuré votre série de tests, l’étape suivante consiste à [soumettre la série de tests d’apprentissage](#submit).


### <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

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

## <a name="set-up-in-azure-machine-learning-studio"></a>Effectuer une configuration dans Azure Machine Learning Studio

Vous pouvez accéder aux cibles de calcul associées à votre espace de travail dans Azure Machine Learning Studio.  Vous pouvez utiliser Studio pour effectuer les opérations suivantes :

* [Afficher les cibles de calcul](#portal-view) attachées à votre espace de travail
* [Créer une cible de calcul](#portal-create) dans votre espace de travail
* [Joindre une cible de calcul](#portal-reuse) créée en dehors de l'espace de travail


Après avoir créé une cible et l’avoir attachée à votre espace de travail, vous allez l’utiliser dans votre configuration de série de tests avec un objet `ComputeTarget` : 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a name="view-compute-targets"></a><a id="portal-view"></a>Afficher les cibles de calcul


Pour consulter les cibles de calcul de votre espace de travail, procédez comme suit :

1. Accédez à [Azure Machine Learning Studio](https://ml.azure.com).
 
1. Sous __Applications__, sélectionnez __Capacité de calcul__.

    [![Onglet 	Voir le computing](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>Créer une cible de calcul

Suivez la procédure ci-dessus pour afficher la liste des cibles de calcul. Puis procédez comme suit pour créer une cible de calcul : 

1. Cliquez sur le signe plus (+) pour ajouter une cible de calcul.

    ![Ajouter une cible de calcul](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Entrez un nom pour la cible de calcul. 

1. Sélectionnez **Capacité de calcul Machine Learning** comme type de capacité de calcul à utiliser pour __Entraînement__. 

    >[!NOTE]
    >La capacité de calcul Azure Machine Learning est la seule ressource de capacité de calcul managée que vous pouvez créer dans Azure Machine Learning Studio.  Toutes les autres ressources de calcul peuvent être attachées après leur création.

1. Remplissez le formulaire. Indiquez une valeur pour les propriétés requises, notamment **Famille de machines virtuelles**, ainsi que le **nombre maximal de nœuds** à utiliser pour mettre en place la capacité de calcul.  

1. Sélectionnez __Create__ (Créer).


1. Pour afficher l’état de l’opération de création, sélectionnez la cible de calcul dans la liste :

    ![Sélectionnez une cible de calcul pour afficher l’état de l’opération de création](./media/how-to-set-up-training-targets/View_list.png)

1. Vous voyez alors les détails de la cible de calcul : 

    ![Consultez les détails de la cible de calcul](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>Joindre des cibles de calcul

Pour utiliser des cibles de calcul créées en dehors de l’espace de travail Azure Machine Learning, vous devez les joindre. Une fois la cible de calcul jointe, elle sera disponible dans votre espace de travail.

Suivez la procédure décrite plus haut pour afficher la liste des cibles de calcul. Suivez ensuite les étapes ci-dessous pour joindre une cible de calcul : 

1. Cliquez sur le signe plus (+) pour ajouter une cible de calcul. 
1. Entrez un nom pour la cible de calcul. 
1. Sélectionnez le type de capacité de calcul à attacher pour __Entraînement__ :

    > [!IMPORTANT]
    > Il n’est pas possible d’attacher tous les types de capacités de calcul à partir d’Azure Machine Learning Studio. Actuellement, les types qui peuvent être attachés pour l’entraînement sont les suivants :
    >
    > * Machine virtuelle distante
    > * Azure Databricks (pour une utilisation dans des pipelines de Machine Learning)
    > * Azure Data Lake Analytics (pour une utilisation dans des pipelines de Machine Learning)
    > * Azure HDInsight

1. Remplissez le formulaire et indiquez une valeur pour les propriétés requises.

    > [!NOTE]
    > Microsoft recommande d’utiliser des clés SSH, car elles sont plus sûres que les mots de passe. Les mots de passe sont vulnérables aux attaques en force brute. Les clés SSH utilisent des signatures de chiffrement. Pour plus d’informations sur la création de clés SSH pour une utilisation avec Machines virtuelles Azure, consultez les documents suivants :
    >
    > * [Créer et utiliser des clés SSH sur Linux ou macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Créer et utiliser des clés SSH sur Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Sélectionnez __Attacher__. 
1. Pour afficher l’état de l’opération d’attachement, sélectionnez la cible de calcul dans la liste.

## <a name="set-up-with-cli"></a>Configurer avec l’interface CLI

Vous pouvez accéder aux cibles de calcul associées à votre espace de travail à l’aide de l’[extension CLI](reference-azure-machine-learning-cli.md) pour Azure Machine Learning.  Vous pouvez utiliser l’interface CLI pour :

* Créer une cible de calcul gérée
* Mettre à jour une cible de calcul gérée
* Attacher une cible de calcul non gérée

Pour plus d’informations, voir [Gestion des ressources](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Configurer avec VS Code

Vous pouvez accéder, créer et gérer les cibles de calcul associées à votre espace de travail en utilisant l’[extension VS Code](tutorial-train-deploy-image-classification-model-vscode.md#configure-compute-targets) pour Azure Machine Learning.

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>Envoyer une série de tests d’apprentissage à l’aide du Kit de développement logiciel (SDK) Azure Machine Learning

Après avoir créé une configuration de série de tests, vous l’utilisez pour exécuter votre expérience.  Le modèle de code pour soumettre une série de tests d’apprentissage est le même pour tous les types de cibles de calcul :

1. Créer une expérience à exécuter
1. Soumettez l’exécution.
1. Attendez la fin de l’exécution.

> [!IMPORTANT]
> Quand vous soumettez la série de tests d’apprentissage, un instantané du répertoire contenant vos scripts d’apprentissage est créé et envoyé à la cible de calcul. Il est également stocké dans le cadre de l’expérience dans votre espace de travail. Si vous modifiez des fichiers et que vous soumettez à nouveau l’exécution, seuls les fichiers modifiés seront téléchargés.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Pour plus d’informations, consultez [Instantanés](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Créer une expérience

Tout d’abord, créez une expérience dans votre espace de travail.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Soumettre l’expérimentation

Soumettez l’expérience avec un objet `ScriptRunConfig`.  Cet objet inclut ce qui suit :

* **source_directory** : Répertoire source contenant votre script d’apprentissage
* **script** : Identifiez le script d’apprentissage
* **run_config** : Configuration de série de tests qui définit où aura lieu l’apprentissage.

Par exemple, pour utiliser la configuration de [cible de calcul](#local) :

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Basculez la même expérience pour qu’elle s’exécute sur une autre cible de calcul en utilisant une configuration de série de tests différente, telle la [cible amlcompute](#amlcompute) :

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> Cet exemple utilise par défaut un seul nœud de la cible de calcul pour l’entraînement. Pour utiliser plusieurs nœuds, définissez le `node_count` de la configuration d’exécution avec le nombre de nœuds souhaité. Par exemple, le code suivant définit quatre nœuds pour l’entraînement :
>
> ```python
> src.run_config.node_count = 4
> ```

Vous pouvez également :

* Soumettre l’expérience avec un objet `Estimator`, comme indiqué dans [Former des modèles ML avec des estimateurs](how-to-train-ml-models.md).
* Soumettre une série de tests HyperDrive pour un [réglage d’hyperparamètre](how-to-tune-hyperparameters.md).
* Soumettre une expérience via l’[extension VS Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

Pour plus d’informations, consultez la documentation sur [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) et [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py).

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Créer une configuration de série de tests et soumettre une série de tests à l’aide de l’interface de ligne de commande d’Azure Machine Learning

Vous pouvez utiliser [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) et l’[extension d’interface de ligne de commande Machine Learning](reference-azure-machine-learning-cli.md) pour créer des configurations de série de tests et soumettre des séries de tests sur différentes cibles de calcul. Les exemples suivants partent du principe que vous disposez déjà d’un espace de travail Azure Machine Learning et que vous vous êtes connecté à Azure à l’aide de la commande `az login`. 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

### <a name="create-run-configuration"></a>Créer une configuration d’exécution

La façon la plus simple de créer une configuration de série de tests consiste à parcourir le dossier qui contient vos scripts Python de Machine Learning et à utiliser la commande de l’interface de ligne de commande.

```azurecli
az ml folder attach
```

Cette commande crée un sous-dossier `.azureml` qui contient des fichiers de configuration de série de tests modèles pour différentes cibles de calcul. Vous pouvez copier et modifier ces fichiers pour personnaliser votre configuration, par exemple, pour ajouter des packages Python ou modifier des paramètres Docker.  

### <a name="structure-of-run-configuration-file"></a>Structure d’un fichier de configuration de série de tests

Le fichier de configuration de série de tests au format YAML comprend les sections suivantes
 * Script à exécuter et ses arguments
 * Nom de la cible de calcul, soit « local », soit le nom d’un calcul sous l’espace de travail.
 * Paramètres pour l’exécution de la série de tests : infrastructure, communicateur pour les séries de tests distribuées, durée maximale et nombre de nœuds de calcul.
 * Section Environnement. Pour plus d’informations sur les champs de cette section, voir [Créer et gérer des environnements pour l’entraînement et le déploiement](how-to-use-environments.md).
   * Pour spécifier les packages Python à installer pour la série de tests, créez un [fichier d’environnement conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually) et définissez le champ __condaDependenciesFile__.
 * Détails de l’historique d’exécution pour spécifier le dossier du fichier journal, pour activer ou désactiver la collecte de sortie et exécuter des instantanés d’historique.
 * Détails de configuration spécifiques de l’infrastructure sélectionnée.
 * Référence de données et détails du magasin de données.
 * Détails de configuration spécifiques de Capacité de calcul Machine Learning pour la création d’un cluster.

Consultez l’exemple de [fichier JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) pour obtenir un schéma runconfig complet.

### <a name="create-an-experiment"></a>Créer une expérience

Commencez par créer une expérience pour vos séries de tests

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Série de tests de script

Pour soumettre une série de tests de script, exécutez une commande.

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>Série de tests HyperDrive

Vous pouvez utiliser HyperDrive avec Azure CLI pour effectuer des séries de tests pour le réglage des paramètres. Commencez par créer un fichier de configuration HyperDrive au format suivant. Pour plus d’informations sur les paramètres de réglage des hyperparamètres, voir l’article [Optimiser les hyperparamètres pour votre modèle](how-to-tune-hyperparameters.md).

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Ajoutez ce fichier aux fichiers de configuration de série de tests. Ensuite, soumettez une série de tests HyperDrive en utilisant :
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Notez la section *arguments* dans le fichier runconfig et la section *parameter space* dans le fichier config HyperDrive. Elles contiennent les arguments de ligne de commande à passer au script d’apprentissage. La valeur dans le fichier runconfig reste la même pour chaque itération, tandis que la plage dans le fichier config HyperDrive fait l’objet d’une itération. Ne spécifiez pas le même argument dans les deux fichiers.

Pour plus d’informations sur ces commandes CLI ```az ml```, consultez la [documentation de référence](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Intégration et suivi Git

Lorsque vous lancez une exécution d’entraînement où le répertoire source est un répertoire Git local, les informations relatives au répertoire sont stockées dans l’historique des exécutions. Pour plus d’informations, consultez [Obtenir une intégration pour Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Exemples de notebooks

Pour des exemples d’apprentissage avec différentes cibles de calcul, voir les blocs-notes suivants :
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Former un modèle](tutorial-train-models-with-aml.md) utilise une cible de calcul gérée pour former un modèle.
* Découvrez comment [optimiser efficacement les hyperparamètres](how-to-tune-hyperparameters.md) afin de générer des modèles plus efficaces.
* Une fois le modèle formé, découvrez [comment et où déployer les modèles](how-to-deploy-and-where.md).
* Consultez la documentation de référence du Kit de développement logiciel (SDK) de la [classe RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py).
* [Utiliser Azure Machine Learning avec des réseaux virtuels Azure](how-to-enable-virtual-network.md)
