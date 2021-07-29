---
title: Résoudre les problèmes de gestion des charges de travail Hive LLAP
titleSuffix: Azure HDInsight
description: Résoudre les problèmes de gestion des charges de travail Hive LLAP
ms.service: hdinsight
ms.topic: troubleshooting
author: guptanikhil007
ms.author: guptan
ms.reviewer: jasonh
ms.date: 05/25/2021
ms.openlocfilehash: 6a06a67b5039bc3a7e25e8300128c85ee008be3a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482185"
---
# <a name="troubleshoot-hive-llap-workload-management-issues"></a>Résoudre les problèmes de gestion des charges de travail Hive LLAP

La gestion des charges de travail (WLM) est disponible pour les clients qui démarrent des clusters HDInsight 4.0. Utilisez les ressources ci-dessous pour vous aider à déboguer les problèmes liés à la fonctionnalité WLM.

## <a name="get-wlm-resource-plan-and-plan-entities"></a>Obtenir les entités de plan de ressources et de plan WLM
#### <a name="to-get-all-resource-plans-on-the-cluster"></a>Pour obtenir tous les plans de ressources sur le cluster :
```hql
SHOW RESOURCE PLANS;
```

#### <a name="to-get-definition-of-a-given-resource-plan"></a>Pour obtenir la définition d’un plan de ressources donné
```hql
SHOW RESOURCE PLAN <plan_name>;
```

## <a name="get-wlm-entities-information-from-metastore-database"></a>Obtenir les informations d’entités WLM à partir de la base de données metastore
> [!NOTE]
> Applicable uniquement à la base de données metastore Hive

Les informations sur les entités WLM peuvent également être consultées à partir des tables suivantes dans la base de données metastore Hive 

* **WM_RESOURCEPLANS** (chaîne NAME, chaîne STATUS, int QUERY_PARALLELISM, chaîne DEFAULT_POOL_PATH)
* **WM_POOLS** (chaîne RP_NAME, chaîne PATH, double ALLOC_FRACTION, int QUERY_PARALLELISM, chaîne SCHEDULING_POLICY)
* **WM_MAPPINGS** (chaîne RP_NAME, chaîne ENTITY_TYPE, chaîne ENTITY_NAME, chaîne POOL_PATH, int ORDERING)
* **WM_TRIGGERS** (chaîne RP_NAME, chaîne NAME, chaîne TRIGGER_EXPRESSION, chaîne ACTION_EXPRESSION)
* **WM_POOLS_TO_TRIGGERS** (chaîne RP_NAME, chaîne POOL_PATH, chaîne TRIGGER_NAME)

## <a name="wlm-metrics"></a>Métriques WLM

Les métriques WLM sont accessibles directement via l’interface utilisateur HS2Interactive sous l’onglet Vidage métriques. <br>
:::image type="content" source="./media/hive-workload-management/hs2-interactive-wlm.jpg" alt-text="Interface utilisateur HS2Interactive." lightbox="./media/hive-workload-management/hs2-interactive-wlm.jpg":::

Exemples de métriques publiées par WLM pour un pool donné dans un plan de ressources.
```
    "name" : "Hadoop:service=hiveserver2,name=WmPoolMetrics.etl",
    "modelerType" : "WmPoolMetrics.etl",
    "tag.Context" : "HS2",
    "tag.SessionId" : "etl",
    "tag.Hostname" : "hn0-c2b-ll.cu1cgjaim53urggr4psrgczloa.cx.internal.cloudapp.net",
    "NumExecutors" : 10,
    "NumRunningQueries" : 2,
    "NumParallelQueries" : 3,
    "NumQueuedQueries" : 0,
    "NumExecutorsMax" : 10
```

Il est possible que l’interface utilisateur HS2Interactive ne fonctionne pas pour les clusters compatibles ESP (Pack Sécurité Entreprise) commercialisés avant avril 2021. Dans ce cas, les métriques associées à la gestion WLM peuvent être obtenues à partir de tableaux de bord Grafana personnalisés.
<br>
Le nom des métriques suit les modèles ci-dessous :
```
default.General.WM_<pool>_numExecutors
default.General.WM_<pool>_numExecutorsMax
default.General.WM_<pool>_numRunningQueries
default.General.WM_<pool>_numParallelQueries
default.General.WM_<pool>_numQueuedQueries
```
Remplacez `<pool>` par le nom du pool respectif pour obtenir les métriques dans Grafana.

:::image type="content" source="./media/hive-workload-management/grafana-wlm.jpg" alt-text="Métriques WLM Grafana." lightbox="./media/hive-workload-management/grafana-wlm.jpg":::

> Remarque : vérifiez que le composant hiveserver2 est sélectionné dans les filtres et le nom du composant ci-dessus.

<br>

## <a name="wlm-feature-characteristics"></a>Caractéristiques des fonctionnalités WLM
### <a name="lifecycle-of-tez-ams-in-wlm-enabled-clusters"></a>**Cycle de vie des AM Tez dans les clusters compatibles WLM**
Contrairement aux clusters LLAP par défaut, les clusters compatibles WLM activés disposent d’un autre ensemble d’AM Tez. Ces AM Tez sont planifiés pour s’exécuter dans la file d’attente `wm` si *hive.server2.tez.interactive.queue=wm* est défini dans les configurations Hive. <br>
Ces AM Tez sont générés quand la gestion WLM est activée en fonction de la somme QUERY_PARALLELISM de tous les pools définis dans le plan de ressources. <br>
Quand vous désactivez la gestion des charges de travail dans le cluster, ces AM Tez sont automatiquement supprimés (KILLED).
`{ DISABLE WORKLOAD MANAGEMENT; }`

### <a name="resource-contention"></a>**Contention de ressources**
Dans le cluster LLAP compatible WLM, les ressources sont partagées entre les requêtes en fonction de la configuration du plan de ressources. Le partage des ressources entraîne parfois une lenteur des requêtes.
Certains réglages peuvent être effectués sur le plan de ressources pour réduire la contention de ressources qui se produit au sein d’un pool. Par exemple, `scheduling_policy` peut être défini comme `fair`, ce qui garantit une répartition égale des ressources sur le cluster à chaque requête affectée au pool, ou bien défini comme `fifo`, ce qui garantit l’ensemble des ressources à la première requête arrivant au pool.<br>
L’exemple suivant montre comment définir la stratégie de planification pour un pool nommé `etl` dans le plan de ressources `wlm_basic` :
```hql
ALTER POOL wlm_basic.etl SET SCHEDULING_POLICY = fair;
```
Vous pouvez également définir la stratégie de planification lors de la création du pool :
```hql
CREATE POOL wlm_basic.default WITH ALLOC_FRACTION = 0.5, QUERY_PARALLELISM = 2, SCHEDULING_POLICY = fifo;
```

### <a name="query-failures-for-some-specific-use-cases"></a>**Échecs de requête pour certains cas d’usage spécifiques**
Les requêtes en cours d’exécution dans WLM peuvent être supprimées automatiquement dans les cas suivants :
1. Quand le déclencheur Move est appliqué à une requête et à un pool de destination sans AM Tez disponible, la requête est supprimée. <br>
L’élément ci-dessus est une limitation de conception de la fonctionnalité WLM. Vous pouvez contourner cette fonctionnalité en augmentant la propriété `QUERY_PARALLELISM` pour le pool de destination de telle manière que, même pour un scénario de charge maximale, les requêtes soumises au cluster peuvent être prises en charge par ce pool. En outre, ajustez la taille de la file d’attente `wm` pour prendre en compte cette modification. <br>
2. Quand WLM est désactivé, toutes les requêtes en cours d’exécution échouent avec le modèle d’exception suivant :
   ```
   FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.tez.TezTask. Dag received [DAG_TERMINATE, DAG_KILL] in RUNNING state.
   ```
3. Quand un AM Tez WLM est supprimé manuellement, certaines des requêtes peuvent échouer avec le modèle suivant. <br/>Ces requêtes devraient s’exécuter sans problème lors du nouvel envoi.
```
java.util.concurrent.CancellationException: Task was cancelled.
    at com.google.common.util.concurrent.AbstractFuture.cancellationExceptionWithCause(AbstractFuture.java:1349) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.getDoneValue(AbstractFuture.java:550) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.get(AbstractFuture.java:513) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture$TrustedFuture.get(AbstractFuture.java:90) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.Uninterruptibles.getUninterruptibly(Uninterruptibles.java:237) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.Futures.getDone(Futures.java:1064) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.Futures$CallbackListener.run(Futures.java:1013) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.DirectExecutor.execute(DirectExecutor.java:30) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.executeListener(AbstractFuture.java:1137) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.complete(AbstractFuture.java:957) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.cancel(AbstractFuture.java:611) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture$TrustedFuture.cancel(AbstractFuture.java:118) ~[guava-28.0-jre.jar:?]
    at org.apache.hadoop.hive.ql.exec.tez.WmTezSession$TimeoutRunnable.run(WmTezSession.java:264) ~[hive-exec-3.1.3.4.1.3.6.jar:3.1.3.4.1.3.6]
    at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511) [?:1.8.0_275]
    at java.util.concurrent.FutureTask.run(FutureTask.java:266) [?:1.8.0_275]
    at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$201(ScheduledThreadPoolExecutor.java:180) ~[?:1.8.0_275]
    at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:293) ~[?:1.8.0_275]
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149) [?:1.8.0_275]
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624) [?:1.8.0_275]
    at java.lang.Thread.run(Thread.java:748) [?:1.8.0_275]
```

## <a name="known-issues"></a>Problèmes connus
* Les travaux Spark envoyés via [Hive Warehouse Connector (HWC)](apache-hive-warehouse-connector.md) peuvent rencontrer des défaillances intermittentes si la fonctionnalité WLM est activée sur le cluster LLAP cible. <br>
  Pour éviter les problèmes ci-dessus, le client peut avoir deux clusters LLAP : un cluster avec la gestion WLM activée et un autre sans WLM.
  Le client peut ensuite utiliser HWC pour connecter son cluster Spark au cluster LLAP sans WLM.

* La commande `DISABLE WORKLOAD MANAGEMENT;` se bloque parfois pendant une longue période. <br>
Annulez la commande et vérifiez l’état des plans de ressources avec la commande suivante : `SHOW RESOURCE PLANS;`
Vérifiez si un plan de ressources actif est disponible avant d’exécuter de nouveau la commande `DISABLE WORKLOAD MANAGEMENT`. <br>

* Certains AM Tez peuvent continuer à s’exécuter et ne disparaissent pas avec la commande `DISABLE WORKLOAD MANAGEMENT` ni avec le redémarrage de HS2. <br>
Supprimez ces AM Tez via `yarn UI` ou `yarn console application` après avoir désactivé la gestion des charges de travail.

## <a name="related-articles"></a>Articles connexes
* [Gestion des charges de travail (WLM) Hive LLAP](hive-workload-management.md)
* [Résumé des commandes de gestion des charges de travail (WLM) Hive LLAP](workload-management-commands.md)

