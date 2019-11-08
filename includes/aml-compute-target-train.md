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
ms.date: 05/30/2019
ms.openlocfilehash: 2591ab6984ebe4f864540ab290881a6e47f0be71
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580593"
---
**Les cibles de calcul peuvent être réutilisées d’un travail de formation à l’autre**. Par exemple, une fois que vous avez joint une machine virtuelle distante à votre espace de travail, vous pouvez la réutiliser pour différents travaux.

|&nbsp;Cibles de formation| Prise en charge GPU |[ML automatisé](../articles/machine-learning/service/concept-automated-ml.md) | [Pipelines ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Concepteur Azure Machine Learning](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[Ordinateur local](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| peut-être | Oui | &nbsp; | &nbsp; |
|[Cluster de calcul Azure Machine Learning](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| Oui | oui & <br/>optimisation des &nbsp;hyperparamètres | Oui | Oui |
|[Machine virtuelle distante](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |Oui | oui & <br/>optimisation des hyperparamètres | Oui | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | Oui | Oui | &nbsp; |
|[Service Analytique Azure Data Lake](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | Oui | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | Oui | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | Oui | &nbsp; |
