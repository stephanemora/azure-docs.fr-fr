---
title: Prévoir et gérer les coûts
titleSuffix: Azure Machine Learning
description: Planifiez et gérez les coûts d’Azure Machine Learning avec l’analyse des coûts dans le portail Azure. Lorsque vous générez des modèles Machine Learning, découvrez des conseils supplémentaires pour réduire les coûts.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 3173f81f84463cde488dcbf0242f8d65c5b9c9fe
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144989"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Planifier et gérer les coûts d’Azure Machine Learning

Cet article explique comment planifier et gérer les coûts d’Azure Machine Learning. Tout d’abord, vous devez utiliser la calculatrice de prix Azure pour planifier les coûts avant d’ajouter des ressources. Ensuite, lorsque vous ajoutez les ressources Azure, passez en revue les coûts estimés. Enfin, utilisez des conseils de réduction des coûts lorsque vous entraînez votre modèle avec des clusters de calcul Azure Machine Learning gérés.

Une fois que vous avez commencé à utiliser des ressources Azure Machine Learning, utilisez les fonctionnalités de gestion des coûts pour définir des budgets et superviser les coûts. Passez également en revue les coûts prévus et déterminez les tendances des dépenses pour identifier les domaines où vous pourriez agir.

Il est important de comprendre que les coûts pour Azure Machine Learning ne représentent qu’une partie des coûts mensuels sur votre facture Azure. Si vous utilisez d’autres services Azure, tous les services et ressources Azure utilisés dans votre abonnement Azure vous sont facturés, y compris les services tiers. Cet article explique comment planifier et gérer les coûts d’Azure Machine Learning. Quand vous connaîtrez bien la gestion des coûts d’Azure Machine Learning, vous pourrez appliquer des méthodes similaires pour gérer les coûts de tous les services Azure utilisés dans votre abonnement.

Lorsque vous entraînez vos modèles Machine Learning, utilisez des clusters de calcul Azure Machine Learning gérés pour tirer parti de conseils de réduction des coûts supplémentaires :

* Configurer vos clusters d’entraînement pour la mise à l’échelle automatique
* Définir des quotas sur votre abonnement et vos espaces de travail
* Définir des stratégies de résiliation sur votre exécution d’entraînement
* Utiliser des machines virtuelles de basse priorité
* Utiliser une instance de machine virtuelle réservée Azure

## <a name="prerequisites"></a>Prérequis

L’analyse des coûts prend en charge différents types de comptes Azure. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md). Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour votre compte Azure. 

Pour plus d’informations sur l’attribution de l’accès aux données Azure Cost Management, consultez [Assigner l’accès aux données](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs"></a>Estimer les coûts

Utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer les coûts avant de créer les ressources dans un compte Azure Machine Learning. Sur la gauche, sélectionnez **AI + Machine Learning**, puis sélectionnez **Azure Machine Learning** pour commencer.  

La capture d’écran suivante montre l’estimation des coûts effectuée par la calculatrice :

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Estimation des coûts dans la calculatrice Azure":::

À mesure que vous ajoutez de nouvelles ressources dans votre espace de travail, revenez à cette calculatrice et ajoutez la même ressource ici pour mettre à jour vos estimations de coûts.

Bien que l’édition Enterprise soit en préversion, il n’y a pas de supplément ML. Lorsque l’édition Enterprise deviendra globalement disponible, elle présentera un supplément (pour la formation et l’inférence).  Pour plus d’informations, consultez [Tarifs Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

## <a name="get-cost-alerts"></a>Obtenir des alertes de coût

Créez des [budgets](../cost-management/tutorial-acm-create-budgets.md) pour gérer les coûts et créez des [alertes](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) permettant d’avertir automatiquement les parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût. Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts. Les budgets et les alertes peuvent cependant avoir des fonctionnalités limitées pour gérer les coûts de services Azure individuels, car ils sont conçus pour suivre les coûts à un niveau supérieur.

## <a name="monitor-costs"></a>Superviser les coûts

À mesure que vous utilisez des ressources avec Azure Machine Learning, vous générez des coûts. Les coûts unitaires d’utilisation des ressources Azure varient en fonction d’intervalles de temps (secondes, minutes, heures et jours) ou en fonction de l’utilisation d’unités de requête. Dès que vous commencez à utiliser Azure Machine Learning, des coûts sont générés. Visualisez ces coûts dans le volet d’[analyse des coûts](../cost-management/quick-acm-cost-analysis.md) du portail Azure.

Vous pouvez afficher les coûts dans des graphes et des tables pour différents intervalles de temps. Vous pouvez également voir les coûts par rapport aux budgets et aux coûts prévus. Passez à des vues sur des périodes plus longues pour identifier les tendances des dépenses et déterminer où des dépenses excessives ont pu se produire. S vous avez créé des budgets, notez où ils ont été dépassés.  

Vous ne verrez pas une zone de service distincte pour Machine Learning.  Au lieu de cela, vous verrez les différentes ressources que vous avez ajoutées à vos espaces de travail Machine Learning.

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Utiliser le cluster de calcul Azure Machine Learning (AmlCompute)

Avec des données constamment modifiées, vous avez besoin d’un entraînement de modèle rapide et rationalisé et d’un réentraînement pour maintenir des modèles précis. Toutefois, l’entraînement continu est coûteux, en particulier pour les modèles de deep learning sur les GPU. 

Les utilisateurs Azure Machine Learning peuvent utiliser le cluster de calcul Azure Machine Learning géré, également appelé AmlCompute. AmlCompute prend en charge un large éventail d’options de GPU et d’UC. AmlCompute est hébergé en interne pour le compte de votre abonnement par Azure Machine Learning. Il offre les mêmes sécurité, conformité et gouvernance de qualité professionnelle qu’à l’échelle du cloud Azure IaaS.

Ces pools de calcul étant à l’intérieur de l’infrastructure IaaS d’Azure, vous pouvez déployer, mettre à l’échelle et gérer votre entraînement avec les mêmes exigences de sécurité et de conformité que le reste de votre infrastructure.  Ces déploiements se produisent dans votre abonnement et obéissent à vos règles de gouvernance. Découvrez plus en détail la [capacité de calcul Azure Machine Learning](how-to-create-attach-compute-sdk.md#amlcompute).

## <a name="configure-training-clusters-for-autoscaling"></a>Configurer des clusters d’entraînement pour la mise à l’échelle automatique

La mise à l’échelle automatique des clusters selon les besoins de votre charge de travail vous permet de réduire les coûts et d’utiliser uniquement ce dont vous avez besoin.

Les clusters AmlCompute sont conçus pour effectuer dynamiquement la mise à l’échelle en fonction de votre charge de travail. Le cluster peut être mis à l’échelle jusqu’au nombre maximal de nœuds que vous configurez. À la fin de chaque exécution, le cluster publie des nœuds et effectue une mise à l’échelle jusqu’au nombre de nœuds minimal que vous avez configuré.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Vous pouvez également configurer la durée d’inactivité du nœud avant l’exécution du scale-down. Par défaut, le délai d’inactivité avant le scale-down est défini sur 120 secondes.

+ Si vous effectuez une expérimentation moins itérative, réduisez ce délai pour réduire les coûts.
+ Si vous effectuez une expérimentation de développement/test hautement itérative, vous devrez peut-être augmenter la durée pour ne pas avoir à payer des mises à l’échelle constantes après chaque modification apportée à votre environnement ou script d’entraînement.

Les clusters AmlCompute peuvent être configurés pour répondre aux besoins fluctuants de votre charge de travail dans le portail Azure, à l’aide de la [classe SDK AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) et de l’interface [CLI AmlCompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute) avec les [API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azure cli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>Définir des quotas sur les ressources

AmlCompute est fourni avec une [configuration de quota (ou limite)](how-to-manage-quotas.md#azure-machine-learning-compute). Ce quota est défini par famille de machines virtuelles (par exemple, la série Dv2, la série NCv3) et varie selon la région pour chaque abonnement. Les abonnements commencent par des valeurs par défaut réduites pour vous aider, mais utilisez ce paramètre pour contrôler la quantité de ressources AmlCompute pouvant être mises en service dans votre abonnement. 

Configurez également un [quota au niveau de l’espace de travail par famille de machines virtuelles](how-to-manage-quotas.md#workspace-level-quota) pour chaque espace de travail d’un abonnement. Cela vous permet d’avoir un contrôle plus précis sur les coûts que chaque espace de travail peut potentiellement générer, et de restreindre certaines familles de machines virtuelles. 

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


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :
* [Gérer et augmenter les quotas de ressources](how-to-manage-quotas.md)
* [Gestion des coûts avec l’analyse des coûts](../cost-management-billing/costs/quick-acm-cost-analysis.md).
* Créez un calcul Azure Machine Learning avec le [kit SDK](how-to-create-attach-compute-sdk.md#amlcompute) ou dans [Studio](how-to-create-attach-compute-studio.md#amlcompute).
