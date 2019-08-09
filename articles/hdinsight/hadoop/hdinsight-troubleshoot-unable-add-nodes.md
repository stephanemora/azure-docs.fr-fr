---
title: Impossible d’ajouter des nœuds au cluster Azure HDInsight
description: Impossible d’ajouter des nœuds au cluster Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: ea36f8a6a8c1db031de8f6ce2b4e3a88d73907e7
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700145"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scénario : Impossible d’ajouter des nœuds au cluster Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Impossible d’ajouter des nœuds au cluster Azure HDInsight.

## <a name="cause"></a>Cause :

Les raisons peuvent varier.

## <a name="resolution"></a>Résolution :

À l’aide de la fonctionnalité [Taille du cluster](/hdinsight-scaling-best-practices.md), calculez le nombre de cœurs supplémentaires nécessaires au cluster. Ce nombre dépend du nombre total de cœurs dans les nouveaux nœuds Worker. Essayez ensuite une ou plusieurs des étapes suivantes :

* Vérifiez si des cœurs sont disponibles à l’emplacement du cluster.

* Examinez le nombre de cœurs disponibles à d’autres emplacements. Envisagez éventuellement de recréer votre cluster à un autre emplacement et de lui attribuer suffisamment de cœurs.

* Si vous souhaitez augmenter le quota principal d’un emplacement précis, créez un ticket de support en demandant une augmentation du quota principal HDInsight.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une demande de support à partir du [portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus dans votre abonnement Microsoft Azure. En outre, un support technique est fourni via l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
