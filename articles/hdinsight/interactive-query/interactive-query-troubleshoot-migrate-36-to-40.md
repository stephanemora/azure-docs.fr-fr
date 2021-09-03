---
title: Résolution des problèmes de migration de Hive de 3.6 vers 4.0 – Azure HDInsight
description: Guide de résoudre des problèmes pour la migration des charges de travail Hive de HDInsight 3.6 vers 4.0
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/12/2021
ms.openlocfilehash: eb19f3bd726efe018b4c593f324eb1cacd2cc2c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562116"
---
# <a name="troubleshooting-guide-for-migration-of-hive-workloads-from-hdinsight-36-to-hdinsight-40"></a>Guide de dépannage pour la migration des charges de travail Hive de HDInsight 3.6 vers HDInsight 4.0

Cet article fournit des réponses à certains des problèmes les plus courants auxquels les clients font face lors de la migration de charges de travail Hive de HDInsight 3.6 vers HDInsight 4.0.

## <a name="reduce-latency-when-running-describe-table_name"></a>Réduire la latence lors de l’exécution `DESCRIBE TABLE_NAME`

Solution de contournement :
* Augmentez le nombre maximal d’objets (tables/partitions) qui peuvent être récupérés à partir de metastore en un seul lot. Définissez-le sur grand nombre (la valeur par défaut est 300) jusqu’à ce que les niveaux de latence satisfaisants soient atteints. Plus le nombre est élevé, moins il y a d’allers-retours au serveur metastore Hive, mais cela peut également entraîner une plus grande quantité de mémoire nécessaire côté client.

    ```hive.metastore.batch.retrieve.max=2000```
* Redémarrer Hive et tous les services obsolètes

## <a name="unable-to-query-gzipped-text-file-if-skipheaderlinecount-and-skipfooterlinecount-are-set-for-table"></a>Impossible d’interroger le fichier texte gzippé si skip.header.line.count et skip.footer.line.count sont définis pour table

Le problème a été résolu dans Interactive Query 4.0 mais pas encore dans Interactive Query 3.1.0

Solution de contournement :
* Créez une table sans utiliser ```"skip.header.line.count"="1"``` et ```"skip.footer.line.count"="1"```, puis créez une vue à partir de la table d’origine qui exclut la ligne d’en-tête/de pied de page dans la requête.

## <a name="unable-to-use-unicode-characters"></a>Impossible d’utiliser des caractères Unicode

Solution de contournement :
1. Connectez-vous à la base de données de metastore hive pour votre cluster.

2. Prenez la sauvegarde des tables `TBLS` et `TABLE_PARAMS` en utilisant la commande suivante :
    ```sql
        select * into tbls_bak from tbls;
        select * into table_params_bak from table_params;
    ```

3. Remplacez manuellement les types de colonnes affectés par `nvarchar(max)`.
    ```sql 
        alter table TABLE_PARAMS alter column PARAM_VALUE nvarchar(max);
        alter table TBLS alter column VIEW_EXPANDED_TEXT nvarchar(max) null;    
        alter table TBLS alter column VIEW_ORIGINAL_TEXT nvarchar(max) null;
    ```

## <a name="create-table-as-select-ctas-creates-a-new-table-with-same-uuid"></a>La fonction Create Table As Select (CTAS) crée une nouvelle table avec le même UUID

Hive 3.1 (HDInsight 4.0) offre une fonction UDF intégrée pour générer des UUID uniques. La méthode Hive UUID() génère des ID uniques même avec CTAS. Vous pouvez l’utiliser comme suit.
```hql
create table rhive as
select uuid() as UUID
from uuid_test
```

## <a name="hive-job-output-format-differs-from-hdinsight-36"></a>Le format de sortie de la tâche Hive diffère de HDInsight 3.6

Cela est dû à la différence de WebHCat (Templeton) entre HDInsight 3.6 et HDInsight 4.0.

* API Rest Hive – ajouter ```arg=--showHeader=false -d arg=--outputformat=tsv2 -d```

* Kit de développement logiciel (SDK) .NET – initialiser les arguments ```HiveJobSubmissionParameters```
    ```csharp
    List<string> args = new List<string> { { "--showHeader=false" }, { "--outputformat=tsv2" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SELECT clientid,market from hivesampletable LIMIT 10",
                    Defines = defines,
                    Arguments = args
                };
    ```

## <a name="reduce-hive-internal-table-creation-latency"></a>Réduire la latence de création de table interne Hive

1. À partir de Advanced hive-site et Advanced hivemetastore-site, supprimez la valeur ```org.apache.hive.hcatalog.listener.DbNotificationListener``` de ```hive.metastore.transactional.event.listeners```. 

2. Si ```hive.metastore.event.listeners``` a une valeur, supprimez-la.

3. DbNotificationListener est nécessaire uniquement si vous utilisez des commandes REPL et, si ce n’est pas le cas, vous pouvez le supprimer.

    :::image type="content" source="./media/apache-hive-40-migration-guide/hive-reduce-internal-table-creation-latency.png" alt-text="Réduire la latence de table interne dans HDInsight 4.0" border="true":::

## <a name="change-hive-default-table-location"></a>Changer l’emplacement de la table par défaut Hive

Ce changement de comportement est normal sur HDInsight 4.0 (Hive 3.1). La raison principale de ce changement concerne le contrôle des autorisations de fichiers. 

Pour créer des tables externes sous un emplacement personnalisé, spécifiez l’emplacement dans l’instruction create table.

## <a name="disable-acid-in-hdinsight-40"></a>Désactiver ACID dans HDInsight 4.0

Nous vous recommandons d’activer ACID dans HDInsight 4.0. La plupart des améliorations récentes (fonctions et performances) dans Hive sont rendues disponibles uniquement pour les tables ACID.

Étapes de désactivation d’ACID sur HDInsight 4.0 :
1. Modifiez les configurations Hive suivantes dans Ambari :

    ```text
    hive.strict.managed.tables=false
    hive.support.concurrency=false; 
    hive.txn.manager=org.apache.hadoop.hive.ql.lockmgr.DummyTxnManager;
    hive.enforce.bucketing=false;
    hive.compactor.initiator.on=false;
    hive.compactor.worker.threads=0;
    hive.create.as.insert.only=false;
    metastore.create.as.acid=false;
    ```

2. Redémarrez le service Hive.

> [!IMPORTANT]
> Microsoft recommande de ne pas partager les mêmes données/stockage avec les tables gérées par Hive HDInsight 3.6 et HDInsight 4.0. Il s’agit d’un scénario non pris en charge.

* Normalement, les configurations ci-dessus doivent être définies avant même de créer des tables Hive sur le cluster HDInsight 4.0. Nous ne devrions pas désactiver ACID une fois les tables gérées créées. Cela peut entraîner une perte de données ou des résultats incohérents. Par conséquent, il est recommandé de le définir une fois lorsque vous créez un nouveau cluster et que vous ne le modifiez plus ultérieurement.

* La désactivation d’ACID après la création de tables est risquée. Toutefois, au cas où vous le souhaitez, suivez les étapes ci-dessous pour éviter une perte de données ou une incohérence potentielle :

    1. Créez une table externe avec le même schéma et copiez les données de la table managée d’origine à l’aide de la commande CTAS ```create external table e_t1 select * from m_t1```.    
    2. Supprimez la table managée à l’aide de ```drop table m_t1```.
    3. Désactivez ACID à l’aide des configurations suggérées.        
    4. Créez m_t1 à nouveau et copiez les données de la table externe à l’aide de la commande CTAS ```create table m_t1 select * from e_t1```.        
    5. Supprimez la table externe en utilisant ```drop table e_t1```.

Assurez-vous que toutes les tables managées sont converties en tables externes et supprimées avant de désactiver ACID. En outre, comparez le schéma et les données après chaque étape pour éviter toute différence.

## <a name="create-hive-external-table-with-755-permission"></a>Créer une table externe Hive avec l’autorisation 755

Ce problème peut être résolu à l’aide de l’une des deux options suivantes :

1. Définissez manuellement l’autorisation du dossier sur 757 ou 777 pour permettre à l’utilisateur Hive d’écrire dans le répertoire.

2. Remplacez « Hive Authorization Manager » de ```org.apache.hadoop.hive.ql.security.authorization.StorageBasedAuthorizationProvider``` par ```org.apache.hadoop.hive.ql.security.authorization.MetaStoreAuthzAPIAuthorizerEmbedOnly```.

MetaStoreAuthzAPIAuthorizerEmbedOnly désactive efficacement les vérifications de sécurité, car le metastore Hive n’est pas incorporé dans HDInsight 4.0. Toutefois, cela peut entraîner d’autres problèmes potentiels. Soyez prudent lorsque vous utilisez cette option.

## <a name="permission-errors-in-hive-job-after-upgrading-to-hdinsight-40"></a>Erreurs d’autorisation dans le travail Hive après la mise à niveau vers HDInsight 4.0

* Dans HDInsight 4.0, toutes les formes de cluster avec des composants Hive sont configurées avec un nouveau fournisseur d’autorisation : ```org.apache.hadoop.hive.ql.security.authorization.StorageBasedAuthorizationProvider```

* Les autorisations de fichier HDFS doivent être affectées à l’utilisateur Hive pour le fichier qui fait l’objet de l’accès. Le message d’erreur fournit les informations nécessaires pour résoudre le problème.

* Vous pouvez également basculer vers le fournisseur ```MetaStoreAuthzAPIAuthorizerEmbedOnly``` utilisé dans les clusters Hive HDInsight 3.6.
```org.apache.hadoop.hive.ql.security.authorization.MetaStoreAuthzAPIAuthorizerEmbedOnly```

    :::image type="content" source="./media/apache-hive-40-migration-guide/hive-job-permission-errors.png" alt-text="Définir l’autorisation sur MetaStoreAuthzAPIAuthorizerEmbedOnly" border="true":::

## <a name="unable-to-query-table-with-opencsvserde"></a>Impossible d’interroger la table avec OpenCSVSerde

La lecture des données à partir de la table de format `csv` peut renvoyer une exception comme :
```text
MetaException(message:java.lang.UnsupportedOperationException: Storage schema reading not supported)
```

Solution de contournement :

* Ajouter la configuration `metastore.storage.schema.reader.impl`=`org.apache.hadoop.hive.metastore.SerDeStorageSchemaReader` dans `Custom hive-site` via l’IU Ambari

* Redémarrer tous les services Hive obsolètes

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
