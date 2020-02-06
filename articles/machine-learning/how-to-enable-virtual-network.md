---
title: Sécuriser des expériences et une inférence dans un réseau virtuel
titleSuffix: Azure Machine Learning
description: Apprenez à sécuriser les travaux d’expérimentation/de formation et les travaux d’inférence/de notation dans Azure Machine Learning au sein d’un réseau virtuel Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 01/13/2020
ms.openlocfilehash: 53644066276aa8e9fb57b4802142bca3fe4b342f
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760847"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Sécuriser l’expérimentation Azure Machine Learning et les travaux d’inférence au sein d’un réseau virtuel Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous apprendrez à sécuriser les travaux d’expérimentation/de formation et les travaux d’inférence/de notation dans Azure Machine Learning au sein d’un réseau virtuel (vnet) Azure.

Un **réseau virtuel** agit en tant que limite de sécurité, isolant vos ressources Azure de l’Internet public. Vous pouvez également joindre un réseau virtuel Azure à votre réseau local. En joignant les réseaux, cela vous permet d’entraîner vos modèles et d’accéder à vos modèles déployés à des fins d’inférence de façon sécurisée.

Azure Machine Learning s’appuie sur d’autres services Azure pour les ressources de calcul. Les ressources de calcul ou les [cibles de calcul](concept-compute-target.md) sont utilisées pour entraîner et déployer des modèles. Ces cibles peuvent être créées à l’intérieur d’un réseau virtuel. Par exemple, vous pouvez utiliser la machine virtuelle Microsoft Data Science Virtual Machine pour entraîner un modèle, puis déployer le modèle sur Azure Kubernetes Service (AKS). Pour plus d’informations sur les réseaux virtuels, consultez la page [Présentation du réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Cet article fournit aussi des informations détaillées sur les *paramètres de sécurité avancés*, qui ne sont pas nécessaires aux cas d’utilisation de base ou expérimentaux. Certaines sections de cet article fournissent des informations de configuration pour un large éventail de scénarios. Vous n’avez pas besoin de suivre les instructions dans l’ordre ou dans leur intégralité.

> [!TIP]
> Sauf indication contraire, l’utilisation de ressources telles que des comptes de stockage ou des cibles de calcul à l’intérieur d’un réseau virtuel fonctionne avec les pipelines de Machine Learning et les flux de travail sans pipeline tels que les exécutions de script.

> [!WARNING]
> Microsoft ne prend pas en charge l’utilisation du concepteur Azure Machine Learning ou du Machine Learning automatisé (à partir de Studio) avec des ressources au sein d’un réseau virtuel.

## <a name="prerequisites"></a>Conditions préalables requises

+ Un [espace de travail](how-to-manage-workspace.md) Azure Machine Learning.

+ Connaissance générale du [service de réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) et de la [mise en réseau IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Un réseau virtuel et un sous-réseau préexistants à utiliser avec vos ressources de calcul.

## <a name="use-a-storage-account-for-your-workspace"></a>Utilisez un compte de stockage pour votre espace de travail

Pour utiliser le compte Stockage Azure de l’espace de travail d’un réseau virtuel, effectuez les étapes suivantes :

1. Créez une ressource de calcul (par exemple, une instance de Capacité de calcul Machine Learning ou cluster) dans un réseau virtuel ou attachez une ressource de capacité de calcul à l’espace de travail (par exemple, un cluster HDInsight, une machine virtuelle ou un cluster Azure Kubernetes Service). La ressource de capacité de calcul peut servir à l’expérimentation ou au déploiement de modèle.

   Pour plus d’informations, consultez les sections [Utiliser la Capacité de calcul Machine Learning](#amlcompute), [Utiliser une machine virtuelle ou un cluster HDInsight](#vmorhdi) et [Utiliser Azure Kubernetes Service](#aksvnet) de cet article.

1. Dans le Portail Azure, accédez au stockage lié à votre espace de travail.

   [![Image du portail Azure montrant le stockage Azure lié à l’espace de travail Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Sur la page **Stockage Azure**, sélectionnez __Pare-feu et réseaux virtuels__.

   ![La zone « Pare-feu et réseaux virtuels » de la page Stockage Azure dans le portail Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Dans la page __Pare-feux et réseaux virtuels__, effectuez les actions suivantes :
    - Sélectionnez __Réseaux sélectionnés__.
    - Cliquez sur __Réseaux virtuel__ puis choisissez le lien __Ajouter un réseau virtuel existant__. Cette action ajoute le réseau virtuel dans lequel votre capacité de calcul réside (voir l’étape 1).

        > [!IMPORTANT]
        > Le compte de stockage doit se trouver dans le même réseau virtuel que les instances de capacité de calcul ou les clusters utilisés pour l’entraînement ou l’inférence.

    - Vérifiez que __Autoriser les services Microsoft approuvés à accéder à ce compte de stockage__ est coché.

    > [!IMPORTANT]
    > Lorsque vous travaillez avec le SDK Azure Machine Learning, votre environnement de développement doit être en mesure de se connecter au compte de Stockage Azure. Lorsque le compte de stockage se trouve dans un réseau virtuel, le pare-feu doit autoriser l’accès à partir de l’adresse IP de l’environnement de développement.
    >
    > Pour activer l’accès au compte de stockage, consultez les __Pare-feux et réseaux virtuels__ du compte de stockage à partir d’un *navigateur web sur le client de développement*. Utilisez ensuite la case à cocher __Ajouter votre adresse IP client__ pour ajouter l’adresse IP du client à la __PLAGE D’ADRESSES__. Vous pouvez également utiliser le champ __PLAGE D’ADRESSES__ pour entrer manuellement l’adresse IP de l’environnement de développement. Une fois que l’adresse IP du client a été ajoutée, elle peut accéder au compte de stockage à l’aide du SDK.

   [![Le volet « Pare-feu et réseaux virtuels » du Portail Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Vous pouvez placer le _compte de stockage par défaut_ pour Azure Machine Learning ou les _comptes de stockage autres que ceux par défaut_ dans un réseau virtuel.
>
> Le compte de stockage par défaut est automatiquement configuré lorsque vous créez un espace de travail.
>
> Pour les comptes de stockage autres que ceux par défaut, le paramètre `storage_account` dans la fonction [`Workspace.create()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) vous permet de spécifier un compte de stockage personnalisé par ID de ressource Azure.

## <a name="use-azure-data-lake-storage-gen-2"></a>Utiliser Azure Data Lake Storage Gen 2

Azure Data Lake Storage Gen2 est un ensemble de fonctionnalités dédiées à l’analytique du Big Data s’appuyant sur le Stockage Blob Azure. Il permet de stocker des données utilisées pour l’apprentissage de modèles avec Azure Machine Learning. 

Pour utiliser Azure Data Lake Storage Gen 2 à l’intérieur du réseau virtuel de votre espace de travail Azure Machine Learning, procédez comme suit :

1. Créez un compte Azure Data Lake Storage Gen 2. Pour plus d’informations, voir [Créer un compte Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

1. Suivez les étapes 2-4 de la section précédente, [Utiliser un compte de stockage pour votre espace de travail](#use-a-storage-account-for-your-workspace) afin de placer le compte dans le réseau virtuel.

Lorsque vous utilisez Azure Machine Learning avec Azure Data Lake Storage Gen 2 à l’intérieur d’un réseau virtuel, suivez les instructions suivantes :

* Si vous utilisez le __Kit de développement logiciel (SDK) pour créer un jeu de données__ et que le système exécutant le code __n’est pas dans le réseau virtuel__, utilisez le paramètre `validate=False`. Ce paramètre ignore la validation qui échoue si le système n’est pas dans le même réseau virtuel que le compte de stockage. Pour plus d’informations, voir la méthode [from_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-).

* Lorsque vous utilisez une instance ou un cluster de calcul Azure Machine Learning pour effectuer l’apprentissage d’un modèle à l’aide du jeu de données, celui-ci doit se trouver dans le même réseau virtuel que le compte de stockage.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Utilisez une instance de coffre de clés avec votre espace de travail

L’instance de coffre de clés associée à l’espace de travail est utilisée par Azure Machine Learning pour stocker les informations d’identification suivantes :
* Chaîne de connexion du compte de stockage associé
* Mots de passe pour les instances Azure Container Repository
* Chaînes de connexion aux magasins de données

Pour utiliser les fonctionnalités d’expérimentation Azure Machine Learning avec Azure Key Vault derrière un réseau virtuel, effectuez les étapes suivantes :

1. Accédez au coffre de clés associé à l’espace de travail.

   [![Le coffre de clés associé à l’espace de travail Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Sur la page **Key Vault**, sélectionnez le volet de gauche __Pare-feux et réseaux virtuels__.

   ![La section « Pare-feux et réseaux virtuels » dans le volet Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Dans la page __Pare-feux et réseaux virtuels__, effectuez les actions suivantes :
    - Sous __Autoriser l’accès depuis__, cliquez sur __Réseaux sélectionnés__.
    - Sous __Réseaux virtuels__, sélectionnez __Ajouter des réseaux virtuels existants__ pour ajouter le réseau virtuel où se trouve votre calcul d’expérimentation.
    - Sous __Autoriser les services Microsoft approuvés pour contourner ce pare-feu__, sélectionnez __Oui__.

   [![La section « Pare-feux et réseaux virtuels » dans le volet Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="compute-instance"></a>Utiliser une Capacité de calcul Machine Learning

> [!NOTE]
> Les instances de calcul (préversion) ne sont à l’heure actuelle disponibles que pour les espaces de travail des régions **USA Centre Nord** et **Royaume-Uni Sud**. D’autres régions seront bientôt prises en charge.
> Utilisez l’une de ces régions pour créer une instance de calcul qui peut être ajoutée au réseau virtuel.

Pour utiliser une instance de calcul Machine Learning Azure ou un cluster de calcul dans un réseau virtuel, les exigences réseau suivantes doivent être remplies :

> [!div class="checklist"]
> * Le réseau virtuel doit être dans les mêmes abonnement et région que l’espace de travail Azure Machine Learning.
> * Le sous-réseau spécifié pour l’instance ou le cluster de calcul doit avoir suffisamment d’adresses IP non attribuées pour toutes les machines virtuelles ciblées. Si le sous-réseau n’a pas suffisamment d’adresses IP non attribuées, le cluster de calcul est alloué partiellement.
> * Vérifiez si vos stratégies ou verrous de sécurité sur l’abonnement ou le groupe de ressources du réseau virtuel restreignent les autorisations pour gérer le réseau virtuel. Si vous souhaitez sécuriser le réseau virtuel en limitant le trafic, laissez certains ports ouverts pour le service Capacité de calcul. Pour plus d’informations, voir la section [Ports requis](#mlcports).
> * Si vous vous apprêtez à placer plusieurs instances ou clusters de calcul sur un réseau virtuel, vous devrez peut-être demander une augmentation du quota pour une ou plusieurs de vos ressources.
> * Si le ou les comptes de stockage Azure pour l’espace de travail sont également sécurisés dans un réseau virtuel, ils doivent se trouver dans le même réseau virtuel que l’instance ou le cluster de capacité de calcul Azure Machine Learning. Si vous créez une instance de calcul dans le même réseau virtuel, vous devez détacher le ou les comptes de stockage du réseau virtuel, créer l’instance de calcul dans le réseau virtuel, puis rattacher le ou les comptes de stockage au réseau virtuel.

La capacité de calcul ou le cluster Machine Learning Azure alloue automatiquement des ressources réseau supplémentaires au groupe de ressources qui contient le réseau virtuel. Pour chaque instance ou cluster de calcul, le service alloue les ressources suivantes :

* Un seul groupe de sécurité réseau
* Une seule adresse IP publique
* Un seul équilibreur de charge

Ces ressources sont limitées par les [quotas de ressources](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) de l’abonnement.


### <a id="mlcports"></a> Ports requis

Capacité de calcul Machine Learning utilise le service Azure Batch pour provisionner les machines virtuelles dans le réseau virtuel spécifié. Le sous-réseau doit autoriser les communications entrantes à partir du service Batch. Ces communications servent à planifier les exécutions sur les nœuds Capacité de calcul Machine Learning et à communiquer avec Stockage Azure et d’autres ressources. Le service Batch ajoute des groupes de sécurité réseau (NSG) au niveau des interfaces réseau (NIC) qui sont attachées aux machines virtuelles. Ces groupes de sécurité réseau configurent automatiquement des règles de trafic entrant et sortant pour autoriser le trafic suivant :

- Trafic TCP entrant sur les ports 29876 et 29877 à partir d’une __balise de service__ de __BatchNodeManagement__.

    ![Règle de trafic entrant qui utilise la balise de service BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (facultatif) Trafic TCP entrant sur le port 22 pour autoriser l’accès à distance. Utilisez ce port uniquement si vous souhaitez vous connecter à l’aide du protocole SSH sur l’adresse IP publique.

- Le trafic sortant sur n’importe quel port vers le réseau virtuel.

- Le trafic sortant sur n’importe quel port vers internet.

- Le trafic TCP entrant de l’instance de calcul sur le port 44224 à partir d’une __Balise de service__ __AzureMachineLearning__.

Soyez prudent si vous modifiez ou ajoutez des règles de trafic entrant ou sortant dans des groupes de sécurité réseau configurés par Batch. Si un groupe de sécurité réseau bloque la communication vers les nœuds de calcul, le service Capacité de calcul définit l’état de ces nœuds sur « inutilisable ».

Vous n’avez pas besoin de spécifier des groupes de sécurité réseau au niveau du sous-réseau, car le service Azure Batch configure ses propres groupes de sécurité réseau. Cependant, si le sous-réseau spécifié comporte des groupes de sécurité réseau associés ou un pare-feu, configurez les règles de sécurité du trafic entrant et sortant comme indiqué plus haut.

La configuration de la règle de groupe de sécurité réseau dans le Portail Azure est illustrée dans les images suivantes :

[![Les règles de groupe de sécurité réseau de trafic entrant pour Capacité de calcul](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![Les règles des groupes de sécurité réseau de trafic sortant pour Capacité de calcul](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a> Limitez la connectivité sortante à partir du réseau virtuel

Si vous ne souhaitez pas utiliser les règles de trafic sortant par défaut et souhaitez limiter l’accès sortant de votre réseau virtuel, effectuez les étapes suivantes :

- Refusez la connexion internet sortante à l’aide des règles NSG.

- Pour une __instance de calcul__ ou un __cluster de calcul__, limitez le trafic sortant aux éléments suivants :
   - Stockage Azure, à l’aide de la __balise de service__ du __stockage__
   - Azure Container Registry, à l’aide de la __balise du service__ de __AzureContainerRegistry__
   - Azure Machine Learning, à l’aide de la __balise du service__ de __AzureMachineLearning__
   
- Pour une __instance de calcul__, ajoutez également les éléments suivants :
   - Azure Resource Manager, à l’aide de la __balise de service__ de __AzureResourceManager__
   - Azure Active Directory, à l’aide de la __balise de service__ de __AzureActiveDirectory__

La configuration de la règle de groupe de sécurité réseau dans le Portail Azure est illustrée dans l’image suivante :

[![Les règles des groupes de sécurité réseau de trafic sortant pour Capacité de calcul](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Si vous envisagez d’utiliser les images Docker par défaut fournies par Microsoft et que vous activez les dépendances gérées par l’utilisateur, vous devez également utiliser une __Balise de service__ __MicrosoftContainerRegistry.Nom_Région__ (par exemple, MicrosoftContainerRegistry.EastUS).
>
> Cette configuration est nécessaire si les scripts d’apprentissage contiennent du code de ce type :
>
> __Entraînement RunConfig__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Entraînement Estimator__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>Routages définis par l’utilisateur pour le tunneling forcé

Si vous utilisez le tunneling forcé avec la Capacité de calcul Machine Learning, ajoutez des [routages définis par l’utilisateur (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) au sous-réseau qui contient la ressource de calcul.

* Établissez un UDR pour chaque adresse IP utilisée par le service Azure Batch dans la région où se trouvent vos ressources. Ces UDR autorisent le service Batch à communiquer avec les nœuds de calcul pour la planification des tâches. Pour obtenir la liste des adresses IP du service Batch, utilisez l’une des méthodes suivantes :

    * Téléchargez les [plages d’adresses IP Azure et les balises de service](https://www.microsoft.com/download/details.aspx?id=56519) et recherchez `BatchNodeManagement.<region>` dans le fichier, où `<region>` est votre région Azure.

    * Utilisez [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pour télécharger les informations. L’exemple suivant télécharge les informations d’adresse IP et filtre les informations pour la région USA Est 2 :

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* Le trafic sortant vers le stockage Azure ne doit pas être bloqué par votre appliance de réseau local. Plus précisément, les URL se présentent sous la forme `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` et `<account>.blob.core.windows.net`.

Lorsque vous ajoutez les UDR, définissez l’itinéraire pour chaque préfixe d’adresse IP Batch connexe et définissez __Type de tronçon suivant__ sur __Internet__. L’illustration suivante propose un exemple de cet UDR dans le portail Azure :

![Exemple de UDR pour un préfixe d’adresse](./media/how-to-enable-virtual-network/user-defined-route.png)

Pour plus d’informations, consultez l’article [Créer un pool Azure Batch dans un réseau virtuel](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Créer un cluster de calcul dans un réseau virtuel

Pour créer un cluster Capacité de calcul Machine Learning, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre espace de travail Azure Machine Learning.

1. Dans la section __Application__, sélectionnez __Capacité de calcul__, puis __Ajouter un capacité de calcul__.

1. Pour configurer cette ressource de calcul afin d’utiliser un réseau virtuel, effectuez les actions suivantes :

    a. Pour __Configuration du réseau__, sélectionnez __Avancé__.

    b. Dans la liste déroulante __Groupe de ressources__, sélectionnez le groupe de ressources qui contient le réseau virtuel.

    c. Dans la liste déroulante __Réseau virtuel__, sélectionnez le réseau virtuel qui contient le sous-réseau.

    d. Dans la liste déroulante __Sous-réseau__, sélectionnez le sous-réseau à utiliser.

   ![Les paramètres de réseau virtuel Capacité de calcul Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Vous pouvez également créer un cluster Capacité de calcul Machine Learning à l’aide du SDK Azure Machine Learning. Le code suivant crée un cluster Capacité de calcul dans le sous-réseau `default` d’un réseau virtuel nommé `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Une fois le processus de création terminé, vous pouvez entraîner votre modèle en utilisant le cluster dans le cadre d’une expérience. Pour plus d’informations, consultez [Sélectionner et utiliser une cible de calcul pour l’entraînement](how-to-set-up-training-targets.md).

## <a name="use-azure-databricks"></a>Utiliser Azure Databricks

Vous devez remplir les conditions suivantes pour pouvoir utiliser Azure Databricks dans un réseau virtuel avec votre espace de travail :

> [!div class="checklist"]
> * Le réseau virtuel doit être dans les mêmes abonnement et région que l’espace de travail Azure Machine Learning.
> * Si le ou les comptes de stockage Azure pour l’espace de travail sont également sécurisés dans un réseau virtuel, ils doivent se trouver dans le même réseau virtuel que le cluster Azure Databricks.
> * Outre les sous-réseaux __databricks-private__ et __databricks-public__ utilisés par Azure Databricks, le sous-réseau __default__ créé pour le réseau virtuel est également requis.

Pour obtenir des informations spécifiques sur l’utilisation d’Azure Databricks avec un réseau virtuel, consultez [Déployer des Azure Databricks dans votre réseau virtuel Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Utiliser une machine virtuelle ou un cluster HDInsight

> [!IMPORTANT]
> Azure Machine Learning prend uniquement en charge les machines virtuelles exécutant Ubuntu.

Pour utiliser une machine virtuelle ou un cluster Azure HDInsight dans un réseau virtuel avec votre espace de travail, effectuez les étapes suivantes :

1. Créez une machine virtuelle ou un cluster HDInsight à l’aide du portail Azure ou de l’interface de ligne de commande Azure, et placez le cluster dans un réseau virtuel Azure. Pour plus d’informations, consultez les articles suivants :
    * [Créer et gérer des réseaux virtuels Azure pour des machines virtuelles Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Étendre HDInsight à l’aide d’un réseau virtuel Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Pour autoriser Azure Machine Learning à communiquer avec le port SSH sur la machine virtuelle ou le cluster, configurez une entrée source pour le groupe de sécurité réseau. Le port SSH est généralement le port 22. Pour autoriser le trafic provenant de cette source, effectuez les actions suivantes :

    * Dans la liste déroulante __Source__, sélectionnez __Balise de service__.

    * Dans la liste déroulante __Balise de service source__, sélectionnez __AzureMachineLearning__.

    * Dans la liste déroulante __Plages de port source__, sélectionnez __*__ .

    * Dans la liste déroulante __Destination__, sélectionnez __Tous__.

    * Dans la liste déroulante __Plages de port de destination__, sélectionnez __22__.

    * Sous __Protocole__, sélectionnez __Tous__.

    * Sous __Action__, sélectionnez __Autoriser__.

   ![Règles de trafic entrant pour effectuer des expériences sur une machine virtuelle ou un cluster HDInsight à l’intérieur d’un réseau virtuel](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Conservez les règles de trafic sortant par défaut pour le groupe de sécurité réseau. Pour plus d’informations, consultez les règles de sécurité par défaut dans [Groupes de sécurité](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Si vous ne souhaitez pas utiliser les règles de trafic sortant par défaut et souhaitez limiter l’accès sortant de votre réseau virtuel, consultez la section [Limiter la connectivité sortante à partir du réseau virtuel](#limiting-outbound-from-vnet).

1. Attachez la machine virtuelle ou le cluster HDInsight à votre espace de travail Azure Machine Learning. Pour plus d’informations, consultez [Configurer des cibles de calcul pour l’entraînement des modèles](how-to-set-up-training-targets.md).

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Utiliser Azure Kubernetes Service (AKS)

Pour ajouter AKS sur un réseau virtuel à votre espace de travail, effectuez les étapes suivantes :

> [!IMPORTANT]
> Avant de commencer la procédure suivante, suivez les conditions préalables de la section [Configurer la mise en réseau avancée dans AKS (Azure Kubernetes Service)](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) pour savoir comment planifier l’adressage IP de votre cluster.
>
> L’instance AKS et le réseau virtuel Azure doivent être dans la même région. Si vous sécurisez le ou les comptes de stockage Azure utilisés par l’espace de travail dans un réseau virtuel, ils doivent se trouver dans le même réseau virtuel que l’instance AKS.

1. Dans le [portail Azure](https://portal.azure.com), vérifiez que le groupe de sécurité réseau qui contrôle le réseau virtuel dispose d’une règle de trafic entrant activée pour Azure Machine Learning, où __AzureMachineLearning__ est utilisé comme la **SOURCE**.

    [![Volet Ajouter une capacité de calcul d’Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png#lightbox)

1. Sélectionnez votre espace de travail Azure Machine Learning.

1. Dans la section __Application__, sélectionnez __Capacité de calcul__, puis __Ajouter un capacité de calcul__.

1. Pour configurer cette ressource de calcul afin d’utiliser un réseau virtuel, effectuez les actions suivantes :

    - Pour __Configuration du réseau__, sélectionnez __Avancé__.

    - Dans la liste déroulante __Groupe de ressources__, sélectionnez le groupe de ressources qui contient le réseau virtuel.

    - Dans la liste déroulante __Réseau virtuel__, sélectionnez le réseau virtuel qui contient le sous-réseau.

    - Dans la liste déroulante __Sous-réseau__, sélectionnez le sous-réseau.

    - Dans la __plage d’adresses de service Kubernetes__, entrez la plage d’adresses du service Kubernetes. Cette plage d’adresses utilise une plage d’adresses IP de notation CIDR (Classless Inter-Domain Routing) pour définir les adresses IP disponibles pour le cluster. Elle ne doit empiéter sur aucune plage d’adresses IP de sous-réseau (par exemple, 10.0.0.0/16).

    - Dans la zone __plage d’adresses IP du service DNS Kubernetes__, entrez l’adresse IP du service DNS Kubernetes. Cette adresse IP est affectée au service DNS Kubernetes. Elle doit se situer dans la plage d’adresses du service Kubernetes (par exemple, 10.0.0.10).

    - Dans la zone __adresse du pont Docker__, entrez l’adresse du pont Docker. Cette adresse IP est affectée au pont Docker. Elle ne doit appartenir à aucune plage d’adresses IP de sous-réseau, ni à la plage d’adresses du service Kubernetes (par exemple, 172.17.0.1/16).

   ![Azure Machine Learning : Paramètres de réseau virtuel Capacité de calcul Machine Learning](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Vérifiez que le groupe de sécurité réseau qui contrôle le réseau virtuel dispose d’une règle de sécurité du trafic entrant activée pour le point de terminaison de notation, de sorte qu’elle puisse être appelée en dehors du réseau virtuel.
   > [!IMPORTANT]
   > Conservez les règles de trafic sortant par défaut pour le groupe de sécurité réseau. Pour plus d’informations, consultez les règles de sécurité par défaut dans [Groupes de sécurité](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Règle de sécurité de trafic entrant](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Vous pouvez également utiliser le SDK Azure Machine Learning pour ajouter Azure Kubernetes Service dans un réseau virtuel. Si vous avez déjà un cluster AKS dans un réseau virtuel, attachez-le à l’espace de travail comme décrit dans [Comment déployer dans AKS](how-to-deploy-and-where.md). Le code suivant crée une instance Azure Kubernetes Service dans le sous-réseau `default` d’un réseau virtuel nommé `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Une fois le processus de création terminé, vous pouvez effectuer une inférence, ou un scoring de modèle, sur un cluster AKS derrière un réseau virtuel. Pour plus d’informations, consultez [Guide pratique pour déployer sur AKS](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Utiliser des adresses IP privées avec Azure Kubernetes Service

Par défaut, une adresse IP publique est affectée aux déploiements AKS. Quand vous utilisez AKS à l’intérieur d’un réseau virtuel, vous pouvez utiliser une adresse IP privée à la place. Les adresses IP privées sont accessibles uniquement à partir du réseau virtuel ou des réseaux joints.

Une adresse IP privée est activée en configurant AKS pour utiliser un _équilibreur de charge interne_. 

> [!IMPORTANT]
> Vous ne pouvez pas activer une adresse IP privée lors de la création du cluster Azure Kubernetes Service. Elle doit être activée en tant que mise à jour d’un cluster existant.

L’extrait de code suivant montre comment **créer un nouveau cluster AKS**, puis le mettre à jour afin d’utiliser un équilibreur de charge interne/une adresse IP privée :

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = "default"
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = “myaks”, provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", "default")
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com"/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>?api-version=2018-11-19 --body @body.json
```

Le contenu du fichier `body.json` référencé par la commande est semblable au document JSON suivant :

```json
{ 
    "location": “<region>”, 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

> [!NOTE]
> Actuellement, vous ne pouvez pas configurer l’équilibreur de charge lors de l’exécution d’une opération d’__attachement__ sur un cluster existant. Vous devez d’abord attacher le cluster, puis effectuer une opération de mise à jour pour modifier l’équilibreur de charge.

Pour plus d’informations sur l’utilisation de l’équilibreur de charge interne avec AKS, consultez [Utiliser un équilibreur de charge interne avec Azure Kubernetes Service (AKS)](/azure/aks/internal-lb).

## <a name="use-azure-firewall"></a>Utiliser le Pare-feu Azure

Lorsque vous utilisez Pare-feu Azure, vous devez configurer une règle réseau pour autoriser le trafic vers et à partir des adresses suivantes :

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

Lors de l’ajout de la règle, définissez __Protocole__ sur n’importe lequel, et les ports sur `*`.

Pour plus d’informations sur la configuration d’une règle réseau, consultez [Déployer et configurer Pare-feu Azure](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule).

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des environnements d’entraînement](how-to-set-up-training-targets.md)
* [Où déployer les modèles](how-to-deploy-and-where.md)
* [Déployer des modèles en toute sécurité avec SSL](how-to-secure-web-service.md)
