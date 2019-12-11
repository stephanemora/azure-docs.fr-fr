---
title: Migrer des charges de travail Azure HDInsight 3.6 Hive vers HDInsight 4.0
description: Découvrez comment migrer des charges de travail Apache Hive de HDInsight 3.6 vers HDInsight 4.0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 9f49a9224ed123b76f4d300c27a8dd5822e50ea3
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706018"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrer des charges de travail Azure HDInsight 3.6 Hive vers HDInsight 4.0

Ce document explique comment migrer des charges de travail Apache Hive et LLAP de HDInsight 3.6 vers HDInsight 4.0. HDInsight 4.0 fournit des fonctionnalités plus récentes de Hive et LLAP, telles que les vues matérialisées et la mise en cache des résultats de requête. Lorsque vous migrez vos charges de travail vers HDInsight 4.0, vous pouvez utiliser les nombreuses nouvelles fonctionnalités de Hive 3 qui ne sont pas disponibles sur HDInsight 3.6.

Cet article aborde les sujets suivants :

* Migration des métadonnées Hive vers HDInsight 4.0
* Migration sécurisée des tables ACID et non ACID
* Préservation des stratégies de sécurité Hive entre les différentes versions de HDInsight
* Exécution des requêtes et débogage de HDInsight 3.6 vers HDInsight 4.0

L’un des avantages de Hive est la possibilité d’exporter des métadonnées vers une base de données externe (également appelée metastore Hive). Le **metastore Hive** est chargé de stocker les statistiques de table, y compris l’emplacement de stockage des tables, les noms de colonnes et les informations sur les index de table. Le schéma de base de données du metastore diffère selon les versions de Hive. La méthode recommandée pour mettre à niveau le metastore Hive en toute sécurité consiste à créer une copie et à mettre à niveau la copie au lieu de l’environnement de production actuel.

## <a name="copy-metastore"></a>Copier le metastore

HDInsight 3.6 et HDInsight 4.0 nécessitent différents schémas de metastore et ne peuvent pas partager un seul metastore.

### <a name="external-metastore"></a>Metastore externe

Créez une nouvelle copie du metastore externe. Si vous utilisez un metastore externe, l’un des moyens les plus sûrs et les plus simples d’effectuer une copie du metastore consiste à [restaurer la base de données](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) avec un nom différent à l’aide de la fonction restore de la base de données SQL.  Consultez [Utiliser des magasins de métadonnées externes dans Azure HDInsight](../hdinsight-use-external-metadata-stores.md) pour en savoir plus sur l’association d’un metastore externe à un cluster HDInsight.

### <a name="internal-metastore"></a>Metastore interne

Si vous utilisez le metastore interne, vous pouvez utiliser des requêtes pour exporter des définitions d’objets dans le metastore Hive et les importer dans une nouvelle base de données.

1. Connectez-vous au cluster HDInsight à l’aide d’un [client Secure Shell (SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Connectez-vous à HiveServer2 avec votre [client Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) à partir de votre session SSH ouverte en entrant la commande suivante :

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; do echo "create database $d; use $d;" >> alltables.sql; for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"` ; do ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

    Cette commande génère un fichier nommé **alltables.sql**. Étant donné que la base de données par défaut ne peut pas être supprimée ou recréée, supprimez l’instruction `create database default;` dans **alltables.sql**.

1. Fermez votre session SSH. Entrez, ensuite une commande scp pour télécharger **alltables.sql** localement.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.sql c:/hdi
    ```

1. Téléchargez **alltables.sql** dans le *nouveau* cluster HDInsight.

    ```bash
    scp c:/hdi/alltables.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Utilisez ensuite SSH pour vous connecter au *nouveau* cluster HDInsight. Exécutez le code suivant à partir de la session SSH :

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.sql
    ```

## <a name="upgrade-metastore"></a>Mettre à niveau le metastore

Une fois que la **copie** du metastore est terminée, exécutez un script de mise à niveau de schéma dans [Action de script](../hdinsight-hadoop-customize-cluster-linux.md) sur le cluster HDInsight 3.6 existant pour mettre à niveau le nouveau metastore vers le schéma Hive 3. Cela permet à la base de données d’être attachée en tant que metastore HDInsight 4.0.

Utilisez les valeurs du tableau plus loin. Remplacez `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` par les valeurs appropriées pour le metastore Hive **copié**, en les séparant par des espaces. N’incluez pas « .database.windows.net » lorsque vous spécifiez le nom du serveur SQL.

|Propriété | Valeur |
|---|---|
|Type de script|- Personnalisé|
|Nom|Mise à niveau de Hive|
|URI de script bash|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Type(s) de nœud|Nœud principal|
|parameters|SQLSERVERNAME DATABASENAME USERNAME PASSWORD|

> [!Warning]  
> La mise à niveau qui convertit le schéma de métadonnées HDInsight 3.6 vers le schéma HDInsight 4.0 ne peut pas être annulée.

Vous pouvez vérifier la mise à niveau en exécutant la requête SQL suivante sur la base de données :

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrer des tables Hive vers HDInsight 4.0

Après avoir suivi l’ensemble des étapes précédentes de la procédure de migration du metastore Hive vers HDInsight 4.0, les tables et bases de données enregistrées dans le metastore seront visibles dans le cluster HDInsight 4.0 en exécutant `show tables` ou `show databases` depuis le cluster. Consultez [Exécution de requêtes entre les versions HDInsight](#query-execution-across-hdinsight-versions) pour plus d’informations sur l’exécution des requêtes dans des clusters HDInsight 4.0.

Toutefois, les données réelles des tables ne sont pas accessibles tant que le cluster n’a pas accès aux comptes de stockage nécessaires. Pour vous assurer que le cluster HDInsight 4.0 peut accéder aux mêmes données que votre ancien cluster HDInsight 3.6, procédez comme suit :

1. Déterminez le compte de stockage Azure de votre table ou base de données.

1. Si votre cluster HDInsight 4.0 est déjà en cours d’exécution, associez le compte de stockage Azure au cluster via Ambari. Si vous n’avez pas encore créé le cluster HDInsight 4.0, assurez-vous que le compte de stockage Azure est spécifié en tant que compte de stockage de cluster principal ou secondaire. Pour plus d’informations sur l’ajout de comptes de stockage pour les clusters HDInsight, consultez [Ajouter des comptes de stockage supplémentaires à HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Déployez New HDInsight 4.0 et connectez-vous au nouveau metastore

Une fois la mise à niveau du schéma terminée, déployez un nouveau cluster HDInsight 4.0 et connectez le metastore mis à niveau. Si vous avez déjà déployé un cluster 4.0, configurez-le de sorte que vous puissiez vous connecter au metastore depuis Ambari.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Exécuter le script de migration de schéma à partir de HDInsight 4.0

Les tables sont traitées différemment dans les versions HDInsight 3.6 et HDInsight 4.0. Pour cette raison, vous ne pouvez pas partager les mêmes tables pour les clusters de différentes versions. Si vous souhaitez utiliser HDInsight 3.6 en même temps que HDInsight 4.0, vous devez disposer des copies distinctes des données pour chaque version.

Votre charge de travail Hive peut inclure une combinaison de tables ACID et de tables non ACID. Une différence essentielle entre Hive sur HDInsight 3.6 (Hive 2) et sur HDInsight 4.0 (Hive 3) est la conformité ACID pour les tables. Dans HDInsight 3.6, la conformité ACID Hive nécessite une configuration supplémentaire, mais dans HDInsight 4.0, les tables sont compatibles ACID par défaut. La seule action nécessaire avant la migration consiste à exécuter un compactage majeur par rapport à la table ACID sur le cluster 3.6. À partir de l’affichage Hive ou de Beeline, exécutez la requête suivante :

```sql
alter table myacidtable compact 'major';
```

Ce compactage est nécessaire, car les tables ACID de HDInsight 3.6 et HDInsight 4.0 comprennent les écarts ACID différemment. Le compactage applique une réinitialisation qui garantit la cohérence. La section 4 de la [documentation sur la migration Hive](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) contient des conseils pour le compactage en bloc des tables ACID HDInsight 3.6.

Une fois que vous avez terminé les étapes de migration et de compactage du metastore, vous pouvez migrer l’entrepôt. Après avoir effectué la migration de l’entrepôt Hive, l’entrepôt HDInsight 4.0 aura les propriétés suivantes :

|3.6 |4.0 |
|---|---|
|Tables externes|Tables externes|
|Tables gérées non transactionnelles|Tables externes|
|Tables gérées transactionnelles|Tables gérées|

Vous devrez peut-être ajuster les propriétés de votre entrepôt avant d’exécuter la migration. Par exemple, si vous souhaitez que certaines tables soient accessibles par un tiers (par exemple, un cluster HDInsight 3.6), cette table doit être externe une fois la migration terminée. Dans HDInsight 4.0, toutes les tables managées sont transactionnelles. Par conséquent, les tables managées dans HDInsight 4.0 doivent uniquement être accessibles par les clusters HDInsight 4.0.

Une fois les propriétés de la table définies correctement, exécutez l’outil de migration de l’entrepôt Hive à partir d’un des nœuds principaux du cluster à l’aide de l’interpréteur de commandes SSH :

1. Connectez-vous au nœud principal du cluster à l’aide de SSH. Pour obtenir des instructions, consultez [Se connecter à HDInsight à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Ouvrez un interpréteur de commandes de connexion en tant qu’utilisateur Hive en exécutant `sudo su - hive`
1. Déterminez la version de la pile de la plateforme de données en exécutant `ls /usr/hdp`. Cela affichera une chaîne de version que vous devez utiliser dans la commande suivante.
1. Exécutez la commande suivante à partir de l’interpréteur de commandes. Remplacez `STACK_VERSION` par la chaîne de version obtenue à l’étape précédente :

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Une fois l’outil de migration terminé, votre entrepôt Hive sera prêt pour HDInsight 4.0.

> [!Important]  
> Les tables managées dans HDInsight 4.0 (y compris les tables migrées depuis la version 3.6) ne doivent pas être accessibles par d’autres services ou applications, y compris les clusters HDInsight 3.6.

## <a name="secure-hive-across-hdinsight-versions"></a>Sécuriser Hive sur les versions HDInsight

Depuis HDInsight 3.6, HDInsight s’intègre avec Azure Active Directory à l’aide du Pack Sécurité Entreprise (ESP) HDInsight. ESP utilise Kerberos et Apache Ranger à gérer les autorisations de ressources spécifiques au sein du cluster. Les stratégies Ranger déployées sur Hive dans HDInsight 3.6 peuvent être migrées vers HDInsight 4.0 en procédant comme suit :

1. Accédez au panneau Ranger Service Manager dans votre cluster HDInsight 3.6.
2. Accédez à la stratégie nommée **HIVE** et exportez la stratégie vers un fichier json.
3. Assurez-vous que tous les utilisateurs référencés dans le fichier json de stratégie exporté existent dans le nouveau cluster. Si un utilisateur est référencé dans le fichier json de stratégie, mais n’existe pas dans le nouveau cluster, ajoutez l’utilisateur dans le nouveau cluster ou supprimez la référence de la stratégie.
4. Accédez au panneau **Ranger Service Manager** de votre cluster HDInsight 4.0.
5. Accédez à la stratégie nommée **HIVE** et importez le fichier json de stratégie Ranger de l’étape 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Vérifier la compatibilité et modifier les codes si nécessaire dans l’application de test

Lors de la migration de charges de travail comme des programmes et des requêtes, consultez les notes de publication et la documentation pour découvrir les modifications et appliquer les modifications nécessaires. Si votre cluster HDInsight 3.6 utilise un metastore Spark et Hive partagé, [une configuration supplémentaire à l’aide de Hive Warehouse Connector](./apache-hive-warehouse-connector.md) est nécessaire.

## <a name="deploy-new-app-for-production"></a>Déployer une nouvelle application pour production

Pour basculer vers le nouveau cluster, vous pouvez installer une nouvelle application cliente et l’utiliser comme nouvel environnement de production, ou vous pouvez mettre à niveau votre application cliente existante et basculer vers HDInsight 4.0.

## <a name="switch-hdinsight-40-to-the-production"></a>Basculer HDInsight 4.0 en production

Si des différences ont été créées dans le metastore pendant le test, vous devez mettre à jour les modifications juste avant de basculer. Dans ce cas, vous pouvez exporter & importer le metastore, puis procéder à une nouvelle mise à niveau.

## <a name="remove-the-old-production"></a>Supprimer l’ancienne production

Une fois que vous avez confirmé que la mise en production était terminée et entièrement opérationnelle, vous pouvez supprimer la version 3.6 et le metastore précédent. Vérifiez que tout est migré avant de supprimer l’environnement.

## <a name="query-execution-across-hdinsight-versions"></a>Exécution de requêtes sur les versions HDInsight

Il existe deux façons d’exécuter et de déboguer les requêtes Hive/LLAP au sein d’un cluster HDInsight 3.6. L’interface CLI Hive propose une expérience de ligne de commande et les affichages Hive/Tez fournissent un flux de travail basé sur une interface utilisateur graphique.

Dans HDInsight 4.0, l’interface CLI Hive a été remplacée par BeeLine. HiveCLI est un client Thrift pour Hiveserver 1, et Beeline est un client JDBC qui fournit l’accès à Hiveserver 2. Beeline peut également être utilisé pour se connecter à n’importe quel autre point de terminaison de base de données compatible avec JDBC. Beeline est disponible de manière prédéfinie sur HDInsight 4.0 sans aucune installation nécessaire.

Dans HDInsight 3.6, le client de l’interface graphique utilisateur permettant d’interagir avec le serveur Hive est l’affichage Ambari Hive. HDInsight 4.0 n’est pas fourni avec la vue Ambari. Nous avons fourni à nos clients un moyen d’utiliser Data Analytics Studio (DAS), qui n’est pas un service HDInsight principal. DAS n’est pas intégré aux clusters HDInsight et n’est pas un package pris en charge officiellement. Toutefois, vous pouvez installer DAS sur le cluster à l’aide d’une [action de script](../hdinsight-hadoop-customize-cluster-linux.md) comme suit :

|Propriété | Valeur |
|---|---|
|Type de script|- Personnalisé|
|Nom|DAS|
|URI de script bash|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Type(s) de nœud|Nœud principal|

Patientez 5 à 10 minutes, puis lancez Data Analytics Studio à l'aide de l'URL suivante : `https://CLUSTERNAME.azurehdinsight.net/das/`.

Une fois DAS installé, si vous ne voyez pas les requêtes que vous avez exécutées dans la visionneuse de requêtes, procédez comme suit :

1. Définissez les configurations pour Hive, Tez et DAS, comme décrit dans [ce guide pour la résolution des problèmes d’installation DAS](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Assurez-vous que les configurations du répertoire de stockage Azure suivantes sont des objets blob de pages, et qu’elles sont répertoriées sous `fs.azure.page.blob.dirs` :
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Redémarrez HDFS, Hive, Tez et DAS sur les deux nœuds principaux.

## <a name="next-steps"></a>Étapes suivantes

* [Annonce d’HDInsight 4.0](../hdinsight-version-release.md)
* [Présentation approfondie d’HDInsight 4.0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tables ACID Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
