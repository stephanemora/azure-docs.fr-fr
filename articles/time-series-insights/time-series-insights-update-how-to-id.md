---
title: Meilleures pratiques pour le choix d’un ID de série chronologique dans la préversion d’Azure Time Series Insights | Microsoft Docs
description: Présentation des meilleures pratiques lorsque vous choisissez un ID de série chronologique dans la préversion d’Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 7057ce27cbbba8d70835493fc91a88ad823369bb
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947200"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Meilleures pratiques pour choisir un ID de série chronologique

Cet article présente la clé de partition de la préversion d’Azure Time Series Insights, l’ID de série chronologique et les meilleures pratiques associées.

## <a name="choose-a-time-series-id"></a>Choisir un ID Time Series

Le choix d’un ID de série chronologie est identique au choix d’une clé de partition pour une base de données. C’est une décision importante qui doit être effectuée au moment de la conception. Vous ne pouvez pas mettre à jour un environnement Time Series Insights (préversion) existant pour utiliser un ID de série chronologique différent. En d’autres termes, lorsqu’un environnement est créé avec un ID de série chronologique, la stratégie est une propriété immuable qui ne peut pas être modifiée.

> [!IMPORTANT]
> L’ID de série chronologique respecte la casse et il est immuable (il ne peut pas être modifié après sa définition).

Dans cette optique, il est essentiel de sélectionner l’ID de série chronologique approprié. Lorsque vous sélectionnez un ID de série chronologique, tenez compte des meilleures pratiques suivantes :

* Choisissez un nom de propriété qui a une large plage de valeurs et des modèles d’accès uniformes. Il est recommandé de disposer d’une clé de partition comportant de nombreuses valeurs distinctes (plusieurs centaines ou plusieurs milliers). Pour de nombreux clients, cela revient à utiliser un DeviceID ou SensorID dans votre code JSON.
* L’ID de série chronologique doit être unique au niveau du nœud de feuille de votre [modèle de série chronologique](./time-series-insights-update-tsm.md).
* Une chaîne de caractères de nom de propriété d’ID de série chronologique peut avoir jusqu’à 128 caractères, et les valeurs de propriété de l’ID de série chronologique peuvent avoir jusqu’à 1 024 caractères.
* Si certaines valeurs de propriétés d’ID de série chronologique sont manquantes, ces dernières sont traitées comme des valeurs null, qui font partie intégrante de la contrainte d’unicité.

En outre, vous pouvez sélectionner jusqu’à *trois* (3) propriétés de clé comme ID de série chronologique.

  > [!NOTE]
  > Vos *trois* (3) propriétés de clé doivent être des chaînes.

Les scénarios suivants décrivent la sélection de plusieurs propriétés de clé en tant qu’ID de série chronologique :  

### <a name="scenario-one"></a>Scénario 1

* Vous avez des parcs hérités d’actifs, chacun avec une clé unique.
* Par exemple, un parc est identifié de façon unique par la propriété *deviceId* et un autre par la propriété unique *objectId*. Aucun des parcs ne contient la propriété unique de l’autre parc. Dans cet exemple, vous sélectionnez deux clés, deviceId et objectID, comme clés uniques.
* Nous acceptons les valeurs null, et l’absence d’une propriété dans la charge utile d’événement est comptabilisée comme une valeur `null`. Il s’agit également de la méthode appropriée pour gérer l’envoi de données à deux différentes sources d’événements, où les données de chaque source d’événement ont un ID unique de série chronologique.

### <a name="scenario-two"></a>Scénario 2

* Plusieurs propriétés doivent être uniques au sein du même parc d’actifs. 
* Par exemple, supposons que vous êtes un constructeur de bâtiments intelligents et que vous déployez des capteurs dans chaque pièce. Dans chaque pièce, vous avez généralement les mêmes valeurs pour *sensorId*, telles que *sensor1*, *sensor2* et *sensor3*.
* En outre, votre bâtiment a des numéros d’étages et de pièces qui se chevauchent sur les différents sites dans la propriété *flrRm*, qui ont des valeurs telles que *1a*, *2b*, *3a*, et ainsi de suite.
* Enfin, vous disposez d’une propriété, *location*, qui contient des valeurs telles que *Redmond*, *Barcelona* et *Tokyo*. Pour créer l’unicité, vous pouvez désigner les trois propriétés suivantes en tant que clés d’ID de série chronologique : *sensorId*, *flrRm*, et *location*.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [modélisation des données](./time-series-insights-update-tsm.md).

* Planifiez votre [environnement Azure Time Series Insights (préversion)](./time-series-insights-update-plan.md).