---
title: Cibles de calcul
titleSuffix: Azure Machine Learning service
description: Une cible de calcul permet de spécifier la ressource de calcul que vous utilisez pour exécuter votre script de formation ou pour héberger votre déploiement de service. Cet emplacement peut être votre machine locale ou une ressource de calcul basée sur le cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/30/2019
ms.openlocfilehash: 42c0f5460a63b781aafdd43410761e2d7b17944d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755349"
---
#  <a name="what-is-a-compute-target-in-azure-machine-learning-service"></a>Qu’est-ce qu’une cible de calcul dans Azure Machine Learning service ? 

Une cible de calcul permet de spécifier la ressource de calcul que vous utilisez pour exécuter votre script de formation ou pour héberger votre déploiement de service. Cet emplacement peut être votre machine locale ou une ressource de calcul basée sur le cloud.

Les cibles de calcul facilitent la modification de votre environnement de calcul sans modifier votre code.  Cycle de vie de développement de modèle type :

* Commencez par une petite quantité de données. À ce stade, nous recommandons d’utiliser un environnement local. Par exemple, votre ordinateur local ou une machine virtuelle basée cloud.
* Puis passez à des jeux de données plus volumineux, ou entraînez-vous de manière distribuée à l’aide d’une des [cibles de formation](#train).  
* Effectuez des déploiements sur plusieurs environnements d’hébergement web ou sur des appareils IoT à l’aide d’une des [cibles de déploiement](#deploy).

Les ressources de calcul utilisées pour vos cibles de calcul sont associées à un [espace de travail](concept-workspace.md). Les cibles de calcul autres que l’ordinateur local sont partagées par les utilisateurs de l’espace de travail.

## <a name="train"></a> Cibles de formation

La prise en charge par Azure Machine Learning service varie selon les ressources de calcul.  Vous pouvez également attacher votre propre ressource de calcul, bien que la prise en charge des différents scénarios puisse varier comme indiqué ci-dessous :

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


## <a name="deploy"></a>Cibles de déploiement

Les cibles de calcul suivantes peuvent héberger le déploiement de votre modèle.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]


## <a name="managed-compute"></a>Capacité de calcul managée

Une ressource de calcul managée est créée et managée par Azure Machine Learning service. Ce calcul est optimisé pour les charges de travail Machine Learning. À l’heure actuelle, la capacité de calcul Azure Machine Learning est la seule ressource de calcul managée à partir du 30 mai 2019. Il se peut que d’autres ressources de calcul managées soient ajoutées à l’avenir.

### <a name="amlcompute"></a> Capacité de calcul Azure Machine Learning

Vous pouvez utiliser la capacité de calcul Azure Machine Learning pour l’apprentissage et pour l’inférence par lots (préversion).  Grâce à cette ressource de calcul, vous disposez de ce qui suit :

* Cluster unique ou à plusieurs nœuds
* Mises à l’échelle automatique chaque fois que vous envoyez une exécution 
* Gestion des clusters et planification automatiques des travaux 
* Prise en charge des ressources UC et GPU

Vous pouvez créer des instances de capacité de calcul Azure Machine Learning avec :

* Le portail Azure
* Le Kit de développement logiciel (SDK) Azure Machine Learning
* L’interface Azure CLI

Toutes les autres ressources de calcul doivent être créées hors de l’espace de travail avant d’y être jointes.

## <a name="unmanaged-compute"></a>Calcul non managé

Une ressource de calcul non managée n’est *pas* managée par Azure Machine Learning service. Vous créez ce type de calcul en dehors d’Azure Machine Learning, puis l’attachez à votre espace de travail. Il se peut que des étapes supplémentaires soient requises pour ces ressources de calcul non managées afin de maintenir ou d’améliorer les performances des charges de travail Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des cibles de calcul pour l’entraînement des modèles](how-to-set-up-training-targets.md)
* [Déployer des modèles avec Azure Machine Learning service](how-to-deploy-and-where.md)