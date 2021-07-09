---
title: Gérer et optimiser les coûts
titleSuffix: Azure Machine Learning
description: Découvrez des conseils pour optimiser vos coûts lors de la création de modèles Machine Learning dans Azure Machine Learning.
author: luisquintanilla
ms.author: luquinta
ms.custom: subject-cost-optimization
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: f5f0351e21588d6e01a633a11d5638358e4d706b
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112008263"
---
# <a name="manage-and-optimize-azure-machine-learning-costs"></a>Gérer et optimiser les coûts d’Azure Machine Learning

Découvrez comment gérer et optimiser les coûts lors de l’entraînement et du déploiement de modèles Machine Learning sur Azure Machine Learning.

Utilisez les conseils suivants pour vous aider à gérer et à optimiser les coûts de vos ressources de calcul.

- Configurer vos clusters d’entraînement pour la mise à l’échelle automatique
- Définir des quotas sur votre abonnement et vos espaces de travail
- Définir des stratégies de résiliation sur votre exécution d’entraînement
- Utiliser des machines virtuelles de basse priorité
- Utiliser une instance de machine virtuelle réservée Azure
- Entraîner localement
- Paralléliser l’entraînement
- Définir des stratégies de conservation et de suppression des données
- Déployer des ressources dans la même région

Pour plus d’informations sur la planification et la supervision des coûts, consultez le guide [Planifier la gestion des coûts d’Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Utiliser le cluster de calcul Azure Machine Learning (AmlCompute)

Avec des données constamment modifiées, vous avez besoin d’un entraînement de modèle rapide et rationalisé et d’un réentraînement pour maintenir des modèles précis. Toutefois, l’entraînement continu est coûteux, en particulier pour les modèles de deep learning sur les GPU. 

Les utilisateurs Azure Machine Learning peuvent utiliser le cluster de calcul Azure Machine Learning géré, également appelé AmlCompute. AmlCompute prend en charge un large éventail d’options de GPU et d’UC. AmlCompute est hébergé en interne pour le compte de votre abonnement par Azure Machine Learning. Il offre les mêmes sécurité, conformité et gouvernance de qualité professionnelle qu’à l’échelle du cloud Azure IaaS.

Ces pools de calcul étant à l’intérieur de l’infrastructure IaaS d’Azure, vous pouvez déployer, mettre à l’échelle et gérer votre entraînement avec les mêmes exigences de sécurité et de conformité que le reste de votre infrastructure.  Ces déploiements se produisent dans votre abonnement et obéissent à vos règles de gouvernance. Découvrez plus en détail la [capacité de calcul Azure Machine Learning](how-to-create-attach-compute-cluster.md).

## <a name="configure-training-clusters-for-autoscaling"></a>Configurer des clusters d’entraînement pour la mise à l’échelle automatique

La mise à l’échelle automatique des clusters selon les besoins de votre charge de travail vous permet de réduire les coûts et d’utiliser uniquement ce dont vous avez besoin.

Les clusters AmlCompute sont conçus pour effectuer dynamiquement la mise à l’échelle en fonction de votre charge de travail. Le cluster peut être mis à l’échelle jusqu’au nombre maximal de nœuds que vous configurez. À la fin de chaque exécution, le cluster publie des nœuds et effectue une mise à l’échelle jusqu’au nombre de nœuds minimal que vous avez configuré.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Vous pouvez également configurer la durée d’inactivité du nœud avant l’exécution du scale-down. Par défaut, le délai d’inactivité avant le scale-down est défini sur 120 secondes.

+ Si vous effectuez une expérimentation moins itérative, réduisez ce délai pour réduire les coûts.
+ Si vous effectuez une expérimentation de développement/test hautement itérative, vous devrez peut-être augmenter la durée pour ne pas avoir à payer des mises à l’échelle constantes après chaque modification apportée à votre environnement ou script d’entraînement.

Les clusters AmlCompute peuvent être configurés pour répondre aux besoins fluctuants de votre charge de travail dans le portail Azure, à l’aide de la [classe SDK AmlCompute](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute) et de l’interface [CLI AmlCompute](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_amlcompute) avec les [API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>Définir des quotas sur les ressources

AmlCompute est fourni avec une [configuration de quota (ou limite)](how-to-manage-quotas.md#azure-machine-learning-compute). Ce quota est défini par famille de machines virtuelles (par exemple, la série Dv2, la série NCv3) et varie selon la région pour chaque abonnement. Les abonnements commencent par des valeurs par défaut réduites pour vous aider, mais utilisez ce paramètre pour contrôler la quantité de ressources AmlCompute pouvant être mises en service dans votre abonnement. 

Configurez également un [quota au niveau de l’espace de travail par famille de machines virtuelles](how-to-manage-quotas.md#workspace-level-quotas) pour chaque espace de travail d’un abonnement. Cela vous permet d’avoir un contrôle plus précis sur les coûts que chaque espace de travail peut potentiellement générer, et de restreindre certaines familles de machines virtuelles. 

Pour définir des quotas au niveau de l’espace de travail, commencez dans le [portail Azure](https://portal.azure.com).  Sélectionnez un espace de travail quelconque dans votre abonnement, puis sélectionnez **Utilisations + quotas** dans le volet gauche. Sélectionnez ensuite l’onglet **Configurer les quotas** pour afficher les quotas. Vous avez besoin de privilèges dans l’étendue de l’abonnement pour définir ce quota, car il s’agit d’un paramètre qui affecte plusieurs espaces de travail.

## <a name="set-run-autotermination-policies"></a>Définir des stratégies de résiliation automatique d’exécution 

Dans certains cas, vous devez configurer vos exécutions d’apprentissage pour limiter leur durée ou y mettre fin prématurément. Par exemple, lorsque vous utilisez le réglage d’hyperparamètre intégré d’Azure Machine Learning ou Machine Learning automatisé.

Voici quelques options dont vous disposez :
* Définissez un paramètre appelé `max_run_duration_seconds` dans votre RunConfiguration pour contrôler la durée maximale pendant laquelle une exécution peut s’étendre sur le calcul que vous choisissez (calcul cloud local ou distant).
* Pour le [réglage d’hyperparamètre](how-to-tune-hyperparameters.md#early-termination), définissez une stratégie de résiliation anticipée à partir d’une stratégie Bandit, d’une stratégie d’arrêt médiane ou d’une stratégie de sélection de troncation. Pour contrôler davantage les balayages d’hyperparamètres, utilisez des paramètres tels que `max_total_runs` ou `max_duration_minutes`.
* Pour [Machine Learning automatisé](how-to-configure-auto-train.md#exit), définissez des stratégies de résiliation similaires à l’aide de l’indicateur `enable_early_stopping`. Utilisez également des propriétés telles que `iteration_timeout_minutes` et `experiment_timeout_minutes` pour contrôler la durée maximale d’une exécution ou de l’ensemble de l’expérience.

## <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Utiliser des machines virtuelles de faible priorité

Azure vous permet d’utiliser une capacité inutilisée excessive en tant que machines virtuelles de basse priorité sur des groupes identiques de machines virtuelles (VMSS), Batch et le service Machine Learning. Ces allocations sont préventives mais disponibles à prix réduit par rapport aux machines virtuelles dédiées. En général, nous vous recommandons d’utiliser des machines virtuelles de faible priorité pour les charges de travail Batch. Vous devez également les utiliser lorsque des interruptions sont récupérables par le biais de renvois (pour l’inférence par lots) ou de redémarrages (pour un entraînement Deep Learning avec points de contrôle).

Les machines virtuelles de basse priorité ont un quota unique distinct de la valeur de quota dédiée, qui est défini par famille de machines virtuelles. Apprenez-en davantage sur les [quotas AmlCompute](how-to-manage-quotas.md).

 Les machines virtuelles de basse priorité ne fonctionnent pas pour les instances de calcul, car elles doivent prendre en charge les expériences de notebook interactives.

## <a name="use-reserved-instances"></a>Utiliser des instances réservées

Une autre façon d’économiser de l’argent sur les ressources de calcul est l’instance de machine virtuelle réservée Azure. Avec cette offre, vous vous engagez à des termes de 1 an ou 3 ans. Ces remises vont jusqu’à 72 % des prix de paiement à l’utilisation et s’appliquent directement sur votre facture Azure mensuelle.

La capacité de calcul Azure Machine Learning prend en charge les instances réservées par nature. Si vous achetez une instance réservée d’un an ou de trois ans, nous appliquons automatiquement la remise sur la base de votre calcul managé Azure Machine Learning.

## <a name="train-locally"></a>Entraîner localement

Lors du prototypage et de l’exécution de travaux d’entraînement suffisamment petits pour s’exécuter sur votre ordinateur local, privilégiez l’entraînement local. La définition de votre cible de calcul sur `local` à l’aide du kit SDK Python exécute votre script localement. Pour plus d’informations, consultez [Configurer et soumettre des exécutions d’entraînement](how-to-set-up-training-targets.md#select-a-compute-target).

Visual Studio Code fournit un environnement complet pour le développement de vos applications Machine Learning. À l’aide de l’extension visuelle Visual Studio Code Azure Machine Learning et de Docker, vous pouvez exécuter et déboguer localement. Pour plus d’informations, consultez [Débogage interactif avec Visual Studio Code](how-to-debug-visual-studio-code.md).

## <a name="parallelize-training"></a>Paralléliser l’entraînement

L’une des principales méthodes d’optimisation des coûts et des performances consiste à paralléliser la charge de travail à l’aide d’une étape d’exécution parallèle dans Azure Machine Learning. Cette étape vous permet d’utiliser un grand nombre de plus petits nœuds pour exécuter la tâche en parallèle, ce qui vous permet de mettre à l’échelle horizontalement. Il existe une surcharge liée à la parallélisation. En fonction de la charge de travail et du degré de parallélisme pouvant être atteint, cela peut ou non être une option. Pour plus d’informations, consultez la documentation [ParallelRunStep](xref:azureml.contrib.pipeline.steps.ParallelRunStep).

## <a name="set-data-retention--deletion-policies"></a>Définir des stratégies de conservation et de suppression des données

Chaque fois qu’un pipeline est exécuté, des jeux de données intermédiaires sont générés à chaque étape. Au fil du temps, ces jeux de données intermédiaires occupent de l’espace dans votre compte de stockage. Réfléchissez à la configuration de stratégies pour gérer vos données tout au long de leur cycle de vie afin d’archiver et de supprimer vos jeux de données. Pour plus d’informations, consultez [Optimiser les coûts en automatisant les niveaux d’accès au Stockage Blob Azure](/storage/blobs/storage-lifecycle-management-concepts.md).

## <a name="deploy-resources-to-the-same-region"></a>Déployer des ressources dans la même région

Les calculs situés dans des régions différentes peuvent présenter une latence réseau et une augmentation des coûts de transfert de données. Des coûts réseau Azure liés à la bande passante sortante à partir des centres de données Azure sont encourus. Pour aider à réduire les coûts liés au réseau, déployez toutes vos ressources dans la région. Le provisionnement de votre espace de travail Azure Machine Learning et des ressources dépendantes dans la même région que vos données peuvent aider à réduire les coûts et à améliorer les performances.

Pour les scénarios cloud hybrides comme ceux utilisant ExpressRoute, il peut parfois être plus économique de déplacer toutes les ressources vers Azure pour optimiser les coûts et la latence du réseau.

## <a name="next-steps"></a>Étapes suivantes

- [Planifier la gestion des coûts d’Azure Machine Learning](concept-plan-manage-cost.md)
- [Gérer les budgets, les coûts et le quota pour Azure Machine Learning à l’échelle de l’organisation](/azure/cloud-adoption-framework/ready/azure-best-practices/optimize-ai-machine-learning-cost)