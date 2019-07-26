---
title: Créer et utiliser des cibles de calcul pour l’apprentissage du modèle
titleSuffix: Azure Machine Learning service
description: Configurer les environnements d’entraînement (cibles de calcul) pour l’entraînement des modèles de machine learning. Vous pouvez facilement basculer entre différents environnements d’entraînement. Commencer l’entraînement en local. Si une montée en charge est nécessaire, basculez vers une cible de calcul basée sur le cloud.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 06/12/2019
ms.custom: seodec18
ms.openlocfilehash: 267872f2036a0e697f4b2da65064805a0cfbd2b7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358741"
---
# <a name="set-up-compute-targets-for-model-training"></a>Configurer des cibles de calcul pour l’entraînement des modèles 

Azure Machine Learning service vous permet de former votre modèle sur une variété de ressources ou d’environnements, appelés collectivement [__cibles de calcul__](concept-azure-machine-learning-architecture.md#compute-targets). Une cible de calcul peut être un ordinateur local ou une ressource cloud telle qu’une capacité de calcul Azure Machine Learning, Azure HDInsight ou une machine virtuelle distante.  Vous pouvez également créer des cibles de calcul pour le déploiement de modèle, comme décrit dans [« Déployer des modèles avec le service Azure Machine Learning »](how-to-deploy-and-where.md).

Vous pouvez créer et gérer une cible de calcul avec le SDK Azure Machine Learning, Azure CLI, le portail Azure ou l’extension Azure Machine Learning VS Code. Si vous avez des cibles de calcul qui ont été créées via un autre service (par exemple un cluster HDInsight), vous pouvez les utiliser en les attachant à votre espace de travail du service Azure Machine Learning.
 
Cet article explique comment utiliser les différentes cibles de calcul pour l’entraînement des modèles.  Pour toutes les cibles de calcul, le flux de travail est identique :
1. __Créez__ une cible de calcul si vous n’en avez pas encore.
2. __Attachez__ la cible de calcul à votre espace de travail.
3. __Configurez__ la cible de calcul pour qu’elle contienne les dépendances d’environnement et de package Python requises par votre script.


>[!NOTE]
> Le code présenté dans cet article a été testé avec le SDK Azure Machine Learning version 1.0.39.

## <a name="compute-targets-for-training"></a>Cibles de calcul pour l’entraînement

La prise en charge par Azure Machine Learning service varie selon les cibles de calcul. Un cycle de vie typique du développement d’un modèle commence par le développement/l’expérience sur une petite quantité de données. À ce stade, nous recommandons d’utiliser un environnement local. Par exemple, votre ordinateur local ou une machine virtuelle basée cloud. Quand vous effectuez un scale-up de votre entraînement sur des jeux de données plus grands ou que vous faites un entraînement distribué, nous recommandons d’utiliser Capacité de calcul Azure Machine Learning pour créer un cluster avec un ou plusieurs nœuds qui se met à l’échelle automatiquement chaque fois que vous lancez une exécution. Vous pouvez également attacher votre propre ressource de calcul, bien que la prise en charge des différents scénarios puisse varier comme indiqué ci-dessous :

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


> [!NOTE]
> La Capacité de calcul Azure Machine Learning peut être créée en tant que ressource persistante ou créée dynamiquement lorsque vous demandez une exécution. La création basée sur l'exécution supprime la cible de calcul au terme de la formation. Vous ne pouvez donc pas réutiliser les cibles de calcul créées de cette façon.

## <a name="whats-a-run-configuration"></a>Qu’est une configuration de série de tests ?

Lors de l’apprentissage, il est courant de commencer par exécuter le script d’apprentissage sur l’ordinateur local, avant de l’exécuter sur une autre cible de calcul. Avec Azure Machine Learning service, vous pouvez exécuter votre script sur différentes cibles de calcul sans avoir à le modifier. 

Il vous suffit de définir l’environnement pour chaque cible de calcul avec une **configuration de série de tests**.  Ensuite, lorsque vous souhaitez exécuter votre expérience de formation sur une autre cible de calcul, spécifiez la configuration de série de tests pour celle-ci. 

Pour en savoir plus, voir [Envoi d’expériences](#submit) à la fin de cet article.

### <a name="manage-environment-and-dependencies"></a>Gérer l’environnement et les dépendances

Lorsque vous créez une configuration de série de tests, vous devez choisir la manière de gérer l’environnement et les dépendances sur la cible de calcul. 

#### <a name="system-managed-environment"></a>Environnement géré par le système

Utilisez un environnement géré par le système lorsque vous souhaitez que [Conda](https://conda.io/docs/) gère l’environnement Python et les dépendances de script pour vous. Un environnement géré par le système est supposé par défaut et constitue le choix le plus courant. Il est utile sur des cibles de calcul distantes, en particulier lorsque vous ne pouvez pas les configurer. 

Il vous suffit de spécifier chaque dépendance de package à l’aide de la [classe CondaDependency](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py). Conda crée ensuite un fichier nommé **conda_dependencies.yml** dans le répertoire **aml_config** au sein de votre espace de travail avec votre liste de dépendances de package, et configure votre environnement Python lorsque vous soumettez votre expérience de formation. 

La configuration initiale d'un nouvel environnement peut prendre quelques minutes en fonction de la taille des dépendances requises. Tant que la liste des packages reste inchangée, le temps de configuration ne change pas.
  
Le code suivant présente un exemple d’environnement géré par le système nécessitant scikit-learn :
    
[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_system_managed)]

#### <a name="user-managed-environment"></a>Environnement géré par l’utilisateur

Pour un environnement géré par l'utilisateur, vous êtes responsable de la configuration de votre environnement et de l'installation de chaque package requis par votre script de formation sur la cible de calcul. Si votre environnement de formation est déjà configuré (par exemple, sur votre ordinateur local), vous pouvez ignorer l'étape de configuration en définissant `user_managed_dependencies` sur True. Conda ne vérifie pas votre environnement et n’installe rien à votre place.

Le code suivant présente un exemple de configuration d’exécutions d’apprentissage pour un environnement géré par l’utilisateur :

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_user_managed)]
  
## <a name="set-up-in-python"></a>Configurer dans Python

Reportez-vous aux sections ci-dessous pour configurer ces cibles de calcul :

* [Ordinateur local](#local)
* [Capacité de calcul Azure Machine Learning](#amlcompute)
* [Machines virtuelles distantes](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>Ordinateur local

1. **Créer et attacher** : Il n’est pas nécessaire de créer ou d’attacher une cible de calcul pour utiliser votre ordinateur local en tant qu’environnement d’apprentissage.  

1. **Configurer** :  Lorsque vous utilisez votre ordinateur local comme cible de calcul, le code d’apprentissage est exécuté dans votre [environnement de développement](how-to-configure-environment.md).  Si celui-ci comprend déjà les packages Python nécessaires, utilisez l’environnement géré par l’utilisateur.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

À présent que vous avez attaché la cible de calcul et configuré votre série de tests, l’étape suivante consiste à [soumettre la série de tests d’apprentissage](#submit).

### <a id="amlcompute"></a>Capacité de calcul Azure Machine Learning

Capacité de calcul Azure Machine Learning est une infrastructure de capacité de calcul managée qui permet à l’utilisateur de créer facilement une capacité de calcul à un ou plusieurs nœuds. La capacité de calcul est créée dans la région de votre espace de travail sous forme de ressource qui peut être partagée avec d’autres utilisateurs dans votre espace de travail. La cible de calcul monte en puissance automatiquement quand un travail est soumis, et peut être placée dans un réseau virtuel Azure. La cible de calcul s’exécute dans un environnement conteneurisé et empaquète les dépendances de votre modèle dans un [conteneur Docker](https://www.docker.com/why-docker).

Vous pouvez utiliser une capacité de calcul Azure Machine Learning pour distribuer le processus d’entraînement sur un cluster de nœuds de capacité de calcul de CPU ou de GPU dans le cloud. Pour plus d’informations sur les tailles de machine virtuelle qui incluent des GPU, consultez [Tailles de machine virtuelle à GPU optimisé](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Capacité de calcul Azure Machine Learning comporte des limites par défaut, par exemple le nombre de cœurs qui peuvent être alloués. Pour plus d’informations, consultez [Gérer et demander des quotas pour les ressources Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).


Vous pouvez créer un environnement Compute Azure Machine Learning à la demande quand vous planifiez une exécution ou comme ressource persistante.

#### <a name="run-based-creation"></a>Création basée sur l’exécution

Vous pouvez créer une capacité de calcul Azure Machine Learning en tant que cible de calcul au moment de l’exécution. La capacité de calcul est automatiquement créée pour votre exécution. La capacité de calcul est automatiquement supprimée une fois l’exécution terminée. 

> [!NOTE]
> Pour spécifier le nombre maximal de nœuds à utiliser, vous définissez normalement `node_count` sur le nombre de nœuds. Il existe actuellement (au 4 avril 2019) un bogue qui empêche cela de fonctionner. Pour résoudre ce problème, utilisez la propriété `amlcompute._cluster_max_node_count` de la configuration d’exécution. Par exemple : `run_config.amlcompute._cluster_max_node_count = 5`.

> [!IMPORTANT]
> La création de la capacité de calcul Azure Machine Learning basée sur l’exécution est actuellement en préversion. N’utilisez pas la création basée sur l’exécution si vous utilisez l’optimisation automatisée des hyperparamètres ou le machine learning automatisé. Pour utiliser un réglage d’hyperparamètre ou un apprentissage automatique, créez plutôt un cible de [calcul persistante](#persistent).

1.  **Créer, attacher et configurer** : La création basée sur l’exécution effectue toutes les étapes nécessaires pour créer, attacher et configurer la cible de calcul avec la configuration de série de tests.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


À présent que vous avez attaché la cible de calcul et configuré votre série de tests, l’étape suivante consiste à [soumettre la série de tests d’apprentissage](#submit).

#### <a id="persistent"></a>Capacité de calcul persistante

Une capacité de calcul Azure Machine Learning persistante peut être réutilisée pour plusieurs travaux. Il peut être partagé avec d’autres utilisateurs dans l’espace de travail et conservé entre les travaux.

1. **Créer et attacher** : Pour créer une ressource de capacité de calcul Azure Machine Learning persistante dans Python, spécifiez les propriétés **vm_size** et **max_nodes**. Azure Machine Learning utilise ensuite des valeurs calculées par défaut pour les autres propriétés. La capacité de calcul diminue en puissance, et passe automatiquement à zéro nœud quand elle n’est pas utilisée.   Des machines virtuelles dédiées sont créées pour exécuter vos travaux en fonction des besoins.
    
    * **vm_size** : famille de machines virtuelles des nœuds créés par Capacité de calcul Azure Machine Learning.
    * **max_nodes** : nombre maximal de nœuds pour la mise à l’échelle automatique lors de l’exécution d’un travail sur une capacité de calcul Azure Machine Learning.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Vous pouvez aussi configurer plusieurs propriétés avancées lors de la création d’une capacité de calcul Azure Machine Learning. Ces propriétés vous permettent de créer un cluster persistant de taille fixe, ou au sein d’un réseau virtuel Azure existant dans votre abonnement.  Pour plus de détails, voir la [classe AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ).
    
   Vous pouvez également créer et attacher une capacité de calcul Azure Machine Learning Compute persistante [via le portail Azure](#portal-create).

1. **Configurer** : Créez une configuration de série de tests pour la cible de calcul persistante.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

À présent que vous avez attaché la cible de calcul et configuré votre série de tests, l’étape suivante consiste à [soumettre la série de tests d’apprentissage](#submit).


### <a id="vm"></a>Machines virtuelles distantes

Azure Machine Learning prend également en charge l’utilisation de votre propre ressource de calcul et son attachement à votre espace de travail. Un tel type de ressource est une machine virtuelle distante arbitraire tant qu’elle est accessible depuis Azure Machine Learning service. Il peut s’agir d’une machine virtuelle Azure, d’un serveur distant dans votre organisation, ou encore d’un serveur local. En particulier, avec l’adresse IP et les informations d’identification (nom d’utilisateur/mot de passe ou clé SSH), vous pouvez utiliser n’importe quelle machine virtuelle accessible pour les exécutions à distance.

Vous pouvez utiliser un environnement Conda intégré au système, un environnement Python déjà existant ou un conteneur Docker. Pour exécuter sur un conteneur Docker, vous devez disposer d’un moteur Docker en cours d’exécution sur la machine virtuelle. Cette fonctionnalité est particulièrement pratique quand vous voulez obtenir un environnement cloud de développement/expérience plus flexible que votre ordinateur local.

Pour ce scénario, utilisez Azure Data Science Virtual Machine (DSVM) en tant que machine virtuelle Azure. Cette machine virtuelle est un environnement de science des données et de développement d’intelligence artificielle préconfiguré dans Azure. La machine virtuelle offre un choix organisé d’outils et d’infrastructures pour le développement de l’apprentissage automatique en cycle de vie complet. Pour plus d’informations sur l’utilisation de la DSVM avec Azure Machine Learning, consultez [Configurer un environnement de développement](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

1. **Créer** : Créez une Data Science Virtual Machine (DSVM) à utiliser pour former votre modèle. Pour savoir comment créer cette ressource, voir [Approvisionner une machine virtuelle pour la science des données pour Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning prend uniquement en charge les machines virtuelles exécutant Ubuntu. Lorsque vous créez une machine virtuelle ou en choisissez une existante, celle-ci doit utiliser Ubuntu.

1. **Attacher** : Pour attacher une machine virtuelle existante comme cible de calcul, vous devez fournir le nom de domaine complet, le nom d’utilisateur et le mot de passe de la machine virtuelle. Dans l’exemple, remplacez \<fqdn> par le nom de domaine complet public de la machine virtuelle, ou l’adresse IP publique. Remplacez \<username> et \<password> par le nom d’utilisateur SSH et le mot de passe de la machine virtuelle.

   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
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

   Vous pouvez également attacher la Data Science Virtual Machine (DSVM) à votre espace de travail [via le portail Azure](#portal-reuse).

1. **Configurer** : Créez une configuration de série de tests pour la cible de calcul Data Science Virtual Machine (DSVM). Docker et Conda sont utilisés pour créer et configurer l’environnement d’entraînement sur la DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


À présent que vous avez attaché la cible de calcul et configuré votre série de tests, l’étape suivante consiste à [soumettre la série de tests d’apprentissage](#submit).

### <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight est une plateforme populaire pour l’analytique de Big Data. Elle fournit Apache Spark, que vous pouvez utiliser pour entraîner votre modèle.

1. **Créer** :  Créez le cluster HDInsight à utiliser pour former votre modèle. Pour créer un cluster Spark sur HDInsight, consultez [Créer un cluster Spark dans HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Lors de la création du cluster, vous devez spécifier un nom d’utilisateur SSH et un mot de passe. Notez ces valeurs, car vous en aurez besoin lors de l’utilisation de HDInsight comme cible de calcul.
    
    Une fois le cluster créé, connectez-le au nom d’hôte \<clustername>.azurehdinsight.net, où \<clustername> est le nom que vous avez donné au cluster. 

1. **Attacher** : Pour attacher un cluster HDInsight en tant que cible de calcul, vous devez fournir le nom d’hôte, le nom d’utilisateur et le mot de passe du cluster HDInsight. L’exemple suivant utilise le SDK pour attacher un cluster à votre espace de travail. Dans l’exemple, remplacez \<clustername > par le nom de votre cluster. Remplacez \<username> et \<password> par le nom d’utilisateur SSH et le mot de passe du cluster.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azureinsight.net', 
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

   Vous pouvez également attacher le cluster HDInsight à votre espace de travail [via le portail Azure](#portal-reuse).

1. **Configurer** : Créez une configuration de série de tests pour la cible de calcul HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


À présent que vous avez attaché la cible de calcul et configuré votre série de tests, l’étape suivante consiste à [soumettre la série de tests d’apprentissage](#submit).


### <a id="azbatch"></a>Azure Batch 

Azure Batch sert à exécuter efficacement des applications de calcul haute performance (HPC) en parallèle et à grande échelle dans le cloud. AzureBatchStep peut être utilisé dans un pipeline Azure Machine Learning pour envoyer des travaux à un pool de machines Azure Batch.

Pour attacher Azure Batch comme cible de calcul, vous devez utiliser le Kit de développement logiciel Azure Machine Learning et fournir les informations suivantes :

-   **Nom de calcul Azure Batch** : Nom convivial à utiliser pour le calcul au sein de l’espace de travail
-   **Nom du compte Azure Batch** : Nom du compte Azure Batch
-   **Groupe de ressources** : Groupe de ressources qui contient le compte Azure Batch.

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

## <a name="set-up-in-azure-portal"></a>Configuré dans le portail Azure

Vous pouvez accéder aux cibles de calcul associées à votre espace de travail à partir du portail Azure.  Vous pouvez utiliser le portail pour :

* [Afficher les cibles de calcul](#portal-view) attachées à votre espace de travail
* [Créer une cible de calcul](#portal-create) dans votre espace de travail
* [Joindre une cible de calcul](#portal-reuse) créée en dehors de l'espace de travail

Après avoir créé une cible et l’avoir attachée à votre espace de travail, vous allez l’utiliser dans votre configuration de série de tests avec un objet `ComputeTarget` : 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Afficher les cibles de calcul


Pour consulter les cibles de calcul de votre espace de travail, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre espace de travail. 
1. Sous __Applications__, sélectionnez __Capacité de calcul__.

    ![Onglet Calcul](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a id="portal-create"></a>Créer une cible de calcul

Suivez la procédure ci-dessus pour afficher la liste des cibles de calcul. Puis procédez comme suit pour créer une cible de calcul : 

1. Cliquez sur le signe plus (+) pour ajouter une cible de calcul.

    ![Ajouter une cible de calcul](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Entrez un nom pour la cible de calcul. 

1. Sélectionnez **Capacité de calcul Machine Learning** comme type de capacité de calcul à utiliser pour __Entraînement__. 

    >[!NOTE]
    >La capacité de calcul Azure Machine Learning est la seule ressource de calcul géré que vous pouvez créer sur le portail Azure.  Toutes les autres ressources de calcul peuvent être attachées après leur création.

1. Remplissez le formulaire. Indiquez une valeur pour les propriétés requises, notamment **Famille de machines virtuelles**, ainsi que le **nombre maximal de nœuds** à utiliser pour mettre en place la capacité de calcul.  

    ![Remplir le formulaire](./media/how-to-set-up-training-targets/add-compute-form.png) 

1. Sélectionnez __Créer__.


1. Pour afficher l’état de l’opération de création, sélectionnez la cible de calcul dans la liste :

    ![Sélectionnez une cible de calcul pour afficher l’état de l’opération de création](./media/how-to-set-up-training-targets/View_list.png)

1. Vous voyez alors les détails de la cible de calcul : 

    ![Consultez les détails de la cible de calcul](./media/how-to-set-up-training-targets/compute-target-details.png) 



### <a id="portal-reuse"></a>Joindre des cibles de calcul

Pour utiliser des cibles de calcul créées en dehors de l'espace de travail d'Azure Machine Learning service, vous devez les joindre. Une fois la cible de calcul jointe, elle sera disponible dans votre espace de travail.

Suivez la procédure décrite plus haut pour afficher la liste des cibles de calcul. Suivez ensuite les étapes ci-dessous pour joindre une cible de calcul : 

1. Cliquez sur le signe plus (+) pour ajouter une cible de calcul. 
1. Entrez un nom pour la cible de calcul. 
1. Sélectionnez le type de capacité de calcul à attacher pour __Entraînement__ :

    > [!IMPORTANT]
    > Il n’est pas possible d’attacher tous les types de capacités de calcul à l’aide du portail Azure. Actuellement, les types qui peuvent être attachés pour l’entraînement sont les suivants :
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

Vous pouvez accéder aux cibles de calcul associées à votre espace de travail à l’aide de l’[extension CLI](reference-azure-machine-learning-cli.md) pour Azure Machine Learning service.  Vous pouvez utiliser l’interface CLI pour :

* Créer une cible de calcul gérée
* Mettre à jour une cible de calcul gérée
* Attacher une cible de calcul non gérée

Pour plus d’informations, voir [Gestion des ressources](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Configurer avec VS Code

Vous pouvez accéder aux cibles de calcul associées à votre espace de travail, et les créer et les gérer à l’aide de l’[extension VS Code](how-to-vscode-tools.md#create-and-manage-compute-targets) pour Azure Machine Learning service.

## <a id="submit"></a>Soumettre une série de tests d’apprentissage

Après avoir créé une configuration de série de tests, vous l’utilisez pour exécuter votre expérience.  Le modèle de code pour soumettre une série de tests d’apprentissage est le même pour tous les types de cibles de calcul :

1. Créer une expérience à exécuter
1. Soumettez l’exécution.
1. Attendez la fin de l’exécution.

> [!IMPORTANT]
> Quand vous soumettez la série de tests d’apprentissage, un instantané du répertoire contenant vos scripts d’apprentissage est créé et envoyé à la cible de calcul. Il est également stocké dans le cadre de l’expérience dans votre espace de travail. Si vous modifiez des fichiers et que vous soumettez à nouveau l’exécution, seuls les fichiers modifiés seront téléchargés.
>
> Pour empêcher les fichiers d’être inclus dans la capture instantanée, créez un élément [.gitignore](https://git-scm.com/docs/gitignore) ou un fichier `.amlignore` dans le répertoire et ajoutez-y les fichiers. Le fichier `.amlignore` utilise les mêmes modèles et syntaxe que le fichier [.gitignore](https://git-scm.com/docs/gitignore). Si les deux fichiers existent, le fichier `.amlignore` est prioritaire.
> 
> Pour plus d’informations, consultez [Instantanés](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Création d'une expérience

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

Vous pouvez également :

* Soumettre l’expérience avec un objet `Estimator`, comme indiqué dans [Former des modèles ML avec des estimateurs](how-to-train-ml-models.md).
* Soumettre une expérience [à l’aide de l’extension CLI](reference-azure-machine-learning-cli.md#experiments).
* Soumettre une expérience via l’[extension VS Code](how-to-vscode-tools.md#train-and-tune-models).

## <a name="github-tracking-and-integration"></a>Intégration et suivi GitHub

Lorsque vous lancez une exécution d’entraînement où le répertoire source est un répertoire Git local, les informations relatives au répertoire sont stockées dans l’historique des exécutions. Par exemple, l’ID de validation en cours pour le répertoire est consigné au sein de l’historique.

## <a name="notebook-examples"></a>Exemples de notebooks

Pour des exemples d’apprentissage avec différentes cibles de calcul, voir les blocs-notes suivants :
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Former un modèle](tutorial-train-models-with-aml.md) utilise une cible de calcul gérée pour former un modèle.
* Découvrez comment [optimiser efficacement les hyperparamètres](how-to-tune-hyperparameters.md) afin de générer des modèles plus efficaces.
* Une fois le modèle formé, découvrez [comment et où déployer les modèles](how-to-deploy-and-where.md).
* Consultez la documentation de référence du Kit de développement logiciel (SDK) de la [classe RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py).
* [Utiliser Azure Machine Learning service avec des réseaux virtuels Azure](how-to-enable-virtual-network.md)
