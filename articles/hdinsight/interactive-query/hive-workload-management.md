---
title: Fonctionnalité Hive LLAP Workload Management
titleSuffix: Azure HDInsight
description: Fonctionnalité Hive LLAP Workload Management
ms.service: hdinsight
ms.topic: how-to
author: guptanikhil007
ms.author: guptan
ms.reviewer: jasonh
ms.date: 05/25/2021
ms.openlocfilehash: 8dde65d0547552af90caddd214dd1ceda5a754ee
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482104"
---
# <a name="hive-llap-workload-management-wlm-feature"></a>Fonctionnalité Hive LLAP Workload Management (WLM)
Dans un cluster d’Interactive Query, la gestion des ressources est impérative, en particulier dans un environnement à plusieurs locataires. Hive LLAP (traitement analytique à faible latence) utilise la gestion des charges de travail pour permettre aux utilisateurs de répondre aux besoins spécifiques de la charge de travail et d’empêcher les conflits pour ces ressources. <br> La gestion des charges de travail implémente des pools de ressources (également appelés pools de requêtes), ce qui vous permet de diviser les ressources disponibles pour Hive/LLAP dans des pools à utiliser pour des charges de travail spécifiques.
Elle vous permet également de configurer le pourcentage de ressources et le parallélisme des requêtes pour chaque pool de ressources.

![« Architecture LLAP. »](./media/hive-workload-management/llap-architecture.png)

## <a name="enable-hive-llap-workload-management-feature-for-hdinsight-clusters"></a>Activer la fonctionnalité de gestion des charges de travail LLAP Hive pour les clusters HDInsight

Activez la fonctionnalité de gestion de la charge de travail dans les clusters d’Interactive Query HDInsight en suivant les étapes indiquées ci-dessous :
1. Créez une file d’attente Yarn, qui peut être utilisée pour afficher les AM Tez de gestion des charges de travail.
2. Modifiez les configurations de cluster via Ambari pour activer la fonctionnalité dans Hive.
3. Créez et activez un plan de ressources.

### <a name="create-a-new-yarn-queue-suitable-for-workload-management-feature"></a>Créer une file d’attente Yarn adaptée à la fonctionnalité de gestion des charges de travail
Créez une file d’attente Yarn appelée `wm` avec l’aide du [Guide](../hdinsight-troubleshoot-yarn.md) suivant.
Configurez la file d’attente `wm` sur le cluster en fonction des configurations suivantes :

| QueueName   | Capacité | Capacité max. | Priorité | Ressource AM maximale |
|------------|---------|--------------|----------|---------------------|
| `default`   | 5 %       | 5 %           | 0        | 33 %                 |
| `llap`      | 85 %      | 100 %         | 10       | 33 %                 |
| `wm`        | 10 %      | 15 %          | 9        | 100 %                |

Vérifiez si la configuration de la file d’attente `wm` ressemble à ce qui suit.
:::image type="content" source="./media/hive-workload-management/wm-yarn-queue.png" alt-text="configuration de la file d'attente wm.":::

### <a name="enable-workload-management-feature-in-hive-configs"></a>Activer la fonctionnalité de gestion de la charge de travail dans les configurations Hive
Ajoutez la propriété suivante au hiveserver2-interactive-site personnalisé et définissez sa valeur sur le nom de la file d’attente Yarn nouvellement créée qui est `wm`. Redémarrez l’Interactive HiveServer pour que les modifications de configuration prennent effet.
```
hive.server2.tez.interactive.queue=wm
```

### <a name="create-resource-plan"></a>Créer un plan de ressources
Voici un exemple de création d’un plan de ressources de base.
![« Plan de ressources de base. »](./media/hive-workload-management/wlm-resourceplan.jpg)

Exécutez les commandes suivantes via Beeline pour créer le plan de ressources ci-dessus.

#### <a name="commands-to-create-view-and-drop-the-resource-plan"></a>Commandes pour créer, afficher et supprimer le plan de ressources
```hql
# CREATE RESOURCE PLAN
CREATE RESOURCE PLAN demo_plan;

# CREATE POOLS
ALTER POOL demo_plan.default SET ALLOC_FRACTION = 0.65, QUERY_PARALLELISM = 2;
CREATE POOL demo_plan.etl WITH ALLOC_FRACTION = 0.20, QUERY_PARALLELISM = 2;
CREATE POOL demo_plan.sys_accounts WITH ALLOC_FRACTION = 0.15, QUERY_PARALLELISM = 1;

# CREATE MAPPING
CREATE USER MAPPING 'hive' IN demo_plan TO sys_accounts WITH ORDER 1;
 
# CREATE TRIGGERS
CREATE TRIGGER demo_plan.defaultToETL WHEN  ELAPSED_TIME > 20000 DO MOVE TO etl;
ALTER TRIGGER demo_plan.defaultToETL ADD TO POOL default;
CREATE TRIGGER demo_plan.ETLKill  WHEN ELAPSED_TIME > 40000 DO KILL;
ALTER TRIGGER demo_plan.ETLKill ADD TO POOL etl;

# VALIDATE PLAN
ALTER RESOURCE PLAN demo_plan VALIDATE;

# ENABLE PLAN
ALTER RESOURCE PLAN demo_plan ENABLE;

#  ACTIVATE PLAN
ALTER RESOURCE PLAN demo_plan ACTIVATE;

# SHOW RESOURCE PLAN
SHOW RESOURCE PLANS;
SHOW RESOURCE PLAN demo_plan;

# VALIDATE PLAN
ALTER RESOURCE PLAN demo_plan VALIDATE;

# ENABLE PLAN
ALTER RESOURCE PLAN demo_plan ENABLE;

#  ACTIVATE PLAN
ALTER RESOURCE PLAN demo_plan ACTIVATE;

# SHOW RESOURCE PLAN
SHOW RESOURCE PLANS;
SHOW RESOURCE PLAN demo_plan;

# DISABLE PLAN
-- In case plan is in active state first run:
-- DISABLE WORKLOAD MANAGEMENT;
ALTER RESOURCE PLAN demo_plan DISABLE;

# DROP RESOURCE PLAN
DROP RESOURCE PLAN demo_plan;
```

## <a name="understanding-resource-plan"></a>Présentation des ensembles de ressources
Pour disposer d’un plan de ressources optimal, vous devez avoir une compréhension approfondie des exigences de charge de travail.

### <a name="number-of-pools"></a>Nombre de pools
Le nombre de pools est limité par le parallélisme total des requêtes (une requête au minimum par pool).
La plupart des charges de travail requièrent rarement plus de trois pools. 
- par défaut, pour les requêtes interactives 
- etl/batch, pour les requêtes longues
- sys, pour les administrateurs système

### <a name="total-query_parallelism"></a>QUERY_PARALLELISM total
QUERY_PARALLELISM total ou nombre total de requêtes simultanées pouvant être obtenu à l’aide de la formule suivante :

```
Number of total concurrent queries(Tez AMs) = Math.floor( (total cluster memory capacity / size of Tez AM container) x percentage of wm queue capacity)
```

Par exemple : <br/>
Supposons que la taille du conteneur AM de Tez est de 4 Go et que la capacité de mémoire totale du cluster de fils est de 400 Go, à savoir 10 % alloués pour la file d’attente wm, <br/>
Nombre total de requêtes simultanées = floor((400/4) x 0,10) = 10

> [!Tip]
> La capacité de la file d’attente wm est légèrement supérieure à celle requise pour éviter que les AM Tez soient bloqués dans un état accepté. la capacité de la file d’attente `wm` peut être de 10,01% et la capacité de la file d’attente `default` peut être réduite à 4,99%.

### <a name="mappings"></a>Mappages
Les mappages fournissent un mécanisme permettant de diriger les requêtes vers certains pools. À mesure que le nombre de mappages augmente, plusieurs règles peuvent s’appliquer pour une requête donnée. Pour définir la règle qui doit être prioritaire : si l’ordre est spécifié avec la clause facultative `WITH ORDER`, la règle d’ordre inférieur prend la priorité. Sinon, les règles `user` prévalent sur les règles `application` et les règles `application` prévalent sur les règles `group`. <br/>
L’ordre des règles de groupe avec la même priorité n’est pas défini.


> [!Note]
> * Les AM Tez dans la file d’attente `llap` reste inutilisé lorsque le plan WLM est actif. Ces AM Tez dans la file d’attente `llap` seront facilement disponibles si le plan de ressources WLM est désactivé.
> * L’activation du plan de ressources WLM lance un nombre d’AM Tez égal au `QUERY_PARALLELISM` total configuré pour le plan de ressources donné. la taille de la file d’attente `wm` doit être paramétrée pour éviter que ces AM Tez soient bloqués dans l’état ACCEPTÉ.
> * Nous prenons uniquement en charge l’utilisation des deux compteurs suivants pour une utilisation dans les plans de ressources :
>    * EXECUTION_TIME
>    * ELAPSED_TIME

## <a name="related-articles"></a>Articles connexes
* [Résumé des commandes de gestion des charges de travail (WLM) Hive LLAP](workload-management-commands.md)
* [Résoudre les problèmes de gestion des charges de travail Hive LLAP](troubleshoot-workload-management-issues.md)


