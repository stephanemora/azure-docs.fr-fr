---
title: Migrer des charges de travail Azure HDInsight 3.6 Hive HDInsight 4.0
description: Découvrez comment migrer des charges de travail Apache Hive sur HDInsight 3.6 vers HDInsight 4.0.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 04/15/2019
ms.openlocfilehash: 144e0ada0ce3a15a5dcd13b31f46f50162750a74
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126560"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrer des charges de travail Azure HDInsight 3.6 Hive HDInsight 4.0

Ce document vous montre comment migrer des charges de travail Apache Hive et LLAP sur HDInsight 3.6 vers HDInsight 4.0. HDInsight 4.0 fournit des fonctionnalités plus récentes de Hive et LLAP telles que les vues matérialisées et la mise en cache de résultat de requête. Lorsque vous migrez vos charges de travail vers HDInsight 4.0, vous pouvez utiliser les nombreuses nouvelles fonctionnalités de 3 Hive ne sont pas disponibles sur HDInsight 3.6.

Cet article aborde les sujets suivants :

* Migration des métadonnées Hive HDInsight 4.0
* Migration sans échec de ACID et les tables non ACID
* Préservation Hive des stratégies de sécurité entre les versions de HDInsight
* L’exécution des requêtes et débogage à partir de HDInsight 3.6 de HDInsight 4.0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Migrer les métadonnées d’Apache Hive HDInsight 4.0

L’un des avantages de la ruche sont la possibilité d’exporter des métadonnées pour une base de données externe (également appelé le Metastore Hive). Le **Metastore Hive** est chargé de stocker les statistiques de table, y compris l’emplacement de stockage de table, les noms de colonnes et les informations sur les index de table. Le schéma de base de données de metastore diffère entre les versions de Hive. Procédez comme suit pour mettre à niveau d’un Metastore Hive de HDInsight 3.6 afin qu’il soit compatible avec HDInsight 4.0.

1. Créer une nouvelle copie de votre metastore externe. HDInsight 3.6 et 4.0 de HDInsight nécessitent metastore différents schémas et ne peuvent pas partager un seul metastore.
1. Attacher la nouvelle copie du metastore à a) un cluster HDInsight 4.0 existant, ou (b) d’un cluster que vous créez pour la première fois. Consultez [utiliser des magasins de métadonnées externes dans Azure HDInsight](../hdinsight-use-external-metadata-stores.md) pour en savoir plus sur l’attachement d’un metastore externe à un cluster HDInsight. Une fois que le Metastore est attaché, il sera automatiquement converti en un metastore 4.0 compatibles.

> [!Warning]
> La mise à niveau qui convertit le schéma de métadonnées HDInsight 3.6 au schéma HDInsight 4.0, ne peut pas être annulée.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrer des tables Hive à HDInsight 4.0

À l’issue de l’ensemble précédent de la procédure de migration du Metastore Hive HDInsight 4.0, les tables et les bases de données enregistrées dans le metastore sera visibles à partir du cluster HDInsight 4.0 en exécutant `show tables` ou `show databases` à partir du cluster . Consultez [exécution de la requête entre les versions de HDInsight](#query-execution-across-hdinsight-versions) pour plus d’informations sur l’exécution des requêtes dans des clusters HDInsight 4.0.

Les données réelles à partir des tables, toutefois, n’est pas accessibles tant que le cluster a accès aux comptes de stockage nécessaire. Pour vous assurer que votre cluster HDInsight 4.0 permettre accéder les mêmes données que votre ancien cluster HDInsight 3.6, procédez comme suit :

1. Déterminer le compte de stockage Azure de votre table ou à l’aide de la base de données décrivent mis en forme.
2. Si votre cluster HDInsight 4.0 est déjà en cours d’exécution, attachez le compte de stockage Azure au cluster via Ambari. Si vous n’avez pas encore créé le cluster HDInsight 4.0, assurez-vous que le compte de stockage Azure est spécifié en tant que le réplica principal ou un compte de stockage de cluster secondaire. Pour plus d’informations sur l’ajout de comptes de stockage pour les clusters HDInsight, consultez [ajouter des comptes de stockage supplémentaires à HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Les tables sont traitées différemment dans la version 3.6 de HDInsight et HDInsight 4.0. Pour cette raison, vous ne pouvez pas partager les mêmes tables pour les clusters de différentes versions. Si vous souhaitez utiliser HDInsight 3.6 en même temps que HDInsight 4.0, vous devez disposer des copies distinctes des données pour chaque version.

Votre charge de travail Hive peut inclure une combinaison de ACID et les tables non ACID. Une différence essentielle entre Hive sur HDInsight 3.6 (2 Hive) et Hive sur HDInsight 4.0 (Hive 3) est la conformité ACID pour les tables. Dans HDInsight 3.6, l’activation de ruche ACID-conformité nécessite une configuration supplémentaire, mais dans HDInsight 4.0 les tables sont ACID conformes par défaut. La seule action nécessaire avant la migration consiste à exécuter un compactage majeur par rapport à la table ACID sur le cluster 3.6. À partir de la vue Hive ou à partir de Beeline, exécutez la requête suivante :

```bash
alter table myacidtable compact 'major';
```

Cette compression est nécessaire, car les tables de la version 3.6 de HDInsight et HDInsight 4.0 ACID comprennent les deltas ACID différemment. Compactage applique vierge qui garantit la cohérence. La section 4 de la [documentation sur la migration de la ruche](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) contient des conseils pour la compression en bloc des tables de HDInsight 3.6 ACID.

Une fois que vous avez terminé les étapes de migration et de compactage de metastore, vous pouvez migrer l’entrepôt réelle. Après avoir effectué la migration de l’entrepôt Hive, l’entrepôt HDInsight 4.0 aura les propriétés suivantes :

* Les tables externes dans HDInsight 3.6 sera tables externes dans HDInsight 4.0
* Non transactionnel des tables gérées dans HDInsight 3.6 sera tables externes dans HDInsight 4.0
* Transactionnelles géré les tables dans HDInsight 3.6 seront être géré dans HDInsight 4.0

Vous devrez peut-être ajuster les propriétés de votre entrepôt avant d’exécuter la migration. Par exemple, si vous prévoyez que certains table est accessible par un tiers (par exemple, un cluster HDInsight 3.6), cette table doit être externe une fois la migration est terminée. Dans HDInsight, 4.0, toutes les tables gérés sont transactionnelles. Par conséquent, les tables gérés dans HDInsight 4.0 doivent uniquement être accessible par les clusters HDInsight 4.0.

Une fois que les propriétés de la table sont définies correctement, exécutez l’outil de migration de l’entrepôt Hive à partir d’un des nœuds principaux du cluster à l’aide de l’interpréteur de commandes SSH :

1. Se connecter à votre nœud principal du cluster à l’aide de SSH. Pour obtenir des instructions, consultez [se connecter à HDInsight à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Ouvrez un interpréteur de commandes de connexion en tant que l’utilisateur Hive en exécutant `sudo su - hive`
1. Déterminer la version de la pile Hortonworks Data Platform en exécutant `ls /usr/hdp`. Ceci affichera une chaîne de version que vous devez utiliser dans la commande suivante.
1. Exécutez la commande suivante à partir de l’interpréteur de commandes. Remplacez `${{STACK_VERSION}}` avec la chaîne de version de l’étape précédente :

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true  -m automatic  automatic  --modifyManagedTables --oldWarehouseRoot /apps/hive/warehouse
```

Une fois l’outil de migration terminée, votre entrepôt Hive sera prêt pour HDInsight 4.0. 

> [!Important]
> Tables gérées dans 4.0 HDInsight (y compris les tables migrées à partir de 3.6) ne doivent pas être accessible par d’autres services ou les applications, y compris les clusters HDInsight 3.6.

## <a name="secure-hive-across-hdinsight-versions"></a>Sécurisez Hive sur les versions de HDInsight

Depuis HDInsight 3.6, HDInsight s’intègre avec Azure Active Directory à l’aide de HDInsight Enterprise Security Package (ESP). ESP utilise Kerberos et Apache Ranger pour gérer les autorisations de ressources spécifiques au sein du cluster. Les stratégies ranger déployés sur Hive dans HDInsight 3.6 peuvent être migrés vers HDInsight 4.0 en procédant comme suit :

1. Accédez au panneau Ranger Service Manager dans votre cluster HDInsight 3.6.
2. Accédez à la stratégie nommée **HIVE** et exporter la stratégie vers un fichier json.
3. Assurez-vous que tous les utilisateurs fait référence dans le json de stratégie exportées existent dans le nouveau cluster. Si un utilisateur est appelé dans le json de stratégie, mais n’existe pas dans le nouveau cluster, ajoutez l’utilisateur vers le nouveau cluster ou supprimez la référence de la stratégie.
4. Accédez à la **Ranger Service Manager** Panneau de configuration de votre cluster HDInsight 4.0.
5. Accédez à la stratégie nommée **HIVE** et importer le fichier de stratégie ranger json de l’étape 2.

## <a name="query-execution-across-hdinsight-versions"></a>Exécution des requêtes entre les versions de HDInsight

Il existe deux façons d’exécuter et déboguer les requêtes Hive/LLAP au sein d’un cluster HDInsight 3.6. HiveCLI offre une expérience de ligne de commande et la vue de la vue/Hive Tez fournit un workflow basé sur l’interface utilisateur graphique.

Dans HDInsight 4.0, HiveCLI a été remplacée par Beeline. HiveCLI est un client de thrift pour Hive 1 et Beeline est un client JDBC qui fournit l’accès à Hive 2. Beeline peut également être utilisé pour se connecter à n’importe quel autre compatible avec JDBC de base de données point de terminaison. Beeline est disponible out-of-box sur HDInsight 4.0 sans aucune installation nécessitée.

Dans HDInsight 3.6, le client de l’interface graphique utilisateur permettant d’interagir avec le serveur Hive est l’affichage Ambari Hive. HDInsight 4.0 remplace la vue Hive avec Hortonworks données Analytique Studio (DAS). DAS n’est pas fourni avec les clusters de HDInsight out-of-box et n’est pas un package officiellement pris en charge. Toutefois, DAS peut être installé sur le cluster comme suit :

Lancez une action de script sur votre cluster, avec « Nœuds principaux » comme type de nœud pour l’exécution. Collez l’URI suivant dans la zone de texte marqué « URI de Script Bash » : https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh



Une fois DAS est installé, si vous ne voyez pas les requêtes que vous avez exécuté dans la visionneuse de requêtes, procédez comme suit :

1. Définissez les configurations pour Hive Tez et DAS, comme décrit dans [ce guide pour la résolution des problèmes d’installation de DAS](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Assurez-vous que les configurations de répertoire stockage Azure suivantes sont des objets BLOB de pages, et qu’ils sont répertoriés sous `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Redémarrez HDFS, Hive, Tez et DAS sur les deux nœuds principaux.

## <a name="next-steps"></a>Étapes suivantes

* [Annonce de HDInsight 4.0](../hdinsight-version-release.md)
* [Immersion dans HDInsight 4.0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [3 Tables ACID Hive](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
