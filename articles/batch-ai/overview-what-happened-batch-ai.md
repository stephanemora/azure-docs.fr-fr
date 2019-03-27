---
title: Qu’arrive-t-il à Azure Batch AI ? | Microsoft Docs
description: Découvrez ce qui se passe pour Azure Batch AI et l’option de calcul Azure Machine Learning service.
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: edd6a7e5f385d766d51631d77f5889233af2469a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194500"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Qu’arrive-t-il à Azure Batch AI ?

**Le service Azure Batch AI sera mis hors service en mars.** Les fonctionnalités de formation et de notation à l’échelle de Batch AI sont désormais disponibles dans [Azure Machine Learning service](../machine-learning/service/overview-what-is-azure-ml.md), qui a été mis à la disposition générale le 4 décembre 2018.

Entre autres fonctionnalités de Machine Learning, Azure Machine Learning service inclut une cible de calcul gérée sur le cloud pour les modèles Machine Learning de formation, de déploiement et de notation. Cette cible de calcul est appelée [Capacité de calcul Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Commencez à migrer et à l’utiliser dès aujourd’hui](#migrate). Vous pouvez interagir avec Azure Machine Learning service via ses [Kits de développement logiciel (SDK) Python](../machine-learning/service/quickstart-create-workspace-with-python.md), son interface de ligne de commande et le [portail Azure](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Chronologie de prise en charge

À ce stade, vous pouvez utiliser vos abonnements Azure Batch AI existants comme auparavant. Cependant, aucun **nouvel abonnement** n’est possible et il n’est fait aucun nouvel investissement.

À compter du 31&nbsp; &nbsp;mars 2019, les abonnements Batch AI existants ne fonctionneront plus.

## <a name="compare-to-azure-machine-learning"></a>Comparaison avec Azure Machine Learning
Il s’agit d’un service cloud que vous utilisez pour entraîner, déployer, automatiser et gérer des modèles Machine Learning, le tout à l’échelle très vaste fournie par le cloud. Obtenez une compréhension générale du [service Azure Machine Learning dans cette vue d’ensemble](../machine-learning/service/overview-what-is-azure-ml.md).
 

Un cycle de vie de développement de modèle classique implique la préparation des données, l’entraînement et l’expérimentation, et une phase de déploiement. Ce cycle de bout en bout peut être orchestré avec des pipelines Machine Learning.

![Diagramme de flux](./media/overview-what-happened-batch-ai/lifecycle.png)


[Découvrez plus d’informations sur le fonctionnement du service et sur ses principaux concepts](../machine-learning/service/concept-azure-machine-learning-architecture.md). Beaucoup des concepts du workflow d’entraînement des modèles sont similaires aux concepts existants dans Batch AI. 

Plus précisément, voici un mappage de la façon dont vous pouvez les considérer :
 
|Service Batch AI|  Service Azure Machine Learning|
|:--:|:---:|
|Espace de travail|Espace de travail|
|Cluster|   Calcul de type `AmlCompute`|
|Serveurs de fichiers|DataStores|
|Expériences|Expériences|
|Tâches|Exécutions (autorise les exécutions imbriquées)|
 
Voici une autre vue du même tableau, qui vous aidera à mieux visualiser les choses :
 
### <a name="batch-ai-hierarchy"></a>Hiérarchie Batch AI
![Diagramme de flux](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
### <a name="azure-machine-learning-service-hierarchy"></a>Hiérarchie du service Azure Machine Learning
![Diagramme de flux](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

## <a name="platform-capabilities"></a>Fonctionnalités de la plateforme
Le service Azure Machine Learning offre un ensemble étendu de nouvelles fonctionnalités, notamment un entraînement de bout en bout->pile de déploiement, que vous pouvez utiliser pour votre développement en intelligence artificielle sans avoir à gérer des ressources Azure. Ce tableau compare la prise en charge des fonctionnalités pour l’entraînement entre les deux services.

|Fonctionnalité|Service Batch AI|Service Azure Machine Learning|
|-------|:-------:|:-------:|
|Choix de taille de machine virtuelle |UC/GPU    |UC/GPU. Prend également en charge FPGA pour l’inférence|
|Cluster prêt pour l’intelligence artificielle (pilotes, Docker, etc.)|  OUI |OUI|
|Préparation des nœuds| OUI|    Non |
|Choix de famille de systèmes d’exploitation   |Partiel    |Non |
|Machines virtuelles dédiées et de basse priorité  |OUI    |OUI|
|Mise à l’échelle automatique   |OUI    |Oui (par défaut)|
|Temps d’attente pour la mise à l’échelle automatique  |Non  |OUI|
|SSH    |OUI|   OUI|
|Montage au niveau du cluster |Oui (Partages de fichiers, Objets Blob, NFS, Personnalisé)   |Oui (monter ou télécharger votre magasin de données)|
|Entraînement distribué|  OUI |OUI|
|Mode d’exécution des travaux|    Machine virtuelle ou conteneur|    Conteneur|
|Image de conteneur personnalisée|    OUI |OUI|
|N’importe quel kit de ressources    |OUI    |Oui (Exécuter un script Python)|
|JobPreparation|    OUI |Pas encore|
|Montage au niveau du travail |Oui (Partages de fichiers, Objets Blob, NFS, Personnalisé)   |Oui (Partages de fichiers, Objets Blob)|
|Supervision des travaux     |via GetJob|    via l’historique d’exécution (informations plus détaillées, exécution personnalisée pour envoyer plus de métriques)|
|Récupérer les journaux des travaux et les fichiers/modèles |   via les API ListFiles et Stockage  |via le service d’artefact|
 |Prise en charge de Tensorboard   |Non |    OUI|
|Quotas au niveau des familles de machines virtuelles |OUI    |Oui (avec votre capacité précédente reportée)|
 
En plus du tableau précédent, il existe des fonctionnalités dans le service Azure Machine Learning qui n’étaient traditionnellement pas prises en charge dans Batch AI.

|Fonctionnalité|Service Batch AI|Service Azure Machine Learning|
|-------|:-------:|:-------:|
|Préparation de l’environnement    |Non  |Oui (Préparation et chargement Conda dans ACR)|
|Optimisation des hyperparamètres  |Non |    OUI|
|Gestion des modèles   |Non  |OUI|
|Opérationnalisation/Déploiement| Non   |Via AKS et ACI|
|Préparation des données   |Non  |OUI|
|Cibles de calcul    |Machines virtuelles Azure  |Locales, Batch AI (comme AmlCompute), DataBricks, HDInsight|
|Machine Learning automatisé |Non |    OUI|
|Pipelines  |Non  |OUI|
|Scoring par lots  |OUI    |OUI|
|Prise en charge du portail/CLI|    OUI |OUI|


## <a name="programming-interfaces"></a>Interfaces de programmation

Ce tableau présente les différentes interfaces de programmation disponibles pour chaque service.
    
|Fonctionnalité|Service Batch AI|Service Azure Machine Learning|
|-------|:-------:|:-------:|
|Kit SDK     |Java, C#, Python, Node.js   |Python (basé sur la configuration de l’exécution et basé sur un estimateur pour les frameworks courants)|
|Interface de ligne de commande    |OUI    |Pas encore|
|Portail Azure   |OUI    |Oui (à l’exception de la soumission de travaux)|
|API REST   |OUI    |Oui, mais distribué entre des microservices|


La mise à niveau à partir de Batch AI Preview vers le service Azure Machine Learning en disponibilité générale vous donne une meilleure expérience au travers de concepts qui sont plus faciles à utiliser, comme les estimateurs et les magasins de données. Il garantit également les contrats SLA et le support technique des clients du service Azure au niveau de la disponibilité générale.

Le service Azure Machine Learning offre également de nouvelles fonctionnalités, comme le Machine Learning automatisé, l’optimisation des hyperparamètres et les pipelines Machine Learning, qui sont pratiques dans la plupart des charges de travail d’intelligence artificielle à grande échelle. La possibilité de déployer un modèle entraîné sans passer à un service distinct permet de terminer la boucle de science des données, depuis la préparation des données (avec le SDK de préparation des données) jusqu’à l’opérationnalisation et la supervision du modèle.

<a name="migrate"></a>
## <a name="migrate"></a>Migrer

Découvrez comment migrer et comment le code que vous utilisez correspond au code dans le service Azure Machine Learning dans l’article [Migrer vers le service Azure Machine Learning](how-to-migrate.md).

## <a name="get-support"></a>Obtenir de l’aide

La mise hors service de Batch AI est prévue pour le 31 mars ; l’inscription de nouveaux abonnements auprès du service est déjà bloquée, sauf en cas d’exception accordée par le support technique.  Contactez-nous sur [Azure Batch AI Training Preview](mailto:AzureBatchAITrainingPreview@service.microsoft.com) pour poser vos questions ou pour nous donner un feedback sur votre migration vers le service Azure Machine Learning.

Le service Azure Machine Learning est en disponibilité générale. Cela signifie qu’il est fourni avec un contrat SLA validé et un choix de différents plans de support.

Les tarifs pour l’utilisation de l’infrastructure Azure via le service Batch AI ou le service Azure Machine Learning ne devrait pas varier, car nous facturons seulement le coût du calcul sous-jacent dans les deux cas. Pour plus d’informations, consultez la [calculatrice de prix](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Vous trouverez la disponibilité des deux services selon les régions sur le [portail Azure](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>Étapes suivantes

+ Découvrez [comment migrer](how-to-migrate.md) et comment le code que vous utilisez correspond maintenant au code du service Azure Machine Learning.

+ Lisez la [Vue d’ensemble d’Azure Machine Learning service](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Configurez une cible de calcul pour l’entraînement de modèle](../machine-learning/service/how-to-set-up-training-targets.md) avec Azure Machine Learning service.

+ Passez en revue la [feuille de route Azure](https://azure.microsoft.com/updates/) pour découvrir d’autres mises à jour des services Azure.
