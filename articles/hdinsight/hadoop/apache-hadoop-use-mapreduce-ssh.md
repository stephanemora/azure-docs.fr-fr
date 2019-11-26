---
title: MapReduce et connexion SSH avec Apache Hadoop – HDInsight
description: Apprenez à utiliser le protocole SSH pour exécuter des tâches MapReduce avec Apache Hadoop sur HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: b4075de1a184896d598c11d09ae2b2bda5e257ed
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044509"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Utilisation de MapReduce avec Apache Hadoop sur HDInsight avec SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Découvrez comment soumettre des tâches MapReduce à partir d’une connexion SSH (Secure Shell) vers HDInsight.

> [!NOTE]
> Si vous connaissez déjà l’utilisation de serveurs Apache Hadoop basés sur Linux, mais pas HDInsight, consultez la rubrique [Informations sur l’utilisation de HDInsight sur Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Configuration requise

* un cluster HDInsight basé sur Linux (Hadoop sur HDInsight)

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
    > Pour plus d’informations sur ce travail MapReduce et pour des exemples de données, consultez [Utiliser MapReduce dans Apache Hadoop sur HDInsight](hdinsight-use-mapreduce.md).

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

* [Utiliser Apache Hive avec Apache Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utiliser Apache Pig avec Apache Hadoop sur HDInsight](hdinsight-use-pig.md)
