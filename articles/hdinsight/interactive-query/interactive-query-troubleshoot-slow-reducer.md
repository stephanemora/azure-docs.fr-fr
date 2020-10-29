---
title: Le réducteur est lent dans Azure HDInsight
description: Le réducteur est lent dans Azure HDInsight à partir d’une éventuelle distorsion des données
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 2fbe68c5dfe9b0d5b60e1e3eeabc95b201b878c6
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532814"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Scénario : Le réducteur est lent dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Interactive Query dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Lorsque vous exécutez une requête comme `insert into table1 partition(a,b) select a,b,c from table2`, le plan de requête démarre un ensemble de regroupements, mais les données de chaque partition sont dirigées vers un seul réducteur. La requête est alors aussi lente que le temps pris par le réducteur de la plus grande partition.

## <a name="cause"></a>Cause

Ouvrez [beeline](../hadoop/apache-hadoop-use-hive-beeline.md) et vérifiez la valeur du `hive.optimize.sort.dynamic.partition`.

La valeur de cette variable est censée être définie sur true/false en fonction de la nature des données.

Si les partitions de la table d’entrée sont moins nombreuses (moins de 10), de même que le nombre de partitions de sortie, et que la variable est définie sur `true`, cela entraîne le tri global et l’écriture des données à l’aide d’un seul réducteur par partition. Même si le nombre de réducteurs disponibles est plus important, il est possible que quelques réducteurs soient en retard en raison de la distorsion des données et que le parallélisme maximal ne puisse pas être atteint. Une fois passé à `false`, plusieurs réducteurs peuvent traiter une seule partition et plusieurs fichiers plus petits sont écrits, ce qui accélère l’insertion. Cela peut affecter les requêtes ultérieures en raison de la présence de fichiers plus petits.

La valeur `true` est logique lorsque le nombre de partitions est plus grand et que les données ne sont pas inclinées. Dans ce cas, le résultat de la phase de mappage sera écrit de sorte que chaque partition sera gérée par un réducteur unique, ce qui entraînera de meilleures performances pour les requêtes ultérieures.

## <a name="resolution"></a>Résolution

1. Essayez de repartitionner les données pour normaliser en plusieurs partitions.

1. Si #1 n’est pas possible, définissez la valeur de la configuration sur false dans la session Beeline et recommencez la requête. `set hive.optimize.sort.dynamic.partition=false`. La définition de la valeur sur false au niveau du cluster n’est pas recommandée. La valeur de `true` est optimale et définit le paramètre selon les besoins en fonction de la nature des données et de la requête.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support** . Pour en savoir plus, voir [Création d’une requête de support Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).