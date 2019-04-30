---
title: Détection intelligente dans Azure Application Insights | Microsoft Docs
description: Application Insights réalise une analyse télémétrique approfondie automatique de votre application et vous avertit des éventuels problèmes de performances.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mbullwin
ms.openlocfilehash: edbd7000001ae6927078e2f1bb9e348cc78f9efa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61299647"
---
# <a name="smart-detection-in-application-insights"></a>Détection intelligente dans Application Insights
 La détection intelligente vous informe automatiquement des éventuels problèmes de performances dans votre application web. Elle effectue une analyse proactive des données de télémétrie que votre application envoie à [Application Insights](../../azure-monitor/app/app-insights-overview.md). S’ils détectent une augmentation soudaine du taux d’échec, ou des modèles anormaux de performances client ou serveur, vous recevez une alerte. Cette fonctionnalité ne nécessite aucune configuration. Elle fonctionne si votre application envoie suffisamment de données de télémétrie.

Vous pouvez accéder aux alertes de détection intelligente via les courriers électroniques que vous recevez et à partir du panneau Détection intelligente.

## <a name="review-your-smart-detections"></a>Passer en revue vos détections intelligentes
Vous pouvez découvrir des détections de deux manières :

* **Vous recevez un message électronique** de la part d’Application Insights. Voici un exemple typique :
  
    ![Alerte par courrier électronique](./media/proactive-diagnostics/03.png)
  
    Cliquez sur le grand bouton pour afficher plus de détails dans le portail.
* **La vignette Détection intelligente** figurant sur le panneau d’aperçu de votre application indique le nombre d’alertes récentes. Cliquez sur la mosaïque pour afficher la liste des alertes récentes.

![Afficher les détections récentes](./media/proactive-diagnostics/04.png)

Sélectionnez une alerte pour afficher les détails la concernant.

## <a name="what-problems-are-detected"></a>Quels sont les problèmes détectés ?
Il existe trois types de détection :

* [Détection intelligente des anomalies de type échec](../../azure-monitor/app/proactive-failure-diagnostics.md). Nous utilisons l’apprentissage automatique pour définir la fréquence attendue des demandes entraînant un échec pour votre application, en la mettant en corrélation avec la charge et d’autres facteurs. Si le taux d’échec est situé en dehors de la plage attendue, nous envoyons une alerte.
* [Détection intelligente des anomalies de performances](../../azure-monitor/app/proactive-performance-diagnostics.md). Vous recevez des notifications si le temps de réponse d’une opération ou la durée d’une dépendance ralentit par rapport à l’historique de la ligne de base ou si nous identifions un modèle anormal dans le temps de réponse ou le temps de chargement de la page.   
* [Détection intelligente - Dépannage de problèmes de service cloud Azure](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Vous recevez des alertes si votre application est hébergée dans Azure Cloud Services et qu’une instance de rôle présente des échecs de démarrage, un recyclage fréquent ou des erreurs d’exécution.

(Les liens d’aide dans chaque notification vous renvoient vers les articles pertinents.)

## <a name="smart-detection-email-notifications"></a>Notifications par e-mail de détection intelligente

Toutes les règles de détection intelligente, à l’exception des règles marquées en tant que préversion, sont configurées par défaut pour envoyer des notifications par e-mail en cas de détection.

La configuration de notifications par e-mail pour une règle de détection intelligente spécifique peut être effectuée en ouvrant le panneau **Paramètres** de détection intelligente et en sélectionnant la règle, ce qui ouvre le panneau **Modifier une règle**.

Vous pouvez également utiliser les modèles Azure Resource Manager pour modifier la configuration. Pour plus d'informations, consultez [Gérer les règles de détection intelligente d’Application Insights à l’aide de modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config).

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Étapes suivantes
Ces outils de diagnostic vous aident à inspecter les données de télémétrie à partir de votre application :

* [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md)
* [Navigateur de recherche](../../azure-monitor/app/diagnostic-search.md)
* [Analytics : un puissant langage de requête](../../azure-monitor/log-query/get-started-portal.md)

La détection intelligente est entièrement automatique. Mais vous souhaitez peut-être configurer des alertes supplémentaires ?

* [Alertes de mesures configurées manuellement](../../azure-monitor/app/alerts.md)
* [Tests web de disponibilité](../../azure-monitor/app/monitor-web-app-availability.md) 

