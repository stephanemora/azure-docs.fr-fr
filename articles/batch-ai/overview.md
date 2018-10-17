---
title: Service Azure Batch AI - Apprentissage de l’intelligence artificielle | Microsoft Docs
description: En savoir plus sur l’utilisation du service géré Azure Batch AI pour effectuer l’apprentissage de l’intelligence artificielle (IA) et d’autres modèles de Machine Learning sur des clusters de GPU et d’UC.
services: batch-ai
documentationcenter: ''
author: alexsuttonms
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: 98497812e75d07fc153e0e351331c05484164fdd
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052697"
---
# <a name="what-is-azure-batch-ai"></a>Présentation d’Azure Batch AI

Azure Batch AI est un service géré aidant les chercheurs de données et les chercheurs en IA à former et tester des modèles de machine Learning et IA à grande échelle dans Azure sans avoir à gérer la complexité d’une infrastructure. Décrivez les ressources de calcul, les tâches que vous souhaitez exécuter, l’emplacement où stocker les entrées et les sorties du modèle, et Batch AI s’occupe du reste.

Vous pouvez utiliser Batch AI de façon autonome ou pour effectuer l’apprentissage du modèle dans le cadre d’un flux de travail de développement plus large :

* Utilisez Batch AI seul pour effectuer l'apprentissage, tester et noter par lots des modèles Machine Learning et d’intelligence artificielle (IA) sur des clusters de [GPU](../virtual-machines/linux/sizes-gpu.md) ou d’unités centrales. 

* Ciblez un cluster Batch AI dans un flux de travail avec [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) ou d’autres [outils de la plateforme Azure AI](https://azure.microsoft.com/overview/ai-platform/). Azure ML offre une expérience riche pour la préparation des données, l’expérimentation et l’historique des travaux. Azure ML peut également empaqueter un modèle formé dans un conteneur, et déployer un modèle pour l’inférence ou sur des appareils IoT.  

## <a name="train-machine-learning-and-ai-models"></a>Effectuer l'apprentissage des modèles Machine Learning et AI

Utilisez Batch AI pour effectuer l’apprentissage de modèles Machine Learning et de réseaux neuronaux profonds (apprentissage profond) et autres approches d’intelligence artificielle. Batch AI intègre la prise en charge des infrastructures et bibliothèques d’intelligence artificielle open source populaires, notamment [TensorFlow](https://github.com/tensorflow/tensorflow), [PyTorch](https://github.com/pytorch/pytorch), [Chainer](https://github.com/chainer/chainer) et [Microsoft Cognitive Toolkit (CNTK)](https://github.com/Microsoft/CNTK).

Après avoir identifié votre problème et préparé vos données, travaillez de manière interactive avec Batch AI pour tester des idées de modèles. Lorsque vous êtes prêt pour des essais à grande échelle, démarrez des travaux sur plusieurs GPU avec MPI ou d’autres bibliothèques de communication, puis effectuez d’autres expériences en parallèle.

Batch AI vous aide à effectuer l'apprentissage de modèles à l’échelle de plusieurs façons. Par exemple :  

|  |  |
|---------|---------|
| **Distribuer l’apprentissage**<br/>![Apprentissage distribué](./media/overview/distributed-training.png)  | Montez en puissance l’apprentissage pour un seul travail sur plusieurs GPU connectés au réseau, afin d’effectuer l'apprentissage de réseaux de grande taille avec de grands volumes de données.|
| **Expérimenter**<br/>![Expérimentation](./media/overview/experimentation.png) | Augmentez la taille de l’apprentissage avec plusieurs tâches. Effectuez des balayages de paramètres afin de tester de nouvelles idées, ou réglez les hyperparamètres pour améliorer les performances et la précision. |
| **Exécuter en parallèle**![Exécution en parallèle](./media/overview/parallel-execution.png) | Effectuez l’apprentissage ou notez plusieurs modèles à la fois, avec une exécution en parallèle dans un parc de serveurs pour obtenir plus rapidement les tâches effectuées.|

Après avoir effectué l'apprentissage d’un modèle, utilisez Batch AI pour tester le modèle si cela ne faisait pas partie de votre script de formation, ou effectuez une notation par lots.

## <a name="how-it-works"></a>Fonctionnement

Utilisez les kits de développement logiciel (SDK) Batch AI, les scripts de ligne de commande ou le portail Azure pour gérer les ressources de calcul et planifier des travaux pour l’apprentissage et le test de l’intelligence artificielle : 

* **Approvisionner et mettre à l’échelle des clusters de machines virtuelles** - choisissez le nombre de nœuds (machines virtuelles) et sélectionnez une machine virtuelle compatible GPU ou une autre taille de machine virtuelle qui répond à vos besoins d’apprentissage. Augmentez ou diminuez le nombre de nœuds automatiquement ou manuellement afin d’utiliser uniquement les ressources nécessaires. 

* **Gérer les dépendances et les conteneurs** - par défaut, les clusters Batch AI exécuter des images de machines virtuelles Linux avec des dépendances préinstallées pour exécuter des infrastructures d’apprentissage basées sur des conteneurs, sur GPU ou unités centrales. Pour une configuration supplémentaire, utilisez des images personnalisées ou exécutez des scripts de démarrage.

* **Distribuer des données** - choisissez une ou plusieurs options de stockage pour gérer les données d’entrée, les scripts et la sortie du travail : Azure Files, stockage Blob Azure ou un serveur NFS managé. Batch AI prend également en charge des solutions de stockage personnalisées, y compris les systèmes de fichiers parallèles hautes performances. Montez des systèmes de fichiers de stockage sur les nœuds de cluster et les conteneurs de travaux en utilisant de simples fichiers de configuration.

* **Planifier des travaux** - envoyez des travaux vers une file d’attente axée sur les priorités pour partager des ressources de cluster et tirer parti des machines virtuelles à basse priorité ainsi que des instances réservées.

* **Gérer les échecs** : surveillez l’état du travail et redémarrez les travaux en cas de défaillance de la machine virtuelle pendant des travaux qui peuvent durer longtemps.

* **Collecter les résultats** - accédez facilement aux journaux de sortie, à Stdout, Stderr et aux modèles formés. Configurez vos travaux Batch AI pour envoyer (push) la sortie directement au le stockage monté.

Tel un service Azure, Batch AI prend en charge des outils courants tels que le contrôle d’accès en fonction du rôle (RBAC) et les réseaux virtuels Azure pour la sécurité.  

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [ressources Batch AI](resource-concepts.md) pour l’apprentissage d’un modèle Machine Learning ou AI.

* Prise en main de l’[apprentissage d’un exemple de modèle d’apprentissage profond](quickstart-tensorflow-training-cli.md) avec Batch AI.

* Consultez des exemples de [recettes d’apprentissage](https://github.com/Azure/BatchAI/blob/master/recipes) pour les infrastructures AI courantes.
