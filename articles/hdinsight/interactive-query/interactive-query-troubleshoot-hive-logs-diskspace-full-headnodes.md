---
title: 'Résoudre les problèmes : Journaux Apache Hive saturant l’espace disque – Azure HDInsight'
description: Cet article décrit la procédure de dépannage à suivre lorsque les journaux Apache Hive saturent l’espace disque sur les nœuds principaux dans Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.date: 10/05/2020
ms.openlocfilehash: cd7e6a7f13f6cccb5be5d23d69c2a44fc655cf55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930956"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scénario : Journaux Apache Hive saturant l’espace disque sur les nœuds principaux dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes d’insuffisance de l’espace disque sur les nœuds principaux dans les clusters Azure HDInsight.

## <a name="issue"></a>Problème

Sur un cluster Apache Hive/LLAP, des journaux indésirables occupent la totalité de l’espace disque sur les nœuds principaux. Cette situation risque de provoquer les problèmes suivants :

- L’accès SSH échoue car il ne reste pas d’espace sur le nœud principal.
- Ambari lève *ERREUR HTTP : 503 Service indisponible*.
- Le redémarrage de HiveServer2 Interactive échoue.

Les journaux `ambari-agent` comportent les entrées suivantes lorsque le problème se produit :
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Cause

Dans les configurations Hive log4j avancées, la planification par défaut actuelle consiste à supprimer les fichiers de plus de 30 jours (date de dernière modification).

## <a name="resolution"></a>Résolution

1. Accédez au résumé des composants Hive sur le portail Ambari, puis sélectionnez l’onglet **Configurations**.

2. Accédez à la section `Advanced hive-log4j` dans **Paramètres avancés**.

3. Définissez le paramètre `appender.RFA.strategy.action.condition.age` sur l’âge de votre choix, dans cet exemple 14 jours : `appender.RFA.strategy.action.condition.age = 14D`.

4. Si vous ne voyez aucun paramètre associé, ajoutez les suivants :
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. Définissez `hive.root.logger` sur `INFO,RFA`, comme dans l’exemple suivant. Le paramètre par défaut est `DEBUG`, ce qui donne des journaux volumineux.

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. Enregistrez les configurations et redémarrez les composants nécessaires.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
