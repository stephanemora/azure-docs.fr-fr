---
title: Augmentation anormale du volume des exceptions – Azure Application Insights
description: Surveillez les exceptions d’application avec la détection intelligente d’Azure Application Insights pour identifier des motifs inhabituels dans le volume des exceptions.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 4220415a0ab907c2cdf9268a59a4e85400cad86c
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111536688"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Élévation anormale du volume des exceptions (préversion)

>[!NOTE]
>Vous pouvez migrer vos ressources Application Insights vers la détection intelligente avec alertes (préversion). La migration crée des règles d’alerte pour les différents modules de détection intelligente. Il est alors possible de gérer et de configurer ces règles comme n’importe quelle autre règle d’alerte Azure Monitor. Vous pouvez également configurer des groupes d’actions pour ces règles, ce qui offre plusieurs moyens de prendre des mesures ou de déclencher une notification en cas de nouvelle détection.
>
> Pour plus d’informations, consultez [Migration des alertes de détection intelligente](../alerts/alerts-smart-detections-migration.md).

La détection intelligente analyse automatiquement les exceptions levées dans votre application. Elle peut vous avertir si les données de télémétrie des exceptions présentent des motifs inhabituels.

Cette fonctionnalité ne requiert aucune configuration spéciale, autre que la [configuration du rapport des exceptions](./asp-net-exceptions.md#set-up-exception-reporting) pour votre application. Elle est active quand votre application génère suffisamment de données de télémétrie pour les exceptions.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quand reçoit-on ce type de notification de détection intelligente ?
Vous recevez ce type de notification si votre application présente une augmentation anormale du nombre d’exceptions d’un type spécifique un certain jour. Ce nombre est comparé à une ligne de base calculée au cours des sept jours précédents.
Des algorithmes Machine Learning sont utilisés pour détecter l’augmentation du nombre d’exceptions, tout en tenant compte d’une croissance naturelle de l’utilisation de l’application.

## <a name="does-my-app-definitely-have-a-problem"></a>Mon application rencontre-t-elle vraiment un problème ?
Non, une notification ne signifie pas que votre application rencontre réellement un problème. Bien qu’un nombre excessif d’exceptions indique généralement un problème lié à l’application, ces exceptions peuvent être bénignes et être correctement traitées par votre application.

## <a name="how-do-i-fix-it"></a>Comment la corriger ?
Les notifications incluent des informations de diagnostic qui facilitent le processus de diagnostic :
1. **Triage.** La notification vous indique le nombre d’utilisateurs ou le nombre de demandes affectés. Ces informations vous permettent d’attribuer une priorité au problème.
2. **Portée.** Le problème affecte-t-il tout le trafic ou une opération seulement ? Ces informations peuvent être obtenues dans la notification.
3. **Diagnostic**. La détection contient des informations sur la méthode dans laquelle l’exception a été levée et sur le type d’exception. Pour mieux diagnostiquer le problème, vous pouvez également utiliser les éléments liés et les rapports pointant vers des informations de prise en charge.
