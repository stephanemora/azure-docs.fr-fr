---
title: 'Monitorage : Journaux Apache Ambari et Azure Monitor – Azure HDInsight'
description: Découvrez comment utiliser les journaux Ambari et Azure Monitor pour surveiller la disponibilité et l’intégrité du cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060170"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>Superviser la disponibilité des clusters à l’aide d’Apache Ambari et des journaux Azure Monitor

Les clusters HDInsight incluent Apache Ambari, qui fournit des informations sur l’intégrité en un clin d’œil, des alertes prédéfinies, ainsi que l’intégration des journaux Azure Monitor, qui fournit des mesures et des journaux qui peuvent être interrogés, ainsi que des alertes configurables.

Ce document montre comment utiliser ces outils pour superviser votre cluster. À travers plusieurs exemples, il explique pas à pas comment configurer une alerte Ambari, superviser le taux de disponibilité des nœuds, et créer une alerte Azure Monitor qui se déclenche quand aucune pulsation n’a été reçue d’un ou de plusieurs nœuds durant une période de cinq heures.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>tableau de bord

Vous pouvez accéder au tableau de bord Ambari en sélectionnant le lien **Accueil Ambari** dans la section **Tableaux de bord du cluster** du panneau HDInsight Overview (Vue d’ensemble de HDInsight) dans le portail Azure, comme illustré ci-dessous. Vous pouvez également y accéder à partir d’un navigateur, à l’adresse `https://CLUSTERNAME.azurehdinsight.net` (où CLUSTERNAME est le nom de votre cluster).

![Affichage du portail de ressources HDInsight](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Vous êtes ensuite invité à entrer un nom d’utilisateur et un mot de passe pour vous connecter au cluster. Saisissez les informations d’identification que vous avez choisies lors de la création du cluster.

Vous êtes alors redirigé vers le tableau de bord Ambari, qui contient des widgets affichant diverses métriques qui vous donnent un aperçu rapide de l’état d’intégrité de votre cluster HDInsight. Ces widgets affichent des métriques telles que le nombre de DataNodes (nœuds Worker) et de JournalNodes (nœud Zookeeper) en direct, la durée de fonctionnement des NameNodes (nœuds principaux), ainsi que des mesures spécifiques à certains types de cluster, tels que la durée de fonctionnement de YARN ResourceManager pour les clusters Spark et Hadoop.

![Apache Ambari - Affichage du tableau de bord](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hôtes - afficher l’état de chaque nœud

Vous pouvez également afficher des informations d’état pour chaque nœud. Sélectionnez l’onglet **Hôtes** pour afficher une liste de tous les nœuds de votre cluster et consulter des informations de base sur chaque nœud. La coche verte à gauche de chaque nom de nœud indique que tous les composants du nœud sont opérationnels. Si un composant est arrêté sur un nœud, vous verrez un triangle d’alerte rouge au lieu de la coche verte.

![HDInsight Apache Ambari - Affichage des hôtes](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Vous pouvez ensuite sélectionner le **nom** d’un nœud pour afficher des métriques d’hôtes plus détaillées pour ce nœud. Cet affichage indique l’état/la disponibilité de chaque composant.

![Affichage de nœud unique pour les hôtes Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Alertes Ambari

Ambari offre également plusieurs alertes configurables qui peuvent fournir des notifications pour certains événements. Les alertes déclenchées sont signalées dans le coin supérieur gauche d’Ambari, sous la forme d’un badge rouge indiquant le nombre d’alertes. Sélectionnez ce badge pour voir une liste des alertes actives.

![Apache Ambari - Nombre d’alertes actuelles](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Pour afficher une liste de définitions d’alerte et leurs états, sélectionnez l’onglet **Alertes**, comme ci-dessous.

![Affichage des définitions d’alertes Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari offre de nombreuses alertes prédéfinies relatives à la disponibilité, notamment :

| Nom de l’alerte                        | Description   |
|---|---|
| DataNode Health Summary (Récapitulatif d’intégrité DataNode)           | Cette alerte de niveau de service est déclenchée si des DataNodes ne sont pas sains|
| NameNode High Availability Health (Intégrité de la haute disponibilité de NameNode) | Cette alerte de niveau de service est déclenchée si Active NameNode ou Standby NameNode ne sont pas exécutés.|
| Percent JournalNodes Available (Pourcentage de JournalNodes disponibles)    | Cette alerte est déclenchée si le nombre de JournalNodes en panne dans le cluster est supérieur au seuil critique configuré. Elle agrège les résultats des vérifications de processus JournalNode. |
| Percent DataNodes Available (Pourcentage de DataNodes disponibles)       | Cette alerte est déclenchée si le nombre de DataNodes en panne dans le cluster est supérieur au seuil critique configuré. Elle agrège les résultats des vérifications de processus DataNode.|

Une liste complète des alertes Ambari qui permet de surveiller la disponibilité d’un cluster se trouve [ici](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui).

Pour afficher les détails d’une alerte ou en modifier les critères, sélectionnez le **nom** de l’alerte. Prenez **DataNode Health Summary** (Récapitulatif d’intégrité DataNode) comme exemple. Vous voyez la description de l’alerte, les critères spécifiques qui déclencheront une alerte de type « avertissement » ou « critique » ainsi que l’intervalle de vérification des critères. Pour modifier la configuration, sélectionnez le bouton **Modifier** dans le coin supérieur droit de la zone Configuration.

![Configuration de l’alerte Apache Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Ici, vous pouvez modifier la description et, plus important encore, l’intervalle de vérification et les seuils des alertes de type avertissement ou critique.

![Vue pour modifier les configurations d’alertes Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

Dans cet exemple, vous pouvez faire en sorte que 2 DataNodes non sains déclenchent une alerte critique et qu’1 DataNode non sain déclenche uniquement un avertissement. Sélectionnez **Enregistrer** quand vous avez terminé les modifications.

### <a name="email-notifications"></a>Notifications par e-mail

Vous pouvez également configurer les notifications par e-mail pour les alertes Ambari. Pour ce faire, dans l’onglet **Alertes**, cliquez sur le bouton **Actions** dans le coin supérieur gauche, puis sur **Gérer les modifications**.

![Action de gestion des notifications Ambari](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Une boîte de dialogue pour la gestion des notifications d’alerte s’ouvre. Sélectionnez **+** en bas de la boîte de dialogue et renseignez les champs obligatoires pour indiquer à Ambari les détails du serveur de messagerie à partir duquel envoyer des e-mails.

> [!TIP]
> Configurer les notifications par e-mail Ambari peut être un bon moyen pour centraliser la réception d’alertes lors de la gestion de nombreux clusters HDInsight.

## <a name="azure-monitor-logs-integration"></a>Intégration de Journaux Azure Monitor

Les journaux Azure Monitor permettent de collecter et d’agréger au même endroit des données générées par plusieurs sources, comme les clusters HDInsight, pour offrir une expérience de supervision unifiée.

Vous aurez besoin d’un espace de travail Log Analytics pour stocker les données collectées. Si vous n’en avez pas, créez-en un en suivant les instructions données ici : [Créer un espace de travail Log Analytics dans le portail Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Activer l’intégration des journaux Azure Monitor HDInsight

Dans la page des ressources du cluster HDInsight du portail, sélectionnez **Azure Monitor**. Ensuite, sélectionnez **Activer**, puis votre espace de travail Log Analytics dans la liste déroulante.

![HDInsight Operations Management Suite](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>Interroger les tables de métriques et de journaux

Une fois que l’intégration des journaux Azure Monitor est activée (cela peut prendre plusieurs minutes), accédez à votre ressource **Espace de travail Log Analytics** et sélectionnez **Journaux**.

![Journaux de l’espace de travail Log Analytics](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

Les journaux listent des exemples de requêtes, comme ci-dessous :

| Nom de la requête                      | Description                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Computers availability today (Disponibilité des ordinateurs aujourd’hui)    | Suit le nombre d’ordinateurs envoyant des journaux, heure par heure                     |
| List heartbeats (Répertorier les pulsations)                 | Répertorie toutes les pulsations d’ordinateur à partir de la dernière heure                           |
| Last heartbeat of each computer (Dernière pulsation de chaque ordinateur) | Indique la dernière pulsation envoyée par chaque ordinateur                             |
| Unavailable computers (Ordinateurs non disponibles)           | Répertorie tous les ordinateurs connus qui n’ont pas envoyé de pulsation au cours des 5 dernières heures |
| Availability rate (Taux de disponibilité)               | Calcule le taux de disponibilité de chaque ordinateur connecté                |

Par exemple, exécutez l’exemple de requête **Availability rate** (Taux de disponibilité) en sélectionnant **Exécuter** pour cette requête, comme illustré dans la capture d’écran ci-dessus. Vous pourrez ainsi connaître le taux de disponibilité, exprimé en pourcentage, de chaque nœud de votre cluster. Si vous avez activé plusieurs clusters HDInsight pour l’envoi de métriques vers le même espace de travail Log Analytics, vous pouvez voir le taux de disponibilité de tous les nœuds de ces clusters.

![Exemple de requête « taux de disponibilité » dans les journaux de l’espace de travail Log Analytics](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> Le taux de disponibilité étant mesuré sur une période de 24 heures, votre cluster devra s’exécuter pendant au moins 24 heures avant de pouvoir consulter le taux de disponibilité précis.

Vous pouvez épingler ce tableau à un tableau de bord partagé en cliquant sur **Épingler** dans le coin supérieur droit. Si vous n’avez pas de tableau de bord partagé accessible en écriture, vous pouvez voir comment en créer un ici : [Créer et partager des tableaux de bord dans le portail Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Alertes Azure Monitor

Vous pouvez également configurer des alertes Azure Monitor qui se déclencheront lorsque la valeur d’une métrique ou les résultats d’une requête respectent certaines conditions. Par exemple, nous allons créer une alerte pour envoyer un e-mail quand un ou plusieurs nœuds n’ont envoyé aucune pulsation depuis cinq heures (ils sont alors considérés comme indisponibles).

Dans **Journaux**, exécutez l’exemple de requête **Unavailable computers** (Ordinateurs non disponibles) en sélectionnant **Exécuter** pour cette requête, comme illustré ci-dessous.

![Exemple de requête « ordinateurs non disponibles » dans les journaux de l’espace de travail Log Analytics](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Si tous les nœuds sont disponibles, cette requête ne doit retourner aucun résultat pour l’instant. Cliquez sur **Nouvelle règle d’alerte** pour commencer à configurer votre alerte pour cette requête.

![Nouvelle règle d’alerte de l’espace de travail Log Analytics](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Trois composants constituent une alerte : la *ressource* pour laquelle créer la règle (ici, l’espace de travail Log Analytics), la *condition* pour déclencher l’alerte et les *groupes d’actions* qui déterminent le comportement prévu au moment du déclenchement de l’alerte.
Cliquez sur le **titre de la condition**, comme illustré ci-dessous, pour terminer la configuration de la logique de signal.

![Créer une règle d'alerte dans le portail - Condition](media/hdinsight-cluster-availability/portal-condition-title.png)

La page **Configurer la logique du signal** s’ouvre.

Définissez la section **Logique d’alerte** comme suit :

*Basé sur : Nombre de résultats, Condition : Supérieur à, Seuil : 0.*

Étant donné que cette requête renvoie uniquement les nœuds non disponibles comme résultats, si le nombre de résultats est toujours supérieur à 0, l’alerte doit se déclencher.

Dans la section **Évaluées sur la base de**, définissez la **période** et la **fréquence** selon la fréquence à laquelle vous souhaitez rechercher les nœuds non disponibles.

Pour les besoins de cette alerte, vous devez vérifier que **la période est identique à la fréquence**. Vous pouvez trouver plus d’informations sur la période, la fréquence et d’autres paramètres d’alerte [ici](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Sélectionnez **Terminé** lorsque vous avez terminé de configurer la logique du signal.

![Création d’une règle d’alerte - Configurer la logique du signal](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Si vous n’avez pas de groupe d’actions existant, cliquez sur **Créer nouveau** sous la section **Groupes d’actions**.

![Création d’une règle d’alerte - Nouveau groupe d’actions](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Cette opération ouvre **Ajouter un groupe d’actions**. Choisissez un **nom de groupe d’actions**, un **nom court**, un **abonnement** et un **groupe de ressources**. Dans la section **Actions**, choisissez un **nom d’action** et sélectionnez **E-mail/SMS/Push/Voix** pour **Type d’action**.

> [!NOTE]
> Plusieurs autres actions peuvent être déclenchées par une alerte, par exemple Fonction Azure, LogicApp, Webhook, ITSM et Runbook Automation. [En savoir plus](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information).

La page **E-mail/SMS/Push/Voix** s’ouvre. Choisissez un **nom** de destinataire, **cochez** la case **E-mail**, puis tapez une adresse de messagerie à laquelle vous souhaitez envoyer l’alerte. Sélectionnez **OK** dans **Email/SMS/Push/Voice**, puis dans **Ajouter un groupe d’actions** pour terminer la configuration de votre groupe d’actions.

![Création d’une règle d’alerte - Ajouter un groupe d'actions](media/hdinsight-cluster-availability/portal-add-action-group.png)

Une fois ces panneaux fermés, vous devez voir votre groupe d’actions répertorié sous la section **Groupes d’actions**. Pour finir, complétez la section **Détails de l’alerte** en tapant un **nom de règle d’alerte** et une **description** et en choisissant une **gravité**. Cliquez sur **Créer une règle d’alerte** pour terminer.

![Création d’une règle d’alerte - Terminer dans le portail](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> La possibilité de spécifier une **gravité** est très utile, notamment lorsque vous créez plusieurs alertes. Par exemple, vous pouvez créer une alerte pour déclencher un avertissement (Gravité 1) si un seul nœud principal tombe en panne et une autre alerte de type critique (Gravité 0) dans le cas peu probable où les deux nœuds principaux tomberaient en panne.

Lorsque la condition de cette alerte est remplie, l’alerte se déclenche et vous recevez un e-mail contenant les détails de l’alerte comme suit :

![Exemple d'e-mail d’alerte Azure Monitor](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

Vous pouvez également voir toutes les alertes qui ont été déclenchées, regroupées par gravité, en accédant à **Alertes** dans votre **espace de travail Log Analytics**.

![Alertes d’espace de travail Log Analytics](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Vous pouvez sélectionner un regroupement de gravité (par exemple, **Gravité 1**, comme illustré ci-dessus) pour voir les enregistrements de toutes les alertes de cette gravité qui ont été déclenchées, comme ci-dessous :

![Espace de travail Log Analytics - Alerte de gravité 1](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Étapes suivantes

- [Availability and reliability of Apache Hadoop clusters in HDInsight](hdinsight-high-availability-linux.md) (Disponibilité et fiabilité des clusters Apache Hadoop dans HDInsight)
