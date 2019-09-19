---
title: 'Cibles de calcul : emplacement où entraîner et déployer des modèles'
titleSuffix: Azure Machine Learning service
description: Définissez l’emplacement où vous souhaitez entraîner ou déployer votre modèle avec Azure Machine Learning service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: b2e4c9160ccad66b04dd30979f0568da68836d76
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860589"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning-service"></a>Qu’est-ce qu’une cible de calcul dans Azure Machine Learning service ? 

Une **cible de calcul** est un environnement/une ressource de calcul où vous exécutez votre script d’entraînement ou hébergez votre déploiement de service. Cet emplacement peut être votre machine locale ou une ressource de calcul basée sur le cloud. Les cibles de calcul facilitent la modification de votre environnement de calcul sans modifier votre code.  

Dans un cycle de vie de développement de modèle type, vous pouvez effectuer les opérations suivantes :
1. Commencez par développer et expérimenter une petite quantité de données. À ce stade, nous vous recommandons d’utiliser votre environnement local (ordinateur local ou machine virtuelle cloud) comme cible de calcul. 
2. Passez ensuite à des jeux de données plus volumineux, ou effectuez un entraînement distribué à l’aide d’une des [cibles de calcul d’entraînement](#train).  
3. Une fois que votre modèle est prêt, déployez-le sur un environnement d’hébergement web ou un appareil IoT avec l’une de ces [cibles de calcul de déploiement](#deploy).

Les ressources de calcul utilisées pour vos cibles de calcul sont associées à un [espace de travail](concept-workspace.md). Les cibles de calcul autres que l’ordinateur local sont partagées par les utilisateurs de l’espace de travail.

## <a name="train"></a> Cibles de calcul d’entraînement

La prise en charge par Azure Machine Learning service varie selon les ressources de calcul.  Vous pouvez également attacher votre propre ressource de calcul, bien que la prise en charge des différents scénarios puisse varier.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

En savoir plus sur [la configuration et l’utilisation d’une cible de calcul pour l’entraînement du modèle](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Cibles de déploiement

Les cibles de calcul suivantes peuvent héberger le déploiement de votre modèle.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

Découvrez [où et comment déployer votre modèle sur une cible de calcul](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Calcul Azure Machine Learning (managé)

Une ressource de calcul managée est créée et managée par Azure Machine Learning service. Ce calcul est optimisé pour les charges de travail Machine Learning. À l’heure actuelle, la capacité de calcul Azure Machine Learning est la seule ressource de calcul managée à partir du 30 mai 2019. Il se peut que d’autres ressources de calcul managées soient ajoutées à l’avenir.

Vous pouvez utiliser la capacité de calcul Azure Machine Learning pour l’entraînement et l’inférence par lots (préversion).  Grâce à cette ressource de calcul, vous disposez de ce qui suit :

* Cluster unique ou à plusieurs nœuds
* Mises à l’échelle automatique chaque fois que vous envoyez une exécution 
* Gestion des clusters et planification automatiques des travaux 
* Prise en charge des ressources UC et GPU

Vous pouvez créer des instances de capacité de calcul Azure Machine Learning dans le portail Azure ou dans la [page d’accueil de votre espace de travail (préversion)](https://ml.azure.com), avec le SDK ou avec l’interface CLI. Une fois l’instance créée, elle fait automatiquement partie de votre espace de travail, contrairement à d’autres types de cibles de calcul.

## <a name="unmanaged-compute"></a>Calcul non managé

Une cible de calcul non managée n’est *pas* managée par Azure Machine Learning service. Vous créez ce type de cible de calcul en dehors d’Azure Machine Learning, puis l’attachez à votre espace de travail. Il se peut que des étapes supplémentaires soient requises pour ces ressources de calcul non managées afin de maintenir ou d’améliorer les performances des charges de travail Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment :
* [Configurer une cible de calcul pour entraîner votre modèle](how-to-set-up-training-targets.md)
* [Déployer votre modèle sur une cible de calcul](how-to-deploy-and-where.md)