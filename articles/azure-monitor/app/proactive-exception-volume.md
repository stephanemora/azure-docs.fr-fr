---
title: Augmentation anormale du volume des exceptions – Azure Application Insights
description: Surveillez les exceptions d’application à l’aide de la détection intelligente dans Azure Application Insights afin de déterminer si le volume des exceptions présente des anomalies.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/08/2017
ms.openlocfilehash: b524000f78741b2ff8cf442d2daaef0c7945799a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432434"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Élévation anormale du volume des exceptions (préversion)

Application Insights analyse automatiquement les exceptions levées dans votre application et peut vous avertir si vos données de télémétrie pour les exceptions présentent des anomalies.

Cette fonctionnalité ne requiert aucune configuration spéciale, autre que la [configuration du rapport des exceptions](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) pour votre application. Elle est active quand votre application génère suffisamment de données de télémétrie pour les exceptions.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quand reçoit-on ce type de notification de détection intelligente ?
Vous pouvez recevoir ce type de notification si votre application présente une élévation anormale du nombre d’exceptions d’un type spécifique pendant une journée, par rapport à une ligne de base calculée sur les sept jours précédents.
Des algorithmes d’apprentissage automatique sont utilisés pour détecter l’augmentation du nombre d’exceptions, tout en tenant compte d’une croissance naturelle de l’utilisation de votre application.

## <a name="does-my-app-definitely-have-a-problem"></a>Mon application rencontre-t-elle vraiment un problème ?
Non, une notification ne signifie pas que votre application rencontre réellement un problème. Bien qu’un nombre excessif d’exceptions indique généralement un problème lié à l’application, ces exceptions peuvent être bénignes et être correctement traitées par votre application.

## <a name="how-do-i-fix-it"></a>Comment la corriger ?
Les notifications incluent des informations de diagnostic qui facilitent le processus de diagnostic :
1. **Triage.** La notification vous indique le nombre d’utilisateurs ou le nombre de demandes affectés. Ceci vous permet d’attribuer une priorité au problème.
2. **Portée.** Le problème affecte-t-il tout le trafic ou une opération seulement ? Ces informations peuvent être obtenues dans la notification.
3. **Diagnostic**. La détection contient des informations sur la méthode à partir de laquelle l’exception a été levée, ainsi que le type d’exception. Pour mieux diagnostiquer le problème, vous pouvez également utiliser les éléments liés et les rapports pointant vers des informations de prise en charge.
