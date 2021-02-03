---
title: Optimiser pour les recommandations Advisor du cluster
titleSuffix: Azure HDInsight
description: Optimiser Apache HBase pour les recommandations Advisor du cluster dans Azure HDInsight.
author: ramkrish86
ms.author: ramvasu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: acb7a6aeb4084949be3b0ad40e770a414a13ab6d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943006"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Conseils Apache HBase dans Azure HDInsight

Cet article décrit plusieurs conseils pour vous aider à optimiser les performances d’Apache HBase dans Azure HDInsight. 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>Optimiser HBase pour lire les données récemment écrites

Si votre cas d’usage implique la lecture des données les plus récentes de HBase, ce conseil peut vous aider. Pour des performances élevées, il est préférable que les lectures HBase soient servies à partir de memstore, au lieu du stockage à distance.

Le conseil en termes de requête indique que pour une famille de colonnes donnée dans une table, plus de 75 % des lectures sont servies à partir de memstore. Cet indicateur suggère que même si un vidage se produit sur memstore, le fichier récent doit être accessible et se situer dans le cache. Les données sont d’abord écrites dans memstore où le système accède aux données récentes. Il est possible que les threads internes de vidage HBase détectent qu’une région donnée a atteint la taille de 128M (valeur par défaut) et peuvent déclencher un vidage. Ce scénario se produit même pour les données les plus récentes écrites lorsque la taille de memstore était d’environ 128M. Par conséquent, une lecture ultérieure de ces enregistrements récents peut nécessiter une lecture de fichier plutôt qu’une lecture à partir de memstore. Il est donc préférable d’optimiser pour que même les données récentes récemment vidées puissent résider dans le cache.

Pour optimiser les données récentes dans le cache, prenez en compte les paramètres de configuration suivants :

1. Définissez `hbase.rs.cacheblocksonwrite` sur `true`. Cette configuration par défaut dans HDInsight HBase est `true`. Vérifiez donc qu’elle n’est pas réinitialisée sur `false`.

2. Augmentez la valeur de `hbase.hstore.compactionThreshold` afin de pouvoir éviter le compactage. Par défaut, cette valeur est `3`. Vous pouvez l’augmenter à une valeur supérieure comme `10`.

3. Si vous suivez l’étape 2 et que vous définissez compactionThreshold, modifiez `hbase.hstore.compaction.max` sur une valeur plus élevée, par exemple `100`, et augmentez également la valeur de `hbase.hstore.blockingStoreFiles` dans la configuration sur une valeur plus élevée, par exemple `300`.

4. Si vous êtes certain de devoir lire uniquement les données récentes, définissez la configuration `hbase.rs.cachecompactedblocksonwrite` sur **ON**. Cette configuration indique au système que, même si le compactage se produit, les données restent dans le cache. Les configurations peuvent aussi être définies au niveau de la famille. 

   Dans l’interpréteur de commandes HBase, exécutez la commande suivante pour définir la configuration `hbase.rs.cachecompactedblocksonwrite` :
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. Le cache de blocs peut être désactivé pour une famille donnée dans une table. Assurez-vous qu’il est activé (**ON**) pour les familles qui ont les lectures de données les plus récentes. Par défaut, le cache de blocs est activé pour toutes les familles d’une table. Si vous avez désactivé le cache de blocs pour une famille et que vous devez l’activer, utilisez la commande alter à partir de HBase Shell.

   Ces configurations permettent de s’assurer que les données sont disponibles dans le cache et que les données récentes ne subissent pas de compactage. S’il est possible d’utiliser une durée de vie dans votre scénario, envisagez d’utiliser un compactage en fonction de la date. Pour plus d’informations, consultez [Guide de référence Apache HBase : Compactage en fonction de la date](https://hbase.apache.org/book.html#ops.date.tiered)  

## <a name="optimize-the-flush-queue"></a>Optimiser la file d’attente de vidage

Ce conseil indique que les vidages de HBase peuvent nécessiter un réglage. La configuration actuelle des gestionnaires de vidages peut ne pas être suffisamment élevée pour gérer le trafic d’écriture, ce qui peut entraîner un ralentissement des vidages.

Dans l’interface utilisateur du serveur de la région, notez si la file d’attente de vidage dépasse 100. Ce seuil indique que les vidages sont lents et vous devrez peut-être régler la configuration de `hbase.hstore.flusher.count`. Par défaut, la valeur est 2. Assurez-vous que les threads de vidage maximum n’augmentent pas au-delà de 6.

En outre, vérifiez si vous avez une recommandation pour le paramétrage du nombre de régions. Si c’est le cas, nous vous suggérons d’essayer le réglage de la région pour voir si cela permet d’accélérer les vidages. Dans le cas contraire, le réglage des threads de vidage peut vous aider.

## <a name="region-count-tuning"></a>Paramétrage du nombre de régions

Le conseil de paramétrage du nombre de régions indique que HBase a bloqué les mises à jour et que le nombre de régions peut être supérieur à la taille maximale du segment de mémoire prise en charge. Vous pouvez ajuster la taille du segment de mémoire, la taille de memstore et le nombre de régions.

Exemple de scénario :

- Supposons que la taille du segment de mémoire du serveur de région est de 10 Go. Par défaut, `hbase.hregion.memstore.flush.size` est `128M`. La valeur par défaut pour `hbase.regionserver.global.memstore.size` est `0.4`. Ce qui signifie que sur les 10 Go, 4 Go sont alloués à memstore (globalement).

- Supposons qu’il existe une répartition uniforme de la charge d’écriture entre toutes les régions et que chaque région augmente jusqu’à 128 Mo, alors le nombre maximal de régions dans cette configuration est de `32` régions. Si un serveur de région donné est configuré pour avoir 32 régions, le système peut mieux éviter le blocage des mises à jour.

- Avec ces paramètres en place, le nombre de régions est de 100. Le memstore global de 4 Go est désormais réparti entre 100 régions. Ainsi, chaque région obtient uniquement 40 Mo pour memstore. Lorsque les écritures sont uniformes, le système effectue régulièrement des vidages avec une taille plus petite de la commande < 40 Mo. Le fait d’avoir de nombreux threads de vidage peut augmenter la vitesse de vidage `hbase.hstore.flusher.count`.

Le conseil signifie qu’il serait judicieux de réévaluer le nombre de régions par serveur, la taille du segment de mémoire et la configuration globale de la taille de memstore, ainsi que le réglage des threads de vidage afin d’éviter le blocage de ces mises à jour.

## <a name="compaction-queue-tuning"></a>Paramétrage de la file d’attente de compactage

Si la file d’attente de compactage HBase atteint plus de 2 000 de manière régulière, vous pouvez augmenter les threads de compactage sur une valeur plus élevée.

Lorsqu’il y a un nombre excessif de fichiers pour le compactage, cela peut entraîner une utilisation plus importante du segment de mémoire lié à la façon dont les fichiers interagissent avec le système de fichiers Azure. Il est donc préférable d’effectuer le compactage le plus rapidement possible. Dans les clusters plus anciens, les configurations de compactage liées à la limitation peuvent entraîner une baisse du taux de compactage.

Vérifiez les configurations `hbase.hstore.compaction.throughput.lower.bound` et `hbase.hstore.compaction.throughput.higher.bound`. Si elles sont déjà définies sur 50M et 100M, laissez-les telles quelles. Toutefois, si vous avez configuré ces paramètres sur une valeur inférieure (ce qui était le cas avec les clusters plus anciens), modifiez les limites sur 50M et 100M respectivement.

Les configurations sont `hbase.regionserver.thread.compaction.small` et `hbase.regionserver.thread.compaction.large` (les valeurs par défaut sont 1 pour chaque).
Limitez la valeur maximale pour cette configuration pour qu’elle soit inférieure à 3.

## <a name="full-table-scan"></a>Analyse complète de la table

Le conseil d’analyse complète de la table indique que plus de 75 % des analyses émises sont des analyses complètes de la table/région. Vous pouvez revoir la façon dont votre code appelle les analyses pour améliorer les performances des requêtes. Prenez en compte les pratiques suivantes :

* Définissez les lignes de démarrage et d’arrêt appropriées pour chaque analyse.

* Utilisez l’API **MultiRowRangeFilter** pour pouvoir interroger différentes plages dans un seul appel d’analyse. Pour plus d’informations, consultez la [documentation sur l’API MultiRowRangeFilter](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html).

* Dans les cas où vous avez besoin d’une analyse complète de la table ou de la région, vérifiez s’il est possible d’éviter l’utilisation du cache pour ces requêtes, afin que les autres requêtes qui utilisent le cache ne suppriment pas les blocs à chaud. Pour vous assurer que les analyses n’utilisent pas le cache, utilisez l’API **scan** avec l’option **setCaching (false)** dans votre code : 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>Étapes suivantes

[Optimiser Apache HBase à l’aide d’Ambari](../optimize-hbase-ambari.md)
