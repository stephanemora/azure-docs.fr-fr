---
title: Détection intelligente dans Azure Application Insights | Microsoft Docs
description: Application Insights réalise une analyse télémétrique approfondie automatique de votre application et vous avertit des éventuels problèmes de performances.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 79fe723a7972d265621a1a52ea589bf7dcf6ec62
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111536736"
---
# <a name="smart-detection-in-application-insights"></a>Détection intelligente dans Application Insights

>[!NOTE]
>Vous pouvez migrer la détection intelligente de votre fonctionnalité Application Insights vers une logique basée sur des alertes. La migration a pour effet de créer des règles d’alerte pour les différents modules de détection intelligente. Vous pouvez alors gérer et configurer ces règles comme n’importe quelle autre règle d’alerte d’Azure Monitor. Vous pouvez également configurer des groupes d’actions pour ces règles, ce qui permet à plusieurs méthodes de prendre des mesures ou de déclencher des notifications sur de nouvelles détections.
>
> Pour plus d’informations, consultez [Migration des alertes de détection intelligente](../alerts/alerts-smart-detections-migration.md).

La détection intelligente vous informe automatiquement des éventuels problèmes de performances et anomalies d’échecs dans votre application web. Elle effectue une analyse proactive des données de télémétrie que votre application envoie à [Application Insights](./app-insights-overview.md). S’ils détectent une augmentation soudaine du taux d’échec, ou des modèles anormaux de performances client ou serveur, vous recevez une alerte. Cette fonctionnalité ne nécessite aucune configuration. Elle fonctionne si votre application envoie suffisamment de données de télémétrie.

Vous pouvez accéder aux détections émises par la détection intelligente via les e-mails que vous recevez et à partir du panneau Détection intelligente.

## <a name="review-your-smart-detections"></a>Examiner vos détections intelligentes
Vous pouvez découvrir des détections de deux manières :

* **Vous recevez un message électronique** de la part d’Application Insights. Voici un exemple typique :
  
    ![Alerte par courrier électronique](./media/proactive-diagnostics/03.png)
  
    Cliquez sur le grand bouton pour afficher plus de détails dans le portail.
* **Panneau Détection intelligente** dans Application Insights. Pour afficher une liste des détections récentes, dans le menu **Examiner**, sélectionnez **Détection intelligente**.

![Afficher les détections récentes](./media/proactive-diagnostics/04.png)

Sélectionnez une détection pour en voir les détails.

## <a name="what-problems-are-detected"></a>Quels sont les problèmes détectés ?

La fonctionnalité de détection intelligente détecte et signale divers problèmes, par exemple :

* [Détection intelligente des anomalies de type échec](./proactive-failure-diagnostics.md). Nous utilisons l’apprentissage automatique pour définir la fréquence attendue des demandes entraînant un échec pour votre application, en établit une corrélation avec la charge et d’autres facteurs. Envoie une notification si le taux d’échec se situe en dehors de la plage attendue.
* [Détection intelligente des anomalies de performances](./proactive-performance-diagnostics.md). Envoie une notification si le temps de réponse d’une opération ou LA durée d’une dépendance se dégradent par rapport à la ligne de base historique. Envoie également une notification si nous détectons une anomalie dans le temps de réponse ou le temps de chargement des pages.   
* Les dégradations et problèmes généraux, notamment la [dégradation de Trace](./proactive-trace-severity.md), la [fuite de mémoire](./proactive-potential-memory-leak.md), l’[élévation anormale du volume des exceptions](./proactive-exception-volume.md) et les [anti-modèles de sécurité](./proactive-application-security-detection-pack.md).

(Les liens d’aide dans chaque notification vous renvoient vers les articles pertinents.)

## <a name="smart-detection-email-notifications"></a>Notifications par e-mail de détection intelligente

Toutes les règles de détection intelligente, à l’exception des règles portant la mention _Préversion_, sont configurées par défaut pour envoyer des notifications par e-mail suite à des détections.

La configuration de notifications par e-mail pour une règle de détection intelligente spécifique peut être effectuée en ouvrant le panneau **Paramètres** de détection intelligente et en sélectionnant la règle, ce qui a pour effet d’ouvrir le panneau **Modifier une règle**.

Vous pouvez également utiliser les modèles Azure Resource Manager pour modifier la configuration. Pour plus d’informations, consultez [Gérer les règles de détection intelligente d’Application Insights à l’aide de modèles Azure Resource Manager](./proactive-arm-config.md).

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]



## <a name="next-steps"></a>Étapes suivantes
Ces outils de diagnostic vous aident à inspecter les données de télémétrie à partir de votre application :

* [Metrics Explorer](../essentials/metrics-charts.md)
* [Navigateur de recherche](./diagnostic-search.md)
* [Analytics : un puissant langage de requête](../logs/log-analytics-tutorial.md)

La détection intelligente est automatique. Mais vous souhaitez peut-être configurer des alertes supplémentaires ?

* [Alertes de mesures configurées manuellement](../alerts/alerts-log.md)
* [Tests web de disponibilité](./monitor-web-app-availability.md)
