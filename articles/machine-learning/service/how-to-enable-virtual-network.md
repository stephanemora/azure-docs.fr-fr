---
title: Exécuter des expériences et une inférence dans un réseau virtuel
titleSuffix: Azure Machine Learning service
description: Exécutez de manière sécurisée des expériences et une inférence de machine learning à l’intérieur d’un réseau virtuel Azure. Découvrez comment créer des cibles de calcul pour l'entraînement des modèles et exécuter une inférence dans un réseau virtuel. Découvrez les exigences liées aux réseaux virtuels sécurisés, telles que les ports entrants et sortants nécessaires.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: f1cb7c9aa0844c82acd333c4f9dd87a4dda013e7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165348"
---
# <a name="securely-run-experiments-and-inference-inside-an-azure-virtual-network"></a>Exécuter en toute sécurité des expériences et une inférences dans un réseau virtuel Azure

Dans cet article, vous allez apprendre à exécuter vos expériences et inférence à l’intérieur d’un réseau virtuel. Un réseau virtuel agit en tant que limite de sécurité, isolant vos ressources Azure de l’Internet public. Vous pouvez également joindre un réseau virtuel Azure à votre réseau local. Cela vous permet d’entraîner vos modèles et d’accéder à vos modèles déployés à des fins d’inférence de façon sécurisée. L’inférence, ou scoring du modèle, est la phase où le modèle déployé est utilisé pour la prédiction, généralement sur des données de production.

Le service Azure Machine Learning s’appuie sur d’autres services Azure pour les ressources de calcul. Les ressources de calcul (cibles de calcul) sont utilisées pour entraîner et déployer des modèles. Ces cibles de calcul peuvent être créées à l’intérieur d’un réseau virtuel. Par exemple, vous pouvez utiliser la machine virtuelle Microsoft Data Science Virtual Machine pour entraîner un modèle, puis déployer le modèle sur Azure Kubernetes Service (AKS). Pour plus d’informations sur les réseaux virtuels, consultez la page [Présentation du réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="prerequisites"></a>Prérequis

Ce document suppose que vous êtes familiarisé avec les réseaux virtuels Azure et la gestion réseau IP en général. Ce document suppose également que vous avez créé un réseau virtuel et un sous-réseau à utiliser avec vos ressources de calcul. Si vous n’êtes pas familiarisé avec les réseaux virtuels Azure, lisez les articles suivants pour en savoir plus sur le service :

* [Adressage IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [Groupes de sécurité](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [Démarrage rapide : Créer un réseau virtuel](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [Filtrer le trafic réseau](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>Compte de stockage pour votre espace de travail

> [!IMPORTANT]
> Le __compte de stockage par défaut__ d’Azure Machine Learning service peut être placé dans un réseau virtuel __uniquement lors de l’expérimentation__.
>
> Pour les __comptes de stockage destinés à l’expérimentation autres que ceux par défaut__, ou si vous utilisez un compte de stockage pour l’__inférence__, vous devez avoir un __accès illimité au compte de stockage__.
> 
> Si vous ne savez pas si vous avez modifié ces paramètres ou pas, consultez __Changer la règle d’accès réseau par défaut__ dans [Configurer des pare-feux et des réseaux virtuels dans Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security). Utilisez les étapes permettant d’autoriser l’accès à partir de tous les réseaux lors de l’inférence ou de l’évaluation du modèle.

Pour placer le compte de stockage Azure par défaut pour l’espace de travail dans un réseau virtuel, procédez comme suit :

1. Créez un calcul d’expérimentation, par exemple. Placez une capacité de calcul Machine Learning derrière un réseau virtuel ou associez un calcul d’expérimentation à l’espace de travail, par exemple. Cluster HDInsight ou machine virtuelle. Pour plus d’informations, consultez les sections [Utiliser la Capacité de calcul Machine Learning](#use-machine-learning-compute) et [Utiliser une machine virtuelle ou un cluster HDInsight](#use-a-virtual-machine-or-hdinsight-cluster) de ce document
2. Accédez au stockage attaché à l’espace de travail. ![Image du portail Azure montrant le stockage Azure attaché à l’espace de travail Azure Machine Learning service](./media/how-to-enable-virtual-network/workspace-storage.png)
3. Sur la page Stockage Azure, sélectionnez __Pare-feu et réseaux virtuels__. ![Image du portail Azure montrant la section relative aux pare-feux et réseaux virtuels de la page Stockage Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)
4. Sur la page __Pare-feux et réseaux virtuels__, sélectionnez les entrées suivantes :
    - Sélectionnez __Réseaux sélectionnés__.
    - Sous __Réseaux virtuels__, sélectionnez __Ajouter un réseau virtuel existant__ pour ajouter le réseau virtuel où se trouve votre calcul d’expérimentation. Consultez l’étape 1.
    - Sélectionnez __Autoriser les services Microsoft approuvés à accéder à ce compte de stockage__.
![Image du portail Azure montrant la page relative aux pare-feux et réseaux virtuels sous Stockage Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png) 

5. Lors de l’exécution d’une expérience, dans votre code d’expérimentation, modifiez la configuration d’exécution pour utiliser le stockage d’objets blob :
    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```
    
## <a name="key-vault-for-your-workspace"></a>Coffre de clés pour votre espace de travail
L’instance de coffre de clés associée à l’espace de travail est utilisée par Azure Machine Learning service pour stocker les informations d’identification de différents types :
* Chaîne de connexion du compte de stockage associé
* Mots de passe pour les instances Azure Container Repository
* Chaînes de connexion aux magasins de données. 

Pour utiliser les fonctionnalités d’expérimentation Azure Machine Learning avec un coffre de clés derrière un réseau virtuel, procédez comme suit :
1. Accédez au coffre de clés associé à l’espace de travail. ![Image du portail Azure montrant le coffre de clés associé à l’espace de travail Azure Machine Learning service](./media/how-to-enable-virtual-network/workspace-key-vault.png)
2. Sur la page Coffre de clés, sélectionnez la section __Pare-feux et réseaux virtuels__. ![Image du portail Azure montrant la section relative aux pare-feux et réseaux virtuels de la page Coffre de clés](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)
3. Sur la page __Pare-feux et réseaux virtuels__, sélectionnez les entrées suivantes :
    - Sélectionnez __Réseaux sélectionnés__.
    - Sous __Réseaux virtuels__, sélectionnez __Ajouter des réseaux virtuels existants__ pour ajouter le réseau virtuel où se trouve votre calcul d’expérimentation.
    - Sélectionnez __Autoriser les services Microsoft approuvés pour contourner ce pare-feu__.
![Image du portail Azure montrant la page relative aux pare-feux et réseaux virtuels sous Coffre de clés](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png) 


## <a name="use-machine-learning-compute"></a>Utiliser Capacité de calcul Machine Learning

Pour utiliser Capacité de calcul Azure Machine Learning dans un réseau virtuel, utilisez les informations suivantes sur les exigences réseau :

- Le réseau virtuel doit être dans les mêmes abonnement et région que l’espace de travail de service Azure Machine Learning.

- Le sous-réseau spécifié pour le cluster Capacité de calcul doit avoir suffisamment d’adresses IP non attribuées pour contenir le nombre de machines virtuelles ciblées pour le cluster. Si le sous-réseau n’a pas suffisamment d’adresses IP non attribuées, le cluster est alloué partiellement.

- Si vous souhaitez sécuriser le réseau virtuel en limitant le trafic, laissez certains ports ouverts pour le service Capacité de calcul. Pour plus d’informations, consultez [Ports requis](#mlcports).

- Vérifiez si vos stratégies ou verrous de sécurité sur l’abonnement ou le groupe de ressources du réseau virtuel restreignent les autorisations pour gérer le réseau virtuel.

- Si vous vous apprêtez à placer plusieurs clusters Capacité de calcul Machine Learning dans un réseau virtuel, vous devrez peut-être demander une augmentation du quota pour une ou plusieurs de vos ressources.

    Capacité de calcul Machine Learning alloue automatiquement des ressources de mise en réseau supplémentaires dans le groupe de ressources contenant le réseau virtuel. Pour chaque cluster Capacité de calcul Machine Learning, le service Azure Machine Learning alloue les ressources suivantes :

    - Un groupe de sécurité réseau (NSG)

    - Une seule adresse IP publique

    - Un seul équilibreur de charge

  Ces ressources sont limitées par les [quotas de ressources](https://docs.microsoft.com/azure/azure-subscription-service-limits) de l’abonnement.

### <a id="mlcports"></a> Ports requis

Capacité de calcul Machine Learning utilise le service Azure Batch pour provisionner les machines virtuelles dans le réseau virtuel spécifié. Le sous-réseau doit autoriser les communications entrantes à partir du service Batch. Ces communications servent à planifier les exécutions sur les nœuds Capacité de calcul Machine Learning et à communiquer avec Stockage Azure et d’autres ressources. Azure Batch ajoute des NSG au niveau des cartes réseau attachées aux machines virtuelles. Ces groupes de sécurité réseau configurent automatiquement des règles de trafic entrant et sortant pour autoriser le trafic suivant :

- Trafic TCP entrant sur les ports 29876 et 29877 à partir d’une __balise de service__ de __BatchNodeManagement__.

    ![Image du portail Azure montrant une règle entrante utilisant la balise de service BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)
 
- (facultatif) Trafic TCP entrant sur le port 22 pour autoriser l’accès à distance. Ce port est nécessaire uniquement si vous souhaitez vous connecter à l’aide du protocole SSH sur l’adresse IP publique.
 
- Le trafic sortant sur n’importe quel port vers le réseau virtuel.

- Le trafic sortant sur n’importe quel port vers internet. 

Soyez prudent si vous modifiez ou ajoutez des règles de trafic entrant/sortant dans des groupes de sécurité réseau configurés par Batch. Si un NSG bloque la communication vers les nœuds de calcul, le service Capacité de calcul définit l’état de ces nœuds sur « inutilisable ».

Vous n’avez pas besoin spécifier de groupes de sécurité réseau au niveau du sous-réseau, car Batch configure ses propres groupes de sécurité réseau. Cependant, si le sous-réseau spécifié comporte des groupes de sécurité réseau associés et/ou un pare-feu, configurez les règles de sécurité du trafic entrant et sortant comme indiqué plus haut. 

Les captures d’écran suivantes montrent à quoi ressemble la configuration des règles NSG dans le portail Azure :

![Capture d’écran des règles NSG de trafic entrant pour Capacité de calcul](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Capture d’écran des règles NSG de trafic sortant pour Capacité de calcul](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a> Limiter la connectivité sortante à partir du réseau virtuel

Si vous ne souhaitez pas utiliser les règles de trafic sortant par défaut et souhaitez limiter l’accès sortant de votre réseau virtuel, suivez les étapes ci-dessous :

- Refuser la connexion internet sortante à l’aide des règles NSG 

- Limiter le trafic sortant vers Stockage Azure (à l’aide de la __balise du service__ de __Storage.Region_Name__, par exemple, Storage.EastUS), Azure Container Registry (à l’aide de la __balise du service__ de __AzureContainerRegistry.Region_Name__, par exemple, AzureContainerRegistry.EastUS) et Azure Machine Learning service (à l’aide de la __balise du service__ de __AzureMachineLearning__)

Les captures d’écran suivantes montrent à quoi ressemble la configuration des règles NSG dans le portail Azure :

![Capture d’écran des règles NSG de trafic sortant pour Capacité de calcul](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>Routages définis par l’utilisateur pour le tunneling forcé

Si vous utilisez le tunneling forcé avec la Capacité de calcul Machine Learning, vous devez ajouter des [itinéraires définis par l’utilisateur (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) au sous-réseau qui contient la ressource de calcul.

* Un itinéraire défini par l’utilisateur pour chaque adresse IP utilisée par le service Azure Batch dans la région où se trouvent vos ressources. Ces UDR autorisent le service Batch à communiquer avec les nœuds de calcul pour la planification des tâches. Pour obtenir la liste des adresses IP du service Batch, contactez le support Azure.

* Le trafic sortant vers le stockage Azure (plus précisément, les URL sous la forme `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` et `<account>.blob.core.windows.net`) ne doit pas être bloqué par votre appliance de réseau local.

Lorsque vous ajoutez les itinéraires définis par l’utilisateur, définissez l’itinéraire pour chaque préfixe d’adresse IP Batch connexe et définissez __Type de tronçon suivant__ sur __Internet__. L’illustration suivante propose un exemple de cet UDR dans le portail Azure :

![Exemple d’itinéraire défini par l’utilisateur pour un préfixe d’adresse](./media/how-to-enable-virtual-network/user-defined-route.png)

Pour plus d’informations, consultez l’article [Créer un pool Azure Batch dans un réseau virtuel](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Créer un cluster Capacité de calcul dans un réseau virtuel

Pour créer un cluster Capacité de calcul Machine Learning à l’aide du portail Azure, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre espace de travail de service Azure Machine Learning.

1. Dans la section __Application__, sélectionnez __Capacité de calcul__. Ensuite, sélectionnez __Ajouter une capacité de calcul__. 

    ![Comment ajouter une capacité de calcul dans le service Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Pour configurer cette ressource de calcul afin d’utiliser un réseau virtuel, utilisez ces options :

    - __Configuration réseau__ : Sélectionnez __Advanced (Avancé)__ .

    - __Groupe de ressources__ : sélectionnez le groupe de ressources qui contient le réseau virtuel.

    - __Réseau virtuel__ : sélectionnez le réseau virtuel qui contient le sous-réseau.

    - __Sous-réseau__ : sélectionnez le sous-réseau à utiliser.

   ![Capture d’écran montrant les paramètres de réseau virtuel pour la capacité de calcul Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

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
                                                           vnet_resourcegroup_name = vnet_resourcegroup_name,
                                                           vnet_name = vnet_name,
                                                           subnet_name = subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    
    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Une fois le processus de création terminé, vous pouvez entraîner votre modèle à l’aide du cluster. Pour plus d’informations, consultez [Sélectionner et utiliser une cible de calcul pour l’entraînement](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Utiliser une machine virtuelle ou un cluster HDInsight

Pour utiliser une machine virtuelle ou un cluster Azure HDInsight dans un réseau virtuel avec votre espace de travail, procédez comme suit :

> [!IMPORTANT]
> Le service Azure Machine Learning prend uniquement en charge les machines virtuelles exécutant Ubuntu.

1. Créez une machine virtuelle ou un cluster HDInsight à l’aide du portail Azure ou de l’interface de ligne de commande Azure, et placez-le dans un réseau virtuel Azure. Pour plus d’informations, consultez les documents suivants :
    * [Créer et gérer des réseaux virtuels Azure pour des machines virtuelles Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Étendre HDInsight à l’aide d’un réseau virtuel Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Pour autoriser le service Azure Machine Learning à communiquer avec le port SSH sur la machine virtuelle ou le cluster, vous devez configurer une entrée source pour le groupe de sécurité réseau. Le port SSH est généralement le port 22. Pour autoriser le trafic à partir de cette source, utilisez les informations suivantes :

    * __Source__ : Sélectionnez __Balise du service__.

    * __Balise du service source__ : Sélectionnez __AzureMachineLearning__.

    * __Plages de ports source__ : Sélectionnez __*__ .

    * __Destination__ : Sélectionnez __N’importe laquelle__.

    * __Plages de ports de destination__ : Sélectionnez __22__.

    * __Protocole__ : Sélectionnez __N’importe laquelle__.

    * __Action__ : Sélectionnez __Autoriser__.

   ![Capture d’écran de règles de trafic entrant pour effectuer des expériences sur une machine virtuelle ou un cluster HDInsight à l’intérieur d’un réseau virtuel](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Conservez les règles de trafic sortant par défaut pour le groupe de sécurité réseau. Pour plus d’informations, consultez les règles de sécurité par défaut dans [Groupes de sécurité](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Si vous ne souhaitez pas utiliser les règles de trafic sortant par défaut et souhaitez limiter l’accès sortant de votre réseau virtuel, consultez [Limiter la connectivité sortante à partir du réseau virtuel](#limiting-outbound-from-vnet).
    
1. Attachez la machine virtuelle ou le cluster HDInsight à votre espace de travail de service Azure Machine Learning. Pour plus d’informations, consultez [Configurer des cibles de calcul pour l’entraînement des modèles](how-to-set-up-training-targets.md).

## <a name="use-azure-kubernetes-service"></a>Utiliser Azure Kubernetes Service

> [!IMPORTANT]
> Vérifiez les prérequis et planifiez l’adressage IP pour votre cluster avant d’effectuer les étapes indiquées. Pour plus d’informations, consultez [Configurer la mise en réseau avancée dans Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
> 
>
> Conservez les règles de trafic sortant par défaut pour le groupe de sécurité réseau. Pour plus d’informations, consultez les règles de sécurité par défaut dans [Groupes de sécurité](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
>
> Azure Kubernetes Service et le réseau virtuel Azure doivent être dans la même région.

Pour ajouter Azure Kubernetes Service dans un réseau virtuel à votre espace de travail, procédez comme suit dans le portail Azure :

1. Assurez-vous que le groupe NSG qui contrôle le réseau virtuel dispose d’une règle de trafic entrant activée pour Azure Machine Learning service à l’aide de la __balise du service__ de __AzureMachineLearning__

    ![Comment ajouter une capacité de calcul dans le service Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)     
 
1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre espace de travail de service Azure Machine Learning.

1. Dans la section __Application__, sélectionnez __Capacité de calcul__. Ensuite, sélectionnez __Ajouter une capacité de calcul__. 

    ![Comment ajouter une capacité de calcul dans le service Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Pour configurer cette ressource de calcul afin d’utiliser un réseau virtuel, utilisez ces options :

    - __Configuration réseau__ : Sélectionnez __Advanced (Avancé)__ .

    - __Groupe de ressources__ : sélectionnez le groupe de ressources qui contient le réseau virtuel.

    - __Réseau virtuel__ : sélectionnez le réseau virtuel qui contient le sous-réseau.

    - __Sous-réseau__ : Sélectionnez le sous-réseau.

    - __Plage d’adresses du service Kubernetes__ : Sélectionnez la plage d’adresses du service Kubernetes. Cette plage, qui définit les adresses IP disponibles pour le cluster, correspond à une plage d’adresses IP exprimée selon la notation CIDR. Elle ne doit empiéter sur aucune plage d’adresses IP de sous-réseau. Par exemple :  10.0.0.0/16.

    - __Adresse IP du service DNS Kubernetes__ : sélectionnez l’Adresse IP du service DNS Kubernetes. Cette adresse IP est affectée au service DNS Kubernetes. Elle doit se situer dans la plage d’adresses du service Kubernetes. Par exemple :  10.0.0.10.

    - __Adresse du pont Docker__ : sélectionnez l’adresse du pont Docker. Cette adresse IP est affectée au pont Docker. Elle ne doit appartenir à aucune plage d’adresses IP de sous-réseau, ni à la plage d’adresses du service Kubernetes. Par exemple :  172.17.0.1/16.

   ![Azure Machine Learning service : Paramètres de réseau virtuel Capacité de calcul Machine Learning](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Assurez-vous que le groupe NSG qui contrôle le réseau virtuel dispose d’une règle de trafic entrant activée pour le point de terminaison d’évaluation de sorte qu’elle puisse être appelée depuis l’extérieur du réseau virtuel

    ![Comment ajouter une capacité de calcul dans le service Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > Si vous avez déjà un cluster AKS dans un réseau virtuel, vous pouvez l’attacher à l’espace de travail. Pour plus d’informations, consultez [Guide pratique pour déployer sur AKS](how-to-deploy-to-aks.md).

Vous pouvez également utiliser le **SDK Azure Machine Learning** pour ajouter Azure Kubernetes Service dans un réseau virtuel. Le code suivant crée une instance Azure Kubernetes Service dans le sous-réseau `default` d’un réseau virtuel nommé `mynetwork` :

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
aks_target = ComputeTarget.create(workspace = ws,
                                  name = "myaks",
                                  provisioning_configuration = config)
```

Une fois le processus de création terminé, vous pouvez effectuer une inférence/une évaluation sur un cluster AKS derrière un réseau virtuel. Pour plus d’informations, consultez [Guide pratique pour déployer sur AKS](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des environnements d’entraînement](how-to-set-up-training-targets.md)
* [Où déployer les modèles](how-to-deploy-and-where.md)
* [Déployer des modèles en toute sécurité avec SSL](how-to-secure-web-service.md)

