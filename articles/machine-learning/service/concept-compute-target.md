---
title: Cibles de calcul
titleSuffix: Azure Machine Learning service
description: Une cible de calcul vous permet de pour spécifier la ressource de calcul où vous exécutez votre script de formation ou d’un hôte de votre déploiement de service. Cet emplacement peut être votre ordinateur local ou une ressource de calcul basé sur le cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/30/2019
ms.openlocfilehash: 42c0f5460a63b781aafdd43410761e2d7b17944d
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755349"
---
#  <a name="what-is-a-compute-target-in-azure-machine-learning-service"></a>Qu’est une cible de calcul dans le service Azure Machine Learning ? 

Une cible de calcul vous permet de pour spécifier la ressource de calcul où vous exécutez votre script de formation ou d’un hôte de votre déploiement de service. Cet emplacement peut être votre ordinateur local ou une ressource de calcul basé sur le cloud.

Cibles de calcul facilitent la modification de votre environnement de calcul sans modifier votre code.  Un cycle de vie du développement de modèle typique :

* Démarrez avec dev/expérimentation sur une petite quantité de données. À ce stade, nous recommandons d’utiliser un environnement local. Par exemple, votre ordinateur local ou une machine virtuelle basée cloud.
* Monter en puissance votre formation sur les jeux de données volumineux, ou distributed apprentissage à l’aide d’une de la [cibles de formation](#train).  
* Déployer sur le web de plusieurs environnements d’hébergement ou sur des appareils IoT à l’aide d’un de le [cibles de déploiement](#deploy).

Les ressources de calcul que vous utilisez pour vos cibles de calcul sont attachés à un [espace de travail](concept-workspace.md). Ressources autres que l’ordinateur local sont partagés par les utilisateurs de l’espace de travail de calcul.

## <a name="train"></a> Cibles de formation

Service Azure Machine Learning a prise en charge entre les ressources de calcul différentes.  Vous pouvez également attacher votre propre ressource de calcul, bien que la prise en charge des différents scénarios puisse varier comme indiqué ci-dessous :

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


## <a name="deploy"></a>Cibles de déploiement

Les ressources de calcul suivantes peuvent être utilisés pour héberger votre déploiement de modèle.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]


## <a name="managed-compute"></a>Calcul managé

Une ressource de calcul gérée est créée et gérée par service Azure Machine Learning. Ce calcul est optimisée pour les charges de travail machine learning. Azure Machine Learning Compute est le seul calcul gérée depuis le 30 mai 2019. Ressources de calcul gérés supplémentaires peuvent être ajoutés à l’avenir.

### <a name="amlcompute"></a> Azure Machine Learning Compute

Vous pouvez utiliser Azure Machine Learning Compute pour l’apprentissage et pour l’inférence de lot (version préliminaire).  Avec cette ressource de calcul, vous avez :

* Cluster unique ou plusieurs node
* Diminution chaque fois que vous envoyez une exécution 
* Gestion de cluster automatique et planification des travaux 
* Prise en charge des ressources UC et GPU

Vous pouvez créer des instances de calcul d’Azure Machine Learning avec les éléments suivants :

* Le portail Azure
* Azure Machine Learning SDK
* L’interface Azure CLI

Toutes les autres ressources de calcul doivent être créés en dehors de l’espace de travail et puis attachées à ce dernier.

## <a name="unmanaged-compute"></a>Calcul non managé

Une ressource de calcul non managé est *pas* gérés par le service Azure Machine Learning. Vous créez ce type de calcul en dehors d’Azure Machine Learning, puis l’attacher à votre espace de travail. Ressources de calcul non managé peuvent nécessiter des étapes supplémentaires pour vous permet de mettre à jour ou d’améliorer les performances des charges de travail machine learning.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des cibles de calcul pour l’apprentissage du modèle](how-to-set-up-training-targets.md)
* [Déployer des modèles avec le service Azure Machine Learning](how-to-deploy-and-where.md)