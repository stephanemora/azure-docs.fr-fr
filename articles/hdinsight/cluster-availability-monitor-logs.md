---
title: Comment surveiller la disponibilité des clusters avec les journaux Azure Monitor et Ambari
description: Découvrez comment utiliser les journaux Azure Monitor pour surveiller la disponibilité et l’intégrité des clusters.
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: 299a17e23ca3eb2d954bae7335571ae1f645152e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867149"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>Comment surveiller la disponibilité des clusters avec les journaux Azure Monitor et Ambari

Les clusters HDInsight incluent l’intégration des journaux Azure Monitor pour obtenir des mesures et des journaux interrogeables, ainsi que des alertes configurables. Cet article explique comment utiliser Azure Monitor pour surveiller vos clusters.

## <a name="azure-monitor-logs-integration"></a>Intégration de Journaux Azure Monitor

Les journaux Azure Monitor permettent de collecter et d’agréger au même endroit des données générées par plusieurs sources, comme les clusters HDInsight, pour offrir une expérience de supervision unifiée.

Vous aurez besoin d’un espace de travail Log Analytics pour stocker les données collectées. Si vous n’en avez pas, créez-en un en suivant les instructions données ici : [Créer un espace de travail Log Analytics dans le portail Azure](../azure-monitor/logs/quick-create-workspace.md).

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Activer l’intégration des journaux Azure Monitor HDInsight

Dans la page des ressources du cluster HDInsight du portail, sélectionnez **Azure Monitor**. Ensuite, sélectionnez **Activer**, puis votre espace de travail Log Analytics dans la liste déroulante.

:::image type="content" source="media/cluster-availability-monitor-logs/azure-portal-monitoring.png" alt-text="HDInsight Operations Management Suite":::

Par défaut, cela installe l’agent OMS sur tous les nœuds de cluster hormis pour les nœuds de périphérie. Comme aucun agent OMS n’est installé sur les nœuds de périphérie de cluster, il n’y a pas de télémétrie sur les nœuds de périphérie dans Log Analytics par défaut.

## <a name="query-metrics-and-logs-tables"></a>Interroger les tables de métriques et de journaux

Une fois que l’intégration des journaux Azure Monitor est activée (cela peut prendre plusieurs minutes), accédez à votre ressource **Espace de travail Log Analytics** et sélectionnez **Journaux**.

:::image type="content" source="media/cluster-availability-monitor-logs/hdinsight-portal-logs.png" alt-text="Journaux de l’espace de travail Log Analytics":::

Les journaux listent des exemples de requêtes, comme ci-dessous :

| Nom de la requête                      | Description                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Computers availability today (Disponibilité des ordinateurs aujourd’hui)    | Suit le nombre d’ordinateurs envoyant des journaux, heure par heure                     |
| List heartbeats (Répertorier les pulsations)                 | Répertorie toutes les pulsations d’ordinateur à partir de la dernière heure                           |
| Last heartbeat of each computer (Dernière pulsation de chaque ordinateur) | Indique la dernière pulsation envoyée par chaque ordinateur                             |
| Unavailable computers (Ordinateurs non disponibles)           | Répertorie tous les ordinateurs connus qui n’ont pas envoyé de pulsation au cours des 5 dernières heures |
| Availability rate (Taux de disponibilité)               | Calcule le taux de disponibilité de chaque ordinateur connecté                |

Par exemple, exécutez l’exemple de requête **Availability rate** (Taux de disponibilité) en sélectionnant **Exécuter** pour cette requête, comme illustré dans la capture d’écran ci-dessus. Vous pourrez ainsi connaître le taux de disponibilité, exprimé en pourcentage, de chaque nœud de votre cluster. Si vous avez activé plusieurs clusters HDInsight pour l’envoi de métriques vers le même espace de travail Log Analytics, vous pouvez voir le taux de disponibilité de tous les nœuds (hormis les nœuds de périphérie) de ces clusters.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-availability-rate.png" alt-text="Exemple de requête « taux de disponibilité » dans les journaux de l’espace de travail Log Analytics":::

> [!NOTE]  
> Le taux de disponibilité étant mesuré sur une période de 24 heures, votre cluster devra s’exécuter pendant au moins 24 heures avant de pouvoir consulter le taux de disponibilité précis.

Vous pouvez épingler ce tableau à un tableau de bord partagé en cliquant sur **Épingler** dans le coin supérieur droit. Si vous n’avez pas de tableau de bord partagé accessible en écriture, vous pouvez voir comment en créer un ici : [Créer et partager des tableaux de bord dans le portail Azure](../azure-portal/azure-portal-dashboards.md#publish-and-share-a-dashboard).

## <a name="azure-monitor-alerts"></a>Alertes Azure Monitor

Vous pouvez également configurer des alertes Azure Monitor qui se déclencheront lorsque la valeur d’une métrique ou les résultats d’une requête respectent certaines conditions. Par exemple, nous allons créer une alerte pour envoyer un e-mail quand un ou plusieurs nœuds n’ont envoyé aucune pulsation depuis cinq heures (ils sont alors considérés comme indisponibles).

Dans **Journaux**, exécutez l’exemple de requête **Unavailable computers** (Ordinateurs non disponibles) en sélectionnant **Exécuter** pour cette requête, comme illustré ci-dessous.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-unavailable-computers.png" alt-text="Exemple de requête « ordinateurs non disponibles » dans les journaux de l’espace de travail Log Analytics":::

Si tous les nœuds sont disponibles, cette requête ne doit retourner aucun résultat pour l’instant. Cliquez sur **Nouvelle règle d’alerte** pour commencer à configurer votre alerte pour cette requête.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png" alt-text="Nouvelle règle d’alerte de l’espace de travail Log Analytics":::

Trois composants constituent une alerte : la *ressource* pour laquelle créer la règle (ici, l’espace de travail Log Analytics), la *condition* pour déclencher l’alerte et les *groupes d’actions* qui déterminent le comportement prévu au moment du déclenchement de l’alerte.
Cliquez sur le **titre de la condition**, comme illustré ci-dessous, pour terminer la configuration de la logique de signal.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-condition-title.png" alt-text="Créer une règle d'alerte dans le portail - Condition":::

La page **Configurer la logique du signal** s’ouvre.

Définissez la section **Logique d’alerte** comme suit :

*Basé sur : Nombre de résultats, Condition : Supérieur à, Seuil : 0.*

Étant donné que cette requête renvoie uniquement les nœuds non disponibles comme résultats, si le nombre de résultats est toujours supérieur à 0, l’alerte doit se déclencher.

Dans la section **Évaluées sur la base de**, définissez la **période** et la **fréquence** selon la fréquence à laquelle vous souhaitez rechercher les nœuds non disponibles.

Pour les besoins de cette alerte, vous devez vérifier que **la période est identique à la fréquence**. Vous pouvez trouver plus d’informations sur la période, la fréquence et d’autres paramètres d’alerte [ici](../azure-monitor/alerts/alerts-unified-log.md#alert-logic-definition).

Sélectionnez **Terminé** lorsque vous avez terminé de configurer la logique du signal.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-configure-signal-logic.png" alt-text="Création d’une règle d’alerte - Configurer la logique du signal":::

Si vous n’avez pas de groupe d’actions existant, cliquez sur **Créer nouveau** sous la section **Groupes d’actions**.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-create-new-action-group.png" alt-text="Création d’une règle d’alerte - Nouveau groupe d’actions":::

Cette opération ouvre **Ajouter un groupe d’actions**. Choisissez un **nom de groupe d’actions**, un **nom court**, un **abonnement** et un **groupe de ressources**. Dans la section **Actions**, choisissez un **nom d’action** et sélectionnez **E-mail/SMS/Push/Voix** pour **Type d’action**.

> [!NOTE]
> Plusieurs autres actions peuvent être déclenchées par une alerte, par exemple Fonction Azure, LogicApp, Webhook, ITSM et Runbook Automation. [En savoir plus](../azure-monitor/alerts/action-groups.md#action-specific-information).

La page **E-mail/SMS/Push/Voix** s’ouvre. Choisissez un **nom** de destinataire, **cochez** la case **E-mail**, puis tapez une adresse de messagerie à laquelle vous souhaitez envoyer l’alerte. Sélectionnez **OK** dans **Email/SMS/Push/Voice**, puis dans **Ajouter un groupe d’actions** pour terminer la configuration de votre groupe d’actions.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-add-action-group.png" alt-text="Création d’une règle d’alerte - Ajouter un groupe d'actions":::

Une fois ces panneaux fermés, vous devez voir votre groupe d’actions répertorié sous la section **Groupes d’actions**. Pour finir, complétez la section **Détails de l’alerte** en tapant un **nom de règle d’alerte** et une **description** et en choisissant une **gravité**. Cliquez sur **Créer une règle d’alerte** pour terminer.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png" alt-text="Création d’une règle d’alerte - Terminer dans le portail":::

> [!TIP]
> La possibilité de spécifier une **gravité** est très utile, notamment lorsque vous créez plusieurs alertes. Par exemple, vous pouvez créer une alerte pour déclencher un avertissement (Gravité 1) si un seul nœud principal tombe en panne et une autre alerte de type critique (Gravité 0) dans le cas peu probable où les deux nœuds principaux tomberaient en panne.

Lorsque la condition de cette alerte est remplie, l’alerte se déclenche et vous recevez un e-mail contenant les détails de l’alerte comme suit :

:::image type="content" source="media/cluster-availability-monitor-logs/portal-oms-alert-email.png" alt-text="Exemple d'e-mail d’alerte Azure Monitor":::

Vous pouvez également voir toutes les alertes qui ont été déclenchées, regroupées par gravité, en accédant à **Alertes** dans votre **espace de travail Log Analytics**.

:::image type="content" source="media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png" alt-text="Alertes d’espace de travail Log Analytics":::

Vous pouvez sélectionner un regroupement de gravité (par exemple, **Gravité 1**, comme illustré ci-dessus) pour voir les enregistrements de toutes les alertes de cette gravité qui ont été déclenchées, comme ci-dessous :

:::image type="content" source="media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png" alt-text="Espace de travail Log Analytics - Alerte de gravité 1":::

## <a name="next-steps"></a>Étapes suivantes

* [Disponibilité des clusters – Apache Ambari](./hdinsight-cluster-availability.md)
* [Utilisez les journaux Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)
