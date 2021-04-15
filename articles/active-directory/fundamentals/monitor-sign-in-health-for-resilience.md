---
title: Superviser l’intégrité de connexion de l’application pour la résilience dans Azure Active Directory
description: Créez des requêtes et des notifications pour superviser l’intégrité de connexion de vos applications.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7c32940d5fc40249257d1d0bb38ef67c12dc2ef
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029678"
---
# <a name="monitoring-application-sign-in-health-for-resilience"></a>Supervision de l’intégrité de connexion de l’application pour la résilience

Pour augmenter la résilience de l’infrastructure, configurez la supervision de l’intégrité de connexion d’application pour vos applications critiques afin de recevoir une alerte en cas d’incident. Pour vous aider dans cette tâche, vous pouvez configurer des alertes basées sur le classeur d’intégrité de connexion. 

Ce classeur permet aux administrateurs de superviser les demandes d’authentification pour les applications de votre locataire. Il fournit les fonctionnalités clés suivantes :

* Configurez le classeur pour superviser toutes les applications ou celles contenant des données en quasi-temps réel.

* Configurez des alertes pour vous avertir lorsque les modèles d’authentification changent, afin de pouvoir examiner et prendre des mesures.

* Comparez les tendances sur une période, par exemple d’une semaine à l’autre (paramètre par défaut du classeur).

> [!NOTE]
> Pour afficher tous les classeurs disponibles et les conditions préalables à leur utilisation, consultez [Utilisation des classeurs Azure Monitor pour les rapports](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

Lors d’un événement impactant, deux choses peuvent se produire :

* Le nombre de connexions pour une application peut chuter précipitamment, car les utilisateurs ne peuvent pas se connecter.

* Le nombre d’échecs de connexion peut augmenter. 

Cet article explique comment configurer le classeur d’intégrité de connexion pour superviser les interruptions des connexions de vos utilisateurs.

## <a name="prerequisites"></a>Prérequis

* Un locataire Azure AD.

* Un utilisateur avec le rôle Administrateur général ou Administrateur de la sécurité pour le locataire Azure AD.

* Un espace de travail Log Analytics dans votre abonnement Azure pour l’envoi de journaux d’activité aux journaux d’activité Azure Monitor. 

   * Découvrir comment [créer un espace de travail Log Analytics](../../azure-monitor/logs/quick-create-workspace.md)

* Journaux Azure AD intégrés aux journaux Azure Monitor

   * Découvrez comment [intégrer des journaux de connexion Azure AD à Azure Monitor Stream.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="configure-the-app-sign-in-health-workbook"></a>Configurer le classeur d’intégrité de connexion à l’application 

Pour accéder aux classeurs, ouvrez le **portail Azure**, sélectionnez **Azure Active Directory**, puis **Classeurs**.

Des classeurs apparaissent sous Utilisation, Accès conditionnel et Dépanner. Le classeur d’intégrité de l’application s’affiche dans la section Utilisation.

Une fois que vous avez utilisé un classeur, il peut apparaître dans la section des classeurs récemment modifiés.

![Capture d’écran montrant la galerie de classeurs dans le portail Azure.](./media/monitor-sign-in-health-for-resilience/sign-in-health-workbook.png)


Le classeur d’intégrité de connexion à l’application vous permet de visualiser les événements qui affectent vos connexions. 

Par défaut, le classeur présente deux graphiques. Ces graphiques comparent ce qui se passe actuellement dans vos applications à la même période la semaine dernière. Les lignes bleues représentent les événements actuels et les lignes oranges ceux de la semaine précédente.

![Capture d’écran montrant les graphiques d’intégrité de la connexion.](./media/monitor-sign-in-health-for-resilience/sign-in-health-graphs.png)

**Le premier graphique montre l’utilisation horaire (nombre d’utilisateurs connectés)** . Comparer votre nombre actuel d’utilisateurs connectés à une période d’utilisation classique vous permet de repérer une baisse de l’utilisation pouvant nécessiter une investigation. Une baisse du taux d’utilisation peut vous aider à identifier les problèmes de performances et d’utilisation que le taux d’échec ne peut pas détecter. Par exemple, si les utilisateurs ne peuvent pas accéder à votre application pour s’y connecter, il n’y a pas d’échec, mais uniquement une baisse d’utilisation. Vous trouverez un exemple de requête pour ces données dans la section suivante.

**Le second graphique indique le taux d’échec horaire**. Un pic dans le taux d’échec peut indiquer un problème au niveau de vos mécanismes d’authentification. Le taux d’échec ne peut être mesuré que si les utilisateurs peuvent tenter de s’authentifier. Si les utilisateurs ne peuvent pas effectuer la tentative, les échecs ne s’afficheront pas.

Vous pouvez configurer une alerte qui avertit un groupe spécifique lorsque le taux d’utilisation ou d’échec dépasse un seuil défini. Vous trouverez un exemple de requête pour ces données dans la section suivante.

## <a name="configure-the-query-and-alerts"></a>Configurer la requête et les alertes

Vous créez des règles d'alerte dans Azure Monitor et pouvez exécuter automatiquement des requêtes enregistrées ou des recherches personnalisées dans les journaux à intervalles réguliers.

Utilisez les instructions suivantes pour créer des alertes par e-mail en fonction des requêtes reflétées dans les graphiques. Les exemples de scripts ci-dessous enverront une notification par e-mail quand :

* l’utilisation réussie chute de 90 % par rapport à la même heure il y a deux jours, comme indiqué dans le graphique d’utilisation horaire de la section précédente. 

* le taux d’échec augmente de 90 % par rapport à la même heure il y a deux jours, comme indiqué dans le graphique d’utilisation de taux d’échec horaire de la section précédente. 

 Pour configurer la requête sous-jacente et définir des alertes, procédez comme suit. Vous utiliserez l’exemple de requête comme base pour votre configuration. Une explication de la structure de la requête s’affiche à la fin de cette section.

Pour plus d’informations sur la création, l’affichage et la gestion des alertes de journal à l’aide d’Azure Monitor, consultez [Gérer les alertes de journal](../../azure-monitor/alerts/alerts-log.md).

1. Dans le classeur, sélectionnez **Modifier**, puis cliquez sur l’**icône de requête** à droite du graphique.   

   [![Capture d’écran montrant la modification d’un classeur.](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)

   Le journal des requêtes s’ouvre.

   [![Capture d’écran montrant le journal des requêtes.](./media/monitor-sign-in-health-for-resilience/query-log.png)](./media/monitor-sign-in-health-for-resilience/query-log.png)
‎

2. Copiez l’un des exemples de scripts pour une nouvelle requête Kusto.  
   * [Requête Kusto pour augmentation du taux d’échec](#kusto-query-for-increase-in-failure-rate)
   * [Requête Kusto pour baisse de l’utilisation](#kusto-query-for-drop-in-usage)

3. Collez la requête dans la fenêtre, puis sélectionnez **Exécuter**. Vérifiez que vous voyez le message Terminé dans l’image ci-dessous, et les résultats sous ce message.

   [![Capture d’écran montrant les résultats de la requête exécutée.](./media/monitor-sign-in-health-for-resilience/run-query.png)](./media/monitor-sign-in-health-for-resilience/run-query.png)

4. Mettez en surbrillance la requête, puis sélectionnez + **Nouvelle règle d’alerte**. 
 
   [![Capture d’écran montrant l’écran de la nouvelle règle d'alerte.](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)


5. Configurez des conditions d’alerte. ‎Dans la section Condition, sélectionnez le lien **Whenever the average custom log search is greater than logic defined count** (Chaque fois que la recherche dans les journaux personnalisée moyenne est supérieure au nombre défini par la logique). Dans le volet Configurer la logique du signal, faites défiler jussqu’à Logique d’alerte

   [![Capture d’écran montrant l’écran de configuration des alertes.](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)
 
   * **Valeur de seuil** : 0. Cette valeur génère une alerte sur tous les résultats.

   * **Période d’évaluation (en minutes)**  : 2880. Cette valeur porte sur une heure

   * **Fréquence (en minutes)** : 60. Cette valeur définit la période d’évaluation sur une fois par heure pour l’heure précédente.

   * Sélectionnez **Terminé**.

6. Dans la section **Actions**, configurez ces paramètres :  

    [![Capture d’écran montrant la page Créer une règle d'alerte.](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)

   * Sous **Actions**, choisissez **Sélectionner un groupe d'actions**, puis ajoutez le groupe pour lequel vous souhaitez recevoir des alertes.

   * Sous **Personnaliser les actions**, sélectionnez **Alertes par e-mail**.

   * Ajoutez une **ligne d’objet**.

7. Sous **Détails de la règle d'alerte**, configurez ces paramètres :

   * Ajoutez un nom descriptif et une description.

   * Sélectionnez le **groupe de ressources** auquel ajouter l’alerte.

   * Sélectionnez la **gravité** par défaut de l’alerte.

   * Sélectionnez l’option **Activer la règle d’alerte lors de la création** si vous souhaitez la publier immédiatement ; sinon, sélectionnez **Supprimer les alertes**.

8. Sélectionnez **Créer une règle d’alerte**.

9. Sélectionnez **Enregistrer**, entrez un nom pour la requête, puis **enregistrez-la en tant que requête avec une catégorie d’alerte**. Sélectionnez ensuite à nouveau **Enregistrer**.  

   [![Capture d’écran montrant le bouton Enregistrer la requête.](./media/monitor-sign-in-health-for-resilience/save-query.png)](./media/monitor-sign-in-health-for-resilience/save-query.png)

### <a name="refine-your-queries-and-alerts"></a>Affiner vos requêtes et alertes

Modifiez vos requêtes et alertes pour une efficacité maximale.

* Veillez à tester vos alertes.

* Modifiez la sensibilité et la fréquence des alertes afin d’obtenir des notifications importantes. Les administrateurs risquent de ne pas prêter attention aux alertes si elles deviennent trop nombreuses, et ainsi de manquer d’importantes informations. 

* Assurez-vous que l'adresse e-mail à partir de laquelle les alertes sont envoyées dans les clients de messagerie de votre administrateur est ajoutée à la liste des expéditeurs autorisés. Sinon, vous risquez de manquer certaines notifications en raison d’un filtre de courrier indésirable sur votre client de messagerie. 

* La requête d’alertes dans Azure Monitor peut inclure uniquement les résultats des 48 dernières heures. [Il s’agit d’une limitation par défaut](https://github.com/MicrosoftDocs/azure-docs/issues/22637).

## <a name="sample-scripts"></a>Exemples de scripts

### <a name="kusto-query-for-increase-in-failure-rate"></a>Requête Kusto pour augmentation du taux d’échec

   Le ratio en bas de l’écran peut être ajusté selon les besoins et représente la variation en pourcentage du trafic au cours de la dernière heure par rapport à la même heure la veille. 0,5 signifie qu’il y a une différence de 50 % dans le trafic.

```kusto

let today = SigninLogs
| where TimeGenerated > ago(1h) // Query failure rate in the last hour 
| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")
// Optionally filter by a specific application
//| where AppDisplayName == **APP NAME**
| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate
| project TimeGenerated, failureRate = (failure * 1.0) / ((failure + success) * 1.0)
| sort by TimeGenerated desc
| serialize rowNumber = row_number();
let yesterday = SigninLogs
| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Query failure rate at the same time yesterday
| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")
// Optionally filter by a specific application
//| where AppDisplayName == **APP NAME**
| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate at same time yesterday
| project TimeGenerated, failureRateYesterday = (failure * 1.0) / ((failure + success) * 1.0)
| sort by TimeGenerated desc
| serialize rowNumber = row_number();
today
| join (yesterday) on rowNumber // join data from same time today and yesterday
| project TimeGenerated, failureRate, failureRateYesterday
// Set threshold to be the percent difference in failure rate in the last hour as compared to the same time yesterday
// Day variable is the number of days since the previous Sunday. Optionally ignore results on Sat, Sun, and Mon because large variability in traffic is expected.
| extend day = dayofweek(now())
| where day != time(6.00:00:00) // exclude Sat
| where day != time(0.00:00:00) // exclude Sun
| where day != time(1.00:00:00) // exclude Mon
| where abs(failureRate - failureRateYesterday) > 0.5

```

### <a name="kusto-query-for-drop-in-usage"></a>Requête Kusto pour baisse de l’utilisation

Dans la requête suivante, nous comparons le trafic de la dernière heure à celui de la veille à la même heure.
Nous excluons les samedis, dimanches et lundis parce que ces jours-là, nous nous attendons à une grande variabilité du trafic à la même heure le jour précédent. 

Le ratio en bas de l’écran peut être ajusté selon les besoins et représente la variation en pourcentage du trafic au cours de la dernière heure par rapport à la même heure la veille. 0,5 signifie qu’il y a une différence de 50 % dans le trafic.

*Vous devez ajuster ces valeurs en fonction du modèle opérationnel de votre entreprise.*

```Kusto
 let today = SigninLogs // Query traffic in the last hour
| where TimeGenerated > ago(1h)
| project TimeGenerated, AppDisplayName, UserPrincipalName
// Optionally filter by AppDisplayName to scope query to a single application
//| where AppDisplayName contains "Office 365 Exchange Online"
| summarize users = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr) // Count distinct users in the last hour
| sort by TimeGenerated desc
| serialize rn = row_number();
let yesterday = SigninLogs // Query traffic at the same hour yesterday
| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Count distinct users in the same hour yesterday
| project TimeGenerated, AppDisplayName, UserPrincipalName
// Optionally filter by AppDisplayName to scope query to a single application
//| where AppDisplayName contains "Office 365 Exchange Online"
| summarize usersYesterday = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)
| sort by TimeGenerated desc
| serialize rn = row_number();
today
| join // Join data from today and yesterday together
(
yesterday
)
on rn
// Calculate the difference in number of users in the last hour compared to the same time yesterday
| project TimeGenerated, users, usersYesterday, difference = abs(users - usersYesterday), max = max_of(users, usersYesterday)
| extend ratio = (difference * 1.0) / max // Ratio is the percent difference in traffic in the last hour as compared to the same time yesterday
// Day variable is the number of days since the previous Sunday. Optionally ignore results on Sat, Sun, and Mon because large variability in traffic is expected.
| extend day = dayofweek(now())
| where day != time(6.00:00:00) // exclude Sat
| where day != time(0.00:00:00) // exclude Sun
| where day != time(1.00:00:00) // exclude Mon
| where ratio > 0.7 // Threshold percent difference in sign-in traffic as compared to same hour yesterday

```

## <a name="create-processes-to-manage-alerts"></a>Créer des processus pour gérer les alertes

Une fois que vous avez configuré la requête et les alertes, créez des processus d’entreprise pour gérer les alertes.

* Qui surveillera le classeur et quand ?

* Quand une alerte est générée, qui effectue une investigation ?

* Quels sont les besoins en communication ? Qui créera les communications et qui les recevra ?

* Si une panne se produit, quels sont les processus métier qui doivent être déclenchés ?

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur les classeurs](../reports-monitoring/howto-use-azure-monitor-workbooks.md)
