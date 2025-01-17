---
title: Erreur de refus d’autorisation avec une table Apache Hive dans Azure HDInsight
description: Erreur de refus d’autorisation lors de la tentative de création d’une table Apache Hive dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: bd9f4f6da3ee82947192041cefe40261a182341b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290644"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Scénario : Erreur de refus d’autorisation lors de la tentative de création d’une table Apache Hive dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Interactive Query dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

L’erreur suivante s’affiche lors de la tentative de création d’une table :

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Vous verrez un message d’erreur similaire si vous exécutez la commande de stockage HDFS suivante :

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Cause

La possibilité de créer une table dans Apache Hive est déterminée par les autorisations appliquées au compte de stockage du cluster. Si les autorisations du compte de stockage de cluster sont incorrectes, vous ne pourrez pas créer de tables. Cela signifie que vous pouvez avoir les stratégies Ranger appropriées pour la création de table et voir néanmoins des erreurs « Autorisation refusée ».

## <a name="resolution"></a>Résolution

Cela est dû à un manque d’autorisations suffisantes sur le conteneur de stockage utilisé. L’utilisateur qui crée la table Hive a besoin d’autorisations de lecture, d’écriture et d’exécution sur le conteneur. Pour plus d’informations, consultez [Best Practices for Hive Authorization Using Apache Ranger in HDP 2.2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]