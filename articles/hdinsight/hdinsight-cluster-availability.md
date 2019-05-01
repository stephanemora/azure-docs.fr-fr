---
title: Guide pratique pour surveiller la disponibilité du cluster avec les journaux de Ambari et d’Azure Monitor
description: Découvrez comment utiliser Ambari et les journaux d’Azure Monitor pour surveiller la disponibilité et l’intégrité du cluster.
keywords: surveillance, ambari, analyse, analytique de journal, alerte, la disponibilité, l’intégrité
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 459de569916af14b0efea0ff08b92e5c93ed2369
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718915"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Guide pratique pour surveiller la disponibilité du cluster avec les journaux de Ambari et d’Azure Monitor

Les clusters HDInsight incluent Apache Ambari, qui fournit des informations de contrôle d’intégrité en un clin de œil et alertes prédéfinies, ainsi que des intégration des journaux Azure Monitor, qui fournit des mesures peut être interrogées et des journaux, ainsi que des alertes configurables vous avertissant.

Ce document montre comment utiliser ces outils pour surveiller votre cluster et guide au travers des exemples de configuration d’une alerte d’Ambari, la surveillance des taux de disponibilité de nœud et la création d’une alerte Azure Monitor qui se déclenche lorsque aucune pulsation n’a pas été reçue à partir d’un ou plusieurs nœuds dans les cinq heures.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>tableau de bord

Le tableau de bord Ambari sont accessibles en cliquant sur le **Ambari domestique** lien dans le **des tableaux de bord de Cluster** section du panneau Vue d’ensemble de HDInsight dans le portail Azure, comme illustré ci-dessous. Vous pouvez également, il est accessible en entrant l’URL suivante dans un navigateur [https://\<clustername\>. azurehdinsight.net](https://clustername.azurehdinsight.net/)

![Affichage de ressources du portail HDInsight](media/hdinsight-cluster-availability/portal-overview.png)

Vous devez ensuite un nom de connexion au cluster et le mot de passe. Entrez les informations d’identification que vous avez choisi lorsque vous avez créé le cluster.

Vous êtes alors redirigé vers le tableau de bord Ambari, qui contient les widgets qui affichent un certain nombre de mesures pour vous donner un aperçu rapide de l’intégrité de votre cluster HDInsight. Ces widgets affichent des métriques telles que le nombre de live DataNodes (nœuds worker) et JournalNodes (nœud zookeeper), temps de fonctionnement NameNodes (nœuds principaux), en tant que mesures bien spécifiques à certains types de cluster, tels que YARN ResourceManager de durée de fonctionnement pour les clusters Spark et Hadoop.

![Tableau de bord Ambari](media/hdinsight-cluster-availability/ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hôtes – afficher l’état de nœud individuel

Vous pouvez également afficher des informations d’état des nœuds individuels. Cliquez sur le **hôtes** tab pour afficher une liste de tous les nœuds de votre cluster et afficher des informations sur chaque nœud. La coche verte à gauche de chaque nom de nœud indique tous les composants ne soient opérationnels sur le nœud. Si un composant s’est arrêté sur un nœud, vous verrez un triangle d’alerte rouge au lieu de la coche verte.

![Afficher les ordinateurs hôtes d’Ambari](media/hdinsight-cluster-availability/ambari-hosts.png)

Vous pouvez ensuite cliquer sur le **nom** d’un nœud pour afficher plus des métriques de l’hôte pour ce nœud particulier. Cet affichage indique l’état/la disponibilité de chaque composant.

![Ambari hôtes unique vue du nœud](media/hdinsight-cluster-availability/ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Alertes Ambari

Ambari offre également plusieurs alertes configurables qui peuvent fournir une notification de certains événements. Lorsque des alertes sont déclenchées, elles sont affichées dans le coin supérieur gauche de Ambari dans un badge rouge contenant le nombre d’alertes. En cliquant sur ce badge affiche une liste des alertes en cours.

![Nombre d’alertes Ambari](media/hdinsight-cluster-availability/ambari-alerts.png)

Pour afficher une liste de définitions d’alerte et leur état, cliquez sur le **alertes** onglet, comme indiqué ci-dessous.

![Afficher les définitions d’alertes Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari offre de nombreuses alertes prédéfinies relatives à la disponibilité, notamment :

| Nom de l’alerte                        | Description                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Récapitulatif d’intégrité DataNode           | Cette alerte de niveau de service est déclenchée si y est DataNodes défectueux                                                                                                                |
| Intégrité de la disponibilité élevée de NameNode | Cette alerte de niveau de service est déclenchée si NameNode actifs ou du NameNode de secours n’est pas exécutés.                                                                              |
| JournalNodes pourcentage disponible    | Cette alerte est déclenchée si le nombre de JournalNodes vers le bas dans le cluster est supérieur au seuil critique configuré. Il regroupe les résultats des vérifications de processus JournalNode. |
| DataNodes pourcentage disponible       | Cette alerte est déclenchée si le nombre de DataNodes vers le bas dans le cluster est supérieur au seuil critique configuré. Il regroupe les résultats des vérifications de processus DataNode.       |

Une liste complète des Ambari alertes qui surveille l’aide la disponibilité d’un cluster peut être trouvée [ici](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Pour afficher les détails d’une alerte ou modifier les critères, cliquez sur le **nom** de l’alerte. Prendre **résumé de contrôle d’intégrité DataNode** comme exemple. Vous pouvez voir une description de l’alerte, ainsi que des critères spécifiques qui déclencheront une alerte « Avertissement » ou « critique » et l’intervalle de vérification pour les critères. Pour modifier la configuration, cliquez sur le **modifier** bouton dans le coin supérieur droit de la zone de Configuration.

![Configuration des alertes Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Ici, vous pouvez modifier la description et, plus important encore, la vérification intervalle et les seuils d’alertes d’avertissement ou critiques.

![Vue de modification de configuration des alertes Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

Dans cet exemple, vous pouvez créer 2 DataNodes défectueux déclenchent une alerte critique et 1 défectueux DataNode déclencher uniquement un avertissement. Cliquez sur **enregistrer** lorsque vous avez terminé édition.

### <a name="email-notifications"></a>Notifications par e-mail

Vous pouvez également configurer les notifications par courrier électronique pour les alertes Ambari. Pour ce faire, sur le **alertes** , cliquez sur le **Actions** bouton dans l’angle supérieur gauche, puis **gérer les Notifications.**

![Ambari gérer les actions de notifications](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Une boîte de dialogue pour la gestion des notifications d’alerte s’ouvre. Cliquez sur le **+** en bas de la boîte de dialogue et renseignez les champs requis pour fournir Ambari avec l’adresse e-mail détails du serveur à partir de laquelle envoyer des courriers électroniques.

> [!TIP]
> Configuration Ambari les notifications par courrier électronique peuvent être un bon moyen pour recevoir des alertes au même endroit lors de la gestion du nombre de clusters HDInsight.

## <a name="azure-monitor-logs-integration"></a>Intégration des journaux de Azure Monitor

Azure Monitor enregistre des données permet générées par plusieurs ressources, tels que les clusters HDInsight, être collectés et agrégés dans un endroit unique afin d’obtenir une expérience unifiée d’analyse.

Vous devez au préalable, un espace de travail Log Analytique pour stocker les données collectées. Si vous n’en n'avez pas déjà créé un, vous pouvez suivre les instructions fournies ici : [Créer un espace de travail Log Analytique](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Activer l’intégration des journaux HDInsight Azure Monitor

Dans la page de ressources du cluster HDInsight dans le portail, cliquez sur le **Operations Management Suite** panneau. Ensuite, cliquez sur **activer** et sélectionnez votre espace de travail Analytique de journal dans la liste déroulante.

![Panneau de HDInsight Operations Management Suite](media/hdinsight-cluster-availability/portal-enable-oms.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Interroger les tables de métriques et les journaux dans le panneau des journaux

Une fois que l’intégration des journaux Azure Monitor est activée (Cela peut prendre quelques minutes), accédez à votre **espace de travail Log Analytique** ressource et cliquez sur le **journaux** panneau

![Panneau journaux de journal Analytique espace de travail](media/hdinsight-cluster-availability/portal-logs.png)

Le **journaux** panneau répertorie des exemples de requêtes, telles que :

| Nom de la requête                      | Description                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Disponibilité des ordinateurs dès aujourd'hui    | Graphique du nombre d’ordinateurs, envoi de journaux, chaque heure                     |
| Liste des pulsations                 | Liste de tous les pulsations d’ordinateur à partir de la dernière heure                           |
| Dernière pulsation de chaque ordinateur | Afficher la dernière pulsation envoyée par chaque ordinateur                             |
| Ordinateurs non disponibles           | Liste de tous les ordinateurs connus qui n’a pas d’envoyer une pulsation au cours des 5 dernières heures |
| Taux de disponibilité               | Calculer le taux de disponibilité de chaque ordinateur connecté                |

Par exemple, exécuter le **taux de disponibilité** exemple de requête en cliquant sur **exécuter** sur cette requête, comme illustré dans la capture d’écran ci-dessus. Cette commande affiche le taux de disponibilité de chaque nœud dans votre cluster sous forme de pourcentage. Si vous avez activé plusieurs clusters HDInsight à envoyer des mesures à l’espace de travail Analytique de journal, vous verrez le taux de disponibilité pour tous les nœuds dans ces clusters affichées.

![Journal Analytique espace de travail journaux panneau « taux de disponibilité » exemple de requête](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> Taux de disponibilité est mesuré sur une période de 24 heures, votre cluster sera donc à exécuter pendant au moins 24 heures avant de pouvoir consulter le taux de disponibilité précis.

Vous pouvez épingler ce tableau à un tableau de bord partagé en cliquant sur **Pin** dans le coin supérieur droit. Si vous n’avez pas les tableaux de bord partagé accessible en écriture, vous pouvez voir comment créer un ici : [Créer et partager des tableaux de bord dans le portail Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-a-dashboard-and-manage-access-control).

### <a name="azure-monitor-alerts"></a>Alertes Azure Monitor

Vous pouvez également définir des alertes Azure Monitor qui va déclencher lorsque la valeur d’une métrique ou les résultats d’une requête conforme à certaines conditions. Par exemple, nous allons créer une alerte pour envoyer un e-mail quand un ou plusieurs nœuds n’a pas envoyé de pulsation dans 5 heures (par exemple, est censé être indisponible).

À partir de la **journaux** panneau, exécutez le **ordinateurs indisponibles** exemple de requête en cliquant sur **exécuter** sur cette requête, comme indiqué ci-dessous.

![Enregistre les requêtes de l’exemple de « ordinateurs non disponible » du panneau de journaux Analytique espace de travail](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Si tous les nœuds sont disponibles, cette requête doit retourner des résultats 0 pour l’instant. Cliquez sur **nouvelle règle d’alerte** pour commencer à configurer votre alerte pour cette requête.

![Journal Analytique espace de travail nouvelle règle d’alerte](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Il existe trois composants à une alerte : le *ressource* pour lequel créer la règle (l’Analytique de journal espace de travail dans ce cas), le *condition* pour déclencher l’alerte et le *groupes d’actions*  qui déterminent ce qui se produit lorsque l’alerte est déclenchée.

Cliquez sur le **condition titre**, comme illustré ci-dessous, pour terminer la configuration de la logique de signal.

![Condition de règle d’alerte](media/hdinsight-cluster-availability/portal-condition-title.png)

Cette opération ouvre le **logique du signal configurer** panneau.

Définir le **logique d’alerte** section comme suit :

*Basé sur : Nombre de résultats, Condition : Supérieur au seuil : 0.*

Étant donné que cette requête retourne uniquement les nœuds non disponibles en tant que résultats, si le nombre de résultats n’est jamais supérieur à 0, l’alerte doit se déclencher.

Dans le **évalué selon** section, définissez le **période** et **fréquence** selon la fréquence à laquelle vous souhaitez rechercher les nœuds non disponibles.

Notez que pour les besoins de cette alerte, vous souhaitez vous assurer **= période de fréquence.** Vous pouvez trouver plus d’informations sur la période, la fréquence et autres paramètres d’alerte [ici](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Cliquez sur **fait** lorsque vous avez terminé de configurer la logique de signal.

![Configurer la règle d’alerte logique du signal](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Si vous ne disposez pas d’un groupe d’actions existant, cliquez sur **créer un nouveau** sous le **groupes d’actions** section.

![Nouveau groupe d’actions d’une règle d’alerte](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Cette opération ouvre le **ajouter un groupe d’action** panneau. Choisissez un **nom groupe d’actions**, **nom court**, **abonnement**, et **groupe de ressources.** Sous le **Actions** , choisissez un **nom de l’Action** et sélectionnez **E-mail/SMS/Push, voix** comme le **Type d’Action.**

> [!NOTE]
> Il existe plusieurs autres actions qu'une alerte peut se déclencher en dehors d’une E-mail, SMS, Push/voix, par exemple une fonction Azure, application logique, Webhook, ITSM et de Runbook Automation. [Pour en savoir plus.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Cette opération ouvre le **E-mail/SMS/Push, voix** panneau. Choisissez un **nom** du destinataire, **vérifier** le **E-mail** , puis tapez une adresse de messagerie à laquelle vous souhaitez l’alerte. Cliquez sur **OK** dans le **E-mail/SMS/Push, voix** panneau, puis, dans le **ajouter un groupe d’action** panneau pour terminer la configuration de votre groupe d’actions.

![Règle d’alerte ajouter le groupe d’actions](media/hdinsight-cluster-availability/portal-add-action-group.png)

Une fois que ces panneaux fermé, vous devez voir votre groupe d’actions répertorié sous la **groupes d’actions** section. Pour finir, terminez la **détails de l’alerte** section en tapant un **nom de règle d’alerte** et **Description** et en choisissant un **gravité**.
Cliquez sur **créer une règle d’alerte** se termine.

![Créer une règle d’alerte terminer](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> La possibilité de spécifier **gravité** est un outil puissant qui peut être utilisé lors de la création de plusieurs alertes. Par exemple, vous pouvez créer une alerte pour déclencher un avertissement (gravité 1) si un seul nœud principal tombe en panne et une autre alerte qui déclenche critique (gravité 0) dans le cas peu probable que les deux nœuds principaux tombent en panne.

Lorsque la condition de cette alerte est remplie, l’alerte se déclenche et vous recevrez un e-mail contenant les détails de l’alerte comme suit :

![E-mail d’alerte Azure Monitor](media/hdinsight-cluster-availability/alert-email.png)

Vous pouvez également afficher toutes les alertes qui ont été déclenchées, regroupées par niveau de gravité, en accédant à la **alertes** panneau dans votre **espace de travail Log Analytique**.

![Alertes d’espace de travail Analytique de journal](media/hdinsight-cluster-availability/portal-alerts.png)

En cliquant sur un regroupement de niveau de gravité (par exemple, **1 de gravité,** comme illustré ci-dessus) affiche les enregistrements pour toutes les alertes de ce niveau de gravité qui ont été déclenchées, comme ci-dessous :

![Alertes de gravité 1 espace de travail Analytique de journal](media/hdinsight-cluster-availability/portal-alerts-sev-1.png)

## <a name="next-steps"></a>Étapes suivantes
- [Disponibilité et fiabilité des clusters Apache Hadoop dans HDInsight](hdinsight-high-availability-linux.md)
