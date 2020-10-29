---
title: La vue Apache Hive expire à partir du résultat de requête - Azure HDInsight
description: La vue Apache Hive expire lors de la récupération d’un résultat de requête dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 2ed60dc6404d16e5d7df302a0cc255e18f1f5b34
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534649"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Scénario : La vue Apache Hive expire lors de la récupération d’un résultat de requête dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Interactive Query dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Lors de l’exécution de certaines requêtes à partir de la vue Apache Hive, l’erreur suivante peut se produire :

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Cause

La valeur du délai d’expiration par défaut de la vue Hive peut ne pas convenir à la requête que vous exécutez. La période spécifiée est trop courte pour que la vue Hive récupère le résultat de la requête.

## <a name="resolution"></a>Résolution

Augmentez les délais d’expiration de la vue Apache Ambari Hive en définissant les propriétés suivantes dans `/etc/ambari-server/conf/ambari.properties`.

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

La valeur de `HIVE_VIEW_INSTANCE_NAME` est disponible à la fin de l’URL de la vue Hive.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support** . Pour en savoir plus, voir [Création d’une requête de support Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).