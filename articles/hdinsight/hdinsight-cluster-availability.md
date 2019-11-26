---
title: 'Monitorage : Journaux Apache Ambari et Azure Monitor – Azure HDInsight'
description: Découvrez comment utiliser les journaux Ambari et Azure Monitor pour surveiller la disponibilité et l’intégrité du cluster.
keywords: surveillance, ambari, surveiller, analytique des journaux d’activité, alerte, disponibilité, intégrité
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.openlocfilehash: eeaef8851035bbb8d2f39bcf9f366118545fcf0f
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044478"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Comment surveiller la disponibilité de cluster avec les journaux Azure Monitor et Ambari

Les clusters HDInsight incluent Apache Ambari, qui fournit des informations sur l’intégrité en un clin d’œil, des alertes prédéfinies, ainsi que l’intégration des journaux Azure Monitor, qui fournit des mesures et des journaux qui peuvent être interrogés, ainsi que des alertes configurables.

Ce document montre comment utiliser ces outils pour surveiller votre cluster et vous guide au travers des exemples de configuration d’une alerte d’Ambari, de surveillance des taux de disponibilité de nœud et de création d’une alerte Azure Monitor qui se déclenche lorsqu’aucune pulsation n’a été reçue à partir d’un ou de plusieurs nœuds en cinq heures.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>tableau de bord

Le tableau de bord Ambari est accessible en cliquant sur le lien **d’accueil Ambari** lien dans la section **Tableaux de bord du cluster** du panneau HDInsight Overview (Vue d’ensemble de HDInsight) dans le portail Azure, comme illustré ci-dessous. Vous pouvez également y accéder en entrant l’URL suivante dans un navigateur [https://\<clustername\>.azurehdinsight.net](https://clustername.azurehdinsight.net/)

![Affichage du portail de ressources HDInsight](media/hdinsight-cluster-availability/portal-oms-overview1.png)

Vous devez ensuite fournir un nom d’utilisateur et un mot de passe pour vous connecter au cluster. Saisissez les informations d’identification que vous avez choisies lors de la création du cluster.

Vous êtes alors redirigé vers le tableau de bord Ambari, qui contient des widgets affichant un certain nombre de mesures pour vous donner un aperçu rapide de l’intégrité de votre cluster HDInsight. Ces widgets affichent des métriques telles que le nombre de DataNodes (nœuds Worker) et de JournalNodes (nœud Zookeeper) en direct, la durée de fonctionnement des NameNodes (nœuds principaux), ainsi que des mesures spécifiques à certains types de cluster, tels que la durée de fonctionnement de YARN ResourceManager pour les clusters Spark et Hadoop.

![Apache Ambari - Affichage du tableau de bord](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hôtes - afficher l’état de chaque nœud

Vous pouvez également afficher des informations d’état pour chaque nœud. Cliquez sur l’onglet **Hôtes** pour afficher une liste de tous les nœuds de votre cluster et consulter des informations de base sur chaque nœud. La coche verte à gauche de chaque nom de nœud indique que tous les composants du nœud sont opérationnels. Si un composant d’un nœud est en panne, vous verrez un triangle d’alerte rouge au lieu de la coche verte.

![HDInsight Apache Ambari - Affichage des hôtes](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Vous pouvez ensuite cliquer sur le **nom** d’un nœud pour afficher des métriques d’hôtes plus détaillées pour ce nœud. Cet affichage indique l’état/la disponibilité de chaque composant.

![Affichage de nœud unique pour les hôtes Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Alertes Ambari

Ambari offre également plusieurs alertes configurables qui peuvent fournir des notifications pour certains événements. Lorsque des alertes sont déclenchées, elles sont affichées dans le coin supérieur gauche d’Ambari, sous la forme d’un badge rouge indiquant le nombre d’alertes. Cliquer sur ce badge permet d’afficher une liste des alertes actuelles.

![Apache Ambari - Nombre d’alertes actuelles](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Pour afficher une liste de définitions d’alerte et leurs états, cliquez sur l’onglet **Alertes**, comme indiqué ci-dessous.

![Affichage des définitions d’alertes Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari offre de nombreuses alertes prédéfinies relatives à la disponibilité, notamment :

| Nom de l’alerte                        | Description                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DataNode Health Summary (Récapitulatif d’intégrité DataNode)           | Cette alerte de niveau de service est déclenchée si des DataNodes ne sont pas sains                                                                                                                |
| NameNode High Availability Health (Intégrité de la haute disponibilité de NameNode) | Cette alerte de niveau de service est déclenchée si Active NameNode ou Standby NameNode ne sont pas exécutés.                                                                              |
| Percent JournalNodes Available (Pourcentage de JournalNodes disponibles)    | Cette alerte est déclenchée si le nombre de JournalNodes en panne dans le cluster est supérieur au seuil critique configuré. Elle agrège les résultats des vérifications de processus JournalNode. |
| Percent DataNodes Available (Pourcentage de DataNodes disponibles)       | Cette alerte est déclenchée si le nombre de DataNodes en panne dans le cluster est supérieur au seuil critique configuré. Elle agrège les résultats des vérifications de processus DataNode.       |

Une liste complète des alertes Ambari qui permet de surveiller la disponibilité d’un cluster se trouve [ici](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui).

Pour afficher les détails d’une alerte ou modifier les critères, cliquez sur le **nom** de l’alerte. Prenez **DataNode Health Summary** (Récapitulatif d’intégrité DataNode) comme exemple. Vous pouvez voir une description de l’alerte, ainsi que des critères spécifiques qui déclencheront une alerte de type « avertissement » ou « critique » et l’intervalle de vérification des critères. Pour modifier la configuration, cliquez sur le bouton **Modifier** dans le coin supérieur droit de la zone Configuration.

![Configuration de l’alerte Apache Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Ici, vous pouvez modifier la description et, plus important encore, l’intervalle de vérification et les seuils des alertes de type avertissement ou critique.

![Affichage de configuration de l’alerte Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

Dans cet exemple, vous pouvez faire en sorte que 2 DataNodes non sains déclenchent une alerte critique et qu’1 DataNode non sain déclenche uniquement un avertissement. Cliquez sur **Enregistrer** quand vous avez terminé d’appliquer les modifications.

### <a name="email-notifications"></a>Notifications par e-mail

Vous pouvez également configurer les notifications par e-mail pour les alertes Ambari. Pour ce faire, dans l’onglet **Alertes**, cliquez sur le bouton **Actions** dans le coin supérieur gauche, puis sur **Gérer les modifications**.

![Action de gestion des notifications Ambari](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Une boîte de dialogue pour la gestion des notifications d’alerte s’ouvre. Cliquez sur le **+** en bas de la boîte de dialogue et renseignez les champs obligatoires pour indiquer à Ambari les détails du serveur de messagerie à partir duquel envoyer des e-mails.

> [!TIP]
> Configurer les notifications par e-mail Ambari peut être un bon moyen pour centraliser la réception d’alertes lors de la gestion de nombreux clusters HDInsight.

## <a name="azure-monitor-logs-integration"></a>Intégration des journaux Azure Monitor

Les journaux Azure Monitor permettent de collecter et d’agréger au même endroit des données générées par plusieurs sources, comme les clusters HDInsight, pour offrir une expérience de surveillance unifiée.

Vous aurez besoin d’un espace de travail Log Analytics pour stocker les données collectées. Si vous n’en avez pas, suivez les instructions données ici : [Créer un espace de travail Log Analytics dans le portail Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Activer l’intégration des journaux Azure Monitor HDInsight

Sur la page de ressources du cluster HDInsight dans le portail, cliquez sur le panneau **Operations Management Suite**. Ensuite, cliquez sur **activer** et sélectionnez votre espace de travail Log Analytics dans la liste déroulante.

![Panneau Operations Management Suite HDInsight](media/hdinsight-cluster-availability/hdi-portal-oms-enable.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Interroger des métriques et de tables de journaux dans le panneau des journaux

Une fois que l’intégration des journaux Azure Monitor est activée (cela peut prendre quelques minutes), accédez à votre ressource **d’espace de travail Log Analytics** et cliquez sur le panneau **Journaux**

![Panneau des journaux d’espace de travail Log Analytics](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

Le panneau **Journaux** répertorie de nombreux exemples de requêtes, tels que :

| Nom de la requête                      | Description                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Computers availability today (Disponibilité des ordinateurs aujourd’hui)    | Suit le nombre d’ordinateurs envoyant des journaux, heure par heure                     |
| List heartbeats (Répertorier les pulsations)                 | Répertorie toutes les pulsations d’ordinateur à partir de la dernière heure                           |
| Last heartbeat of each computer (Dernière pulsation de chaque ordinateur) | Indique la dernière pulsation envoyée par chaque ordinateur                             |
| Unavailable computers (Ordinateurs non disponibles)           | Répertorie tous les ordinateurs connus qui n’ont pas envoyé de pulsation au cours des 5 dernières heures |
| Availability rate (Taux de disponibilité)               | Calcule le taux de disponibilité de chaque ordinateur connecté                |

Par exemple, exécutez l’exemple de requête **Availability rate** (Taux de disponibilité) en cliquant sur **Exécuter** pour cette requête, comme indiqué dans la capture d’écran ci-dessus. Vous pourrez ainsi connaître le taux de disponibilité, exprimé en pourcentage, de chaque nœud de votre cluster. Si vous avez activé plusieurs clusters HDInsight pour l’envoi de métriques vers le même espace de travail Log Analytics, vous pouvez voir le taux de disponibilité de tous les nœuds de ces clusters.

![Exemple de requête « taux de disponibilité » du panneau de journaux d’espace de travail Log Analytics](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> Le taux de disponibilité étant mesuré sur une période de 24 heures, votre cluster devra s’exécuter pendant au moins 24 heures avant de pouvoir consulter le taux de disponibilité précis.

Vous pouvez épingler ce tableau à un tableau de bord partagé en cliquant sur **Épingler** dans le coin supérieur droit. Si vous n’avez pas de tableau de bord partagé accessible en écriture, vous pouvez voir comment en créer un ici : [Créer et partager des tableaux de bord dans le portail Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Alertes Azure Monitor

Vous pouvez également configurer des alertes Azure Monitor qui se déclencheront lorsque la valeur d’une métrique ou les résultats d’une requête respectent certaines conditions. Par exemple, nous allons créer une alerte pour envoyer un e-mail quand un ou plusieurs nœuds n’ont pas envoyé de pulsation pendant 5 heures (c’est-à-dire, qu’ils sont censés être indisponibles).

Dans le panneau **Journaux**, exécutez l’exemple de requête **Unavailable computers** (Ordinateurs non disponibles) en cliquant sur **Exécuter** pour cette requête, comme illustré ci-dessous.

![Exemple d'« ordinateurs non disponibles » du panneau de journaux d’espace de travail Log Analytics](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Si tous les nœuds sont disponibles, cette requête ne doit renvoyer aucun résultat pour l’instant. Cliquez sur **Nouvelle règle d’alerte** pour commencer à configurer votre alerte pour cette requête.

![Nouvelle règle d’alerte de l’espace de travail Log Analytics](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Trois composants constituent une alerte : la *ressource* pour laquelle créer la règle (ici, l’espace de travail Log Analytics), la *condition* pour déclencher l’alerte et les *groupes d’actions* qui déterminent le comportement prévu au moment du déclenchement de l’alerte.

Cliquez sur le **titre de la condition**, comme illustré ci-dessous, pour terminer la configuration de la logique de signal.

![Créer une règle d'alerte dans le portail - Condition](media/hdinsight-cluster-availability/portal-condition-title.png)

Le panneau **Configurer la logique du signal** s’ouvre.

Définissez la section **Logique d’alerte** comme suit :

*Basé sur : Nombre de résultats, Condition : Supérieur à, Seuil : 0.*

Étant donné que cette requête renvoie uniquement les nœuds non disponibles comme résultats, si le nombre de résultats est toujours supérieur à 0, l’alerte doit se déclencher.

Dans la section **Évaluées sur la base de**, définissez la **période** et la **fréquence** selon la fréquence à laquelle vous souhaitez rechercher les nœuds non disponibles.

Notez que pour les besoins de cette alerte, vous devez vous assurer que la **période est identique à la fréquence.** Vous pouvez trouver plus d’informations sur la période, la fréquence et d’autres paramètres d’alerte [ici](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Cliquez sur **Terminé** lorsque vous avez terminé de configurer la logique de signal.

![Configurer la logique du signal de règle d’alerte](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Si vous n’avez pas de groupe d’actions existant, cliquez sur **Créer nouveau** dans la section **Groupes d’actions**.

![Créer une règle d'alerte - Nouveau groupe d'actions](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Le panneau **Ajouter un groupe d’actions** s’ouvre. Choisissez un **nom de groupe d’actions**, un **nom court**, un **abonnement** et un **groupe de ressources**. Dans la section **Actions**, choisissez un **nom d’action** et sélectionnez **E-mail/SMS/Push/Voix** pour **Type d’action**.

> [!NOTE]
> Plusieurs autres actions peuvent être déclenchées par une alerte, par exemple Fonction Azure, LogicApp, Webhook, ITSM et Runbook Automation. [En savoir plus](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information).

Le panneau **E-mail/SMS/Push/Voix** s’ouvre. Choisissez un **nom** de destinataire, **cochez** la case **E-mail**, puis tapez une adresse de messagerie à laquelle vous souhaitez envoyer l’alerte. Cliquez sur **OK** dans le panneau **E-mail/SMS/Push, voix**, puis dans le panneau **Ajouter un groupe d’actions** pour terminer la configuration de votre groupe d’actions.

![Créer une règle d'alerte - Ajouter un groupe d'actions](media/hdinsight-cluster-availability/portal-add-action-group.png)

Une fois ces panneaux fermés, vous devez voir votre groupe d’actions répertorié sous la section **Groupes d’actions**. Pour finir, complétez la section **Détails de l’alerte** en tapant un **nom de règle d’alerte** et une **description** et en choisissant une **gravité**.
Cliquez sur **Créer une règle d’alerte** pour terminer.

![Créer une règle d'alerte dans le portail - Terminer](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> La possibilité de spécifier une **gravité** est très utile, notamment lorsque vous créez plusieurs alertes. Par exemple, vous pouvez créer une alerte pour déclencher un avertissement (Gravité 1) si un seul nœud principal tombe en panne et une autre alerte de type critique (Gravité 0) dans le cas peu probable où les deux nœuds principaux tomberaient en panne.

Lorsque la condition de cette alerte est remplie, l’alerte se déclenche et vous recevez un e-mail contenant les détails de l’alerte comme suit :

![Exemple d'e-mail d’alerte Azure Monitor](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

Vous pouvez également afficher toutes les alertes qui ont été déclenchées, regroupées par gravité, en accédant au panneau **Alertes** de votre **espace de travail Log Analytics**.

![Alertes d’espace de travail Log Analytics](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Cliquer sur un regroupement de gravité (par exemple, **Gravité 1**, comme illustré ci-dessus) affiche les enregistrements de toutes les alertes avec cette gravité qui ont été déclenchées, comme ci-dessous :

![Alertes de gravité 1 espace de travail Log Analytics](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Étapes suivantes
- [Availability and reliability of Apache Hadoop clusters in HDInsight](hdinsight-high-availability-linux.md) (Disponibilité et fiabilité des clusters Apache Hadoop dans HDInsight)
