---
title: Détecter des fuites de mémoire - Détection intelligente Azure Application Insights
description: Surveiller les applications avec Azure Application Insights pour détecter les fuites de mémoire potentielles.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: f2b13fc97baac2e8fcfb27475e6e018fdc0c5392
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111536620"
---
# <a name="memory-leak-detection-preview"></a>Détection des fuites de mémoire (version préliminaire)

>[!NOTE]
>Vous pouvez migrer vos ressources Application Insights la détection intelligente des alertes (préversion). La migration crée des règles d’alerte pour les différents modules de détection intelligente. Après création, vous pouvez gérer et configurer ces règles comme n’importe quelle autre règle d’alerte Azure Monitor. Vous pouvez également configurer des groupes d’actions pour ces règles, ce qui permet à plusieurs méthodes de prendre des mesures ou de déclencher des notifications sur de nouvelles détections.
>
> Pour plus d’informations, consultez [Migration des alertes de détection intelligente](../alerts/alerts-smart-detections-migration.md).

La détection intelligente analyse automatiquement la consommation de mémoire des processus de votre application et peut vous avertir des fuites de mémoire potentielles ou d’une consommation accrue de la mémoire.

Cette fonctionnalité ne requiert aucune configuration spéciale, autre que la [configuration des compteurs de performances](./performance-counters.md) pour votre application. Elle est active lorsque votre application génère suffisamment de données de télémétrie sur les compteurs de performances mémoire (par exemple, les octets privés).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quand reçoit-on ce type de notification de détection intelligente ?
Une notification classique suit une augmentation constante de la consommation de mémoire sur une période prolongée, pour un ou plusieurs processus et/ou une ou plusieurs machines, qui font partie intégrante de votre application. Des algorithmes Machine Learning sont utilisés pour la détection d’une consommation de mémoire accrue correspondant au modèle d’une fuite de mémoire.

## <a name="does-my-app-really-have-a-problem"></a>Mon application rencontre-t-elle réellement un problème ?
Une notification ne signifie pas que votre application rencontre réellement un problème. Bien que les modèles de fuites de mémoire indiquent souvent un problème au niveau de l’application, ces modèles peuvent être liés à votre processus spécifique, ou peuvent avoir une justification naturelle. Dans ce cas, la notification peut être ignorée.

## <a name="how-do-i-fix-it"></a>Comment la corriger ?
Les notifications incluent des informations de diagnostic qui facilitent le processus d’analyse de diagnostic :
1. **Triage.** La notification vous montre la quantité d’augmentation de mémoire (en Go) et l’intervalle de temps au cours duquel celle-ci s’est produite. Ces informations vous permettent d’attribuer une priorité au problème.
2. **Portée.** Combien de machines présentent un modèle de fuite de mémoire ? Combien d’exceptions ont été déclenchées au cours de la fuite de mémoire potentielle ? Ces informations peuvent être obtenues dans la notification.
3. **Diagnostic**. La détection contient le modèle de fuite de mémoire, montrant la consommation de mémoire du processus au fil du temps. Pour mieux diagnostiquer le problème, vous pouvez également utiliser les éléments liés et les rapports pointant vers des informations de prise en charge.
