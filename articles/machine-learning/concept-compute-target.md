---
title: Qu’est-ce qu’une cible de calcul ?
titleSuffix: Azure Machine Learning
description: Définissez l’emplacement où vous souhaitez déployer votre modèle et en faire l’apprentissage avec Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/27/2020
ms.openlocfilehash: f8b532716e14a8a90b0be061f7d93abf43e55e38
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321987"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Qu’est-ce qu’une cible de calcul dans Azure Machine Learning ? 

Une **cible de calcul** est un environnement/une ressource de calcul où vous exécutez votre script d’entraînement ou hébergez votre déploiement de service. Cet emplacement peut être votre machine locale ou une ressource de calcul basée sur le cloud. Les cibles de calcul facilitent la modification de votre environnement de calcul sans modifier votre code.  

Dans un cycle de vie de développement de modèle type, vous pouvez effectuer les opérations suivantes :
1. Commencez par développer et expérimenter une petite quantité de données. À ce stade, nous vous recommandons d’utiliser votre environnement local (ordinateur local ou machine virtuelle cloud) comme cible de calcul. 
2. Passez ensuite à des jeux de données plus volumineux, ou effectuez un entraînement distribué à l’aide d’une des [cibles de calcul d’entraînement](#train).  
3. Une fois que votre modèle est prêt, déployez-le sur un environnement d’hébergement web ou un appareil IoT avec l’une de ces [cibles de calcul de déploiement](#deploy).

Les ressources de calcul utilisées pour vos cibles de calcul sont associées à un [espace de travail](concept-workspace.md). Les cibles de calcul autres que l’ordinateur local sont partagées par les utilisateurs de l’espace de travail.

## <a name="training-compute-targets"></a><a name="train"></a> Cibles de calcul d’entraînement

La prise en charge d’Azure Machine Learning varie selon les ressources de calcul.  Vous pouvez également attacher votre propre ressource de calcul, bien que la prise en charge des différents scénarios puisse varier.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

En savoir plus sur [la configuration et l’utilisation d’une cible de calcul pour l’entraînement du modèle](how-to-set-up-training-targets.md).

## <a name="deployment-targets"></a><a name="deploy"></a>Cibles de déploiement

Les cibles de calcul suivantes peuvent héberger le déploiement de votre modèle.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Découvrez [où et comment déployer votre modèle sur une cible de calcul](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Calcul Azure Machine Learning (managé)

Une ressource de calcul managée est créée et managée par Azure Machine Learning. Ce calcul est optimisé pour les charges de travail Machine Learning. Les clusters de calcul Azure Machine Learning et les [instances de calcul](concept-compute-instance.md) sont les seuls calculs managés. Il se peut que d’autres ressources de calcul managées soient ajoutées à l’avenir.

Vous pouvez créer des instances de calcul Azure Machine Learning ou des clusters de calcul depuis :
* Azure Machine Learning Studio
* Portail Azure
* Classes [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py) et [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py) du Kit de développement logiciel (SDK) Python
* [Kit de développement logiciel (SDK) R](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (préversion)
* Modèle Azure Resource Manager. Pour un exemple de modèle, consultez [Créer un modèle de calcul Azure Machine Learning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute).
* [Extension de Machine Learning pour l’interface de ligne de commande Azure](reference-azure-machine-learning-cli.md#resource-management).  

Une fois créées, ces instances de calcul font automatiquement partie de votre espace de travail, contrairement à d’autres types de cibles de calcul.


|Fonctionnalité  |Cluster de calcul  |Instance de calcul  |
|---------|---------|---------|
|Cluster unique ou à plusieurs nœuds     |    **&check;**       |         |
|Mises à l’échelle automatique chaque fois que vous envoyez une exécution     |     **&check;**      |         |
|Gestion des clusters et planification automatiques des travaux     |   **&check;**        |     **&check;**      |
|Prise en charge des ressources UC et GPU     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Quand un cluster de calcul est inactif, il adapte son échelle automatiquement à 0 nœud, ce qui vous évite de payer quand il n’est pas utilisé.  Cependant, une *instance* de calcul est toujours activée et n’adapte pas son échelle automatiquement.  Vous devez [arrêter l’instance de calcul](tutorial-1st-experiment-sdk-train.md#stop-the-compute-instance) quand vous ne l’utilisez pas pour éviter des frais supplémentaires.

### <a name="supported-vm-series-and-sizes"></a>Tailles et séries de machine virtuelle prises en charge

Lorsque vous sélectionnez une taille de nœud pour une ressource de calcul managée dans Azure Machine Learning, vous pouvez choisir parmi les tailles de machines virtuelles disponibles dans Azure. Azure propose une gamme de tailles de machines virtuelles Windows et Linux pour différentes charges de travail. Pour en savoir plus sur les différents [types et tailles de machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/linux/sizes), consultez cette page.

Quelques exceptions et limites s’appliquent quant au choix d’une taille de machine virtuelle :
* Certaines séries de machines virtuelles ne sont pas prises en charge dans Azure Machine Learning.
* Certaines séries de machines virtuelles sont restreintes. Pour utiliser une série restreinte, contactez le support technique et demandez une augmentation du quota pour la série. Pour plus d’informations sur la manière de contacter le support, consultez [Options de support Azure](https://azure.microsoft.com/support/options/).

Pour en savoir plus sur les séries prises en charge et les restrictions, consultez le tableau suivant. 

| **Série de machines virtuelles prises en charge**  | **Restrictions** |
|------------|------------|
| D | None |
| Dv2 | None |  
| DSv2 | None |  
| FSv2 | None |  
| M | Nécessite une approbation |
| NC | None |    
| NCsv2 | Nécessite une approbation |
| NCsv3 | Nécessite une approbation |  
| NDs | Nécessite une approbation |
| NDv2 | Nécessite une approbation |
| NV | None |
| NVv3 | Nécessite une approbation | 


Même si Azure Machine Learning prend en charge ces séries de machines virtuelles, elles peuvent ne pas être disponibles dans toutes les régions Azure. Vous pouvez vérifier la disponibilité des séries de machines virtuelles ici : [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

## <a name="unmanaged-compute"></a>Calcul non managé

Une cible de calcul non managée n’est *pas* managée par Azure Machine Learning. Vous créez ce type de cible de calcul en dehors d’Azure Machine Learning, puis l’attachez à votre espace de travail. Il se peut que des étapes supplémentaires soient requises pour ces ressources de calcul non managées afin de maintenir ou d’améliorer les performances des charges de travail Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment :
* [Configurer une cible de calcul pour entraîner votre modèle](how-to-set-up-training-targets.md)
* [Déployer votre modèle sur une cible de calcul](how-to-deploy-and-where.md)
