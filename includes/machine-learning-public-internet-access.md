---
title: Fichier include
description: Fichier include
author: lobrien
ms.service: machine-learning
services: machine-learning
ms.topic: include
ms.date: 08/27/2021
ms.author: larryfr
ms.custom: include file
ms.openlocfilehash: 18d6da8c9156a66a16be7590603ae71b85abb9a4
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123105497"
---
Azure Machine Learning nécessite un accès entrant et sortant à l’Internet public. Les tableaux suivants fournissent une vue d’ensemble de l’accès requis et de son rôle. Le __protocole__ pour l’ensemble des éléments est __TCP__. Pour les balises de service se terminant par `.region`, remplacez `region` par la région Azure qui contient votre espace de travail. Par exemple, `Storage.westus`:

| Sens | Ports | Balise du service | Objectif |
| ----- |:-----:| ----- | ----- |
| Trafic entrant | 29876-29877 | BatchNodeManagement | Créer, mettre à jour et supprimer des instances et clusters de calcul Azure Machine Learning. |
| Entrant | 44224 | AzureMachineLearning | Créer, mettre à jour et supprimer des instances de calcul Azure Machine Learning. |
| Règle de trafic sortant | * | AzureActiveDirectory | Authentification à l’aide d’Azure AD. |
| Règle de trafic sortant | 443 | AzureMachineLearning | Utilisation d’Azure Machine Learning Services. |
| Règle de trafic sortant | 443 | AzureResourceManager | Création de ressources Azure avec Azure Machine Learning. |
| Règle de trafic sortant | 443 | Storage.region | Accès aux données stockées dans le compte stockage Azure pour le service Azure Batch. |
| Règle de trafic sortant | 443 | AzureFrontDoor.FrontEnd</br>* Non requis dans Azure Chine. | Point d’entrée global pour [Azure Machine Learning studio](https://ml.azure.com). | 
| Règle de trafic sortant | 443 | ContainerRegistry.region | Accès aux images Docker fournies par Microsoft. |
| Règle de trafic sortant | 443 | MicrosoftContainerRegistry.region | Accès aux images Docker fournies par Microsoft. Configuration du routeur Azure Machine Learning pour Azure Kubernetes Service. |

> [!TIP]
> Si vous avez besoin des adresses IP au lieu des balises de service, utilisez l’une des options suivantes :
> * Téléchargez une liste à partir des [Plages d’adresses IP et Balises de service](https://www.microsoft.com/download/details.aspx?id=56519).
> * Utilisez la commande [az network list-service-tags](/cli/azure/network#az_network_list_service_tags) Azure CLI.
> * Utilisez la commande [Get-AzNetworkServiceTag](/powershell/module/az.network/get-aznetworkservicetag) Azure PowerShelI.
> 
> Les adresses IP peuvent périodiquement changer.

Vous devrez peut-être également autoriser le trafic __sortant__ vers des sites Visual Studio Code et non-Microsoft pour l’installation des packages requis par votre projet Machine Learning. Le tableau suivant répertorie les référentiels couramment utilisés pour l’apprentissage automatique :

| Nom de l’hôte | Objectif |
| ----- | ----- |
| **anaconda.com**</br>**\*.anaconda.com** | Utilisé pour installer les packages par défaut. |
| **\*.anaconda.org** | Utilisé pour récupérer les données des dépôts. |
| **pypi.org** | Utilisé pour lister les dépendances de l’index par défaut, le cas échéant, et si l’index n’a pas été remplacé par les paramètres utilisateur. Si l’index a été remplacé, vous devez également autoriser **\*.pythonhosted.org**. |
| **cloud.r-project.org** | Utilisé lors de l’installation des packages CRAN pour le développement R. |
| **\*pytorch.org** | Utilisé par certains exemples basés sur PyTorch. |
| **\*.tensorflow.org** | Utilisé par certains exemples basés sur Tensorflow. |
| **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | Utilisé pour récupérer les bits du serveur VS Code qui sont installés sur l’instance de calcul par le biais d’un script d’installation.|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | Utilisé pour récupérer les bits du serveur WebSocket qui sont installés sur l’instance de calcul. Le serveur websocket est utilisé pour transmettre les requêtes du client Visual Studio Code (application de bureau) au serveur Visual Studio Code s’exécutant sur l’instance de calcul.|

Lorsque vous utilisez Azure Kubernetes Service (AKS) avec Azure Machine Learning, le trafic suivant doit être autorisé vers le réseau virtuel AKS :

* Exigences générales en entrée/sortie pour AKS, comme décrit dans l’article [Restreindre le trafic sortant dans Azure Kubernetes Service](/azure/aks/limit-egress-traffic).
* __Sortant__ vers mcr.microsoft.com.
* Lors du déploiement d’un modèle sur un cluster AKS, suivez les instructions de l’article [Déployer des modèles ML dans Azure Kubernetes Service](/azure/machine-learning/how-to-deploy-azure-kubernetes-service#connectivity).