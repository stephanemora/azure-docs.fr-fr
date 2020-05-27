---
title: Fichier include
description: Fichier include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/04/2019
ms.openlocfilehash: 764e4c33182499b922499c798d57fd05d8859742
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673388"
---
**Les cibles de calcul peuvent être réutilisées d’un travail de formation à l’autre**. Par exemple, une fois que vous avez joint une machine virtuelle distante à votre espace de travail, vous pouvez la réutiliser pour différents travaux.  Pour les pipelines de Machine Learning, utilisez l’[étape de pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) appropriée pour chaque cible de calcul.

|&nbsp;Cibles de formation|[ML automatisé](../articles/machine-learning/concept-automated-ml.md) | [Pipelines ML](../articles/machine-learning/concept-ml-pipelines.md) | [Concepteur Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Ordinateur local](../articles/machine-learning/how-to-set-up-training-targets.md#local)| Oui | &nbsp; | &nbsp; |
|[Cluster de calcul Azure Machine Learning](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| oui & <br/>optimisation des &nbsp;hyperparamètres | Oui | Oui |
|[Instance de calcul Azure Machine Learning](../articles/machine-learning/concept-compute-instance.md) | oui & <br/>optimisation des hyperparamètres | Oui |  |
|[Machine virtuelle distante](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | oui & <br/>optimisation des hyperparamètres | Oui | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| Oui (Kit de développement logiciel (SDK) en mode local uniquement) | Oui | &nbsp; |
|[Service Analytique Azure Data Lake](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | Oui | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | Oui | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | Oui | &nbsp; |
