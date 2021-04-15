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
ms.date: 09/17/2020
ms.openlocfilehash: 3eb5ea468a234aea228539c2390ab6cae9352948
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630123"
---
**Les cibles de calcul peuvent être réutilisées d’un travail de formation à l’autre**. Par exemple, une fois que vous avez joint une machine virtuelle distante à votre espace de travail, vous pouvez la réutiliser pour plusieurs travaux. Pour les pipelines de Machine Learning, utilisez l’[étape de pipeline](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) appropriée pour chaque cible de calcul.

Vous pouvez utiliser n’importe laquelle des ressources suivantes pour une cible de calcul de formation pour la plupart des travaux. Certaines ressources ne peuvent pas être utilisées pour l’apprentissage automatique automatisé, des pipelines d’apprentissage automatique ou un concepteur.

|&nbsp;Cibles de formation|[Machine learning automatisé](../articles/machine-learning/concept-automated-ml.md) | [Pipelines d’apprentissage automatique](../articles/machine-learning/concept-ml-pipelines.md) | [Concepteur Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Ordinateur local](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Oui | &nbsp; | &nbsp; |
|[Cluster de calcul Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Oui | Oui | Oui |
|[Instance de calcul Azure Machine Learning](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Oui (via un Kit de développement logiciel (SDK))  | Oui |  |
|[Machine virtuelle distante](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Oui  | Oui | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Oui (Kit de développement logiciel (SDK) en mode local uniquement) | Oui | &nbsp; |
|[Service Analytique Azure Data Lake](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Oui | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Oui | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Oui | &nbsp; |

> [!TIP]
> L’instance de calcul a un disque de système d’exploitation de 120 Go. Si vous ne disposez pas de suffisamment d’espace disque, [utilisez le terminal](../articles/machine-learning/how-to-access-terminal.md) pour effacer au moins 1-2 Go avant d’[arrêter ou redémarrer](../articles/machine-learning/how-to-create-manage-compute-instance.md#manage) l’instance de calcul.
