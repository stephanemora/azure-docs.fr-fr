---
title: Impossible d’ajouter des nœuds au cluster Azure HDInsight
description: Résoudre les problèmes liés à l’impossibilité d’ajouter des nœuds à un cluster Apache Hadoop dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: c6eba18156828892c70136474df5a937ef43f9a3
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299732"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scénario : impossible d’ajouter des nœuds au cluster Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Impossible d’ajouter des nœuds au cluster Azure HDInsight.

## <a name="cause"></a>Cause

Les raisons peuvent varier.

## <a name="resolution"></a>Résolution

À l’aide de la fonctionnalité [Taille du cluster](../hdinsight-scaling-best-practices.md), calculez le nombre de cœurs supplémentaires nécessaires au cluster. Ce nombre dépend du nombre total de cœurs dans les nouveaux nœuds Worker. Essayez ensuite une ou plusieurs des étapes suivantes :

* Vérifiez si des cœurs sont disponibles à l’emplacement du cluster.

* Examinez le nombre de cœurs disponibles à d’autres emplacements. Envisagez éventuellement de recréer votre cluster à un autre emplacement et de lui attribuer suffisamment de cœurs.

* Si vous souhaitez augmenter le quota principal d’un emplacement précis, créez un ticket de support en demandant une augmentation du quota principal HDInsight.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]