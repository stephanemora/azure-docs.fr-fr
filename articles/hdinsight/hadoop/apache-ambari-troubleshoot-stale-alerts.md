---
title: Alertes obsolètes Apache Ambari dans Azure HDInsight
description: Discussion et analyse des causes et solutions possibles pour les alertes Apache Ambari obsolètes dans HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f19d499b5e50fbb5030a0f396296eed46fc6eee3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722665"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scénario : Alertes obsolètes Apache Ambari dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

À partir de l’interface utilisateur Apache Ambari, vous pouvez voir une alerte semblable à l’image suivante :

![Exemple d’alerte obsolète Apache Ambari](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Cause :

Les agents Ambari exécutent continuellement des vérifications d’intégrité pour surveiller l’intégrité de nombreuses ressources. Chaque alerte est configurée pour s’exécuter à des intervalles de temps prédéfinis. Après l’exécution de chaque alerte, les agents Ambari rapportent l’état au serveur Ambari. À ce stade, si le serveur Ambari détecte que l’une des alertes n’a pas été exécutée en temps voulu, il déclenche une alerte « Alertes du serveur Ambari ». Il existe plusieurs raisons pour lesquelles une vérification d’intégrité peut ne pas s’exécuter à l’intervalle défini :

* Lorsque les hôtes sont fortement utilisés (UC élevé), il est possible que l’agent Ambari ne soit pas en mesure d’obtenir suffisamment de ressources système pour exécuter les alertes en temps opportun.

* Le cluster est occupé à exécuter de nombreux travaux/services pendant une charge importante.

* Peu d’hôtes dans le cluster peuvent héberger de nombreux composants et doivent donc exécuter de nombreuses alertes. Si le nombre de composants est important, il est possible que les tâches d’alerte manquent à leurs intervalles planifiés

## <a name="resolution"></a>Résolution

### <a name="increase-alert-interval-time"></a>Augmenter la durée de l’intervalle d’alerte

Vous pouvez choisir d’augmenter la valeur d’un intervalle d’alerte individuel en fonction du temps de réponse de votre cluster et de sa charge.

1. Dans l’interface utilisateur Apache Ambari, sélectionnez l’onglet **Alertes**.
1. Sélectionnez le nom de définition d’alerte de votre choix.
1. Dans la définition, sélectionnez **Modifier**.
1. Modifiez la valeur **Intervalle de vérification** selon votre choix, puis sélectionnez **Enregistrer**.

### <a name="increase-alert-interval-time-for-ambari-server-alerts"></a>Augmenter la durée de l’intervalle d’alerte pour les alertes du serveur Ambari

1. Dans l’interface utilisateur Apache Ambari, sélectionnez l’onglet **Alertes**.
1. Dans la liste déroulante **Groupes**, sélectionnez **AMBARI par défaut**.
1. Sélectionnez l’alerte **Alertes du serveur Ambari**.
1. Dans la définition, sélectionnez **Modifier**.
1. Modifiez la valeur **Intervalle de vérification** selon votre choix.
1. Modifiez la valeur **Multiplicateur d’intervalle** selon votre choix, puis sélectionnez **Enregistrer**.

### <a name="disable-and-enable-the-alert"></a>Désactiver et activer l’alerte

Vous pouvez désactiver, puis réactiver l’alerte pour abandonner les alertes obsolètes.

1. Dans l’interface utilisateur Apache Ambari, sélectionnez l’onglet **Alertes**.
1. Sélectionnez le nom de définition d’alerte de votre choix.
1. Dans la définition, sélectionnez l’option **Activée** située à l’extrême droite.
1. Dans la fenêtre contextuelle **Confirmation**, sélectionnez **Confirmer la désactivation**.
1. Patientez quelques secondes pour que toutes les « instances » d’alerte affichées sur la page soient effacées.
1. Dans la définition, sélectionnez l’option **Désactivée** située à l’extrême droite.
1. Dans la fenêtre contextuelle **Confirmation**, sélectionnez **Confirmer l’activation**.

### <a name="increase-alert-grace-time"></a>Augmenter la période de grâce des alertes

Avant que l’agent Ambari ne signale qu’une alerte configurée a manqué sa planification, une période de grâce est appliquée. Même si l’alerte a manqué son heure planifiée, mais qu’elle a été déclenchée pendant la période de grâce des alertes, l’alerte obsolète n’est pas déclenchée.

La valeur `alert_grace_period` par défaut est 5 secondes. Ce paramètre `alert_grace_period` est configurable dans `/etc/ambari-agent/conf/ambari-agent.ini`. Pour les hôtes à partir desquels les alertes obsolètes sont déclenchées à intervalles réguliers, essayez de les augmenter à une valeur de 10. Ensuite, redémarrer l’agent Ambari

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
