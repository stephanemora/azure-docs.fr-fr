---
title: Erreur de l’interpréteur Apache Hive Zeppelin - Azure HDInsight
description: L’interpréteur JDBC Apache Zeppelin Hive pointe vers la mauvaise URL dans Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 04288fdde74da705f6127c2444b0dc7ac7a2a8dd
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290536"
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

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]