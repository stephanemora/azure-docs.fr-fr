---
title: Fichier Include
description: Fichier Include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/04/2019
ms.openlocfilehash: 25a865296d2ebaabcf9043341878928763d61a6f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875427"
---
**Les cibles de calcul peuvent être réutilisées d’un travail de formation à l’autre**. Par exemple, une fois que vous avez joint une machine virtuelle distante à votre espace de travail, vous pouvez la réutiliser pour différents travaux.  Pour les pipelines de Machine Learning, utilisez l’[étape de pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) appropriée pour chaque cible de calcul.

|&nbsp;Cibles de formation|[ML automatisé](../articles/machine-learning/service/concept-automated-ml.md) | [Pipelines ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Concepteur Azure Machine Learning](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|
|[Ordinateur local](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Oui | &nbsp; | &nbsp; |
|[Cluster de calcul Azure Machine Learning](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| oui & <br/>optimisation des &nbsp;hyperparamètres | Oui | Oui |
|[Machine virtuelle distante](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) | oui & <br/>optimisation des hyperparamètres | Oui | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| Oui | Oui | &nbsp; |
|[Service Analytique Azure Data Lake](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla) | &nbsp; | Oui | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | Oui | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch) | &nbsp; | Oui | &nbsp; |
