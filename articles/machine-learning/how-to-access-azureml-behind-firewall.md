---
title: Utiliser Azure Machine Learning derrière un pare-feu
titleSuffix: Azure Machine Learning
description: Utilisez Azure Machine Learning de façon sécurisée derrière le pare-feu Azure. Apprenez-en davantage sur les hôtes que vous devez autoriser à traverser le pare-feu pour qu’Azure Machine Learning fonctionne correctement.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/27/2020
ms.openlocfilehash: 2c5fe9d46ef408775cc4b99d4820a652a91683c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195756"
---
# <a name="use-azure-machine-learning-workspace-behind-azure-firewall"></a>Utiliser l’espace de travail Azure Machine Learning derrière le pare-feu Azure

Cet article contient des informations sur la configuration du pare-feu Azure pour l’utiliser avec Azure Machine Learning.

Le pare-feu Azure peut être utilisé pour contrôler l’accès à votre espace de travail Azure Machine Learning et à l’Internet public. S’il n’est pas configuré correctement, le pare-feu peut causer des problèmes lors de l’utilisation de votre espace de travail.

## <a name="network-rules"></a>Règles de réseau

Sur votre pare-feu, créez une règle de réseau autorisant le trafic vers et depuis les adresses mentionnées dans cet article.

> [!TIP]
> Lors de l’ajout de la règle de réseau, définissez __Protocole__ sur un protocole quelconque et les ports sur `*`.
>
> Pour plus d’informations sur la configuration du pare-feu Azure, consultez [Déployer et configurer le pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule).

## <a name="microsoft-hosts"></a>Hôtes Microsoft

Les hôtes de cette section sont détenus par Microsoft et fournissent les services nécessaires au bon fonctionnement de votre espace de travail.

| **Nom d’hôte** | **Objectif** |
| ---- | ---- |
| **\*.batchai.core.windows.net** | Clusters d’entraînement |
| **ml.azure.com** | Azure Machine Learning Studio |
| **\*.azureml.ms** | Utilisé par les API Azure Machine Learning |
| **\*.experiments.azureml.net** | Utilisé par les expériences exécutées dans Azure Machine Learning|
| **\*.modelmanagement.azureml.net** | Utilisé pour inscrire et déployer les modèles|
| **mlworkspace.azure.ai** | Utilisé par le portail Azure lors de l’affichage d’un espace de travail |
| **\*.aether.ms** | Utilisé lors de l’exécution des pipelines Azure Machine Learning |
| **\*.instances.azureml.net** | Instances de calcul Azure Machine Learning |
| **windows.net** | Stockage Blob Azure |
| **vault.azure.net** | Azure Key Vault |
| **microsoft.com** | Images Docker de base |
| **azurecr.io** | Azure Container Registry |

## <a name="python-hosts"></a>Hôtes Python

Les hôtes de cette section sont utilisés pour installer les packages Python. Ils sont requis lors du développement, de l’entraînement et du déploiement. 

| **Nom d’hôte** | **Objectif** |
| ---- | ---- |
| **anaconda.com** | Utilisé lors de l’installation des packages Conda |
| **pypi.org** | Utilisé lors de l’installation des packages pip |

## <a name="r-hosts"></a>Hôtes R

Les hôtes de cette section sont utilisés pour installer les packages R. Ils sont requis lors du développement, de l’entraînement et du déploiement.

> [!IMPORTANT]
> En interne, le kit SDK R pour Azure Machine Learning utilise des packages Python. Par conséquent, vous devez également autoriser les hôtes Python à traverser le pare-feu.

| **Nom d’hôte** | **Objectif** |
| ---- | ---- |
| **cloud.r-project.org** | Utilisé lors de l’installation des packages CRAN. |

Étapes suivantes

* [[Déployer et configurer le pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md)]
* [Sécuriser l’expérimentation Azure Machine Learning et les travaux d’inférence au sein d’un réseau virtuel Azure](how-to-enable-virtual-network.md)