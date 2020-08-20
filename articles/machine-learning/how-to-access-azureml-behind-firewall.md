---
title: Utiliser un pare-feu
titleSuffix: Azure Machine Learning
description: Contrôler l’accès aux espaces de travail Azure Machine Learning avec les pare-feu Azure. Apprenez-en davantage sur les hôtes que vous devez autoriser à traverser le pare-feu pour qu’Azure Machine Learning fonctionne correctement.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 990a2d5279c796f354055328e6968ea705ea10b2
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87873634"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Utiliser l’espace de travail derrière un Pare-feu pour Azure Machine Learning

Dans cet article, découvrez comment configurer Pare-feu Azure pour contrôler l’accès à votre espace de travail Azure Machine Learning et à l’Internet public.   Pour en savoir plus sur la sécurisation d’Azure Machine Learning, consultez [Sécurité de l’entreprise pour Azure Machine Learning](concept-enterprise-security.md)

Même si les informations contenues dans ce document sont basées sur l’utilisation de [Pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md), elles devraient également s’appliquer à d’autres produits de pare-feu. Si vous avez des questions sur la façon d’autoriser la communication via votre pare-feu, consultez la documentation du pare-feu que vous utilisez.

## <a name="network-rules"></a>Règles de réseau

Sur votre pare-feu, créez une règle de réseau autorisant le trafic vers et depuis les adresses mentionnées dans cet article.

> [!TIP]
> Lors de l’ajout de la règle de réseau, définissez __Protocole__ sur un protocole quelconque et les ports sur `*`.
>
> Pour plus d’informations sur la configuration du pare-feu Azure, consultez [Déployer et configurer le pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

## <a name="microsoft-hosts"></a>Hôtes Microsoft

S’il n’est pas configuré correctement, le pare-feu peut causer des problèmes lors de l’utilisation de votre espace de travail. Un grand nombre de noms d’hôtes sont utilisés à la fois par l’espace de travail Azure Machine Learning.

Les hôtes de cette section sont détenus par Microsoft et fournissent les services nécessaires au bon fonctionnement de votre espace de travail.

| **Nom d’hôte** | **Objectif** |
| ---- | ---- |
| **\*.batchai.core.windows.net** | Clusters d’entraînement |
| **ml.azure.com** | Azure Machine Learning Studio |
| **default.exp-tas.com** | Utilisé par Azure Machine Learning Studio |
| **\*.azureml.ms** | Utilisé par les API Azure Machine Learning |
| **\*.experiments.azureml.net** | Utilisé par les expériences exécutées dans Azure Machine Learning |
| **\*.modelmanagement.azureml.net** | Utilisé pour inscrire et déployer les modèles|
| **mlworkspace.azure.ai** | Utilisé par le portail Azure lors de l’affichage d’un espace de travail |
| **\*.aether.ms** | Utilisé lors de l’exécution des pipelines Azure Machine Learning |
| **\*.instances.azureml.net** | Instances de calcul Azure Machine Learning |
| **\*.instances.azureml.ms** | Azure Machine Learning calcule des instances lorsque l’espace de travail a un lien privé activé |
| **windows.net** | Stockage Blob Azure |
| **vault.azure.net** | Azure Key Vault |
| **azurecr.io** | Azure Container Registry |
| **mcr.microsoft.com** | Registre de conteneurs Microsoft Container Registry pour les images Docker de base |
| **your-acr-server-name.azurecr.io** | Nécessaire uniquement si votre Azure Container Registry se trouve derrière le réseau virtuel. Dans cette configuration, un lien privé est créé à partir de l’environnement Microsoft vers l’instance ACR dans votre abonnement. Utilisez le nom du serveur ACR pour votre espace de travail Azure Machine Learning. |
| **\*.notebooks.azure.net** | Requis par les notebooks dans Azure Machine Learning Studio. |

## <a name="python-hosts"></a>Hôtes Python

Les hôtes de cette section sont utilisés pour installer les packages Python. Ils sont requis lors du développement, de l’entraînement et du déploiement. 

| **Nom d’hôte** | **Objectif** |
| ---- | ---- |
| **anaconda.com** | Utilisé pour installer les packages par défaut. |
| **\*.anaconda.org** | Utilisé pour récupérer les données des dépôts. |
| **pypi.org** | Utilisé pour lister les dépendances de l’index par défaut, le cas échéant, et si l’index n’a pas été remplacé par les paramètres utilisateur. Si l’index a été remplacé, vous devez également autoriser **\*.pythonhosted.org**. |

## <a name="r-hosts"></a>Hôtes R

Les hôtes de cette section sont utilisés pour installer les packages R. Ils sont requis lors du développement, de l’entraînement et du déploiement.

> [!IMPORTANT]
> En interne, le kit SDK R pour Azure Machine Learning utilise des packages Python. Par conséquent, vous devez également autoriser les hôtes Python à traverser le pare-feu.

| **Nom d’hôte** | **Objectif** |
| ---- | ---- |
| **cloud.r-project.org** | Utilisé lors de l’installation des packages CRAN. |

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Déployer et configurer un pare-feu Azure à l’aide du portail Azure](../firewall/tutorial-firewall-deploy-portal.md)
* [Sécuriser l’expérimentation Azure Machine Learning et les travaux d’inférence au sein d’un réseau virtuel Azure](how-to-enable-virtual-network.md)
