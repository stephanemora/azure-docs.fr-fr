---
title: Surveiller les runbooks Azure Automation avec des alertes de métriques
description: Cet article vous guide tout au long de la procédure de surveillance des runbooks Azure Automation à l’aide de métriques.
services: automation
ms.date: 11/01/2018
ms.topic: article
ms.openlocfilehash: 9bd028157b33817898ef69f9e47cb8b5d9b8f381
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367091"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Surveillance de runbooks avec des alertes de métriques

Dans le cadre de cet article, vous allez apprendre à créer des alertes reposant sur l’état d’achèvement des runbooks.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Se connecter à Azure à https://portal.azure.com

## <a name="create-alert"></a>Créer une alerte

Les alertes vous permettent de définir une condition à surveiller, ainsi qu’une action à entreprendre lorsque cette condition est remplie.

Dans le portail Azure, accédez à votre compte Automation. Sous **Supervision**, sélectionnez **Alertes**, puis cliquez sur **+ Nouvelle règle d’alerte**. L’étendue pour la cible est déjà définie sur votre compte Automation.

### <a name="configure-alert-criteria"></a>Configurer les critères de l’alerte

1. Cliquez sur **+ Ajouter des critères**. Sélectionnez **Métriques** pour le **Type de signal**, puis choisissez **Nombre total de travaux** dans le tableau.

2. La page **Configurer la logique du signal** vous permet de définir la logique qui déclenche l’alerte. Sous le graphique d’historique, deux dimensions apparaissent : **Nom du Runbook** et **État**. Les dimensions correspondent aux différentes propriétés d’une métrique qui sont utilisables pour le filtrage des résultats. Pour la dimension **Nom du Runbook**, sélectionnez le runbook pour lequel vous souhaitez créer une alerte, ou laissez la dimension vide si vous voulez définir une alerte pour tous les runbooks. Pour la dimension **État**, sélectionnez dans la liste déroulante l’état que vous souhaitez surveiller. Les valeurs de nom et d’état de runbook qui s’affichent dans la liste déroulante s’appliquent uniquement aux travaux qui se sont exécutés la semaine précédente.

   Si vous souhaitez créer une alerte pour un état ou un runbook qui ne figurent pas dans la liste déroulante, cliquez sur le signe **\+** en regard de la dimension. Cette action ouvre une boîte de dialogue vous permettant d’entrer une valeur personnalisée non émise récemment pour cette dimension. Si vous entrez une valeur de propriété inexistante, votre alerte ne se déclenchera pas.

   > [!NOTE]
   > Lorsque vous n’appliquez pas de nom à la dimension **RunbookName**, si des runbooks répondent aux critères d’état (cela comprend les runbooks système masqués), vous recevez une alerte.

3. Sous **Logique d’alerte**, définissez la condition et le seuil de votre alerte. Un aperçu de la condition que vous avez définie s’affiche en dessous.

4. Sous **Évaluées sur la base de**, sélectionnez l’intervalle de temps et la fréquence d’exécution de la requête. Par exemple, si vous définissez **Période** sur **Au cours des 5 dernières minutes** et **Fréquence** sur **Toutes les minutes**, l’alerte recherche le nombre de runbooks qui répondaient à vos critères au cours des 5 dernières minutes. Cette requête s’exécute toutes les minutes, et une fois que les critères d’alerte que vous avez définis ne sont plus détectés au cours d’une période de 5 minutes, l’alerte se résout d’elle-même. Lorsque vous avez terminé, cliquez sur **Terminé**.

   ![Sélection d’une ressource pour l’alerte](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Définir les détails de l’alerte

1. Sous **2. Définir les détails de l’alerte**, donnez à l’alerte un nom convivial et une description. Définissez le champ **Gravité** en fonction de la condition de l’alerte. Il existe cinq niveaux de gravité compris entre 0 et 5. Les alertes sont traitées de la même manière quelle que soit leur gravité, et vous pouvez faire correspondre la gravité à votre logique métier.

1. La zone inférieure de la section comporte un bouton vous permettant d’activer la règle une fois que vous avez terminé. Par défaut, les règles sont activées au moment de leur création. Si vous sélectionnez Non lorsque vous créez l’alerte, cette dernière présente l’état **Désactivée**. Lorsque vous serez prêt à activer la règle, vous pourrez la sélectionner dans la page **Règles** d’Azure Monitor, puis cliquer sur **Activer**.

### <a name="define-the-action-to-take"></a>Définir l’action à entreprendre

1. Sous **3. Définir un groupe d’actions**, cliquez sur **+ Nouveau groupe d’actions**. Un groupe d’actions est un groupe que vous pouvez utiliser dans plusieurs alertes. Il peut s’agir, sans s’y limiter, de notifications par e-mail, de runbooks, de webhooks et bien plus encore. Pour en savoir plus sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions](../azure-monitor/platform/action-groups.md).

1. Dans la zone **Nom du groupe d’actions**, définissez un nom convivial et un nom court. Le nom court est utilisé à la place du nom complet du groupe d’actions lorsque les notifications sont envoyées à l’aide de ce groupe.

1. Dans la section **Actions** sous **TYPE D’ACTION**, sélectionnez **E-mail/SMS/Push/Voix**.

1. Dans la page **E-mail/SMS/Push/Voix**, attribuez un nom. Cochez la case **E-mail** et entrez une adresse e-mail valide à utiliser.

   ![Configurer l’e-mail du groupe d’actions](./media/automation-alert-activity-log/add-action-group.png)

1. Cliquez sur **OK** dans la page **E-mail/SMS/Push/Voix** pour la fermer, puis cliquez sur **OK** pour fermer la page **Ajouter un groupe d’actions**. Le nom spécifié dans cette page est enregistré dans le champ **NOM D’ACTION**.

1. Lorsque vous avez terminé, cliquez sur **Enregistrer**. Cette action crée la règle qui vous alerte lorsqu’un runbook s’est achevé avec un état spécifique.

> [!NOTE]
> Lorsque vous ajoutez une adresse e-mail à un groupe d’actions, le système envoie un e-mail de notification indiquant que l’adresse a été ajoutée à un groupe d’actions.

## <a name="notification"></a>Notification

Lorsque les critères d’alerte sont satisfaits, le groupe d’actions exécute l’action définie. Selon l’exemple fourni dans cet article, un e-mail est envoyé. L’image ci-après illustre un exemple d’e-mail que vous recevez après le déclenchement de l’alerte :

![Alerte par courrier électronique](./media/automation-alert-activity-log/alert-email.png)

Une fois que la métrique ne dépasse plus le seuil défini, l’alerte est désactivée et le groupe d’actions exécute l’action définie. Si une action de type e-mail est sélectionnée, le système envoie un e-mail de résolution indiquant que l’alerte a été résolue.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour découvrir d’autres méthodes d’intégration d’alertes dans votre compte Automation.

> [!div class="nextstepaction"]
> [Utiliser une alerte pour déclencher un runbook Azure Automation](automation-create-alert-triggered-runbook.md)
