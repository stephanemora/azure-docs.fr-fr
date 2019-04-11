---
title: Configuration d’alertes dans Azure Application Insights | Microsoft Docs
description: Tenez-vous informé des temps de réponse lents, des exceptions et des autres changements de performances ou d’utilisation de votre application web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.reviewer: lagayhar
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: mbullwin
ms.openlocfilehash: 8026576478b16b753ba960155c383ffec62c61ce
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469792"
---
# <a name="set-alerts-in-application-insights"></a>Configuration d’alertes dans Application Insights
[Azure Application Insights][start] peut vous signaler les modifications des métriques de performances ou d’utilisation de votre application web. 

Application Insights analyse votre application en direct sur un [large éventail de plateformes][platforms] pour vous aider à diagnostiquer les problèmes de performances et comprendre les schémas d’utilisation.

Il existe plusieurs types d’alertes :

* [**Alertes de métrique** ](../../azure-monitor/platform/alerts-metric-overview.md) indiquent quand une métrique dépasse une valeur de seuil pendant une certaine période - tels que les temps de réponse, nombre d’exceptions, l’utilisation du processeur ou les vues de page.
* [**Alertes de journal** ](../../azure-monitor/platform/alerts-unified-log.md) est utilisé pour décrire les alertes où le signal d’alerte est basé sur une requête Kusto personnalisée.
* Les [**tests web**][availability] indiquent quand votre site est indisponible sur Internet ou répond lentement. [En savoir plus][availability].
* Les [**diagnostics proactifs**](../../azure-monitor/app/proactive-diagnostics.md) sont configurés automatiquement pour vous informer des modèles de performances exceptionnelles.

## <a name="set-a-metric-alert"></a>Définir une alerte métrique
Ouvrez l’onglet Règles d’alerte, puis utilisez le bouton Ajouter.

![Dans l’onglet Règles d’alerte, choisissez Ajouter une alerte. Définissez votre application comme ressource à mesurer, fournissez un nom pour l’alerte et sélectionnez une métrique.](./media/alerts/01-set-metric.png)

* Définissez la ressource avant les autres propriétés. **Choisissez la ressource « (composants) »** si vous souhaitez définir des alertes sur les mesures de performances ou d'utilisation.
* Le nom que vous donnez à l’alerte doit être unique dans le groupe de ressources (pas uniquement dans votre application).
* Veillez à noter les unités dans lesquelles vous êtes invité à entrer la valeur seuil.
* Si vous cochez la case « Envoyer des e-mails aux propriétaires… », les alertes sont envoyées par e-mail à toutes les personnes ayant accès à ce groupe de ressources. Pour étendre cet ensemble de personnes, ajoutez-le au [groupe de ressources ou à l’abonnement](../../azure-monitor/app/resources-roles-access-control.md) (pas à la ressource).
* Si vous spécifiez « E-mails supplémentaires », les alertes sont envoyées à ces personnes ou groupes (que vous ayez ou non coché la case « Envoyer des e-mails aux propriétaires… »). 
* Définissez une [adresse de webhook](../../azure-monitor/platform/alerts-webhooks.md) si vous avez configuré une application web qui répondra aux alertes. Elle est appelée lorsque l’alerte est Activée ou Résolue. (Mais notez qu’à l’heure actuelle, les paramètres de requête ne sont pas transmis en tant que propriétés webhook.)
* Vous pouvez désactiver ou activer l’alerte : consultez les boutons en haut.

*Je ne vois pas le bouton Ajouter une alerte.*

* Utilisez-vous un compte professionnel ? Vous pouvez définir des alertes si vous avez un accès propriétaire ou collaborateur à cette ressource d’application. Examinez l’onglet de contrôle d’accès. [En savoir plus sur le contrôle d’accès][roles].

> [!NOTE]
> Dans le panneau Alertes, vous pouvez constater qu’une alerte est déjà configurée : [Diagnostics proactifs](../../azure-monitor/app/proactive-failure-diagnostics.md). Cette alerte automatique surveille une métrique spécifique : le taux d’échec des demandes. Sauf si vous décidez de désactiver l’alerte proactive, vous n’avez pas besoin de définir votre propre alerte pour les taux d’échec des demandes.
> 
> 

## <a name="see-your-alerts"></a>Consultez vos alertes
Vous recevez un e-mail lorsqu’une alerte bascule entre les états inactive et active. 

L’état actuel de chaque alerte est indiqué dans l’onglet Règles d’alerte.

Il existe un résumé de l’activité récente dans la liste déroulante des alertes :

![Liste déroulante Alertes](./media/alerts/010-alert-drop.png)

L’historique des modifications d’état figure dans le journal d’activité :

![Sous l’onglet vue d’ensemble, cliquez sur paramètres, journaux d’Audit](./media/alerts/09-alerts.png)

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

* Les [mesures de navigateur][client], surtout les **temps de chargement des pages** de navigateur, sont efficaces pour les applications web. Si votre page contient de nombreux scripts, vous devez rechercher d’éventuelles **exceptions du navigateur**. Pour obtenir ces métriques et alertes, vous devez configurer la [surveillance de page web][client].
* Le **temps de réponse de serveur** pour le côté serveur des applications web. Outre la définition des alertes, gardez un œil sur cette métrique pour voir si elle varie de façon importante avec un taux de demandes élevé. Une telle variation peut indiquer que votre application manque de ressources. 
* **Exceptions du serveur** - pour les afficher, vous devrez peut-être effectuer une [installation supplémentaire](../../azure-monitor/app/asp-net-exceptions.md).

N’oubliez pas que les [diagnostics de taux d’échec proactifs](../../azure-monitor/app/proactive-failure-diagnostics.md) analysent automatiquement le taux selon lequel votre application répond aux demandes avec des codes d’échec.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Comment définir une alerte d’exception à l’aide de la recherche de journal personnalisée

Dans cette section, nous allons examiner comment définir une alerte d’exception en fonction de requête. Pour cet exemple, supposons que nous voulons une alerte lorsque le taux d’échec est supérieur à 10 % dans les dernières 24 heures.

1. Accédez à votre ressource Application Insight dans le portail Azure.
2. Sur la gauche, sous configurer, cliquez sur **alerte**.

    ![Sur la gauche, sous configurer sur l’alerte](./media/alerts/1appinsightalert.png)

3. En haut de l’onglet Alerte sélectionnez **nouvelle règle d’alerte**.

     ![En haut de l’onglet alerte, cliquez sur Nouvelle règle d’alerte](./media/alerts/2createalert.png)

4. Votre ressource doit être sélectionné automatiquement. Pour définir une condition, cliquez sur **ajouter une condition**.

    ![Cliquez sur Ajouter une condition](./media/alerts/3addcondition.png)

5. Dans l’onglet de logique de signal configurer sélectionnez **recherche de journal personnalisée**

    ![Cliquez sur la recherche de journal personnalisée](./media/alerts/4customlogsearch.png)

6. Dans l’onglet de recherche de journal personnalisé, entrez votre requête dans la zone de « Requête de recherche ». Pour cet exemple, nous utiliserons le ci-dessous requête Kusto.
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

    ![Tapez la requête dans la zone de requête de recherche](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Vous pouvez également appliquer ces étapes pour les autres types d’alertes basées sur la requête. Plus d’informations sur le langage de requête Kusto à partir de ce [Kusto mise en route doc](https://docs.microsoft.com/azure/kusto/concepts/) ou cela [aide-mémoire SQL au Kusto](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. Sous « Logique d’alerte », choisissez si elle est basée sur le nombre de résultats ou de mesure métrique. Sélectionne la condition (supérieure à, égale à, inférieur à) et un seuil. Lorsque vous modifiez ces valeurs, vous pouvez remarquer les modifications de phrase de version préliminaire de condition. Dans cet exemple, nous utilisons « égal à ».

    ![Sous Alert logic choix parmi les options fournies en fonction et une condition, puis tapez un seuil](./media/alerts/6alertlogic.png)

8. Sous « Évalué selon », définir la période et la fréquence. La période ici doit correspondre à la valeur que nous plaçons pour période dans la requête ci-dessus. Puis cliquez sur **fait**.

    ![Définir la période et la fréquence en bas et cliquez ensuite sur terminé](./media/alerts/7evaluate.png)

9. Nous voyons à présent la condition que nous avons créé avec le coût mensuel estimé. Ci-dessous sous [« Groupes d’actions »](../platform/action-groups.md) vous pouvez créer un nouveau groupe ou sélectionnez-en un existant. Si vous le souhaitez, vous pouvez personnaliser les actions.

    ![Cliquez sur l’instruction select ou créer des boutons sous le groupe d’actions](./media/alerts/8actiongroup.png)

10. Enfin ajouter vos détails de l’alerte (nom de la règle, la description, gravité d’alerte). Lorsque vous avez terminé, cliquez sur **créer une règle d’alerte** en bas.

    ![Sous Détails de l’alerte tapez votre nom de règle d’alerte, écrivez une description et choisir un niveau de gravité](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Comment se désabonner des notifications d’alerte par courrier

Cette section s’applique aux **alertes de disponibilité classic**, **les alertes de métrique Application Insights**et **alertes d’échec classique d’anomalies**.

Vous recevez des notifications par courrier électronique pour ces alertes classiques si les éléments suivants s’applique :

* Votre adresse de messagerie est répertorié dans le champ de destinataires de courrier électronique de Notification dans les paramètres de règle d’alerte.

* La possibilité d’envoyer des notifications par courrier électronique aux utilisateurs contenant certains rôles pour l’abonnement est activée et que vous maintenez un rôle respectif pour cet abonnement Azure.

![Capture d’écran de la notification d’alerte](./media/alerts/alert-notification.png)

Pour mieux contrôler votre sécurité et la confidentialité, nous vous recommandons généralement de spécifier explicitement les destinataires de notification pour vos alertes classiques dans le **destinataires de courrier électronique de Notification** champ. L’option pour informer tous les utilisateurs contenant certains rôles est fournie pour la compatibilité descendante.

Pour vous désabonner des notifications par courrier électronique générées par une certaine règle d’alerte, supprimer votre adresse de messagerie à partir de la **destinataires de courrier électronique de Notification** champ.

Si votre adresse de messagerie n’est pas explicitement répertorié, nous vous recommandons de désactiver l’option permettant de notifier tous les membres de certains rôles automatiquement et de liste à la place de tous les messages électroniques d’utilisateurs qui doivent recevoir des notifications pour cette règle d’alerte dans la Notification par courrier électronique champ de destinataires.

## <a name="who-receives-the-classic-alert-notifications"></a>Qui reçoit les notifications d'alerte (classiques) ?

Cette section ne s'applique qu'aux alertes classiques et vous aidera à optimiser vos notifications d'alerte afin que seuls les destinataires de votre choix les reçoivent. Pour en savoir plus sur la différence entre [alertes classiques](../platform/alerts-classic.overview.md) et les nouvelles alertes expérience, reportez-vous à la [article de vue d’ensemble des alertes](../platform/alerts-overview.md). Pour contrôler les notifications d’alerte dans la nouvelle expérience alertes, utilisez [groupes d’actions](../platform/action-groups.md).

* Nous recommandons l'utilisation de destinataires spécifiques pour les notifications d'alertes classiques.

* Pour les alertes liées aux métriques Application Insights (notamment les métriques de disponibilité), l’option **bulk/group** check-box, si elle est activée, envoie des alertes aux utilisateurs ayant des rôles de propriétaire, contributeur ou lecteur dans l’abonnement. En effet, _tous_ les utilisateurs ayant accès à la ressource Application Insights sont dans la portée et recevront des notifications.

> [!NOTE]
> Si vous utilisez actuellement l'option **En bloc/groupe** et que vous la désactivez, vous ne pourrez pas annuler la modification.

Utilisez la nouvelle expérience d'alerte ou les alertes en temps quasi-réel si vous devez notifier les utilisateurs en fonction de leur rôle. Avec les [groupes d’actions](../platform/action-groups.md), vous pouvez configurer des notifications par e-mail aux utilisateurs ayant n’importe quel rôle contributeur/propriétaire/lecteur (sans que ces rôles soient combinés en une même option).

## <a name="automation"></a>Automatisation
* [Utiliser PowerShell pour automatiser la configuration d’alertes](../../azure-monitor/app/powershell-alerts.md)
* [Utiliser des webhooks pour automatiser la réponse aux alertes](../../azure-monitor/platform/alerts-webhooks.md)

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

