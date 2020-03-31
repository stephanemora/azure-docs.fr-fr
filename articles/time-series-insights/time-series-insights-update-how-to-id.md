---
title: Meilleures pratiques pour le choix d’un ID de série chronologique - Azure Time Series Insights | Microsoft Docs
description: Découvrez les meilleures pratiques pour le choix d’un ID de série chronologique dans la préversion d’Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: a62c2460698408f6a2bfa51c6638bdeaf88bb31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083527"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Meilleures pratiques pour choisir un ID de série chronologique

Cet article résume l'importance de l'identifiant de l’ID de série chronologique pour la préversion de votre environnement Azure Time Series Insights et les meilleures pratiques afin d’en choisir un.

## <a name="choose-a-time-series-id"></a>Choisir un ID Time Series

Il est essentiel de sélectionner un ID de série chronologique approprié. Le choix d’un ID de série chronologie est identique au choix d’une clé de partition pour une base de données. Cela est requis lorsque vous créez un environnement Time Series Insights Preview. 

> [!IMPORTANT]
> Les ID des séries chronologiques sont les suivants :
> * Une propriété *respectant la casse* : les cachants de lettres et de caractères sont utilisés dans les recherches, les comparaisons, les mises à jour et le partitionnement.
> * Une *propriété immuable* : une fois créée, elle ne peut pas être modifiée.

> [!TIP]
> Si votre source d’événement est un hub IoT, votre ID de série chronologique sera probablement ***iothub-Connection-Device-ID***.

Les bonnes pratiques principales à suivre sont les suivantes :

* Sélectionnez une clé de partition comportant de nombreuses valeurs distinctes (plusieurs centaines ou plusieurs milliers). Dans de nombreux cas, il peut s'agir de l'ID de l’appareil, de l'ID du capteur ou de l'ID de l'étiquette de votre JSON.
* L’ID de série chronologique doit être unique au niveau du nœud de feuille de votre [modèle de série chronologique](./time-series-insights-update-tsm.md).
* La limite de caractères pour la chaîne de nom de propriété de l’ID de série chronologie est 128. Pour la valeur de propriété de l’ID de série chronologie, la limite de caractères est de 1 024.
* Si une valeur de propriété unique pour l'ID de série chronologique est manquante, elle est traitée comme une valeur nulle et suit la même règle de la contrainte d'unicité.
* Vous pouvez également sélectionner jusqu'à *trois* propriétés clés comme ID de série chronologique. Leur combinaison sera une clé composite qui représente l'ID de série chronologique.  
  > [!NOTE]
  > Vos trois propriétés de clé doivent être des chaînes.
  > Vous devrez interroger cette clé composite au lieu d'une propriété à la fois.

## <a name="select-more-than-one-key-property"></a>Sélectionnez plus d’une propriété de clé

Les scénarios suivants décrivent la sélection de plusieurs propriétés de clé en tant qu’ID de série chronologique.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Exemple 1 : ID de série chronologique avec une clé unique

* Vous avez des parcs hérités d’actifs, chacun avec une clé unique.
* Un parc est identifié de façon unique par la propriété **deviceId**, et un par la propriété unique **objectId**. Aucun des parcs ne contient la propriété unique de l’autre parc. Dans cet exemple, vous sélectionnez deux clés, **deviceId** et **objectId**, comme clés uniques.
* Nous acceptons les valeurs null, et l’absence d’une propriété dans la charge utile d’événement est comptabilisée comme une valeur nulle. Il s’agit également de la méthode appropriée pour gérer l’envoi de données à deux sources d’événements, où les données de chaque source d’événement ont un ID unique de série chronologique.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Exemple 2 : ID de série chronologique avec une clé composite

* Plusieurs propriétés doivent être uniques au sein du même parc d’actifs. 
* Vous construisez des bâtiments intelligents et déployez des capteurs dans chaque pièce. Dans chaque pièce, vous avez généralement les mêmes valeurs pour **sensorId**, telles que **sensor1**, **sensor2** et **sensor3**.
* Votre bâtiment a des numéros d’étages et de pièces qui se chevauchent sur les différents sites dans la propriété **flrRm**, qui ont des valeurs telles que **1a**, **2b**, **3a**, et ainsi de suite.
* Vous disposez d’une propriété, **location**, qui contient des valeurs telles que **Redmond**, **Barcelona** et **Tokyo**. Pour créer l’unicité, vous désignez les trois propriétés suivantes en tant que clés d’ID de série chronologique : **sensorId**, **flrRm**, et **location**.

Exemple d’événement brut :

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

Dans le portail Azure, vous pouvez ensuite saisir cette clé composite sous la forme : 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [modélisation des données](./time-series-insights-update-tsm.md).

* Planifiez votre [environnement Azure Time Series Insights (préversion)](./time-series-insights-update-plan.md).