---
title: Définir des alertes dans Azure Application Insights
description: Tenez-vous informé des temps de réponse lents, des exceptions et des autres changements de performances ou d’utilisation de votre application web.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 80759c94d7cc5b60b6e38a34b85fb64c3c18fd2e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666715"
---
# <a name="set-alerts-in-application-insights"></a>Configuration d’alertes dans Application Insights

[Azure Application Insights][start] peut vous signaler les changements qui se produisent au niveau des métriques de performances ou des métriques d’utilisation de votre application web. 

Application Insights supervise votre application en production sur un [large éventail de plateformes][platforms] pour vous aider à diagnostiquer les problèmes de performances et à comprendre les modèles d’utilisation.

Il existe plusieurs types d’alertes :

* Les [**alertes de métrique**](../../azure-monitor/platform/alerts-metric-overview.md) indiquent quand une métrique dépasse une valeur seuil pendant une certaine période, comme les temps de réponse, le nombre d’exceptions, l’utilisation du processeur ou les affichages de page.
* Les [**alertes de journal**](../../azure-monitor/platform/alerts-unified-log.md) sont utilisées pour décrire les alertes lors desquelles le signal d'alerte repose sur une requête Kusto personnalisée.
* Les [**tests web**][availability] vous indiquent à quel moment votre site est indisponible sur Internet, ou à quel moment il répond lentement. [Plus d’informations][availability]
* Les [**diagnostics proactifs**](../../azure-monitor/app/proactive-diagnostics.md) sont configurés automatiquement pour vous informer des modèles de performances exceptionnelles.

## <a name="set-a-metric-alert"></a>Définir une alerte métrique

Ouvrez l'onglet des règles d’alerte, puis utilisez le bouton Ajouter.

![Dans l'onglet des règles d’alerte, cliquez sur Ajouter une alerte. Définissez votre application comme ressource à mesurer, fournissez un nom pour l’alerte et sélectionnez une métrique.](./media/alerts/01-set-metric.png)

* Définissez la ressource avant les autres propriétés. **Choisissez la ressource « (composants) »** si vous souhaitez définir des alertes sur les mesures de performances ou d'utilisation.
* Le nom que vous donnez à l’alerte doit être unique dans le groupe de ressources (pas uniquement dans votre application).
* Veillez à noter les unités dans lesquelles vous êtes invité à entrer la valeur seuil.
* Si vous cochez la case « Envoyer des e-mails aux propriétaires… », les alertes sont envoyées par e-mail à toutes les personnes ayant accès à ce groupe de ressources. Pour étendre cet ensemble de personnes, ajoutez-le au [groupe de ressources ou à l’abonnement](../../azure-monitor/app/resources-roles-access-control.md) (pas à la ressource).
* Si vous spécifiez « E-mails supplémentaires », les alertes sont envoyées à ces personnes ou groupes (que vous ayez ou non coché la case « Envoyer des e-mails aux propriétaires… »). 
* Définissez une [adresse de webhook](../../azure-monitor/platform/alerts-webhooks.md) si vous avez configuré une application web qui répondra aux alertes. Elle est appelée lorsque l’alerte est Activée ou Résolue. (Mais notez qu’à l’heure actuelle, les paramètres de requête ne sont pas transmis en tant que propriétés webhook.)
* Vous pouvez désactiver ou activer l'alerte avec les boutons du haut.

*Je ne vois pas apparaître le bouton Ajouter une alerte.*

* Utilisez-vous un compte professionnel ? Vous pouvez définir des alertes si vous avez un accès propriétaire ou collaborateur à cette ressource d’application. Consultez l'onglet Access Control. [Découvrez le contrôle d’accès][roles].

> [!NOTE]
> Dans le panneau Alertes, vous pouvez constater qu’une alerte est déjà configurée : [Diagnostics proactifs](../../azure-monitor/app/proactive-failure-diagnostics.md). Cette alerte automatique surveille une métrique spécifique : le taux d’échec des demandes. Sauf si vous décidez de désactiver l’alerte proactive, vous n’avez pas besoin de définir votre propre alerte pour les taux d’échec des demandes.
> 
> 

## <a name="see-your-alerts"></a>Consultez vos alertes
Vous recevez un e-mail lorsqu’une alerte bascule entre les états inactive et active. 

L’état actuel de chaque alerte est indiqué dans l'onglet des règles d'alerte.

Il existe un résumé de l’activité récente dans la liste déroulante des alertes :

![Liste déroulante Alertes](./media/alerts/010-alert-drop.png)

L’historique des modifications d’état figure dans le journal d’activité :

![Dans l'onglet Vue d’ensemble, cliquez sur Paramètres, puis sur Journaux d’audit.](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Fonctionnement des alertes
* Une alerte possède trois états : « Jamais activé », « Activé » et « Résolu ». L’état Activé signifie que la condition spécifiée était vraie lors de la dernière évaluation.
* Une notification est générée lorsqu'une alerte change d'état. (Si la condition d'alerte était déjà vraie lorsque vous avez créé l'alerte, il est possible que vous ne receviez pas de notification tant que la condition n’est pas fausse.)
* Chaque notification génère un message électronique si vous avez coché la case E-mails ou fourni des adresses de messagerie électronique. Vous pouvez également consulter la liste déroulante Notifications.
* Une alerte est évaluée chaque fois qu’une mesure arrive, mais pas autrement.
* L’évaluation agrège la mesure de la période précédente, puis la compare à la valeur de seuil pour déterminer le nouvel état.
* La période que vous choisissez spécifie la durée pendant laquelle les mesures sont agrégées. Cela n’affecte pas la fréquence à laquelle l’alerte est évaluée, mais cela dépend de la fréquence d’arrivée des mesures.
* Si aucune donnée n’arrive pour une mesure particulière pendant un certain temps, l’écart a des effets différents sur l’évaluation de l’alerte et sur les graphiques dans Metrics Explorer. Dans Metrics Explorer, si aucune donnée n’apparaît plus longtemps que l’intervalle d’échantillonnage du graphique, le graphique affiche la valeur 0. Cependant, une alerte basée sur la même métrique n’est pas réévaluée et l’état de l’alerte reste inchangé. 
  
    Lorsque les données arrivent par la suite, le graphique revient à une valeur différente de zéro. L’alerte est évaluée en fonction des données disponibles pour la période spécifiée. Si le nouveau point de données est le seul disponible dans la période, l’agrégat repose uniquement sur ce point.
* Une alerte peut osciller fréquemment entre les états alerte et intègre même si vous définissez une longue période. Cela peut se produire si la valeur de la mesure est placée autour du seuil. Le seuil est cohérent : la transition vers l’état alerte se produit à la même valeur que la transition vers l’état intègre.

## <a name="what-are-good-alerts-to-set"></a>Quelles sont les alertes à définir ?
Cela dépend de votre application. Pour commencer, il est préférable de ne pas définir un trop grand nombre de métriques. Prenez le temps d’examiner vos graphiques de métriques pendant l’exécution de votre application, pour mieux comprendre comment elle se comporte normalement. Cet exercice vous aide à trouver des moyens d’améliorer ses performances. Définissez ensuite des alertes qui vous indiquent lorsque les métriques quittent la zone normale. 

Les alertes les plus appréciées sont les suivantes :

* Les [métriques de navigateur][client], en particulier les **temps de chargement des pages** de navigateur, sont très utiles pour les applications web. Si votre page contient de nombreux scripts, vous devez rechercher d’éventuelles **exceptions du navigateur**. Pour obtenir ces métriques et alertes, vous devez configurer la [supervision de pages web][client].
* Le **temps de réponse de serveur** pour le côté serveur des applications web. Outre la définition des alertes, gardez un œil sur cette métrique pour voir si elle varie de façon importante avec un taux de demandes élevé. Une telle variation peut indiquer que votre application manque de ressources. 
* **Exceptions du serveur** - pour les afficher, vous devrez peut-être effectuer une [installation supplémentaire](../../azure-monitor/app/asp-net-exceptions.md).

N’oubliez pas que les [diagnostics de taux d’échec proactifs](../../azure-monitor/app/proactive-failure-diagnostics.md) analysent automatiquement le taux selon lequel votre application répond aux demandes avec des codes d’échec.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Comment définir une alerte d’exception à l’aide de la recherche personnalisée dans les journaux ?

Cette section vous explique comment définir une alerte d'exception basée sur une requête. Pour cet exemple, supposons que nous souhaitions déclencher une alerte lorsque le taux d’échec est supérieur à 10 % au cours des 24 dernières heures.

1. Accédez à votre ressource Application Insights dans le portail Azure.
2. Sur la gauche, sous configurer, cliquez sur **Alerte**.

    ![Sur la gauche, sous configurer, cliquer sur Alerte](./media/alerts/1appinsightalert.png)

3. En haut de l'onglet d'alerte, sélectionnez **Nouvelle règle d'alerte**.

     ![En haut de l'onglet d'alerte, cliquer sur nouvelle règle d'alerte](./media/alerts/2createalert.png)

4. Votre ressource doit être sélectionnée automatiquement. Pour définir une condition, cliquez sur **Ajouter une condition**.

    ![Cliquer sur ajouter une condition](./media/alerts/3addcondition.png)

5. Dans l'onglet Configurer la logique du signal, sélectionner **Recherche personnalisée dans les journaux**

    ![Cliquer sur Recherche personnalisée dans les journaux](./media/alerts/4customlogsearch.png)

6. Dans l’onglet Recherche personnalisée dans les journaux, entrez votre requête dans la zone « Requête de recherche ». Pour cet exemple, nous utiliserons la requête Kusto ci-dessous.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![Entrer la requête dans la zone Requête de recherche](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Vous pouvez également appliquer cette procédure aux autres types d’alertes basées sur une requête. Pour plus d'informations sur le langage de requête Kusto, consultez ce [Document de prise en main Kusto](https://docs.microsoft.com/azure/kusto/concepts/) ou cet [Aide-mémoire SQL/Kusto](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. Sous « Logique d’alerte », spécifiez si elle repose sur le nombre de résultats ou la mesure métrique. Sélectionnez ensuite la condition (supérieur à, égal à, inférieur à) et un seuil. Lors de la modification de ces valeurs, vous constaterez peut-être une évolution de phrase préliminaire de la condition. Dans cet exemple, nous utilisons « égal à ».

    ![Sous Logique d'alerte, sélectionnez une des options proposées ainsi qu'une condition, puis entrez un seuil.](./media/alerts/6alertlogic.png)

8. Sous « Évalué selon », définissez la période et la fréquence. La période doit correspondre à la valeur utilisée dans la requête ci-dessus. Cliquez ensuite sur **Terminé**.

    ![Définir la période et la fréquence en bas, puis cliquer sur Terminer](./media/alerts/7evaluate.png)

9. La condition créée s'affiche, ainsi que le coût mensuel estimé. Sous [Groupe d'actions](../platform/action-groups.md), vous pouvez créer un groupe ou sélectionner un groupe existant. Vous pouvez personnaliser les actions.

    ![Cliquer sur le bouton Sélectionner ou Créer, sous Groupe d'actions](./media/alerts/8actiongroup.png)

10. Enfin, ajoutez les détails de l'alerte (nom de règle d’alerte, description et gravité). Lorsque vous avez terminé, cliquez sur **Créer une règle d’alerte** en bas.

    ![Sous Détails de l’alerte, entrer le nom de règle d'alerte, une description et sélectionner une gravité](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Comment se désabonner des notifications d’alerte classiques par e-mail ?

Cette section s’applique aux **alertes de disponibilité**, **alertes de métriques Application Insights** et **alertes d'anomalies** classiques.

Vous recevez des notifications par e-mail pour ces alertes classiques dans les cas suivants :

* Votre adresse e-mail est répertoriée dans le champ des destinataires des notifications par e-mail des paramètres de règles d’alerte.

* L'option permettant d’envoyer des notifications par e-mail aux utilisateurs détenant certains rôles pour l’abonnement est activée, et vous détenez un de ces rôles pour l'abonnement Azure en question.

![Capture d'écran de notification d'alerte](./media/alerts/alert-notification.png)

Pour un meilleur contrôle de votre sécurité et de votre confidentialité, nous vous recommandons de spécifier explicitement les destinataires des notifications de vos alertes classiques dans le champ **Destinataires des notifications par e-mail**. L’option permettant d'informer tous les utilisateurs détenant certains rôles est proposée à des fins de compatibilité descendante.

Pour vous désabonner des notifications par e-mail générées par une règle d’alerte donnée, supprimez votre adresse e-mail dans le champ **Destinataires des notifications par e-mail**.

Si votre adresse e-mail n’est pas explicitement répertoriée, nous vous recommandons de désactiver l’option permettant d'informer tous les membres détenant certains rôles automatiquement et de répertorier tous les e-mails des utilisateurs devant recevoir des notifications pour cette règle d’alerte dans le champ Destinataires des notifications par e-mail.

## <a name="who-receives-the-classic-alert-notifications"></a>Qui reçoit les notifications d'alerte (classiques) ?

Cette section ne s'applique qu'aux alertes classiques et vous aidera à optimiser vos notifications d'alerte afin que seuls les destinataires de votre choix les reçoivent. Pour mieux comprendre la différence entre les [alertes classiques](../platform/alerts-classic.overview.md) et les nouvelles alertes, reportez-vous à l’[article de présentation des alertes](../platform/alerts-overview.md). Pour contrôler la notification des alertes dans la nouvelle expérience d'alerte, utilisez des [groupes d'actions](../platform/action-groups.md).

* Nous recommandons l'utilisation de destinataires spécifiques pour les notifications d'alertes classiques.

* Pour les alertes liées aux métriques Application Insights (notamment les métriques de disponibilité), l’option **bulk/group** check-box, si elle est activée, envoie des alertes aux utilisateurs ayant des rôles de propriétaire, contributeur ou lecteur dans l’abonnement. Dans les faits, _tous_ les utilisateurs ayant accès à la ressource Application Insights sont concernés et recevront des notifications.

> [!NOTE]
> Si vous utilisez actuellement l'option **En bloc/groupe** et que vous la désactivez, vous ne pourrez pas annuler la modification.

Utilisez la nouvelle expérience d'alerte ou les alertes en temps quasi-réel si vous devez notifier les utilisateurs en fonction de leur rôle. Avec les [groupes d'actions](../platform/action-groups.md), vous pouvez configurer des notifications par e-mail à l'intention des utilisateurs dotés du rôle de contributeur, de propriétaire ou de lecteur (rôles non combinés en une même option).

## <a name="automation"></a>Automatisation
* [Utiliser PowerShell pour automatiser la configuration d’alertes](../../azure-monitor/app/powershell-alerts.md)
* [Utiliser les webhooks pour automatiser la réponse aux alertes](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Voir aussi
* [Tests web de disponibilité](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatiser la configuration d’alertes](../../azure-monitor/app/powershell-alerts.md)
* [Diagnostics proactifs](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

