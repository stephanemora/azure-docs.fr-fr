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
ms.openlocfilehash: 50905eb987defac612f1055b450b682726f0a56f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752958"
---
|Formation &nbsp;cibles| Prise en charge GPU |[ML automatisé](../articles/machine-learning/service/concept-automated-ml.md) | [Pipelines ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Interface visuelle](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[Ordinateur local](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Peut-être | Oui | &nbsp; | &nbsp; |
|[Capacité de calcul Azure Machine Learning](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| Oui | Oui & <br/>hyperparamètres&nbsp;paramétrage | Oui | Oui |
|[Machine virtuelle distante](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |Oui | Oui & <br/>optimisation des hyperparamètres | Oui | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | Oui | Oui | &nbsp; |
|[Service Analytique Azure Data Lake](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | Oui | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | Oui | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | Oui | &nbsp; |

**Toutes les cibles de calcul peuvent être réutilisées pour plusieurs travaux de formation**. Par exemple, une fois que vous avez joint une machine virtuelle distante à votre espace de travail, vous pouvez la réutiliser pour différents travaux.