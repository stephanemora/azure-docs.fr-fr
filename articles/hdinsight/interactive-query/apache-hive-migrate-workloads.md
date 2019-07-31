---
title: Migrer des charges de travail Azure HDInsight 3.6 Hive vers HDInsight 4.0
description: Découvrez comment migrer des charges de travail Apache Hive de HDInsight 3.6 vers HDInsight 4.0.
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: b9bf3b41bcd0a79027c5dd9a4f3df979fb0bd9f0
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250150"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrer des charges de travail Azure HDInsight 3.6 Hive vers HDInsight 4.0

Ce document explique comment migrer des charges de travail Apache Hive et LLAP de HDInsight 3.6 vers HDInsight 4.0. HDInsight 4.0 fournit des fonctionnalités plus récentes de Hive et LLAP, telles que les vues matérialisées et la mise en cache des résultats de requête. Lorsque vous migrez vos charges de travail vers HDInsight 4.0, vous pouvez utiliser les nombreuses nouvelles fonctionnalités de Hive 3 qui ne sont pas disponibles sur HDInsight 3.6.

Cet article aborde les sujets suivants :

* Migration des métadonnées Hive vers HDInsight 4.0
* Migration sécurisée des tables ACID et non ACID
* Préservation des stratégies de sécurité Hive entre les différentes versions de HDInsight
* Exécution des requêtes et débogage de HDInsight 3.6 vers HDInsight 4.0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Migrer des métadonnées Apache Hive vers HDInsight 4.0

L’un des avantages de Hive est la possibilité d’exporter des métadonnées vers une base de données externe (également appelée metastore Hive). Le **metastore Hive** est chargé de stocker les statistiques de table, y compris l’emplacement de stockage des tables, les noms de colonnes et les informations sur les index de table. Le schéma de base de données du metastore diffère selon les versions de Hive. Procédez comme suit pour mettre à niveau un metastore Hive HDInsight 3.6 afin qu’il soit compatible avec HDInsight 4.0.

1. Créez une nouvelle copie du metastore externe. HDInsight 3.6 et HDInsight 4.0 nécessitent différents schémas de metastore et ne peuvent pas partager un seul metastore. Consultez [Utiliser des magasins de métadonnées externes dans Azure HDInsight](../hdinsight-use-external-metadata-stores.md) pour en savoir plus sur l’association d’un metastore externe à un cluster HDInsight. 
2. Lancez une action de script sur votre cluster HDI 3.6, avec « Nœuds principaux » comme type de nœud pour l’exécution. Collez l’URI suivant dans la zone de texte marqué « URI de script Bash » : https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh. Dans la zone de texte marquée « Arguments », entrez le nom du serveur, la base de données, le nom d’utilisateur et le mot de passe du metastore Hive **copié**, séparés par des espaces. N’incluez pas « .database.windows.net » lorsque vous spécifiez le nom du serveur.

> [!Warning]
> La mise à niveau qui convertit le schéma de métadonnées HDInsight 3.6 vers le schéma HDInsight 4.0 ne peut pas être annulée.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrer des tables Hive vers HDInsight 4.0

Après avoir suivi l’ensemble des étapes précédentes de la procédure de migration du metastore Hive vers HDInsight 4.0, les tables et bases de données enregistrées dans le metastore seront visibles dans le cluster HDInsight 4.0 en exécutant `show tables` ou `show databases` depuis le cluster. Consultez [Exécution de requêtes entre les versions HDInsight](#query-execution-across-hdinsight-versions) pour plus d’informations sur l’exécution des requêtes dans des clusters HDInsight 4.0.

Toutefois, les données réelles des tables ne sont pas accessibles tant que le cluster n’a pas accès aux comptes de stockage nécessaires. Pour vous assurer que le cluster HDInsight 4.0 peut accéder aux mêmes données que votre ancien cluster HDInsight 3.6, procédez comme suit :

1. Déterminez le compte de stockage Azure de votre table ou base de données à l’aide de describe formatted.
2. Si votre cluster HDInsight 4.0 est déjà en cours d’exécution, associez le compte de stockage Azure au cluster via Ambari. Si vous n’avez pas encore créé le cluster HDInsight 4.0, assurez-vous que le compte de stockage Azure est spécifié en tant que compte de stockage de cluster principal ou secondaire. Pour plus d’informations sur l’ajout de comptes de stockage pour les clusters HDInsight, consultez [Ajouter des comptes de stockage supplémentaires à HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Les tables sont traitées différemment dans les versions HDInsight 3.6 et HDInsight 4.0. Pour cette raison, vous ne pouvez pas partager les mêmes tables pour les clusters de différentes versions. Si vous souhaitez utiliser HDInsight 3.6 en même temps que HDInsight 4.0, vous devez disposer des copies distinctes des données pour chaque version.

Votre charge de travail Hive peut inclure une combinaison de tables ACID et de tables non ACID. Une différence essentielle entre Hive sur HDInsight 3.6 (Hive 2) et sur HDInsight 4.0 (Hive 3) est la conformité ACID pour les tables. Dans HDInsight 3.6, la conformité ACID Hive nécessite une configuration supplémentaire, mais dans HDInsight 4.0, les tables sont compatibles ACID par défaut. La seule action nécessaire avant la migration consiste à exécuter un compactage majeur par rapport à la table ACID sur le cluster 3.6. À partir de l’affichage Hive ou de Beeline, exécutez la requête suivante :

```bash
alter table myacidtable compact 'major';
```

Ce compactage est nécessaire, car les tables ACID de HDInsight 3.6 et HDInsight 4.0 comprennent les écarts ACID différemment. Le compactage applique une réinitialisation qui garantit la cohérence. La section 4 de la [documentation sur la migration Hive](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) contient des conseils pour le compactage en bloc des tables ACID HDInsight 3.6.

Une fois que vous avez terminé les étapes de migration et de compactage du metastore, vous pouvez migrer l’entrepôt. Après avoir effectué la migration de l’entrepôt Hive, l’entrepôt HDInsight 4.0 aura les propriétés suivantes :

* Les tables externes dans HDInsight 3.6 seront des tables externes dans HDInsight 4.0
* Les tables managées non transactionnelles dans HDInsight 3.6 seront des tables externes dans HDInsight 4.0
* Les tables managées transactionnelles dans HDInsight 3.6 seront des tables managées dans HDInsight 4.0

Vous devrez peut-être ajuster les propriétés de votre entrepôt avant d’exécuter la migration. Par exemple, si vous souhaitez que certaines tables soient accessibles par un tiers (par exemple, un cluster HDInsight 3.6), cette table doit être externe une fois la migration terminée. Dans HDInsight 4.0, toutes les tables managées sont transactionnelles. Par conséquent, les tables managées dans HDInsight 4.0 doivent uniquement être accessibles par les clusters HDInsight 4.0.

Une fois les propriétés de la table définies correctement, exécutez l’outil de migration de l’entrepôt Hive à partir d’un des nœuds principaux du cluster à l’aide de l’interpréteur de commandes SSH :

1. Connectez-vous au nœud principal du cluster à l’aide de SSH. Pour obtenir des instructions, consultez [Se connecter à HDInsight à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Ouvrez un interpréteur de commandes de connexion en tant qu’utilisateur Hive en exécutant `sudo su - hive`
1. Déterminez la version de la pile Hortonworks Data Platform en exécutant `ls /usr/hdp`. Cela affichera une chaîne de version que vous devez utiliser dans la commande suivante.
1. Exécutez la commande suivante à partir de l’interpréteur de commandes. Remplacez `${{STACK_VERSION}}` par la chaîne de version obtenue à l’étape précédente :

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true  -m automatic  automatic  --modifyManagedTables --oldWarehouseRoot /apps/hive/warehouse
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

## <a name="query-execution-across-hdinsight-versions"></a>Exécution de requêtes sur les versions HDInsight

Il existe deux façons d’exécuter et de déboguer les requêtes Hive/LLAP au sein d’un cluster HDInsight 3.6. L’interface CLI Hive propose une expérience de ligne de commande et les affichages Hive/Tez fournissent un flux de travail basé sur une interface utilisateur graphique.

Dans HDInsight 4.0, l’interface CLI Hive a été remplacée par BeeLine. HiveCLI est un client Thrift pour Hiveserver 1, et Beeline est un client JDBC qui fournit l’accès à Hiveserver 2. Beeline peut également être utilisé pour se connecter à n’importe quel autre point de terminaison de base de données compatible avec JDBC. Beeline est disponible de manière prédéfinie sur HDInsight 4.0 sans aucune installation nécessaire.

Dans HDInsight 3.6, le client de l’interface graphique utilisateur permettant d’interagir avec le serveur Hive est l’affichage Ambari Hive. HDInsight 4.0 remplace l’affichage Hive par Hortonworks Data Analytics Studio (DAS). DAS n’est pas fourni prédéfini avec les clusters HDInsight et n’est pas un package officiellement pris en charge. Toutefois, DAS peut être installé sur le cluster comme suit :

Lancez une action de script sur votre cluster, avec « Nœuds principaux » comme type de nœud pour l’exécution. Collez l’URI suivant dans la zone de texte marqué « URI de script Bash » : https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

Data Analytics Studio peut être lancé avec l’URL https://\<nomcluster>.azurehdinsight.net/das/



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
