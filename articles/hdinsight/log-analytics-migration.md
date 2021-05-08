---
title: Migrer des données Log Analytics pour Azure HDInsight
description: Découvrez les modifications apportées à l’intégration d’Azure Monitor et les meilleures pratiques pour l’utilisation des nouvelles tables.
ms.service: hdinsight
ms.topic: how-to
ms.author: ali
author: AliciaLiMicrosoft
ms.date: 04/19/2021
ms.openlocfilehash: 5e312941e02c5605132971a55041a0b4f7d6dbc9
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108139090"
---
# <a name="log-analytics-migration-guide-for-azure-hdinsight-clusters"></a>Guide de migration Log Analytics pour les clusters Azure HDInsight

 Azure HDInsight est un service de cluster géré et adapté à l’entreprise. Ce service exécute des infrastructures d’analyse open source comme Apache Spark, Hadoop, HBase et Kafka sur Azure. Azure HDInsight s’intègre à d’autres services Azure pour permettre aux clients de mieux gérer leurs applications analytique du Big Data.

Log Analytics fournit un outil du portail Azure pour modifier et exécuter des requêtes de journaux. Les requêtes proviennent de données collectées par les Journaux Azure Monitor et analysent leurs résultats de manière interactive. Les clients peuvent utiliser des requêtes Log Analytics pour récupérer les enregistrements qui correspondent à des critères spécifiques. Ils peuvent également utiliser des requêtes pour identifier des tendances, analyser des modèles et fournir des Insights sur leurs données.

Azure HDInsight a permis l’intégration à Log Analytics en 2017. Les clients HDInsight ont rapidement adopté cette fonctionnalité pour surveiller leurs clusters HDInsight et interroger les journaux dans les clusters. Tandis que l’adoption de cette fonctionnalité a augmenté, les clients ont fourni des commentaires sur l’intégration :

- Les clients ne peuvent pas décider quels journaux stocker et le stockage de tous les journaux peut devenir coûteux.
- Les journaux de schémas HDInsight actuels ne suivent pas des conventions d’affectation de noms cohérentes, et certaines tables se répètent.
- Les clients veulent disposer d’un tableau de bord prêt à l’emploi pour surveiller facilement l’indicateur de performance clé de leurs clusters HDInsight.
- Les clients doivent accéder à Log Analytics pour exécuter des requêtes simples.

## <a name="solution-overview"></a>Vue d’ensemble de la solution

En tenant compte des commentaires des clients, l’équipe Azure HDInsight a investi dans l’intégration avec Azure Monitor. Cette intégration offre :

- Un nouvel ensemble de tables dans l’espace de travail Log Analytics des clients. Les nouvelles tables sont remises via un nouveau pipeline Log Analytics.
- Une fiabilité accrue
- Une remise de journal plus rapide
- Un regroupement de tables basés sur les ressources et des requêtes par défaut

## <a name="benefits-of-the-new-azure-monitor-integration"></a>Avantages de la nouvelle intégration d’Azure Monitor

Ce document décrit les modifications apportées à l’intégration d’Azure Monitor et fournit les meilleures pratiques pour l’utilisation des nouvelles tables.

**Schémas repensés**: la mise en forme du schéma pour la nouvelle intégration d’Azure Monitor est mieux organisée et facile à comprendre. Il existe deux tiers de schémas en moins, afin de réduire le plus possible l’ambiguïté au sein des schémas hérités.

**Journalisation sélective (bientôt disponible)** : il existe des journaux et des métriques disponibles via Log Analytics. Pour vous aider à économiser sur les coûts de surveillance, nous allons proposer une nouvelle fonctionnalité de journalisation sélective. Utilisez cette fonctionnalité pour activer et désactiver différents journaux et sources de métriques. Avec cette fonctionnalité, vous ne payez que pour ce que vous utilisez.

**Intégration des journaux dans le portail de cluster**: le volet **Journaux** est une nouveauté sur le portail de cluster HDInsight. Toute personne ayant accès au cluster peut accéder à ce volet pour interroger une table à laquelle la ressource de cluster envoie des enregistrements. Les utilisateurs n’ont plus besoin d’accéder à l’espace de travail Log Analytics pour voir les enregistrements d’une ressource de cluster spécifique.

**Intégration du portail de cluster Insights** : le volet **Insights** est également nouveau dans le portail de cluster HDInsight. Une fois la nouvelle intégration d’Azure Monitor effectuée, vous pouvez sélectionner le volet **Insights**, ainsi qu’un tableau de bord des métriques et des journaux prêts à l’emploi, spécifiques au type du cluster qui est automatiquement rempli. Ces tableaux de bord ont été repensés à partir de nos anciennes solutions Azure. Ils vous donnent un aperçu approfondi des performances et de l’intégrité de votre cluster.

**Insights à grande échelle** : vous pouvez utiliser le nouveau classeur **Insights à grande échelle** dans le portail **Azure Monitor** pour surveiller l’intégrité et les performances des clusters sur différents abonnements.

## <a name="customer-scenarios"></a>Scénarios clients

Les sections suivantes décrivent comment les clients peuvent utiliser la nouvelle intégration d’Azure Monitor dans différents scénarios. La section [Activer une nouvelle intégration d’Azure Monitor](#activate-a-new-azure-monitor-integration) explique comment activer et utiliser la nouvelle intégration d’Azure Monitor. La section [Migration à partir d’Azure Monitor Classic vers la nouvelle intégration d’Azure Monitor](#migrate-to-the-new-azure-monitor-integration) contient des informations supplémentaires pour les utilisateurs qui dépendent de l’ancienne intégration d’Azure Monitor.

> [!NOTE]
> Seuls les clusters créés fin de septembre 2020 et après peuvent bénéficier de la nouvelle intégration de la supervision d’Azure.

## <a name="activate-a-new-azure-monitor-integration"></a>Activer une nouvelle intégration Azure Monitor 

> [!NOTE]
> Vous devez disposer d’un espace de travail Log Analytics créé dans un abonnement auquel vous avez accès avant d’activer la nouvelle intégration. Pour plus d’informations sur la création d’un espace de travail Log Analytics, consultez [Créer un espace de travail Log Analytics dans le portail Azure](../azure-monitor/logs/quick-create-workspace.md).

Activez la nouvelle intégration en accédant à la page du portail de votre cluster et en faisant défiler le menu de gauche jusqu’à ce que vous atteigniez la section **Surveillance** . Dans la section **Surveillance** , sélectionnez **Superviser l’intégration**. Ensuite, sélectionnez **Activer** pour pouvoir choisir l’espace de travail Log Analytics auquel vous souhaitez envoyer vos journaux. Sélectionnez **Enregistrer** une fois que vous avez choisi votre espace de travail. 

### <a name="access-the-new-tables"></a>Accéder aux nouvelles tables

Vous pouvez accéder aux nouvelles tables de deux manières. 

### <a name="approach-1"></a>Approche 1 :
La première méthode pour accéder aux nouvelles tables passe par l’espace de travail Log Analytics. 

1. Accédez à l’espace de travail Log Analytics que vous avez sélectionné lorsque vous avez activé l’intégration. 
2. Faites défiler le menu sur le côté gauche de l’écran et sélectionnez **Journaux**. Un éditeur de requêtes de journaux affiche une liste de toutes les tables dans l’espace de travail. 
3. Si les tables sont regroupées par **Solution**, les nouvelles tables HDI sont répertoriées dans la section **Gestion des journaux** . 
4. Si vous regroupez les tables par **Type de ressource**, les tables se trouvent dans la section **Clusters HDInsight** , comme indiqué dans l’image ci-dessous. 

> [!NOTE]
> Ce processus décrit de quelle manière les journaux étaient accessibles dans l’ancienne intégration. Cela nécessite que l’utilisateur ait accès à l’espace de travail.

### <a name="approach-2"></a>Approche 2 :

La deuxième façon d’accéder aux nouvelles tables est d’accéder au portail du cluster.
 
1. Accédez à la page du portail de votre cluster et faites défiler le menu sur le côté gauche jusqu’à ce que la section **Surveillance** s’affiche. Dans cette section, vous verrez le volet **Journaux** . 
2. Sélectionnez **Journaux** et un éditeur de requête de journaux s’affiche. L’éditeur contient tous les journaux associés à la ressource du cluster. Vous avez envoyé les journaux à l’espace de travail Log Analytics lorsque vous avez activé l’intégration. Ces journaux fournissent un accès basé sur les ressources (RBAC). Avec RBAC, les utilisateurs qui ont accès au cluster mais pas à l’espace de travail peuvent voir les journaux qui sont associés au cluster.

À titre de comparaison, les captures d’écran suivantes illustrent l’affichage de l’espace de travail d’intégration hérité et la nouvelle vue de l’espace de travail d’intégration :

**Vue de l’espace de travail d’intégration hérité**

  :::image type="content" source="./media/log-analytics-migration/legacy-integration-workspace-view.png"  lightbox="./media/log-analytics-migration/legacy-integration-workspace-view.png" alt-text="Capture d’écran qui montre l’affichage de l’espace de travail d’intégration hérité." border="false":::

**Vue de l’espace de travail d’intégration hérité**

  :::image type="content" source="./media/log-analytics-migration/new-integration-workspace-view.png" lightbox="./media/log-analytics-migration/new-integration-workspace-view.png" alt-text="Capture d’écran qui montre l’affichage de l’espace de travail d’intégration hérité." border="false":::

### <a name="use-the-new-tables"></a>Utiliser les nouvelles tables

Ces intégrations peuvent vous aider à utiliser les nouvelles tables :

#### <a name="default-queries-to-use-with-new-tables"></a>Requêtes par défaut à utiliser avec les nouvelles tables

Dans l’éditeur de requête de journaux, définissez le bouton bascule sur **Requêtes** au-dessus de la liste de tables. Veillez à regrouper les requêtes par **Type de ressource** et à ce qu’aucun filtre ne soit défini pour un type de ressource autre que les **Clusters HDInsight**. L’illustration suivante montre l’apparence des résultats lorsqu’ils sont regroupés par **Type de ressource** et filtrés pour sur **Clusters HDInsight**. Sélectionnez-en un seul et il apparaît dans l’éditeur de requêtes de Journaux. Veillez à lire les commentaires inclus dans les requêtes, car certains vous obligent à entrer des informations, telles que le nom de votre cluster, pour que la requête s’exécute correctement.

:::image type="content" source="./media/log-analytics-migration/default-query-results-grouped-resource-type.png" alt-text="Capture d’écran qui montre le type de ressource groupé des résultats de la requête par défaut." border="true":::


#### <a name="create-your-own-queries"></a>Créer votre propre requête

Vous pouvez entrer vos propres requêtes dans l’éditeur de requêtes de Journaux. Les requêtes utilisées sur les anciennes tables ne sont pas valides sur les nouvelles tables, car la plupart des nouvelles tables ont des schémas nouveaux et affinés. Les requêtes par défaut sont de bonnes références pour la mise en forme des requêtes sur les nouvelles tables.

#### <a name="insights"></a>Insights

Les Insights sont des tableaux de bord de visualisation spécifiques aux clusters créés à l’aide de [Classeurs Azure](../azure-monitor/visualize/workbooks-overview.md). Ces tableaux de bord vous fournissent des graphiques détaillés et des visualisations sur la façon dont votre cluster fonctionne. Les tableaux de bord comportent des sections pour chaque type de cluster, YARN, pour les métriques du système et pour les journaux des composants. Vous pouvez accéder au tableau de bord de votre cluster en visitant la page de votre cluster sur le portail, en faisant défiler jusqu’à la section **Surveillance**, puis en sélectionnant le volet **Insights**. Le tableau de bord se charge automatiquement si vous avez activé la nouvelle intégration. Patientez quelques secondes que les graphiques se chargent au fur et à mesure qu’ils interrogent les journaux.

:::image type="content" source="./media/log-analytics-migration/visualization-dashboard.png" lightbox="./media/log-analytics-migration/visualization-dashboard.png" alt-text="Capture d’écran montrant le tableau de bord de visualisation.":::

#### <a name="custom-azure-workbooks"></a>Classeurs Azure personnalisés

Vous pouvez créer vos propres classeurs Azure avec des graphiques personnalisés et des visualisations. Sur la page du portail de votre cluster, faites défiler jusqu’à la section **Surveillance** , puis sélectionnez le volet **Classeurs** dans le menu de gauche. Vous pouvez commencer à utiliser un modèle vide ou utiliser l’un des modèles de la section **Clusters HDInsight** . Il existe un modèle pour chaque type de cluster. Les modèles sont utiles si vous souhaitez enregistrer des personnalisations spécifiques que les Insights HDInsight par défaut ne fournissent pas. N’hésitez pas à envoyer des demandes de nouvelles fonctionnalités dans les les Insights de HDInsight si vous pensez qu’il en manque.

#### <a name="at-scale-workbooks-for-new-azure-monitor-integrations"></a>Classeurs à grande échelle pour les nouvelles intégrations d’Azure Monitor

Utilisez notre nouveau classeur à grande échelle pour obtenir une expérience de surveillance sur plusieurs clusters pour vos clusters. Notre classeur à grande l’échelle vous montre les clusters pour lesquels la surveillance de pipeline est activée. Le classeur vous offre également un moyen simple de vérifier l’intégrité de plusieurs clusters à la fois. Pour afficher ce classeur :

1. Accédez à la page **Azure Monitor** à partir de la page d’accueil du portail Azure
2. Une fois sur la page **Azure Monitor**, sélectionnez **Hub Insights** dans la section **Insights** .
3. Sélectionnez **Clusters HDInsight** dans la section **Analytics** .

   :::image type="content" source="./media/log-analytics-migration/at-scale-workbook.png" lightbox="./media/log-analytics-migration/at-scale-workbook.png" alt-text="Capture d’écran montrant le classeur à grande échelle" border="false":::

#### <a name="alerts"></a>Alertes

Vous pouvez ajouter des alertes personnalisées à vos clusters et espaces de travail dans l’éditeur de requêtes Journaux. Accédez à l’éditeur de requêtes Journaux en sélectionnant le volet **Journaux** dans votre cluster ou portail d’espace de travail. Exécutez une requête, puis sélectionnez **Nouvelle règle d’alerte**, comme indiqué dans la capture d’écran suivante. Pour plus d'informations, consultez [Configuration des alertes](../azure-monitor/alerts/alerts-log.md).

:::image type="content" source="./media/log-analytics-migration/new-rule-alert.png" alt-text="Capture d’écran montrant l’alerte de nouvelle règle." border="false":::

## <a name="migrate-to-the-new-azure-monitor-integration"></a>Migrer vers la nouvelle intégration d’Azure Monitor

Si vous utilisez l’intégration d’Azure Monitor classique, vous devez apporter quelques ajustements aux nouveaux formats de table après avoir basculé vers la nouvelle intégration d’Azure Monitor.

Pour activer la nouvelle intégration d’Azure Monitor, suivez les étapes décrites dans la section [Activer une nouvelle intégration d’Azure Monitor](#activate-a-new-azure-monitor-integration) .

### <a name="run-queries-in-log-analytics"></a>Exécuter les requêtes dans Log Analytics

Étant donné que le nouveau format de table est différent du précédent, vos requêtes doivent être retravaillées afin que vous puissiez utiliser nos nouvelles tables. Une fois que vous avez activé la nouvelle intégration d’Azure Monitor, vous pouvez parcourir les tables et les schémas pour identifier les champs utilisés dans vos anciennes requêtes.

Nous fournissons une [table de mappage](#appendix-table-mapping) entre l’ancienne table et la nouvelle table pour vous aider à trouver rapidement les nouveaux champs que vous devez utiliser pour migrer vos tableaux de bord et vos requêtes.

**Requêtes par défaut** : nous avons créé des requêtes par défaut qui montrent comment utiliser les nouvelles tables pour les situations courantes. Les requêtes par défaut affichent également les informations qui sont disponibles dans chaque table. Vous pouvez accéder aux requêtes par défaut en suivant les instructions de la section [Requêtes par défaut à utiliser avec les nouvelles tables](#default-queries-to-use-with-new-tables) de cet article.

### <a name="update-dashboards-for-hdinsight-clusters"></a>Mettre à jour des tableaux de bord pour les clusters HDInsight

Si vous avez créé plusieurs tableaux de bord pour surveiller vos clusters HDInsight, vous devez ajuster la requête derrière la table une fois que vous avez activé la nouvelle intégration d’Azure Monitor. Le nom de la table ou le nom du champ peut changer dans la nouvelle intégration, mais toutes les informations dont vous disposez dans l’ancienne intégration sont incluses.

Reportez-vous à la [table de mappage](#appendix-table-mapping) entre l’ancienne table/l’ancien schéma et la nouvelle table/le nouveau schéma pour mettre à jour la requête en arrière-plan des tableaux de bord.

#### <a name="out-of-box-dashboards"></a>Tableaux de bord prêts à l’emploi 

Nous avons également amélioré les tableaux de bord prêts à l’emploi au niveau du cluster. Il y a un bouton en haut à droite de chaque graphique, qui vous permet de voir la requête sous-jacente qui produit les informations. Le graphique est un excellent moyen de vous familiariser avec la façon dont les nouvelles tables peuvent être interrogées efficacement. Vous pouvez accéder aux tableaux de bord prêts à l’emploi en suivant les instructions qui s’affichent dans les sections [Insights](#insights) et [Classeurs à grande échelle pour les nouvelles intégrations d’Azure Monitor](#at-scale-workbooks-for-new-azure-monitor-integrations).

### <a name="use-an-hdinsight-at-scale-monitoring-dashboard"></a>Utiliser un tableau de bord de surveillance à grande échelle HDInsight

Si vous utilisez le tableau de bord de surveillance prêt à l’emploi pour les clusters HDInsight comme HDInsight Spark Monitoring et HDInsight Interactive Monitoring, nous nous efforçons de vous fournir les mêmes fonctionnalités sur le portail Azure Monitor.

Vous verrez qu’il existe une option de clusters HDInsight dans Azure Monitor.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-azure-monitor.png" lightbox="./media/log-analytics-migration/hdinsight-azure-monitor.png" alt-text="Capture d’écran montrant l’option HDInsight dans Azure Monitor." border="false":::

Le Hub Insights du portail Azure Monitor vous permet de surveiller plusieurs clusters HDInsight dans un même emplacement. Nous organisons les clusters en fonction du type de charge de travail. si bien que vous voyez des types tels que Spark, HBase et Hive. Au lieu d’accéder à plusieurs tableaux de bord, vous pouvez maintenant surveiller tous vos clusters HDInsight depuis cet affichage.

> [!NOTE]
> Pour plus d’informations, consultez les sections [Insights](#insights) et [Classeurs à grande échelle pour les nouvelles intégrations d’Azure Monitor](#at-scale-workbooks-for-new-azure-monitor-integrations) dans cet article.

## <a name="enable-both-integrations-to-accelerate-the-migration"></a>Activer les deux intégrations pour accélérer la migration

Vous pouvez activer les intégrations d’Azure Monitor classique et nouvelle en même temps sur un cluster qui est éligible pour les deux intégrations afin de migrer rapidement vers la nouvelle intégration d’Azure Monitor. La nouvelle intégration est disponible pour tous les clusters créés après la mi-septembre 2020.

De cette façon, vous pouvez facilement effectuer une comparaison côte à côte pour les requêtes que vous utilisez.

### <a name="enabling-the-classic-integration"></a>Activation de l’intégration classique

Si vous utilisez un cluster créé après le 1er septembre 2020, vous verrez la nouvelle expérience du portail dans le portail de votre cluster. Pour activer le nouveau pipeline, suivez les étapes décrites dans la section [Activer une nouvelle intégration d’Azure Monitor](#activate-a-new-azure-monitor-integration) . Pour activer l’intégration classique sur ce cluster, accédez à la page du portail de votre cluster. Sélectionnez le volet **Supervision de l’intégration** dans la section **Surveillance** du menu sur le côté gauche de la page du portail de votre cluster. Sélectionnez **Configurer Azure Monitor pour l’intégration des clusters HDInsight (classique)** . Un menu contextuel latéral s’affiche avec un bouton bascule que vous pouvez utiliser pour activer et désactiver l’intégration classique de la supervision Azure. 

> [!NOTE]
> Les journaux et les métriques de l’intégration classique ne s’affichent pas sur la page des journaux et insights du portail de votre cluster. Seuls les nouveaux journaux et métriques d’intégration seront présents à ces emplacements.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-classic-integration.png" alt-text="Capture d’écran montrant le lien permettant d’accéder à l’intégration classique." border="false":::

La création de nouveaux clusters avec l’intégration d’Azure Monitor classique n’est pas disponible après le 1er juillet 2021.

## <a name="release-and-support-timeline"></a>Chronologie de sortie et de support

- L’intégration classique de la supervision Azure sera indisponible après le 15 octobre 2021. Vous ne pourrez plus activer l’intégration classique de la supervision Azure après cette date.
- Les intégrations classiques existantes de la supervision Azure continueront à fonctionner. La prise en charge de l’intégration classique de la supervision Azure est limitée. 
  - Les problèmes sont examinés une fois que les clients ont envoyé le ticket de support.
  - Si la solution nécessite la modification d’une image, les clients doivent passer à la nouvelle intégration.
  - Nous n’appliquerons plus de correctifs aux clusters d’intégration classique de la supervision Azure, sauf en cas de problèmes de sécurité critiques.

## <a name="appendix-table-mapping"></a>Annexe : mappage de table

Les graphiques suivants montrent les mappages de table depuis l’intégration classique de la supervision Azure vers la nouvelle intégration. La colonne **Charge de travail** décrit la charge de travail à laquelle chaque table est associée. La ligne **Nouvelle table** affiche le nom de la nouvelle table. La ligne **Description** décrit le type de journaux/métriques qui seront disponibles dans cette table. La ligne **Ancienne table** est une liste de toutes les tables de l’intégration classique d’Azure Monitor dont les données sont maintenant présentes dans la table répertoriée à la ligne **Nouvelle table**.

> [!NOTE]
> Certaines tables sont nouvelles et ne sont pas basées sur d’anciennes tables.

## <a name="general-workload-tables"></a>Tables de charge de travail générales

| Nouvelle table | Détails |
| --- | --- |
| HDInsightAmbariSystemMetrics | <ul><li>**Description** : cette table contient des métriques système collectées à partir d’Ambari. Les métriques proviennent désormais de chaque nœud du cluster (à l’exception des nœuds de périphérie) au lieu des deux nœuds principaux uniquement. Chaque métrique est désormais une colonne et chaque métrique est signalée une fois par enregistrement.</li><li>**Ancienne table** : metrics\_cpu\_nice\_cl, metrics\_cpu\_system\_cl, metrics\_cpu\_user\_cl, metrics\_memory\_cache\_CL, metrics\_memory\_swap\_CL, metrics\_memory\_total\_CLmetrics\_memory\_buffer\_CL, metrics\_load\_1min\_CL, metrics\_load\_cpu\_CL, metrics\_load\_nodes\_CL, metrics\_load\_procs\_CL, metrics\_network\_in\_CL, metrics\_network\_out\_CL</li></ul>|
| HDInsightAmbariClusterAlerts | <ul><li>**Description** : cette table contient des alertes de cluster Ambari à partir de chaque nœud du cluster (à l’exception des nœuds de périphérie). Chaque alerte est un enregistrement dans cette table.</li><li>**Ancienne table** : metrics\_cluster\_alerts\_CL</li></ul>|
| HDInsightSecurityLogs | <ul><li>**Description** : cette table contient des enregistrements des journaux d’audit et d’authentification Ambari.</li><li>**Ancienne table** : log\_ambari\_audit\_CL, log\_auth\_CL</li></ul>|
| HDInsightRangerAuditLogs | <ul><li>**Description** : cette table contient tous les enregistrements du journal d’audit Ranger pour les clusters ESP.</li><li>**Ancienne table** : Ranger\_audit\_journaux\_CL</li></ul>|
| HDInsightGatewayAuditLogs\_CL | <ul><li>**Description** : cette table contient les informations d’audit des nœuds de passerelle. Il a le même format que le tableau dans la colonne des anciennes tables. **Elle se trouve toujours dans la section des journaux personnalisés.**</li><li>**Ancienne table** : log\_gateway\_Audit\_CL</li></ul>|

## <a name="spark-workload"></a>Charge de travail Spark

> [!NOTE]
> Les tables liées à l’application Spark ont été remplacées par 11 nouvelles tables Spark (à partir de HDInsightSpark *) qui fournissent des informations plus détaillées sur vos charges de travail Spark.


| Nouvelle table | Détails |
| --- | --- |
| HDInsightSparkLogs | <ul><li>**Description** : cette table contient tous les journaux liés à Spark et son composant Livy et Jupyter associé.</li><li>**Ancienne table** : log\_livy,\_CL, log\_jupyter\_CL, log\_spark\_CL, log\_sparkappsexecutors\_CL, log\_sparkappsdrivers\_CL</li></ul>|
| HDInsightSparkApplicationEvents | <ul><li>**Description** : cette table contient des informations sur les événements pour les applications Spark, y compris l’heure d’envoi et d’achèvement, l’ID d’application et le nom de l’application. Elle est utile pour assurer le suivi du moment où les applications sont démarrées et terminées. </li></ul>|
| HDInsightSparkBlockManagerEvents | <ul><li>**Description** : cette table contient des informations sur les événements liés au gestionnaire de bloc Spark. Elle comprend des informations telles que l’utilisation de la mémoire de l’exécuteur.</li></ul>|
| HDInsightSparkEnvironmentEvents | <ul><li>**Description** : cette table contient des informations sur les événements relatives à l’environnement dans lequel une application s’exécute, notamment le mode de déploiement Spark, le maître et les informations relatives à l’exécuteur.</li></ul>|
| HDInsightSparkExecutorEvents | <ul><li>**Description** : cette table contient des informations sur les événements relatifs à l’utilisation d’exécuteur Spark pour une application.</li></ul>|
| HDInsightSparkExtraEvents | <ul><li>**Description** : cette table contient des informations sur les événements qui ne rentrent dans aucune autre table Spark. </li></ul>|
| HDInsightSparkJobEvents | <ul><li>**Description** : cette table contient des informations sur les travaux Spark, y compris leurs heures de début et de fin, le résultat et les étapes associées.</li></ul>|
| HDInsightSparkSqlExecutionEvents | <ul><li>**Description** : cette table contient des informations sur les événements sur les requêtes Spark SQL, y compris les informations de plan et la description, ainsi que les heures de début et de fin.</li></ul>|
| HDInsightSparkStageEvents | <ul><li>**Description** : cette table contient des informations sur les événements pour les étapes Spark, y compris les heures de début et de fin, l’état d’échec et les informations d’exécution détaillées.</li></ul>|
| HDInsightSparkStageTaskAccumulables | <ul><li>**Description** : cette table contient des métriques de performances pour les étapes et les tâches.</li></ul>|
| HDInsightTaskEvents | <ul><li>**Description** : cette table contient des informations sur les événements pour les tâches Spark, y compris l’heure de début et d’achèvement, les étapes associées, l’état d’exécution et le type de tâche.</li></ul>|
| HDInsightJupyterNotebookEvents | <ul><li>**Description** : cette table contient des informations sur les événements pour les Notebooks Jupyter.</li></ul>|

## <a name="hadoopyarn-workload"></a>Charge de travail Hadoop/YARN

| Nouvelle table | Détails |
| --- | --- |
| HDInsightHadoopAndYarnMetrics | <ul><li>**Description** : cette table contient des métriques JMX à partir des infrastructures HADOOP et YARN. Elle contient les mêmes métriques JMX que les anciennes tables de journaux personnalisés, plus des métriques que nous considérons comme importantes. Nous avons ajouté les métriques du serveur de chronologie, du gestionnaire de nœuds et de l’historique des travaux. Elle contient une métrique par enregistrement.</li><li>**Ancienne table** : metrics\_resourcemanager\_clustermetrics\_CL, metrics\_resourcemanager\_jvm\_CL, metrics\_resourcemanager\_queue\_root\_CL, metrics\_resourcemanager\_queue\_root\_joblauncher\_CL, metrics\_resourcemanager\_queue\_root\_default\_CL, metrics\_resourcemanager\_queue\_root\_thriftsvr\_CL</li></ul>|
| HDInsightHadoopAndYarnLogs | <ul><li>**Description** : cette table contient tous les journaux générés à partir des infrastructures HADOOP et YARN.</li><li>**Ancienne table** : log\_mrjobsummary\_CL, log\_resourcemanager\_CL, log\_timelineserver\_CL, log\_nodemanager\_CL</li></ul>|

 
## <a name="hivellap-workload"></a>Charge de travail Hive/LLAP 

| Nouvelle table | Détails |
| --- | --- |
| HDInsightHiveAndLLAPMetrics | <ul><li>**Description** : cette table contient des métriques JMX à partir des infrastructures Hive et LLAP. Elle contient les mêmes mesures JMX que les anciennes tables Journaux personnalisés. Elle contient une métrique par enregistrement.</li><li>**Ancienne table** : llap\_metrics\_hiveserver2\_CL, llap\_metrics\_hs2\_metrics\_subsystemllap\_metrics\_jvm\_CL, llap\_metrics\_llap\_daemon\_info\_CL, llap\_metrics\_buddy\_allocator\_info\_CL, llap\_metrics\_deamon\_jvm\_CL, llap\_metrics\_io\_CL, llap\_metrics\_executor\_metrics\_CL, llap\_metrics\_metricssystem\_stats\_CL, llap\_metrics\_cache\_CL</li></ul>|
| HDInsightHiveAndLLAPLogs | <ul><li>**Description** : cette table contient des journaux générés depuis Hive, LLAP et leurs composants associés : WebHCat et Zeppelin.</li><li>**Ancienne table** : log\_hivemetastore\_CL log\_hiveserver2\_CL, log\_hiveserve2interactive\_CL, log\_webhcat\_CL, log\_zeppelin\_zeppelin\_CL</li></ul>|


## <a name="kafka-workload"></a>Charge de travail Kafka

| Nouvelle table | Détails |
| --- | --- |
| HDInsightKafkaMetrics | <ul><li>**Description** : cette table contient des métriques JMX de Kafka. Elle contient les mêmes métriques JMX que les anciennes tables de journaux personnalisés, plus des métriques que nous considérons comme importantes. Elle contient une métrique par enregistrement.</li><li>**Ancienne table** : metrics\_kafka\_CL</li></ul>|
| HDInsightKafkaLogs | <ul><li>**Description** : cette table contient tous les journaux générés à partir des répartiteurs Kafka.</li><li>**Ancienne table** : log\_kafkaserver\_CL, log\_kafkacontroller\_CL</li></ul>|

## <a name="hbase-workload"></a>Charge de travail HBase

| Nouvelle table | Détails |
| --- | --- |
| HDInsightHBaseMetrics | <ul><li>**Description**: cette table contient des métriques JMX de HBase. Elle contient les mêmes métriques JMX que celles des tables listées dans l’ancienne colonne Schéma. Contrairement aux anciennes tables, chaque ligne contient une mesure.</li><li>**Ancienne table** : metrics\_regionserver\_CL, metrics\_regionserver\_wal\_CL, metrics\_regionserver\_ipc\_CL, metrics\_regionserver\_os\_CL, metrics\_regionserver\_replication\_CL, metrics\_restserver\_CL, metrics\_restserver\_jvm\_CL, metrics\_hmaster\_assignmentmanager\_CL, metrics\_hmaster\_ipc\_CL, metrics\_hmaser\_os\_CL, metrics\_hmaster\_balancer\_CL, metrics\_hmaster\_jvm\_CL, metrics\_hmaster\_CL,metrics\_hmaster\_fs\_CL</li></ul>|
| HDInsightHBaseLogs | <ul><li>**Description** : cette table contient des journaux de HBase et ses composants associés : Phoenix et HDFS.</li><li>**Ancienne table** : log\_regionserver\_CL, log\_restserver\_CL, log\_phoenixserver\_CL, log\_hmaster\_CL, log\_hdfsnamenode\_CL, log\_garbage\_collector\_CL</li></ul>|

## <a name="storm-workload"></a>Charge de travail Storm

| Nouvelle table | Détails |
| --- | --- |
| HDInsightStormMetrics | <ul><li>**Description** : cette table contient les mêmes mesures JMX que les tables de la section Anciennes tables. Ses lignes contiennent une métrique par enregistrement.</li><li>**Ancienne table** : metrics\_stormnimbus\_CL, metrics\_stormsupervisor\_CL</li></ul>|
| HDInsightStormTopologyMetrics | <ul><li>**Description** : cette table contient des métriques au niveau de la topologie de Storm. Il s’agit de la même forme que la table figurant dans la section Anciennes tables.</li><li>**Ancienne table** : metrics\_stormrest\_CL</li></ul>|
| HDInsightStormLogs | <ul><li>**Description** : Cette table contient tous les journaux générés à partir de Storm.</li><li>**Ancienne table** : log\_supervisor\_CL, log\_nimbus\_CL</li></ul>|

## <a name="oozie-workload"></a>Charge de travail Oozie

| Nouvelle table | Détails |
| --- | --- |
| HDInsightOozieLogs | <ul><li>**Description** : cette table contient tous les journaux générés à partir de l’infrastructure Oozie.</li><li>**Ancienne table** : Log\_oozie\_CL</li></ul>|

## <a name="next-steps"></a>Étapes suivantes
[Interroger les journaux Azure Monitor pour surveiller les clusters HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)