---
title: Résoudre des problèmes de performances d’Apache HBase sur Azure HDInsight
description: Instructions et conseils pour le réglage d’Apache HBase afin d’obtenir des performances optimales sur Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: c67f21a6ed8a7697977bb7737f0e46348efb2530
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "71266502"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Résoudre des problèmes de performances d’Apache HBase sur Azure HDInsight

Ce document présente des instructions et conseils pour le réglage d’Apache HBase afin d’obtenir des performances optimales sur Azure HDInsight. La plupart de ces conseils dépendent de la charge de travail particulière et du modèle de lecture/écriture/analyse. Testez soigneusement les changements de configuration avant de les appliquer dans un environnement de production.

## <a name="hdinsight-hbase-performance-insights"></a>Insights sur les performances de HDInsight HBase

Le principal goulot d’étranglement dans la plupart des charges de travail HBase est le journal WAL (write-ahead log). Cela affecte sérieusement les performances d’écriture. HDInsight HBase possède un modèle de calcul de stockage séparé. Cela signifie que les données sont stockées à distance sur Stockage Azure, tandis que les serveurs régionaux sont hébergés sur les machines virtuelles. Jusqu’il y a peu, le journal WAL (write-ahead log) était également écrit dans Stockage Azure, ce qui amplifiait ce goulot d’étranglement dans le cas de HDInsight. La fonctionnalité [Écritures accélérées](./apache-hbase-accelerated-writes.md) est conçue pour résoudre ce problème, en écrivant le journal WAL (write-ahead log) sur des disques managés SSD Azure Premium. Cela permet d’améliorer considérablement les performances et de faire face à de nombreux problèmes liés à certaines charges de travail nécessitant un nombre important d’écritures.

Utilisez un [compte Stockage Blob Azure Premium](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) comme stockage distant pour améliorer sensiblement les opérations de lecture. Cette option est possible uniquement si la fonctionnalité de journal WAL (write-ahead log) est activée.

## <a name="compaction"></a>Compactage

Le compactage est un autre goulot d’étranglement potentiel clairement identifié.  Par défaut, le compactage principal est désactivé sur les clusters HDInsight HBase. En effet, étant donné qu’il s’agit d’un processus nécessitant de nombreuses ressources, nous souhaitons offrir aux clients une flexibilité totale pour le planifier en fonction des caractéristiques de leur charge de travail, c’est-à-dire, en dehors des heures de pointe. Par ailleurs, notre stockage étant distant (adossé à un stockage Azure) par opposition au système de fichiers DFS hadoop (HDFS) local qui est commun à la plupart des instances locales, l’emplacement où résident les données est indifférent, ce qui constitue l’un des principaux objectifs du compactage principal.

L’hypothèse est que le client s’occupe de planifier le compactage principal en fonction de ses besoins. Si cette maintenance n’est pas assurée, le compactage a un impact considérable sur les performances de lecture à long terme.

Pour les opérations d’analyse en particulier, des latences moyennes sensiblement supérieures à 100 ms doivent être considérées comme préoccupantes. Vérifiez si le compactage principal a été planifié avec précision.

## <a name="know-your-apache-phoenix-workload"></a>Connaître votre charge de travail Apache Phoenix

Répondre aux questions suivantes vous aidera à mieux comprendre votre charge de travail Apache Phoenix :

* Vos « lectures » se traduisent-elles toutes en analyses ?
    * Dans ce cas, quelles sont les caractéristiques de ces analyses ?
    * Avez-vous optimisé votre schéma de table Phoenix pour ces analyses, y compris l’indexation appropriée ?
* Avez-vous utilisé l’instruction `EXPLAIN` pour comprendre les plans de requête que vos « lectures » génèrent ?
* Vos écritures sont-elles de type « upsert-Selects » ?
    * Si c’est le cas, elles effectuent également des analyses. La latence attendue pour les analyses est de l’ordre de 100 ms en moyenne, par opposition à 10 ms pour les obtentions de point dans HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Méthodologie de test et surveillance des métriques

Si vous utilisez des tests d’évaluation tels que YCSB, JMeter ou Pherf pour tester et ajuster les performances, vérifiez les points suivants :

1. Les ordinateurs clients ne deviennent pas un goulet d’étranglement (Vérifiez l’utilisation du processeur sur les ordinateurs clients).

1. Les configurations côté client, telles que le nombre de threads, sont paramétrées correctement pour saturer la bande passante allouée au client.

1. Les résultats des tests sont enregistrés précisément et systématiquement.

Si vos requêtes commencent soudainement à produire des résultats bien pires qu’auparavant (vérifiez la présence éventuelle de bogues dans le code de votre application), génèrent-elles soudainement de grandes quantités de données non valides, augmentant ainsi naturellement les latences de lecture ?

## <a name="migration-issues"></a>Problèmes de migration

Si vous opérez une migration vers Azure HDInsight, assurez-vous qu’elle est effectuée de manière systématique et précise, de préférence à l’aide d’une automatisation. Évitez d’effectuer une migration manuelle. Vérifiez les points suivants :

1. Les attributs de table, tels que le compactage, les filtres de Bloom, etc., nécessitent une migration précise.

1. Pour les tables Phoenix, les paramètres de salage doivent être correctement mappés à la nouvelle taille du cluster. Par exemple, il est recommandé que le nombre de seaux de sel soit un multiple du nombre de nœuds Worker dans le cluster, le multiple spécifique dépendant de la quantité de détection de zone réactive observée.  

## <a name="server-side-config-tunings"></a>Réglages de configuration côté serveur

Dans HDInsight HBase, les fichiers HFiles sont stockés sur un stockage distant. Par conséquent, en cas d’absence de cache, le coût des lectures est inévitablement supérieur à celui des systèmes locaux dont les données sont sauvegardées sur un système de fichiers DFS hadoop local en raison de la latence du réseau impliquée. Dans la plupart des scénarios, une utilisation intelligente de caches HBase (caches de bloc et de compartiment) permet de contourner cette difficulté. Toutefois, il reste des cas occasionnels où cela constituer un problème pour le client. L’utilisation d’un compte d’objet blob de blocs Premium s’est avérée utile. Toutefois, l’objet blob WASB (lecteur de stockage Windows Azure) dépendant de certaines propriétés telles que `fs.azure.read.request.size` pour extraire des données dans des blocs en fonction du mode de lecture (séquentielle ou aléatoire), il se peut qu’il y ait encore des instances dont les latences de lecture sont plus élevées. Des expériences empiriques ont montré que le fait de définir la taille de bloc de demande de lecture (`fs.azure.read.request.size`) sur 512 Ko et d’adapter la taille de bloc des tables HBase en conséquence produit le meilleur résultat dans la pratique.

Pour la plupart des clusters de nœuds de grande taille, HDInsight HBase fournit un `bucketcache` en tant que fichier sur un disque SSD local attaché à la machine virtuelle exécutant les `regionservers`. Parfois, la désactivation du cache de la pile système peut entraîner une certaine amélioration. Cela est limité par l’utilisation de la mémoire disponible et le fait que la taille de celle-ci peut être inférieure à celle du cache basé sur un fichier, de sorte qu’il n’est pas évident que ce soit toujours le meilleur choix.

Voici d’autres paramètres que nous avons réglés, qui semblent avoir été utiles à des degrés divers, avec les raisonnements sous-jacents :

1. Augmentation de la taille de `memstore` de 128 Mo (par défaut ) à 256 Mo : ce paramètre est généralement recommandé pour les scénarios d’écriture intensive.

1. Augmentation du nombre de threads dédiés au compactage de 1 (par défaut) à 4. Ce paramètre est pertinent si nous observons de fréquents compactages mineurs.

1. Évitement du blocage du vidage de `memstore` en raison d’une limite de magasin. Il est possible d’augmenter la valeur `Hbase.hstore.blockingStoreFiles` jusqu’à 100 pour fournir ce tampon.

1. Pour contrôler les vidages, les valeurs par défaut peuvent être les suivantes :

    1. Il est possible d’augmenter la valeur `Hbase.regionserver.maxlogs` de 32 à 140 (ce qui évite les vidages en raison des limites de WAL).

    1. `Hbase.regionserver.global.memstore.lowerLimit` = 0,55.

    1. `Hbase.regionserver.global.memstore.upperLimit` = 0,60.

1. Configurations spécifiques de Phoenix pour le réglage du pool de threads :

    1. Il est possible d’augmenter la valeur `Phoenix.query.queuesize` jusqu’à 10000.

    1. Il est possible d’augmenter la valeur `Phoenix.query.threadpoolsize` jusqu’à 512.

1. Autres configurations spécifiques de Phoenix :

    1. La valeur `Phoenix.rpc.index.handler.count` peut être 50 si les recherches dans l’index sont volumineuses ou nombreuses.

    1. Il est possible d’augmenter la valeur `Phoenix.stats.updateFrequency` de 15 minutes (par défaut) à 1 heure.

    1. Il est possible d’augmenter la valeur `Phoenix.coprocessor.maxmetadatacachetimetolivems` de 30 minutes à 1 heure.

    1. Il est possible d’augmenter la valeur `Phoenix.coprocessor.maxmetadatacachesize` de 20 Mo à 50 Mo.

1. Délais d’expiration RPC : il est possible d’augmenter les délais d’expiration de RPC HBase, de scanner client HBase et de requête Phoenix à 3 minutes. Il est important de noter à cet égard que le paramètre `hbase.client.scanner.caching` est défini sur une valeur correspondante côté serveur et côté client. Autrement, ce paramètre entraîne des erreurs liées à `OutOfOrderScannerException` côté client. Ce paramètre doit être défini sur une valeur basse pour les analyses volumineuses. Nous avons défini cette valeur sur 100.

## <a name="other-considerations"></a>Autres points à considérer

Autres paramètres à prendre en compte pour le réglage :

1. `Hbase.rs.cacheblocksonwrite` : ce paramètre est défini sur true par défaut sur HDI.

1. Paramètres permettant de différer un compactage mineur.

1. Paramètres expérimentaux tels que l’ajustement de pourcentages de files d’attente réservés aux demandes de lecture et d’écriture.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

- Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

- Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

- Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
