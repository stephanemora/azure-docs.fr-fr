---
title: Exécuter des expériences et une inférence dans un réseau virtuel
titleSuffix: Azure Machine Learning service
description: Exécutez de manière sécurisée des expériences et une inférence de machine learning à l’intérieur d’un réseau virtuel Azure. Découvrez comment créer des cibles de calcul pour l'entraînement des modèles et exécuter une inférence dans un réseau virtuel Azure. Il aborde également les exigences liées aux réseaux virtuels sécurisés, telles que les ports entrants et sortants nécessaires.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: 21c4a9042a64a58f67f1f94c300b6438895eea5d
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856279"
---
# <a name="securely-run-experiments-and-inferencing-inside-an-azure-virtual-network"></a>Exécuter de manière sécurisée des expériences et une inférence à l’intérieur d’un réseau virtuel Azure

Dans cet article, vous allez apprendre à exécuter vos expériences et inférence à l’intérieur d’un réseau virtuel. Un réseau virtuel agit en tant que limite de sécurité, isolant vos ressources Azure de l’Internet public. Vous pouvez également joindre un réseau virtuel Azure à votre réseau local. Cela vous permet d’entraîner vos modèles et d’accéder à vos modèles déployés à des fins d’inférence de façon sécurisée.

Le service Azure Machine Learning s’appuie sur d’autres services Azure pour des choses telles que les ressources de calcul. Les ressources de calcul (cibles de calcul) sont utilisées pour entraîner et déployer des modèles. Ces cibles de calcul peuvent être créées à l’intérieur d’un réseau virtuel. Par exemple, vous pouvez utiliser une machine virtuelle DSVM pour entraîner un modèle, puis déployer le modèle sur Azure Kubernetes Service. Pour plus d’informations sur les réseaux virtuels, consultez le document [Présentation des réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="storage-account-for-your-workspace"></a>Compte de stockage pour votre espace de travail

Quand vous créez un espace de travail de service Azure Machine Learning, il nécessite un compte de stockage Azure. N’activez pas de règles de pare-feu pour ce compte de stockage. Le service Azure Machine Learning requiert un accès illimité au compte de stockage.

Si vous ne savez pas si vous avez modifié ces paramètres, consultez la section __Changer la règle d’accès réseau par défaut__ du document [Configurer des pare-feux et des réseaux virtuels dans Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security) et suivez les étapes permettant d’_autoriser l’accès_ à partir de __Tous les réseaux__.

## <a name="use-machine-learning-compute"></a>Utiliser Capacité de calcul Machine Learning

Pour utiliser Capacité de calcul Machine Learning dans un réseau virtuel, utilisez les informations suivantes afin de comprendre les exigences réseau :

- Le réseau virtuel doit être dans les mêmes abonnement et région que l’espace de travail de service Azure Machine Learning.

- Le sous-réseau spécifié pour le cluster Capacité de calcul doit avoir suffisamment d’adresses IP non attribuées pour contenir le nombre de machines virtuelles ciblées pour le cluster. Si le sous-réseau n’a pas suffisamment d’adresses IP non attribuées, le cluster est alloué partiellement.

- Si vous souhaitez sécuriser le réseau virtuel en limitant le trafic, vous devez laisser certains ports ouverts pour le service Capacité de calcul. Pour plus d’informations, voir la section [Ports requis](#mlcports).

- Vérifiez si vos stratégies ou verrous de sécurité sur l’abonnement ou le groupe de ressources du réseau virtuel restreignent les autorisations pour gérer le réseau virtuel.

- Si vous vous apprêtez à placer plusieurs clusters Capacité de calcul dans un réseau virtuel, vous devrez peut-être demander une augmentation du quota pour une ou plusieurs des ressources.

    Capacité de calcul alloue automatiquement des ressources de mise en réseau supplémentaires dans le groupe de ressources contenant le réseau virtuel. Pour chaque cluster Capacité de calcul, le service Azure Machine Learning alloue les ressources suivantes : 

    - Un groupe de sécurité réseau (NSG)

    - Une seule adresse IP publique

    - Un seul équilibreur de charge

    Ces ressources sont limitées par les [quotas de ressources](https://docs.microsoft.com/azure/azure-subscription-service-limits) de l’abonnement. 

### <a id="mlcports"></a> Ports requis

Capacité de calcul utilise le service Azure Batch pour provisionner les machines virtuelles dans le réseau virtuel spécifié. Le sous-réseau doit autoriser les communications entrantes à partir du service Batch. Ces communications servent à planifier les exécutions sur les nœuds Capacité de calcul et à communiquer avec Stockage Azure et d’autres ressources. Azure Batch ajoute des NSG au niveau des cartes réseau attachées aux machines virtuelles. Ces groupes de sécurité réseau configurent automatiquement des règles de trafic entrant et sortant pour autoriser le trafic suivant :

- Le trafic TCP entrant sur les ports 29876 et 29877 à partir d’adresses IP du rôle de service Batch. 
 
- Le trafic TCP entrant sur le port 22 pour autoriser l’accès à distance.
 
- Le trafic sortant sur n’importe quel port vers le réseau virtuel.

- Le trafic sortant sur n’importe quel port vers internet.

Soyez prudent si vous modifiez ou ajoutez des règles de trafic entrant/sortant dans des groupes de sécurité réseau configurés par Batch. Si un NSG bloque la communication vers les nœuds de calcul, le service Capacité de calcul définit l’état de ces nœuds sur « inutilisable ».

Vous n’avez pas besoin spécifier de groupes de sécurité réseau au niveau du sous-réseau, car Batch configure ses propres groupes de sécurité réseau. Cependant, si le sous-réseau spécifié comporte des groupes de sécurité réseau associés et/ou un pare-feu, configurez les règles de sécurité du trafic entrant et sortant comme indiqué plus haut. Les captures d’écran suivantes montrent à quoi ressemble la configuration des règles dans le portail Azure :

![Capture d’écran des règles NSG de trafic entrant pour Capacité de calcul](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Capture d’écran des règles NSG de trafic sortant pour Capacité de calcul](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Créer un cluster Capacité de calcul dans un réseau virtuel

Pour créer un cluster Capacité de calcul avec le **portail Azure**, effectuez les étapes suivantes :

1. À partir du [portail Azure](https://portal.azure.com), sélectionnez votre espace de travail de service Azure Machine Learning.

1. À partir de la section __Application__, sélectionnez __Capacité de calcul__. Ensuite, sélectionnez __Ajouter une capacité de calcul__. 

    ![Comment ajouter une capacité de calcul dans le service Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Pour configurer cette ressource de calcul afin d’utiliser un réseau virtuel, utilisez ces options :

    - __Configuration réseau__ : Sélectionnez __Advanced (Avancé)__.

    - __Groupe de ressources__ : sélectionnez le groupe de ressources qui contient le réseau virtuel.

    - __Réseau virtuel__ : sélectionnez le réseau virtuel qui contient le sous-réseau.

    - __Sous-réseau__ : sélectionnez le sous-réseau à utiliser.

    ![Capture d’écran montrant les paramètres de réseau virtuel pour la capacité de calcul Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Vous pouvez également créer un cluster Capacité de calcul à l’aide du **SDK Azure Machine Learning**. Le code suivant crée un cluster Capacité de calcul dans le sous-réseau `default` d’un réseau virtuel nommé `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure Virtual Network name, subnet, and resource group
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

Une fois le processus de création terminé, vous pouvez entraîner votre modèle à l’aide du cluster. Pour plus d’informations, consultez le document [Sélectionner et utiliser une cible de calcul pour l’entraînement](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight"></a>Utiliser une machine virtuelle ou HDInsight

Pour utiliser une machine virtuelle ou un cluster HDInsight dans un réseau virtuel avec votre espace de travail, effectuez les étapes suivantes :

> [!IMPORTANT]
> Le service Azure Machine Learning prend uniquement en charge les machines virtuelles exécutant Ubuntu.

1. Créez une machine virtuelle ou un cluster HDInsight, par exemple à l’aide du portail Azure ou de l’interface de ligne de commande Azure, et placez-le dans un réseau virtuel Azure. Pour plus d’informations, consultez les documents suivants :
    * [Créer et gérer des réseaux virtuels Azure pour des machines virtuelles Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Étendre HDInsight à l’aide de réseaux virtuels Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Pour autoriser le service Azure Machine Learning à communiquer avec le port SSH sur la machine virtuelle ou le cluster, vous devez configurer une entrée source pour le groupe de sécurité réseau. Le port SSH est généralement le port 22. Pour autoriser le trafic à partir de cette source, utilisez les informations suivantes :

    * __Source__ : Sélectionnez __Service Tag__ (Identification)

    * __Balise du service source__ : Sélectionnez __AzureMachineLearning__.

    * __Plages de ports source__ : sélectionnez __*__

    * __Destination__ : sélectionnez __N’importe laquelle__.

    * __Plages de ports de destination__ : Sélectionnez __22__.

    * __Protocole__ : sélectionnez __N’importe laquelle__.

    * __Action__ : sélectionnez __Autoriser__.

   ![Capture d’écran de règles de trafic entrant pour effectuer des expériences sur la machine virtuelle ou le cluster HDInsight à l’intérieur d’un réseau virtuel](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Conservez les règles de trafic sortant par défaut pour le groupe de sécurité réseau. Pour plus d’informations, consultez la section Règles de sécurité par défaut de la documentation [Groupes de sécurité](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
    
1. Attachez la machine virtuelle ou le cluster HDInsight à votre espace de travail de service Azure Machine Learning. Pour plus d’informations, consultez le document [Configurer des cibles de calcul pour l’entraînement des modèles](how-to-set-up-training-targets.md).

## <a name="use-azure-kubernetes-service-aks"></a>Utiliser Azure Kubernetes Service (AKS)

> [!IMPORTANT]
> Vérifiez les prérequis et planifiez l’adressage IP pour votre cluster avant d’effectuer les étapes indiquées ci-après. Vous pouvez consulter [Configurer la mise en réseau avancée dans AKS (Azure Kubernetes Service)](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
> 
> Conservez les règles de trafic sortant par défaut pour le groupe de sécurité réseau. Pour plus d’informations, consultez la section Règles de sécurité par défaut de la documentation [Groupes de sécurité](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
>
> Azure Kubernetes Service et le réseau virtuel Azure doivent être dans la même région.

Pour ajouter Azure Kubernetes Service dans un réseau virtuel à votre espace de travail, effectuez les étapes suivantes à partir du __portail Azure__ :

1. À partir du [portail Azure](https://portal.azure.com), sélectionnez votre espace de travail de service Azure Machine Learning.

1. À partir de la section __Application__, sélectionnez __Capacité de calcul__. Ensuite, sélectionnez __Ajouter une capacité de calcul__. 

    ![Comment ajouter une capacité de calcul dans le service Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Pour configurer cette ressource de calcul afin d’utiliser un réseau virtuel, utilisez ces options :

    - __Configuration réseau__ : Sélectionnez __Advanced (Avancé)__.

    - __Groupe de ressources__ : sélectionnez le groupe de ressources qui contient le réseau virtuel.

    - __Réseau virtuel__ : sélectionnez le réseau virtuel qui contient le sous-réseau.

    - __Sous-réseau__ : Sélectionnez le sous-réseau.

    - __Plage d’adresses du service Kubernetes__ : sélectionnez la plage d’adresses du service Kubernetes. Cette plage, qui définit les adresses IP disponibles pour le cluster, correspond à une plage d’adresses IP exprimée selon la notation CIDR. Elle ne doit empiéter sur aucune plage d’adresses IP de sous-réseau. Par exemple :  10.0.0.0/16.

    - __Adresse IP du service DNS Kubernetes__ : sélectionnez l’Adresse IP du service DNS Kubernetes. Cette adresse IP est affectée au service DNS Kubernetes. Elle doit se situer dans la plage d’adresses du service Kubernetes. Par exemple :  10.0.0.10.

    - __Adresse du pont Docker__ : sélectionnez l’adresse du pont Docker. Cette adresse IP est affectée au pont Docker. Elle ne doit appartenir à aucune plage d’adresses IP de sous-réseau, ni à la plage d’adresses du service Kubernetes. Par exemple :  172.17.0.1/16

   ![Azure Machine Learning service : paramètres de réseau virtuel Capacité de calcul Machine Learning](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

    > [!TIP]
    > Si vous avez déjà un cluster AKS dans un réseau virtuel, vous pouvez l’attacher à l’espace de travail. Pour plus d’informations, consultez [Guide pratique pour déployer sur AKS](how-to-deploy-to-aks.md).

Vous pouvez également utiliser le **SDK Azure Machine Learning** pour ajouter le service Azure Kubernetes dans un réseau virtuel. Le code suivant crée un service AKS dans le sous-réseau `default` d’un réseau virtuel nommé `mynetwork` :

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

Une fois le processus de création terminé, vous pouvez effectuer une inférence sur un cluster AKS derrière un réseau virtuel. Pour plus d’informations, consultez [Guide pratique pour déployer sur AKS](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des environnements d’entraînement](how-to-set-up-training-targets.md)
* [Où déployer les modèles](how-to-deploy-and-where.md)
* [Sécuriser des modèles déployés avec SSL](how-to-secure-web-service.md)
