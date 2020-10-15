---
title: Problèmes de performances dans les requêtes LLAP Apache Hive dans Azure HDInsight
description: Les requêtes d’un LLAP Apache Hive s’exécutent plus lentement que prévu dans Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895126"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Scénario : Problèmes de performances dans les requêtes LLAP Apache Hive dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Interactive Query dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Les configurations de cluster par défaut ne sont pas suffisamment adaptées à votre charge de travail. Dans Hive LLAP, les requêtes s’exécutent plus lentement que prévu.

## <a name="cause"></a>Cause

Plusieurs raisons peuvent être à l’origine de ce problème.

## <a name="resolution"></a>Résolution

LLAP est optimisé pour les requêtes impliquant des jointures et des agrégats. Des requêtes telles que les suivantes ne fonctionnent pas correctement dans un cluster Hive interactif :

```
select * from table where column = "columnvalue"
```

Pour améliorer les performances des requêtes de pointage dans Hive LLAP, définissez les configurations suivantes :

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Vous pouvez également augmenter l’utilisation du cache LLAP pour améliorer les performances moyennant le changement de configuration suivant :

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une requête de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
