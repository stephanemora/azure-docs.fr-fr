---
title: Utiliser Apache Beeline avec Apache Hive - Azure HDInsight
description: Découvrez comment utiliser le client Beeline pour exécuter des requêtes Hive avec Hadoop sur HDInsight. Beeline est un utilitaire qui permet d’utiliser HiveServer2 sur JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 77a451cb9f6598bbe7013f4215cfa7cab40186bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037542"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Utiliser le client Apache Beeline avec Apache Hive

Découvrez comment utiliser [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) pour exécuter des requêtes Apache Hive sur HDInsight.

Beeline est un client Hive inclus dans les nœuds principaux de votre cluster HDInsight. Pour savoir comment installer Beeline localement, voir [Installer un client Beeline](#install-beeline-client) ci-dessous. Beeline utilise JDBC pour se connecter à HiveServer2, un service hébergé sur votre cluster HDInsight. Vous pouvez également utiliser Beeline pour accéder à Hive sur HDInsight à distance via internet. Les exemples suivants présentent les chaînes de connexion les plus courantes utilisées pour se connecter à HDInsight à partir de Beeline.

## <a name="types-of-connections"></a>Types de connexions

### <a name="from-an-ssh-session"></a>À partir d’une session SSH

Lors de la connexion à partir d’une session SSH à un nœud principal de cluster, vous pouvez ensuite vous connecter à l’adresse `headnodehost` sur le port `10001` :

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Sur un réseau virtuel Azure

Lors de la connexion d’un client à HDInsight sur un réseau virtuel Azure, vous devez fournir le nom de domaine complet d’un nœud principal de cluster. Puisque cette connexion est établie directement aux nœuds de cluster, la connexion utilise le port `10001` :

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Remplacez `<headnode-FQDN>` par le nom de domaine complet d’un nœud principal de cluster. Pour rechercher le nom de domaine complet d’un nœud principal, utilisez les informations contenues dans le document [Gérer des clusters HDInsight à l’aide de l’API REST d’Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Vers un cluster du Pack Sécurité Entreprise (ESP) HDInsight à l'aide de Kerberos

Lors de la connexion d’un client à un cluster Pack Sécurité Entreprise (ESP) joint à Azure Active Directory (AAD) DS sur une machine dans le même domaine du cluster, vous devez également spécifier le nom de domaine `<AAD-Domain>` et le nom d’un compte d’utilisateur de domaine autorisé à accéder au cluster `<username>` :

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Remplacez `<username>` par le nom d’un compte de domaine autorisé à accéder au cluster. Remplacez `<AAD-DOMAIN>` par le nom d’Azure Active Directory (AAD) auquel le cluster est joint. Utilisez une chaîne en majuscules pour la valeur `<AAD-DOMAIN>`, sinon les informations d’identification ne seront pas trouvées. Vérifiez `/etc/krb5.conf` pour les noms de domaine si nécessaire.

Pour rechercher l’URL JDBC à partir d’Ambari :

1. Dans un navigateur web, accédez à `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`, où `CLUSTERNAME` est le nom de votre cluster. Vérifiez que HiveServer2 est en cours d’exécution.

1. Utilisez le Presse-papiers pour copier l’URL JDBC HiveServer2.

---

### <a name="over-public-or-private-endpoints"></a>Via des points de terminaison publics ou privés

En cas de connexion à un cluster utilisant des points de terminaison publics ou privés, vous devez fournir le nom de compte de connexion de cluster (par défaut `admin`) et le mot de passe. Par exemple, utilisation de Beeline à partir d’un système client pour se connecter à l’adresse `clustername.azurehdinsight.net`. Cette connexion est établie via le port `443` et est chiffrée à l’aide du protocole SSL.

Remplacez `clustername` par le nom de votre cluster HDInsight : Remplacez `admin` par le compte de connexion de votre cluster. Pour des clusters ESP, utilisez l’UPN complet (par exemple, user@domain.com). Remplacez `password` par le mot de passe du compte de connexion du cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

ou pour le point de terminaison privé :

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Les points de terminaison privés pointent vers un équilibreur de charge de base, qui est uniquement accessible à partir des réseaux virtuels associés dans la même région. Voir [constraints on global VNet peering and load balancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) (Contraintes liées à l’homologation Global VNet Peering et aux équilibreurs de charge) pour plus d’informations. Vous pouvez utiliser la commande `curl` avec l’option `-v` pour résoudre les problèmes de connectivité avec les points de terminaison publics ou privés avant d’utiliser Beeline.

---

### <a name="use-beeline-with-apache-spark"></a>Utiliser Beeline avec Apache Spark

Apache Spark fournit sa propre implémentation de HiveServer2, qui est quelquefois appelée « serveur Spark Thrift ». Ce service utilise Spark SQL pour résoudre les requêtes à la place de Hive, et peut offrir de meilleures performances selon la requête.

#### <a name="through-public-or-private-endpoints"></a>Via des points de terminaison publics ou privés

La chaîne de connexion utilisée est légèrement différente. Au lieu de contenir `httpPath=/hive2`, elle contient `httpPath/sparkhive2`. Remplacez `clustername` par le nom de votre cluster HDInsight : Remplacez `admin` par le compte de connexion de votre cluster. Pour des clusters ESP, utilisez l’UPN complet (par exemple, user@domain.com). Remplacez `password` par le mot de passe du compte de connexion du cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

ou pour le point de terminaison privé :

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Les points de terminaison privés pointent vers un équilibreur de charge de base, qui est uniquement accessible à partir des réseaux virtuels associés dans la même région. Voir [constraints on global VNet peering and load balancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) (Contraintes liées à l’homologation Global VNet Peering et aux équilibreurs de charge) pour plus d’informations. Vous pouvez utiliser la commande `curl` avec l’option `-v` pour résoudre les problèmes de connectivité avec les points de terminaison publics ou privés avant d’utiliser Beeline.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>À partir du cluster principal ou à l’intérieur du réseau virtuel Azure avec Apache Spark

Lorsque vous vous connectez directement depuis le nœud principal du cluster, ou à partir d’une ressource à l’intérieur du même réseau virtuel Azure que le cluster HDInsight, le port `10002` doit être utilisé pour le serveur Spark Thrift à la place de `10001`. L’exemple suivant montre comment se connecter directement au nœud principal :

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>Configuration requise pour les exemples

* Un cluster Hadoop sur HDInsight. Consultez [Bien démarrer avec HDInsight sur Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Notez le [schéma d’URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pour le stockage principal de votre cluster. Par exemple, `wasb://` pour Stockage Azure, `abfs://` pour Azure Data Lake Storage Gen2 ou `adl://` pour Azure Data Lake Storage Gen1. Si le transfert sécurisé est activé pour le stockage Azure, l’URI sera `wasbs://`. Pour plus d’informations, consultez l’article dédié au [transfert sécurisé](../../storage/common/storage-require-secure-transfer.md).

* Option 1 : Un client SSH. Pour plus d’informations, consultez [Se connecter à HDInsight (Apache Hadoop) à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). La plupart des étapes décrites dans ce document supposent que vous utilisez Beeline à partir d’une session SSH sur le cluster.

* Option n°2 :  Un client Beeline local.

## <a name="run-a-hive-query"></a>Exécution d'une tâche Hive

Cet exemple est basé sur l’utilisation du client Beeline à partir d’une connexion SSH.

1. Ouvrez une connexion SSH au cluster avec le code ci-dessous. Remplacez `sshuser` par l’utilisateur SSH de votre cluster, puis remplacez `CLUSTERNAME` par le nom de votre cluster. Lorsque vous y êtes invité, entrez le mot de passe du compte d’utilisateur SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Connectez-vous à HiveServer2 avec votre client Beeline à partir de votre session SSH ouverte en entrant la commande suivante :

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Les commandes Beeline commencent par un caractère `!`. Par exemple, `!help` affiche l’aide. Toutefois, `!` peut être omis pour certaines commandes. Par exemple, `help` fonctionne également.

    Il y a `!sql`, qui est utilisé pour exécuter des instructions HiveQL. Cependant, HiveQL est si souvent utilisé que vous pouvez omettre le `!sql`qui précède. Les deux instructions suivantes sont équivalentes :

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Sur un nouveau cluster, une seule table est listée : **hivesampletable**.

4. Utilisez la commande suivante pour afficher le schéma correspondant à hivesampletable :

    ```hiveql
    describe hivesampletable;
    ```

    Cette commande renvoie les informations suivantes :

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Ces informations décrivent les colonnes de la table.

5. Saisissez les instructions suivantes pour créer une table nommée **log4jLogs** avec les exemples de données fournies avec le cluster HDInsight : (Révisez si nécessaire en fonction de votre [schéma d’URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    Ces instructions effectuent les opérations suivantes :

    |. |Description |
    |---|---|
    |DROP TABLE|Si la table existe, elle est supprimée.|
    |CREATE EXTERNAL TABLE|Crée une table **externe** dans Hive. Les tables externes stockent uniquement la définition de table dans Hive. Les données restent à l'emplacement d'origine.|
    |ROW FORMAT|Formatage des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.|
    |STORED AS TEXTFILE LOCATION|Emplacement de stockage des données et format de fichier.|
    |SELECT|sélectionne toutes les lignes où la colonne **t4** contient la valeur **[ERROR]** . Cette requête renvoie la valeur **3**, car trois lignes contiennent cette valeur.|
    |INPUT__FILE__NAME LIKE '%.log'|Hive tente d’appliquer le schéma à tous les fichiers dans le répertoire. Dans ce cas, le répertoire contient des fichiers qui ne correspondent pas au schéma. Pour éviter que des données incorrectes n’apparaissent dans les résultats, cette instruction indique à Hive de retourner uniquement des données provenant de fichiers se terminant par .log.|

   > [!NOTE]  
   > Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, Par exemple, un processus de chargement de données automatisé ou une opération MapReduce.
   >
   > La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.

    Le résultat de la commande se présente ainsi :

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

6. Quittez Beeline :

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Exécuter un fichier HiveQL

Il s’agit de la suite de l’exemple précédent. Utilisez les étapes suivantes pour créer un fichier, puis exécutez-le à l’aide de Beeline.

1. Utilisez la commande suivante pour créer un fichier nommé **query.hql**:

    ```bash
    nano query.hql
    ```

1. Utilisez le texte ci-après comme contenu du fichier. Cette requête crée une table « interne » nommée **errorLogs** :

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Ces instructions effectuent les opérations suivantes :

    |. |Description |
    |---|---|
    |CREATE TABLE IF NOT EXISTS|Si la table n’existe pas, elle est créée. Étant donné que le mot-clé **EXTERNAL** n’est pas utilisé, cette instruction crée une table interne. Les tables internes sont stockées dans l’entrepôt de données Hive et entièrement gérées par Hive.|
    |STORED AS ORC|Stocke les données dans un format ORC (Optimized Row Columnar). Le format ORC est particulièrement efficace et optimisé pour le stockage de données Hive.|
    |INSERT OVERWRITE ... SELECT|sélectionne les lignes de la table **log4jLogs** qui contiennent **[ERROR]** , puis insère les données dans la table **errorLogs**.|

    > [!NOTE]  
    > Contrairement aux tables externes, la suppression d’une table interne entraîne également la suppression des données sous-jacentes.

1. Pour enregistrer le fichier, utilisez **Ctrl**+**X**, puis, entrez **Y** et enfin appuyez sur **Entrée**.

1. Pour exécuter le fichier à l’aide de Beeline, utilisez les éléments suivants :

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Le paramètre `-i` démarre Beeline et exécute les instructions dans le fichier `query.hql`. Une fois la requête terminée, vous accédez à l’invite `jdbc:hive2://headnodehost:10001/>`. Vous pouvez également exécuter un fichier avec le paramètre `-f`, qui ferme Beeline une fois la requête terminée.

1. Pour vérifier que la table **errorLogs** a été créée, utilisez l’instruction suivante pour renvoyer toutes les lignes à partir **d’errorLogs** :

    ```hiveql
    SELECT * from errorLogs;
    ```

    Trois lignes de données doivent normalement être renvoyées. Elles contiennent toutes **[ERROR]** dans la colonne t4 :

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>Installer un client Beeline

Bien que Beeline soit inclus sur les nœuds principaux de votre cluster HDInsight, vous pouvez l’installer sur un ordinateur local.  Les étapes d’installation de Beeline sur un ordinateur local décrites ci-dessous sont basées sur un [sous-système Windows pour Linux](https://docs.microsoft.com/windows/wsl/install-win10).

1. Mettez à jour vos listes de packages. Entrez la commande suivante dans votre interpréteur de commandes Bash :

    ```bash
    sudo apt-get update
    ```

1. Installez Java s’il n’est pas installé. Vous pouvez vérifier à l’aide de la commande `which java`.

    1. Si aucun package Java n’est installé, entrez la commande suivante :

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Ouvrez le fichier bashrc (généralement situé dans ~/.bashrc) : `nano ~/.bashrc`.

    1. Modifiez le fichier bashrc. Ajoutez la ligne suivante à la fin du fichier :

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Appuyez ensuite sur **Ctrl+X**, sur **Y**, puis sur Entrée.

1. Téléchargez les archives Hadoop et Beeline, puis entrez les commandes suivantes :

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Décompressez les archives, puis entrez les commandes suivantes :

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Modifiez le fichier bashrc. Vous devez identifier le chemin d’accès de l’emplacement où les archives ont été décompressées. Si vous utilisez le [sous-système Windows pour Linux](https://docs.microsoft.com/windows/wsl/install-win10) et avez scrupuleusement suivi les étapes, votre chemin doit être `/mnt/c/Users/user/`, où `user` est votre nom d’utilisateur.

    1. Ouvrez le fichier : `nano ~/.bashrc`

    1. Modifiez les commandes ci-dessous avec le chemin d’accès approprié, puis entrez-les à la fin du fichier bashrc :

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Appuyez ensuite sur **Ctrl+X**, sur **Y**, puis sur Entrée.

1. Fermez, puis rouvrez votre session bash.

1. Testez votre connexion. Utilisez le format de connexion à partir de [Via des points de terminaison publics ou privés](#over-public-or-private-endpoints) ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Hive dans HDInsight, consultez [Utiliser Apache Hive avec Apache Hadoop sur HDInsight](hdinsight-use-hive.md)

* Pour plus d’informations sur les autres façons de travailler avec Hadoop sur HDInsight, consultez [Utiliser MapReduce avec Apache Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
