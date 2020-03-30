---
title: Définir des alertes dans Azure Application Insights
description: Tenez-vous informé des temps de réponse lents, des exceptions et des autres changements de performances ou d’utilisation de votre application web.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295077"
---
# <a name="set-alerts-in-application-insights"></a>Configuration d’alertes dans Application Insights

[Azure Application Insights][start] peut vous signaler les changements qui se produisent au niveau des métriques de performances ou des métriques d’utilisation de votre application web. 

Application Insights supervise votre application en production sur un [large éventail de plateformes][platforms] pour vous aider à diagnostiquer les problèmes de performances et à comprendre les modèles d’utilisation.

Il existe plusieurs types d’alertes :

* Les [**alertes de métrique**](../../azure-monitor/platform/alerts-metric-overview.md) indiquent quand une métrique dépasse une valeur seuil pendant une certaine période, comme les temps de réponse, le nombre d’exceptions, l’utilisation du processeur ou les affichages de page.
* Les [**alertes de journal**](../../azure-monitor/platform/alerts-unified-log.md) sont utilisées pour décrire les alertes lors desquelles le signal d'alerte repose sur une requête Kusto personnalisée.
* Les [**tests web**][availability] vous indiquent à quel moment votre site est indisponible sur Internet, ou à quel moment il répond lentement. [En savoir plus][availability].
* Les [**diagnostics proactifs**](../../azure-monitor/app/proactive-diagnostics.md) sont configurés automatiquement pour vous informer des modèles de performances exceptionnelles.

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

