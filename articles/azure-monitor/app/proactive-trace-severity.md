---
title: Dégradation du rapport entre les niveaux de gravité des suivis - Azure Application Insights
description: Supervisez les suivis d’application avec Azure Application Insights afin de déterminer si les données de télémétrie du suivi présentent des anomalies à l’aide de la détection intelligente.
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 776469f5e5cdab7796df1edccb680ab7151bbd18
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111951520"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Dégradation du rapport entre les niveaux de gravité des suivis (préversion)

>[!NOTE]
>Vous pouvez migrer vos ressources Application Insights la détection intelligente des alertes (préversion). La migration crée des règles d’alerte pour les différents modules de détection intelligente. Vous pouvez alors gérer et configurer ces règles comme n’importe quelle autre règle d’alerte Azure Monitor. Vous pouvez également configurer des groupes d’actions pour ces règles, ce qui permet à plusieurs méthodes de prendre des mesures ou de déclencher des notifications sur de nouvelles détections.
>
> Pour plus d’informations, consultez [Migration des alertes de détection intelligente](../alerts/alerts-smart-detections-migration.md).
> 

Les suivis sont couramment utilisés dans les applications, et ils aident à comprendre ce qui se passe dans les coulisses. En cas de problème, les suivis fournissent une visibilité essentielle de la séquence des événements conduisant à l’état indésirable. Bien que les traces soient essentiellement non structurées, leur niveau de gravité peut toujours fournir des informations précieuses. Dans l’état d’équilibre d’une application, le rapport entre les suivis « positifs » (*Informations* et *Commentaires*) et les suivis « négatifs » (*Avertissement*, *Erreur* et *Critique*) est censé demeurer stable. 

Il est normal d’attendre un certain niveau de traces « mauvaises » pour diverses raisons, comme des problèmes réseau temporaires. Mais quand un vrai problème commence à prendre de l’ampleur, il engendre généralement une augmentation de la proportion des suivis « bons » par rapport aux suivis « mauvais ». La détection intelligente analyse automatiquement les données de télémétrie de trace que votre application journalise et peut vous avertir des modèles inhabituels dans leur gravité.

Cette fonctionnalité ne requiert aucune configuration spéciale, autre que la configuration du journalisation des traces pour votre application. Consultez Comment configurer un écouteur de journalisation des traces pour [.NET](./asp-net-trace-logs.md) ou [Java](./java-in-process-agent.md). Elle est active lorsque votre application génère suffisamment de données de télémétrie de trace.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quand reçoit-on ce type de notification de détection intelligente ?
Vous obtenez ce type de notification si le rapport entre les « bons » suivis (suivis journalisés avec un niveau *Informations* ou *Commentaires*) et les « mauvais » suivis (suivis journalisés avec un niveau *Avertissement*, *Erreur* ou *Irrécupérable*) se dégrade pendant un jour spécifique par rapport à une ligne de base calculée sur les sept jours précédents.

## <a name="does-my-app-definitely-have-a-problem"></a>Mon application rencontre-t-elle vraiment un problème ?
Une notification ne signifie pas que votre application rencontre réellement un problème. Bien qu’une dégradation du rapport entre les suivis « bons » et les suivis « mauvais » puisse indiquer un problème d’application, cela peut également être sans gravité. Par exemple, l’augmentation peut être due à un nouveau flux dans l’application émettant plus de suivis « mauvais » que les flux existants.

## <a name="how-do-i-fix-it"></a>Comment la corriger ?
Les notifications incluent des informations de diagnostic qui facilitent le processus de diagnostic :
1. **Triage.** La notification vous indique le nombre d’opérations affectées. Ces informations vous permettent d’attribuer une priorité au problème.
2. **Portée.** Le problème affecte-t-il tout le trafic ou une opération seulement ? Ces informations peuvent être obtenues dans la notification.
3. **Diagnostic**. Pour mieux diagnostiquer le problème, vous pouvez utiliser les éléments liés et les rapports pointant vers des informations de prise en charge.