---
title: Résilience par la surveillance et l’analytique à l’aide d’Azure AD B2C | Microsoft Docs
description: Résilience par la surveillance et l’analytique à l’aide d’Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a834d4d30c40b618b1601a7f8901c68143ef4912
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648515"
---
# <a name="resilience-through-monitoring-and-analytics"></a>Résilience par la surveillance et l’analytique

La surveillance optimise la disponibilité et les performances de vos applications et services. Elle offre une solution complète pour collecter, analyser et exploiter la télémétrie de votre infrastructure et de vos applications. Les alertes vous informent de façon proactive lorsque des problèmes sont détectés avec votre service ou vos applications. Elles vous permettent d’identifier et de résoudre les problèmes avant que les utilisateurs finaux de votre service ne les remarquent. [Azure AD Log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) vous aide à analyser, à rechercher dans les journaux d’audit et les journaux de connexion et à créer des vues personnalisées.

## <a name="monitor-and-get-notified-through-alerts"></a>Surveiller et recevoir des notifications par le biais d’alertes

La surveillance de votre système et de votre infrastructure est essentielle pour garantir l’intégrité globale de vos services. Cela commence par la définition des métriques métier, telles que l’arrivée de nouveaux utilisateurs, les taux d’authentification de l’utilisateur final et la conversion. Configurez ces indicateurs à surveiller. Si vous prévoyez une augmentation de la demande en raison du trafic induit par une promotion ou des vacances, modifiez vos estimations spécifiquement pour l’événement et le point de référence correspondant pour les métriques de l’entreprise. Après l’événement, restaurez la référence précédente.

De même, pour détecter les défaillances ou les interruptions de performances, la configuration d’une base de référence correcte, puis la définition des alertes, est une pratique indispensable pour répondre rapidement aux problèmes émergents.

![Image montrant les composants de surveillance et d’analytique](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>Implémentation de la supervision et des alertes

- **Supervision** : Utilisez [Azure Monitor](../../active-directory-b2c/azure-monitor.md) pour surveiller en continu l’intégrité par rapport aux principaux objectifs de niveau de service (SLO) et recevoir une notification chaque fois qu’une modification critique se produit. Commencez par identifier la stratégie Azure AD B2C ou une application comme un composant essentiel de votre entreprise dont l’intégrité doit être surveillée pour maintenir le SLO. Identifiez les indicateurs clés qui s’alignent sur votre SLO.
Par exemple, suivez les métriques suivantes, car une baisse soudaine de l’une ou l’autre entraînera une perte d’activité.

  - **Nombre total de requêtes** : Nombre total « n » de requêtes envoyées à la stratégie Azure AD B2C.

  - **Taux de réussite (%)**  : Requêtes réussies/nombre total de requêtes.

  Accédez aux [indicateurs clés](../../active-directory-b2c/view-audit-logs.md) dans [Application Insights](../../active-directory-b2c/analytics-with-application-insights.md), où sont stockés les journaux Azure AD B2C basés sur les stratégies, les [journaux d’audit](../../active-directory-b2c/analytics-with-application-insights.md) et les journaux de connexion.  

   - **Visualisations** : L’utilisation de Log Analytics permet de créer des tableaux de bord pour surveiller visuellement les indicateurs clés.

   - **Période actuelle** : Créez des graphiques temporels pour montrer les changements dans le nombre total de requêtes et le taux de réussite (%) sur la période actuelle, par exemple, la semaine en cours.

   - **Période précédente** : Créez des graphiques temporels pour montrer les changements dans le nombre total de requêtes et le taux de réussite (%) au cours d’une période précédente à des fins de référence, par exemple la semaine dernière.

- **Alertes** : L’utilisation de Log Analytics définit des [alertes](../../azure-monitor/alerts/alerts-log.md) qui se déclenchent en cas de changement soudain des indicateurs clés. Ces changements peuvent avoir un impact négatif sur les SLO. Les alertes utilisent diverses formes de méthode de notification, notamment les e-mails, les SMS et les webhooks. Commencez par définir un critère qui sert de seuil de déclenchement de l’alerte. Par exemple :
  - Alerte sur une chute soudaine du nombre total de requêtes : Déclenchez une alerte lorsque le nombre total de requêtes chute brusquement. Par exemple, en cas de baisse de 25 % du nombre total de requêtes par rapport à la période précédente, déclenchez une alerte.  
  - Alerte sur une chute significative du taux de réussite (%) : Déclenchez une alerte lorsque le taux de réussite de la stratégie sélectionnée chute de manière significative.
  - Lors de la réception d’une alerte, résolvez le problème à l’aide de [Log Analytics](../reports-monitoring/howto-install-use-log-analytics-views.md), d’[Application Insights](../../active-directory-b2c/troubleshoot-with-application-insights.md) et de l’[extension VS Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) pour Azure AD B2C. Après avoir résolu le problème et déployé une application ou une stratégie mise à jour, il continue à surveiller les indicateurs clés jusqu’à ce qu’ils reviennent à la normale.

- **Alertes de service** : Utilisez les [alertes de niveau de service Azure AD B2C](../../service-health/service-health-overview.md) pour être informé des problèmes de service, de la maintenance planifiée, de l’avis d’intégrité et de l’avis de sécurité.

- **Création de rapports** : [En utilisant Log Analytics](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), créez des rapports qui vous aideront à mieux comprendre les insights des utilisateurs, les défis techniques et les possibilités de croissance.
  - **Tableau de bord d’intégrité** : Créez des [tableaux de bord personnalisés à l’aide de la fonctionnalité Tableau de bord Azure](../../azure-monitor/app/tutorial-app-dashboards.md), qui prend en charge l’ajout de graphiques à l’aide de requêtes Log Analytics. Par exemple, identifiez le modèle de connexions réussies et ayant échouées, les raisons de l’échec et la télémétrie concernant les appareils utilisés pour effectuer les requêtes.
  - **Abandon des parcours Azure AD B2C** : Utilisez le [classeur](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys) pour suivre la liste des parcours Azure AD B2C abandonnés où l’utilisateur a commencé le parcours de connexion ou d’inscription, mais ne l’a jamais terminé. Il fournit des informations sur l’ID de stratégie et la décomposition des étapes effectuées par l’utilisateur avant d’abandonner le parcours.
  - **Classeurs de surveillance Azure AD B2C** : Utilisez les [classeurs de surveillance](https://github.com/azure-ad-b2c/siem), qui comprennent le tableau de bord Azure AD B2C, les opérations d’authentification multifacteur (AMF), le rapport d’accès conditionnel et les journaux de recherche par correlationId afin d’obtenir de meilleures informations sur l’intégrité de votre environnement Azure AD B2C.
  
## <a name="next-steps"></a>Étapes suivantes

- [Ressources de résilience pour les développeurs Azure AD B2C](resilience-b2c.md)
  - [Expérience résiliente de l’utilisateur final](resilient-end-user-experience.md)
  - [Interfaces résilientes avec processus externes](resilient-external-processes.md)
  - [Résilience grâce aux meilleures pratiques des développeurs](resilience-b2c-developer-best-practices.md)
- [Renforcer la résilience de votre infrastructure d’authentification](resilience-in-infrastructure.md)
- [Augmenter la résilience de l’authentification et de l’autorisation dans vos applications](resilience-app-development-overview.md)