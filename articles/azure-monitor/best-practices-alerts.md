---
title: 'Meilleures pratiques d’Azure Monitor : Alertes et actions automatisées'
description: Recommandations pour le déploiement d’alertes et d’actions automatisées d’Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: 933c05bc48fedf9502d63acd5f5dc3fa1f4c133f
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181683"
---
# <a name="deploying-azure-monitor---alerts-and-automated-actions"></a>Déploiement Azure Monitor : Alertes et actions automatisées
Cet article fait partie du scénario [Recommandations pour la configuration d’Azure Monitor](best-practices.md). Il fournit une aide sur les alertes dans Azure Monitor, qui vous informent de manière proactive des données ou des modèles importants identifiés dans vos données de surveillance. Vous pouvez afficher les alertes dans le portail Azure, leur demander d’envoyer une notification proactive ou leur demander d’initier une action automatisée pour tenter de corriger le problème. 
## <a name="alerting-strategy"></a>Stratégie d’alerte
Une stratégie d’alerte définit les normes de votre organisation pour les types de règles d’alerte que vous créerez pour différents scénarios, la manière dont vous classerez et gérerez les alertes après leur création, ainsi que les notifications et les actions automatisées que vous prendrez en réponse aux alertes. La définition d’une stratégie d’alerte vous aide à définir la configuration des règles d’alerte, notamment la gravité d’alerte et les groupes d’actions.

Consultez [Stratégie d’alerte réussie](/azure/cloud-adoption-framework/manage/monitor/alerting#successful-alerting-strategy) pour connaître les facteurs à prendre en compte lors de l’élaboration d’une stratégie d’alerte.


## <a name="alert-rule-types"></a>Types de règles d’alerte
Les alertes dans Azure Monitor sont créées par des règles d’alerte que vous devez créer. Pour obtenir de l’aide concernant les règles d’alerte recommandées, consultez la documentation relative à la surveillance de chaque service Azure. Azure Monitor n’a pas de règles d’alerte par défaut. 

Plusieurs types de règles d’alerte sont définis par le type de données qu’ils utilisent. Chacun a des capacités différentes et un coût différent. La stratégie de base que vous devez suivre consiste à utiliser le type de règle d’alerte avec le coût le plus bas fournissant la logique dont vous avez besoin.

- [Règles du journal d’activité](alerts/activity-log-alerts.md). Crée une alerte en réponse à un nouvel événement du journal d’activité qui correspond aux conditions spécifiées. Ces alertes sont gratuites et doivent donc être votre premier choix, bien que les conditions qu’elles peuvent détecter soient limitées. Pour plus d’informations sur la création d’alertes de journal d'activité, consultez [Créer, afficher et gérer des alertes de métriques à l’aide d’Azure Monitor](alerts/alerts-activity-log.md).
- [Règles d’alerte de métriques](alerts/alerts-metric-overview.md). Crée une alerte en réponse à une ou plusieurs valeurs métriques dépassant un seuil. Les alertes métriques sont des alertes par état, c'est-à-dire qu'elles se ferment automatiquement lorsque la valeur passe en dessous du seuil, et qu'elles n'envoient des notifications que lorsque l'état change. Les alertes de métrique ont un coût, mais celui-ci est souvent nettement inférieur à celui des alertes de journal. Pour plus d’informations sur la création d’alertes de métriques, consultez [Créer, afficher et gérer des alertes de métrique à l’aide d’Azure Monitor](alerts/alerts-metric.md).
- [Règles d’alerte de journal](alerts/alerts-unified-log.md). Crée une alerte lorsque les résultats d’une requête de planification correspondent à des critères spécifiés. Il s’agit des règles d’alerte les plus coûteuses, mais elles autorisent les critères les plus complexes. Pour plus d’informations sur la création d’alertes de requête de journal, consultez [Créer, afficher et gérer des alertes de métrique à l’aide d’Azure Monitor](alerts/alerts-log.md).
- [Les alertes d’application](app/monitor-web-app-availability.md) vous permettent d’effectuer des tests de performances et de disponibilité proactifs de votre application Web. Vous pouvez effectuer un test ping simple gratuitement, mais un test plus complexe engendrera des coûts. Consultez [Surveiller la disponibilité de tout site Web](app/monitor-web-app-availability.md) pour une description des différents types de test et des détails de leur création.

## <a name="alert-severity"></a>Gravité de l’alerte
Chaque règle d’alerte définit la gravité des alertes qu’elle crée en fonction du tableau ci-dessous. Les alertes du portail Azure sont regroupées par niveau afin que vous puissiez gérer ensemble les alertes similaires et identifier rapidement celles qui nécessitent la plus grande urgence.

| Level | Nom | Description |
|:---|:---|:---|
| Gravité 0 | Critique  | Perte de disponibilité du service ou de l’application ou détérioration importante des performances. Nécessite une attention immédiate. |
| Gravité 1 | Erreur  | Dégradation des performances ou perte de disponibilité de certains aspects d’une application ou d’un service. Nécessite une attention, mais pas immédiatement. |
| Gravité 2 | Avertissement | Problème qui n’inclut pas de perte actuelle de disponibilité ou de performance, mais peut entraîner des problèmes plus graves s’il n’est pas traité. |
| Gravité 3 | Informationnel | N’indique pas un problème, mais plutôt des informations intéressantes pour un opérateur, comme l’achèvement réussi d’un processus normal. |
| Gravité 4 | Commentaires | Informations détaillées non utiles. 

Vous devez évaluer la gravité de la condition que chaque règle identifie pour lui attribuer un niveau approprié. Les types de problèmes que vous attribuez à chaque niveau de gravité et votre réponse standard à chacun doivent être définis dans votre stratégie d’alertes. 

## <a name="action-groups"></a>Groupes d’actions
Les réponses automatiques aux alertes dans Azure Monitor sont définies dans des [groupes d’actions](alerts/action-groups.md). Un groupe d’actions est un ensemble d’une ou plusieurs notifications et actions qui sont déclenchées lorsqu’une alerte est déclenchée. Un seul groupe d’actions peut être utilisé avec plusieurs règles d’alerte et contenir un ou plusieurs des éléments suivants :

- Notifications. Messages qui informent les opérateurs et les administrateurs qu’une alerte a été créée.
- Actions. Processus automatiques qui tentent de corriger le problème détecté. 
## <a name="notifications"></a>Notifications
Les notifications sont des messages envoyés à un ou plusieurs utilisateurs pour les informer qu’une alerte a été créée. Dans la mesure où un seul groupe d’actions peut être utilisé avec plusieurs règles d’alerte, vous devez concevoir un ensemble de groupes d’actions pour différents ensembles d’administrateurs et d’utilisateurs qui recevront les mêmes ensembles d’alertes. Utilisez l’un des types de notifications suivants selon les préférences de vos opérateurs et les normes de votre organisation.

- Courrier
- sms
- Notification Push vers une application Azure
- Voix
- Envoyer un message au rôle Azure Resource Manager

## <a name="actions"></a>Actions
Les actions sont des réponses automatiques à une alerte. Vous pouvez utiliser les actions disponibles pour tous les scénarios qu’elles prennent en charge, mais les sections suivantes décrivent la façon dont chacune est généralement utilisée.

### <a name="automated-remediation"></a>Correction automatisée
Utilisez les actions suivantes pour tenter de corriger automatiquement le problème identifié par l’alerte. 

- Runbook Automation : Démarrez un runbook intégré ou personnalisé dans Azure Automation. Par exemple, des runbooks intégrés sont disponibles pour exécuter des fonctions telles que le redémarrage ou le scale-up d’une machine virtuelle.
- Fonction Azure : Démarrez une fonction Azure.


### <a name="itsm-and-on-call-management"></a>ITSM et gestion de l’astreinte

- ITSM : Utilisez le [connecteur ITSM]() pour créer des éléments de travail dans votre outil ITSM en fonction des alertes provenant d’Azure Monitor. Vous devez d’abord configurer le connecteur, puis utiliser l’action **ITSM** dans les règles d’alerte.
- Webhooks : Envoyez l’alerte à un système de gestion des incidents qui prend en charge les webhooks, tels que PagerDuty et Splunk on-Call.
- Webhook sécurisé : Intégration d’ITSM à l’authentification Azure AD.


## <a name="minimizing-alert-activity"></a>Réduction de l’activité des alertes
Bien que vous souhaitiez créer des alertes pour toute information importante dans votre environnement, vous devez vous assurer que vous ne créez pas d’alertes et de notifications excessives pour des problèmes qui ne les justifient pas. Utilisez les instructions suivantes pour réduire l’activité des alertes afin de vous assurer que les problèmes critiques sont mis en évidence et que vous ne générez pas d’informations et de notifications excessives pour les administrateurs. 

- Consultez [Stratégie d’alerte réussie](/azure/cloud-adoption-framework/manage/monitor/alerting#successful-alerting-strategy) pour connaître les principes permettant de déterminer si un symptôme est un candidat approprié pour une alerte.
- Utilisez l’option **Résoudre automatiquement les alertes** dans les règles d’alerte de métrique pour résoudre les alertes lorsque la condition a été corrigée.
- Utilisez l’option **Supprimer les alertes** dans les règles d’alerte de requête sur un journal pour éviter de créer plusieurs alertes pour le même problème.
- Veillez à utiliser les niveaux de gravité appropriés pour les règles d’alerte afin que les problèmes de priorité élevée puissent être analysés ensemble.
- Limitez les notifications pour les alertes dont la gravité est égale ou inférieure à Avertissement, car elles ne nécessitent pas une attention immédiate.

## <a name="create-alert-rules-at-scale"></a>Créer des règles d’alerte à grande échelle
Étant donné que vous voudrez généralement déclencher des alertes sur les problèmes de toutes vos applications et ressources Azure critiques, vous devriez tirer parti des méthodes permettant de créer des règles d’alerte à grande échelle.

- Azure Monitor prend en charge la supervision de plusieurs ressources du même type avec une seule règle d’alerte de métrique pour les ressources qui existent dans la même région Azure. Pour obtenir la liste des services Azure qui prennent actuellement en charge cette fonctionnalité, consultez [Surveillance à grande échelle à l’aide d’alertes de métrique dans Azure Monitor](alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).
- Pour les règles d’alerte de métrique des services Azure qui ne prennent pas en charge plusieurs ressources, exploitez les outils d’automatisation tels que CLI et PowerShell avec les modèles Resource Manager pour créer la même règle d’alerte pour plusieurs ressources. Pour voir quelques exemples, consultez [Exemples de modèle Resource Manager pour les règles d’alerte de métrique dans Azure Monitor](alerts/resource-manager-alerts-metric.md).
- Écrivez des requêtes dans les règles d’alerte de requête sur un journal pour renvoyer des données pour plusieurs ressources. Utilisez le paramètre **Fractionner par dimensions** de la règle pour créer des alertes distinctes pour chaque ressource.


> [!NOTE]
> Les règles d’alerte de requête sur un journal centrées sur les ressources qui sont actuellement en préversion publique vous permettent d’utiliser toutes les ressources d’un abonnement ou d’un groupe de ressources comme cible d’une alerte de requête sur un journal. 

## <a name="next-steps"></a>Étapes suivantes

- [Définir des alertes et des actions automatisées à partir des données d’Azure Monitor](best-practices-alerts.md)