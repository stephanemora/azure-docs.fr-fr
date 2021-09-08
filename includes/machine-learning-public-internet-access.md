---
title: Fichier Include
description: inclure fichier
author: lobrien
ms.service: machine-learning
services: machine-learning
ms.topic: include
ms.date: 07/21/2021
ms.author: larryfr
ms.custom: include file
ms.openlocfilehash: b0b46da23de802d9f26e53d7b3acc96f166e78d0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443386"
---
Azure Machine Learning nécessite un accès entrant et sortant à l’Internet public. Les tableaux suivants fournissent une vue d’ensemble de l’accès requis et de son rôle. Le __protocole__ pour l’ensemble des éléments est __TCP__. Pour les balises de service se terminant par `.region`, remplacez `region` par la région Azure qui contient votre espace de travail. Par exemple, `Storage.westus`:

| Sens | Ports | Balise du service | Objectif |
| ----- |:-----:| ----- | ----- |
| Entrant | 29876-29877 | BatchNodeManagement | Instance de calcul et de cluster de calcul Azure Machine Learning. |
| Trafic entrant | 44224 | AzureMachineLearning | Instance de calcul Azure Machine Learning. |
| Règle de trafic sortant | * | AzureActiveDirectory | Authentification Azure Active Directory. |
| Règle de trafic sortant | 443 | AzureMachineLearning | Azure Machine Learning. |
| Règle de trafic sortant | 443 | AzureResourceManager | Azure Resource Manager. |
| Règle de trafic sortant | 443 | Storage.region | Compte Azure Storage. |
| Règle de trafic sortant | 443 | AzureFrontDoor.FrontEnd</br>* Non requis dans Azure Chine. | Azure Front Door. | 
| Règle de trafic sortant | 443 | ContainerRegistry.region | Azure Container Registry. Nécessaire uniquement lorsque vous utilisez des images Docker personnalisées. Y compris les petites modifications (telles que les packages supplémentaires) pour les images de base fournies par Microsoft. |
| Règle de trafic sortant | 443 | MicrosoftContainerRegistry.region | Nécessaire uniquement si vous utilisez des images Docker fournies par Microsoft et activez les dépendances gérées par l’utilisateur. |

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