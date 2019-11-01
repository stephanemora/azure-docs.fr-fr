---
title: Détection intelligente - Dégradation du rapport entre les niveaux de gravité des suivis dans Azure Application Insights | Microsoft Docs
description: Surveillez les suivis d’application avec Azure Application Insights afin de déterminer si les données de télémétrie du suivi présentent des anomalies.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/27/2017
ms.openlocfilehash: 83c1296beabaaae78289a653c6b30f6665f725c2
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820526"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Dégradation du rapport entre les niveaux de gravité des suivis (préversion)

Les suivis sont couramment utilisés dans les applications, car ils aident à comprendre ce qui se passe dans les coulisses. En cas de problème, les suivis fournissent une visibilité essentielle de la séquence des événements conduisant à l’état indésirable. Bien que généralement non structurés, les suivis indiquent un élément d’information concret, à savoir leur niveau de gravité. Dans l’état d’équilibre d’une application, le rapport entre les suivis « positifs » (*Informations* et *Commentaires*) et les suivis « négatifs » (*Avertissement*, *Erreur* et *Critique*) est censé demeurer stable. Dans une certaine mesure, des suivis « mauvais » peuvent effectivement se produire à intervalles réguliers pour un nombre quelconque de raisons (telles que des problèmes temporaires liés au réseau). Mais quand un vrai problème commence à prendre de l’ampleur, il engendre généralement une augmentation de la proportion des suivis « bons » par rapport aux suivis « mauvais ». La détection intelligente d’Application Insights analyse automatiquement les suivis journalisés par votre application et peut vous avertir si le niveau de gravité de vos données de télémétrie des suivis présente des anomalies.

Cette fonctionnalité ne nécessite aucune configuration particulière, autre que la configuration de la journalisation des suivis pour votre application (voir comment configurer un écouteur de journalisation de suivis [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) ou [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). Elle est active quand votre application génère suffisamment de données de télémétrie pour les exceptions.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quand reçoit-on ce type de notification de détection intelligente ?
Vous pouvez obtenir ce type de notification si le rapport entre les « bons » suivis (suivis journalisés avec un niveau *Informations* ou *Commentaires*) et les « mauvais » suivis (suivis journalisés avec un niveau *Avertissement*, *Erreur* ou *Irrécupérable*) se dégrade pendant un jour spécifique par rapport à une ligne de base calculée sur les sept jours précédents.

## <a name="does-my-app-definitely-have-a-problem"></a>Mon application rencontre-t-elle vraiment un problème ?
Non, une notification ne signifie pas que votre application rencontre réellement un problème. Bien qu’une dégradation du rapport entre les suivis « bons » et les suivis « mauvais » puisse indiquer un problème d’application, cette évolution du rapport peut être sans gravité. Par exemple, l’augmentation peut être due à un nouveau flux dans l’application émettant plus de suivis « mauvais » que les flux existants.

## <a name="how-do-i-fix-it"></a>Comment la corriger ?
Les notifications incluent des informations de diagnostic qui facilitent le processus de diagnostic :
1. **Tri.** La notification vous indique le nombre d’opérations affectées. Ceci vous permet d’attribuer une priorité au problème.
2. **Portée.** Le problème affecte-t-il tout le trafic ou une opération seulement ? Ces informations peuvent être obtenues dans la notification.
3. **Diagnostic**. Pour mieux diagnostiquer le problème, vous pouvez utiliser les éléments liés et les rapports pointant vers des informations de prise en charge.


