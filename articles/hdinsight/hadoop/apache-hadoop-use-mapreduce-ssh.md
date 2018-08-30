---
title: MapReduce et connexion SSH avec Hadoop dans HDInsight - Azure
description: Apprenez à utiliser le protocole SSH pour exécuter des tâches MapReduce avec Hadoop sur HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: jasonh
ms.openlocfilehash: 71472edf74fba433f24b83362b2880575b73ce85
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047321"
---
# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Utilisation de MapReduce avec Hadoop sur HDInsight avec SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Découvrez comment soumettre des tâches MapReduce à partir d’une connexion SSH (Secure Shell) vers HDInsight.

> [!NOTE]
> Si vous êtes déjà familiarisé avec l’utilisation de serveurs Hadoop sur Linux, mais pas avec HDInsight, consultez la rubrique [Informations sur l’utilisation de HDInsight sur Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Configuration requise

* un cluster HDInsight basé sur Linux (Hadoop sur HDInsight)

  > [!IMPORTANT]
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un client SSH. Pour en savoir plus, consultez [Utilisation de SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="ssh"></a>Connexion avec SSH

Connectez-vous au cluster à l'aide de SSH. Par exemple, la commande suivante permet de se connecter à un cluster nommé **myhdinsight** sous le compte **sshuser** :

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Si vous utilisez une clé de certificat pour l’authentification SSH** vous devrez peut-être spécifier l’emplacement de la clé privée sur votre système client, par exemple :

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.net
```

**Si vous utilisez un mot de passe pour l’authentification SSH**, vous devez fournir le mot de passe lorsque vous y êtes invité.

Pour en savoir plus sur l’utilisation de SSH avec HDInsight, voir [Utilisation de SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Utilisation de commandes Hadoop

1. Une fois connecté au cluster HDInsight, utilisez la commande suivante pour lancer une tâche MapReduce :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Cette commande démarre la classe `wordcount`, qui est contenue dans le fichier `hadoop-mapreduce-examples.jar`. Il utilise le document `/example/data/gutenberg/davinci.txt` comme entrée. La sortie est stockée dans `/example/data/WordCountOutput`.

    > [!NOTE]
    > Pour plus d’informations sur cette tâche MapReduce et sur les exemples de données, consultez la rubrique [Utilisation de MapReduce dans Hadoop sur HDInsight](hdinsight-use-mapreduce.md).

2. La tâche émet des informations lors de son traitement, avant de renvoyer des informations semblables au texte suivant lorsqu’elle est terminée :

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Une fois la tâche terminée, utilisez la commande suivante pour afficher les fichiers sortants :

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Cette commande affiche deux fichiers, `_SUCCESS` et `part-r-00000`. Le fichier `part-r-00000` contient le résultat pour cette tâche.

    > [!NOTE]
    > Certaines tâches MapReduce peuvent fractionner les résultats sur plusieurs fichiers **part-r-#####** . Dans ce cas, utilisez le suffixe ##### pour indiquer l’ordre des fichiers.

4. Pour afficher la sortie, utilisez la commande suivante :

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Cette commande affiche une liste des mots contenus dans le fichier **wasb://example/data/gutenberg/davinci.txt**, ainsi que le nombre d’occurrences de chaque mot. Le texte suivant est un exemple des données contenues dans le fichier :

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Résumé

Comme vous pouvez le voir, les commandes Hadoop fournissent un moyen facile pour exécuter des tâches MapReduce sur un cluster HDInsight avant d’afficher le résultat de la tâche.

## <a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight :

* [Utilisation de MapReduce dans Hadoop HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)
