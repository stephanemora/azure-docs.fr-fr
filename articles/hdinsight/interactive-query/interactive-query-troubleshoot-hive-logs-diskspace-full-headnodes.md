---
title: 'Résoudre les problèmes : Journaux Hive saturant l’espace disque Azure HDInsight'
description: Cet article décrit la procédure de dépannage à suivre lorsque les journaux Apache Hive saturent l’espace disque sur les nœuds principaux dans Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: kcheeeung
ms.author: kecheung
ms.date: 05/21/2021
ms.openlocfilehash: d4fb0c4a197f28321e4328d17f00173f45b28d1b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290734"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scénario : Journaux Apache Hive saturant l’espace disque sur les nœuds principaux dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes d’insuffisance de l’espace disque sur les nœuds principaux dans les clusters Azure HDInsight.

## <a name="issue"></a>Problème

Dans un cluster Apache Hive/LLAP HDI 4.0, des journaux indésirables occupent la totalité de l’espace disque sur les nœuds principaux. Cette situation risque de provoquer les problèmes suivants :

- L’accès SSH échoue car il ne reste pas d’espace sur le nœud principal.
- Le redémarrage de HiveServer2 Interactive échoue.

## <a name="cause"></a>Cause

La suppression automatique du journal Hive n’est pas configurée dans les configurations hive-log4j2 avancées. La limite de taille par défaut de 60 Go prend trop d’espace pour le modèle d’utilisation du client.

## <a name="resolution"></a>Résolution

1. Accédez au résumé des composants Hive sur le portail Ambari, puis sélectionnez l’onglet **Configurations**.

2. Accédez à la section `Advanced hive-log4j2` dans **Paramètres avancés**.

3. Vérifiez que vous disposez de ces paramètres. Si vous ne voyez aucun paramètre associé, ajoutez les suivants :
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfFileName
    appender.RFA.strategy.action.condition.regex = hive*.*log.*
    appender.RFA.strategy.action.condition.nested_condition.type = IfAny
    # Deletes logs based on total accumulated size, keeping the most recent
    appender.RFA.strategy.action.condition.nested_condition.fileSize.type = IfAccumulatedFileSize
    appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds = 60GB
    # Deletes logs IfLastModified date is greater than number of days
    #appender.RFA.strategy.action.condition.nested_condition.lastMod.type = IfLastModified
    #appender.RFA.strategy.action.condition.nested_condition.lastMod.age = 30D
    ```

4. Nous allons passer en revue trois options de base avec la suppression basée sur :
- **Total Size**
    - Remplacez `appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds` par une limite de taille de votre choix.

- **Date**
    - Vous pouvez également supprimer les marques de commentaire et changer les conditions. Ensuite, passez `appender.RFA.strategy.action.condition.nested_condition.lastMod.age` à l’âge de votre choix.

    ```
    # Deletes logs based on total accumulated size, keeping the most recent 
    #appender.RFA.strategy.action.condition.nested_condition.fileSize.type = IfAccumulatedFileSize 
    #appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds = 60GB
    # Deletes logs IfLastModified date is greater than number of days 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.type = IfLastModified 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.age = 30D
    ```

- **Combinaison de la taille totale et de la date**
    - Vous pouvez combiner ces deux options en supprimer les marques de commentaire ci-dessous. Le log4j2 se comporte alors comme suit : démarrer la suppression des journaux lorsque l’une ou l’autre condition est remplie.
    
    ```
    # Deletes logs based on total accumulated size, keeping the most recent 
    appender.RFA.strategy.action.condition.nested_condition.fileSize.type = IfAccumulatedFileSize 
    appender.RFA.strategy.action.condition.nested_condition.fileSize.exceeds = 60GB
    # Deletes logs IfLastModified date is greater than number of days 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.type = IfLastModified 
    appender.RFA.strategy.action.condition.nested_condition.lastMod.age = 30D
    ```
5. Enregistrez les configurations et redémarrez les composants nécessaires.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
