---
title: Utiliser un pare-feu
titleSuffix: Azure Machine Learning
description: Contrôler l’accès aux espaces de travail Azure Machine Learning avec les pare-feu Azure. En savoir plus sur les hôtes que vous devez autoriser via le pare-feu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 08/12/2021
ms.custom: devx-track-python
ms.openlocfilehash: 790b5a3e34d36d674511507bc5e9ed452c5ba74e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563019"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Utiliser l’espace de travail derrière un Pare-feu pour Azure Machine Learning

Dans cet article, découvrez comment configurer Pare-feu Azure pour contrôler l’accès à votre espace de travail Azure Machine Learning et à l’Internet public. Pour en savoir plus sur la sécurisation d’Azure Machine Learning, consultez [Sécurité de l’entreprise pour Azure Machine Learning](concept-enterprise-security.md).

> [!NOTE]
> Cet article s’applique à l’espace de travail Azure Machine Learning, que celui-ci utilise un point de terminaison privé ou un point de terminaison de service.

> [!TIP]
> Cet article fait partie d’une série sur la sécurisation d’un workflow Azure Machine Learning. Consultez les autres articles de cette série :
>
> * [Présentation du réseau virtuel](how-to-network-security-overview.md)
> * [Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
> * [Sécuriser l’environnement d’entraînement](how-to-secure-training-vnet.md)
> * [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
> * [Activer les fonctionnalités de Studio](how-to-enable-studio-virtual-network.md)
> * [Utiliser le DNS personnalisé](how-to-custom-dns.md)

## <a name="required-public-internet-access"></a>Accès Internet public obligatoire

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

## <a name="azure-firewall"></a>Pare-feu Azure

> [!IMPORTANT]
> Le Pare-feu Azure permet de sécuriser les _ressources du réseau virtuel Azure_. Certains services Azure, tels que les comptes Stockage Azure, présentent des paramètres de pare-feu qui leur sont propres et _s’appliquent au point de terminaison public de cette instance de service spécifique_. Les informations contenues dans ce document sont spécifiques au Pare-feu Azure.
> 
> Pour plus d’informations sur les paramètres de pare-feu d’instance de service, consultez [Utiliser Studio dans un réseau virtuel](how-to-enable-studio-virtual-network.md#firewall-settings).

* Pour le trafic __entrant__ vers le cluster de calcul Azure Machine Learning et l’instance de calcul, utilisez des [itinéraires définis par l’utilisateur (UDR)](../virtual-network/virtual-networks-udr-overview.md) afin d’éviter le pare-feu.

* Pour le trafic __sortant__, créez des règles de type __réseau__ et __application__. 

Ces regroupements de règles sont décrits plus en détail dans [Quels sont les concepts de Pare-feu Azure ?](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts).

### <a name="inbound-configuration"></a>Configuration entrante

[!INCLUDE [udr info for computes](../../includes/machine-learning-compute-user-defined-routes.md)]

### <a name="outbound-configuration"></a>Configuration sortante

1. Ajoutez des __règles de réseau__, permettant le trafic __vers__ et __depuis__ les étiquettes de service suivantes :

    | Étiquette de service | Protocol | Port |
    | ----- |:-----:|:-----:|
    | AzureActiveDirectory | TCP | * |
    | AzureMachineLearning | TCP | 443 |
    | AzureResourceManager | TCP | 443 |
    | Storage.region       | TCP | 443 |
    | AzureFrontDoor.FrontEnd</br>* Non requis dans Azure Chine. | TCP | 443 | 
    | ContainerRegistry.region  | TCP | 443 |
    | MicrosoftContainerRegistry.region | TCP | 443 |

    > [!TIP]
    > * ContainerRegistry.Region est nécessaire uniquement pour les images Docker personnalisées. Cela comprend des modifications mineures (par exemple, des packages supplémentaires) pour les images de base fournies par Microsoft.
    > * MicrosoftContainerRegistry.region est requis uniquement si vous prévoyez d’utiliser les _images Docker par défaut fournies par Microsoft_ et que vous _activez les dépendances gérées par l’utilisateur_.
    > * Pour les entrées contenant `region`, remplacez cette valeur par la région Azure que vous utilisez. Par exemple : `ContainerRegistry.westus`.

1. Ajoutez des __règles d’application__ pour les hôtes suivants :

    > [!NOTE]
    > Il ne s’agit pas d’une liste complète des ordinateurs hôtes requis pour toutes les ressources Python sur Internet, uniquement celles les plus couramment utilisées. Par exemple, si vous avez besoin d’accéder à un référentiel GitHub ou à un autre hôte, vous devez identifier et ajouter les ordinateurs hôtes requis pour ce scénario.

    | **Nom d’hôte** | **Objectif** |
    | ---- | ---- |
    | **graph.windows.net** | Utilisé par l’instance ou le cluster de calcul Azure Machine Learning. |
    | **anaconda.com**</br>**\*.anaconda.com** | Utilisé pour installer les packages par défaut. |
    | **\*.anaconda.org** | Utilisé pour récupérer les données des dépôts. |
    | **pypi.org** | Utilisé pour lister les dépendances de l’index par défaut, le cas échéant, et si l’index n’a pas été remplacé par les paramètres utilisateur. Si l’index a été remplacé, vous devez également autoriser **\*.pythonhosted.org**. |
    | **cloud.r-project.org** | Utilisé lors de l’installation des packages CRAN pour le développement R. |
    | **\*pytorch.org** | Utilisé par certains exemples basés sur PyTorch. |
    | **\*.tensorflow.org** | Utilisé par certains exemples basés sur Tensorflow. |
    | **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | Utilisé pour récupérer les bits du serveur VS Code qui sont installés sur l’instance de calcul par le biais d’un script d’installation.|
    | **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | Utilisé pour récupérer les bits du serveur websocket qui sont installés sur l’instance de calcul. Le serveur websocket est utilisé pour transmettre les requêtes du client Visual Studio Code (application de bureau) au serveur Visual Studio Code s’exécutant sur l’instance de calcul.|
    

    Pour __Protocol:Port__, sélectionnez __http, https__.

    Pour plus d’informations sur la configuration des règles d’application, consultez [Déployer et configurer le pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

1. Pour limiter le trafic sortant pour les modèles déployés sur Azure Kubernetes Service (AKS), consultez les articles [Restreindre le trafic sortant dans Azure Kubernetes Service](../aks/limit-egress-traffic.md) et [Déployer des modèles Machine Learning dans Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity).

### <a name="diagnostics-for-support"></a>Diagnostics pour la prise en charge

S’il vous faut collecter des informations de diagnostic dans le cadre du support Microsoft, procédez comme suit :

1. Ajoutez une __règle de réseau__ pour autoriser le trafic vers et depuis la balise `AzureMonitor`.
1. Ajoutez des __règles d’application__ pour les hôtes suivants. Sélectionnez __http, https__ pour __Protocol:Port__ pour ces hôtes :

    + **dc.applicationinsights.azure.com**
    + **dc.applicationinsights.microsoft.com**
    + **dc.services.visualstudio.com**

    Pour obtenir la liste des adresses IP des hôtes Azure Monitor, consultez [Adresses IP utilisées par Azure Monitor](../azure-monitor/app/ip-addresses.md).
## <a name="other-firewalls"></a>Autres pare-feu

Les instructions de cette section sont génériques, car chaque pare-feu dispose de sa propre terminologie et de configurations spécifiques. Si vous avez des questions, consultez la documentation du pare-feu que vous utilisez.

S’il n’est pas configuré correctement, le pare-feu peut causer des problèmes lors de l’utilisation de votre espace de travail. Des noms d’hôtes différents sont utilisés à la fois par l’espace de travail Azure Machine Learning. Les sections suivantes répertorient les hôtes requis pour Azure Machine Learning.

### <a name="microsoft-hosts"></a>Hôtes Microsoft

Les hôtes des tableaux suivants sont détenus par Microsoft et fournissent les services nécessaires au bon fonctionnement de votre espace de travail. Les tableaux suivants répertorient les hôtes des régions Azure (public), Azure Government et Azure China 21Vianet.

**Hôtes Azure généraux**

| **Obligatoire pour** | **Azure public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | .chinacloudapi.cn |
| Portail Azure | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Hôtes Azure Machine Learning**

> [!IMPORTANT]
> Dans le tableau suivant, remplacez `<storage>` par le nom du compte de stockage par défaut de votre espace de travail Azure Machine Learning.

| **Obligatoire pour** | **Azure public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*.azureml.ms | \*.ml.azure.us | \*.ml.azure.cn |
| Notebook intégré | \*.notebooks.azure.net | \*.notebooks.usgovcloudapi.net |\*.notebooks.chinacloudapi.cn |
| Notebook intégré | \<storage\>.file.core.windows.net | \<storage\>.file.core.usgovcloudapi.net | \<storage\>.file.core.chinacloudapi.cn |
| Notebook intégré | \<storage\>.dfs.core.windows.net | \<storage\>.dfs.core.usgovcloudapi.net | \<storage\>.dfs.core.chinacloudapi.cn |
| Notebook intégré | \<storage\>.blob.core.windows.net | \<storage\>.blob.core.usgovcloudapi.net | \<storage\>.blob.core.chinacloudapi.cn |
| Notebook intégré | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Notebook intégré | \*.aznbcontent.net |  | |

**Hôtes d’instance de calcul et de cluster de calcul Azure Machine Learning**

| **Obligatoire pour** | **Azure public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Cluster/instance de calcul | \*.batchai.core.windows.net | \*.batchai.core.usgovcloudapi.net |\*.batchai.ml.azure.cn |
| Cluster/instance de calcul | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| Instance de calcul | \*.instances.azureml.net | \*.instances.azureml.us | \*.instances.azureml.cn |
| Instance de calcul | \*.instances.azureml.ms |  |  |

> [!IMPORTANT]
> Votre pare-feu doit autoriser la communication avec \*.instances.azureml.ms sur les ports __TCP__ __18881, 443 et 8787__.

**Ressources associées utilisées par Azure Machine Learning**

| **Obligatoire pour** | **Azure public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Compte Stockage Azure | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure Container Registry | azurecr.io | azurecr.us | azurecr.cn |
| Registre de conteneurs Microsoft | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |
| Images prédéfinies Azure Machine Learning | viennaglobal.azurecr.io | viennaglobal.azurecr.io | viennaglobal.azurecr.io |

> [!TIP]
> * __Azure Container Registry__ est requis pour toute image Docker personnalisée. Cela comprend des modifications mineures (par exemple, des packages supplémentaires) pour les images de base fournies par Microsoft.
> * __Microsoft Container Registry__ est requis uniquement si vous prévoyez d’utiliser les _images Docker par défaut fournies par Microsoft_ et que vous _activez les dépendances gérées par l’utilisateur_.
> * Si vous envisagez d’utiliser une identité fédérée, suivez les instructions de l’article [Bonnes pratiques pour sécuriser les services de fédération Active Directory (AD FS)](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs).

En outre, utilisez les informations de la section [configuration entrante](#inbound-configuration) afin d’ajouter des adresses IP pour `BatchNodeManagement` et `AzureMachineLearning`.

Pour plus d’informations sur la restriction de l’accès aux modèles déployés sur AKS, consultez [Limiter le trafic de sortie dans Azure Kubernetes Service (AKS)](../aks/limit-egress-traffic.md).

> [!TIP]
> Si vous collaborez avec le Support Microsoft pour collecter des informations de diagnostic, vous devez autoriser le trafic sortant vers les adresses IP utilisées par les hôtes Azure Monitor. Pour obtenir la liste des adresses IP des hôtes Azure Monitor, consultez [Adresses IP utilisées par Azure Monitor](../azure-monitor/app/ip-addresses.md).

### <a name="python-hosts"></a>Hôtes Python

Les hôtes de cette section permettent d’installer des packages Python et sont requis lors du développement, de la formation et du déploiement. 

> [!NOTE]
> Il ne s’agit pas d’une liste complète des ordinateurs hôtes requis pour toutes les ressources Python sur Internet, uniquement celles les plus couramment utilisées. Par exemple, si vous avez besoin d’accéder à un référentiel GitHub ou à un autre hôte, vous devez identifier et ajouter les ordinateurs hôtes requis pour ce scénario.

| **Nom d’hôte** | **Objectif** |
| ---- | ---- |
| **anaconda.com**</br>**\*.anaconda.com** | Utilisé pour installer les packages par défaut. |
| **\*.anaconda.org** | Utilisé pour récupérer les données des dépôts. |
| **pypi.org** | Utilisé pour lister les dépendances de l’index par défaut, le cas échéant, et si l’index n’a pas été remplacé par les paramètres utilisateur. Si l’index a été remplacé, vous devez également autoriser **\*.pythonhosted.org**. |
| **\*pytorch.org** | Utilisé par certains exemples basés sur PyTorch. |
| **\*.tensorflow.org** | Utilisé par certains exemples basés sur Tensorflow. |

### <a name="r-hosts"></a>Hôtes R

Les hôtes de cette section permettent d’installer des packages R et sont requis lors du développement, de la formation et du déploiement.

> [!NOTE]
> Il ne s’agit pas d’une liste complète des ordinateurs hôtes requis pour toutes les ressources R sur Internet, uniquement celles les plus couramment utilisées. Par exemple, si vous avez besoin d’accéder à un référentiel GitHub ou à un autre hôte, vous devez identifier et ajouter les ordinateurs hôtes requis pour ce scénario.

| **Nom d’hôte** | **Objectif** |
| ---- | ---- |
| **cloud.r-project.org** | Utilisé lors de l’installation des packages CRAN. |

### <a name="azure-kubernetes-services-hosts"></a>Hôtes Azure Kubernetes Services

Pour plus d’informations sur les hôtes avec lesquels AKS doit communiquer, consultez les articles [Restreindre le trafic sortant dans Azure Kubernetes Service](../aks/limit-egress-traffic.md) et [Déployer des modèles Machine Learning dans Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity).

### <a name="visual-studio-code-hosts"></a>Hôtes Visual Studio Code

Les hôtes de cette section permettent d’installer les packages Visual Studio Code afin d’établir une connexion à distance entre Visual Studio Code et les instances de calcul dans votre espace de travail Azure Machine Learning.

> [!NOTE]
> Il ne s’agit pas d’une liste complète des ordinateurs hôtes requis pour toutes les ressources Visual Studio Code sur Internet, uniquement pour celles les plus couramment utilisées. Par exemple, si vous avez besoin d’accéder à un référentiel GitHub ou à un autre hôte, vous devez identifier et ajouter les ordinateurs hôtes requis pour ce scénario.

| **Nom d’hôte** | **Objectif** |
| ---- | ---- |
|  **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | Utilisé pour récupérer les bits du serveur VS Code qui sont installés sur l’instance de calcul par le biais d’un script d’installation.|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** |Utilisé pour récupérer les bits du serveur websocket qui sont installés sur l’instance de calcul. Le serveur websocket est utilisé pour transmettre les requêtes du client Visual Studio Code (application de bureau) au serveur Visual Studio Code s’exécutant sur l’instance de calcul. |

## <a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série sur la sécurisation d’un workflow Azure Machine Learning. Consultez les autres articles de cette série :

* [Présentation du réseau virtuel](how-to-network-security-overview.md)
* [Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
* [Sécuriser l’environnement d’entraînement](how-to-secure-training-vnet.md)
* [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
* [Activer les fonctionnalités de Studio](how-to-enable-studio-virtual-network.md)
* [Utiliser le DNS personnalisé](how-to-custom-dns.md)

Pour plus d’informations sur la configuration du Pare-feu Azure, consultez [Didacticiel : Déployer et configurer le Pare-feu Azure à l’aide du portail Azure](../firewall/tutorial-firewall-deploy-portal.md).
