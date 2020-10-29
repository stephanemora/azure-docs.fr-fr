---
title: Erreur de l’interpréteur Apache Hive Zeppelin - Azure HDInsight
description: L’interpréteur JDBC Apache Zeppelin Hive pointe vers la mauvaise URL dans Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: a5dd7d9f292036af00140d89347678e5e548b8d9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534632"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Scénario : L’interpréteur Apache Hive Zeppelin génère une erreur Zookeeper dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Interactive Query dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Sur un cluster Apache Hive LLAP, l’interpréteur Zeppelin génère le message d’erreur suivant lors de la tentative d’exécution d’une requête :

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Cause

l’interpréteur JDBC Zeppelin Hive pointe vers une URL incorrecte.

## <a name="resolution"></a>Résolution

1. accédez au résumé du composant Hive et copiez l’URL JDBC Hive dans le presse-papiers.

1. Accédez à `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`.

1. Modifiez les paramètres JDBC : mettez à jour la valeur hive.url sur l’URL JDBC Hive copiée à l’étape 1

1. Enregistrez, puis réessayer d’exécuter la requête

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support** . Pour en savoir plus, voir [Création d’une requête de support Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).