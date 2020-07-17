---
title: Ajout de la prise en charge du type de données Long | Microsoft Docs
description: Prise en charge du type de données Long
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: dpalled
ms.openlocfilehash: a0b53749a32e79d690cf4412fdac82b18dfe2f2e
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86183628"
---
# <a name="adding-support-for-long-data-type"></a>Ajout de la prise en charge du type de données Long

Ces modifications ne vont être appliquées qu’aux environnements (PAYG) en préversion. Si vous avez un environnement TSI SKU standard (S), vous pouvez ignorer ces modifications.

Nous modifions notre façon de stocker et d’indexer les données numériques dans la préversion d’Azure Time Series Insights et cela peut avoir un impact sur vous. Si vous êtes concerné par l’un des cas ci-dessous, apportez les modifications nécessaires dès que possible. Vos données commenceront à être indexées comme Long et Double entre le 29 et le 30 juin 2020, selon votre région. Si vous avez des questions ou des préoccupations concernant cette modification, soumettez un ticket de support par le biais du portail Azure et mentionnez cette communication.

Cette modification vous concerne dans les cas suivants :

1. Si vous utilisez actuellement des variables de modèle de série chronologique et n’envoyez que des types de données intégrales dans vos données de télémétrie.
1. Si vous utilisez actuellement des variables de modèle de série chronologique et envoyez des types de données intégrales et non intégrales dans vos données de télémétrie.
1. Si vous utilisez des variables de catégorie pour mapper des valeurs entières à des catégories.
1. Si vous utilisez le kit de développement logiciel (SDK) JavaScript pour créer une application frontale personnalisée.
1. Si vous approchez de la limite de 1 000 noms de propriété dans le magasin Warm (WS) et que vous envoyez des données intégrales et non intégrales, le nombre de propriétés peut être affiché en tant que métrique dans le [portail Azure](https://portal.azure.com/).

Si l’un des cas ci-dessus vous concerne, vous devez modifier votre modèle de manière à prendre cette modification en compte. Mettez à jour l’expression de série chronologique dans votre définition de variable à la fois dans l’explorateur TSI et dans tout autre client personnalisé utilisant nos API avec les modifications recommandées. Voir les détails ci-dessous.

En fonction de votre solution IoT et de vos contraintes, vous risquez de ne pas avoir de visibilité sur les données envoyées à votre environnement PAYG TSI. Si vous ne savez pas si vos données sont intégrales uniquement ou intégrales et non intégrales, plusieurs options s’offrent à vous. Vous pouvez attendre que la fonctionnalité soit publiée, puis explorer vos événements bruts dans l’interface utilisateur de l’explorateur pour comprendre les propriétés qui ont été enregistrées dans deux colonnes distinctes. Vous pouvez effectuer les modifications ci-dessous de manière préventive pour toutes les étiquettes numériques ou acheminer temporairement un sous-ensemble d’événements vers le stockage pour mieux comprendre et explorer votre schéma. Pour stocker des événements, activez la [capture d’événements](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) pour Event Hubs ou le [routage](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage) de votre IoT Hub vers le stockage Blob Azure. Les données peuvent également être observées par le biais de l’[explorateur Event Hub](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer) ou à l’aide de l’[hôte du processeur d’événements](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events). Si vous utilisez IoT Hub, consultez [ici](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) la documentation sur l’accès au point de terminaison intégré.

Notez que si vous êtes concerné par ces modifications et que vous ne parvenez pas à les effectuer avant les dates indiquées ci-dessus, vous risquez de subir des interruptions lorsque les variables de la série chronologique impactées faisant l’objet d’un accès via des API de requête ou l’explorateur Time Series Insights retournent la valeur *null* (c.-à-d. qu’elles n’affichent aucune donnée dans l’explorateur).

## <a name="recommended-changes"></a>Modifications recommandées

Cas 1 et 2 : **Utilisation de variables de modèle de série chronologique et envoi de types de données intégrales uniquement OU envoi de types de données intégrales et non intégrales dans les données de télémétrie.**

Si vous envoyez actuellement des données de télémétrie entières, vos données sont divisées en deux colonnes : « propertyValue_double » et « propertyValue_long ».

Vos données entières sont écrites dans « propertyValue_long » lorsque les modifications entrent en vigueur et les données numériques reçues précédemment (et les futures données ingérées) dans « propertyValue_double » ne sont pas copiées.

Si vous souhaitez interroger des données dans ces deux colonnes pour la propriété « propertyValue », vous devez utiliser la fonction scalaire *coalesce()* dans votre TSX. La fonction accepte les arguments de même type de données et retourne la première valeur non null de la liste d’arguments (pour en savoir plus sur l’utilisation, voir [ici](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)).

### <a name="variable-definition-in-time-series-explorer---numeric"></a>Définition de variable dans l’explorateur de série chronologique - Numérique

*Définition précédente de la variable :*

[![Définition précédente de la variable](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Nouvelle définition de la variable :*

[![Nouvelle définition de la variable](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

Vous pouvez également utiliser *« coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long)) »* comme [expression de série chronologique](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) personnalisée.

### <a name="inline-variable-definition-using-time-series-query-apis---numeric"></a>Définition de variable Inline à l’aide d’API de requête de série chronologique - Numérique

*Définition précédente de la variable :*

```tsx
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
```

*Nouvelle définition de la variable :*

```tsx
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
```

Vous pouvez également utiliser *« coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long)) »* comme [expression de série chronologique](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) personnalisée.

> [!NOTE]
> Nous vous recommandons de mettre à jour ces variables partout où elles peuvent être utilisées (modèle de série chronologique, requêtes enregistrées, requêtes de connecteur Power BI).

Cas n° 3 : **Utilisation de variables de catégorie pour mapper des valeurs entières à des catégories**

Si vous utilisez actuellement des variables de catégorie qui mappent des valeurs entières à des catégories, vous utilisez probablement la fonction toLong pour convertir des données de type Double en type Long. Comme dans les cas ci-dessus, vous allez devoir fusionner les colonnes de type de données Double et Long.

### <a name="variable-definition-in-time-series-explorer---categorical"></a>Définition de variable dans l’explorateur de série chronologique - Catégorie

*Définition précédente de la variable :*

[![Définition précédente de la variable](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Nouvelle définition de la variable :*

[![Nouvelle définition de la variable](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

Vous pouvez également utiliser *« coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long)) »* comme [expression de série chronologique](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) personnalisée.

Les variables de catégorie requièrent toujours une valeur de type entier. Le type de données de tous les arguments de coalesce() doit être Long dans l’[expression de série chronologique](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) personnalisée.

### <a name="inline-variable-definition-using-time-series-query-apis---categorical"></a>Définition de variable Inline à l’aide d’API de requête de série chronologique - Catégorie

*Définition précédente de la variable :*

```tsx
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "tolong($event.propertyValue.Double)"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }

}
```

*Nouvelle définition de la variable :*

```tsx
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }

}
```

Les variables de catégorie requièrent toujours une valeur de type entier. Le type de données de tous les arguments de coalesce() doit être Long dans l’[expression de série chronologique](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) personnalisée.

> [!NOTE]
> Nous vous recommandons de mettre à jour ces variables partout où elles peuvent être utilisées (modèle de série chronologique, requêtes enregistrées, requêtes de connecteur Power BI).

Cas 4 : **Utilisation du kit de développement logiciel (SDK) JavaScript pour créer une application frontale personnalisée**

Si vous êtes concerné par les cas 1 à 3 ci-dessus et si vous créez des applications personnalisées, vous devez mettre à jour vos requêtes de manière à utiliser la fonction *coalesce()* , comme illustré dans les exemples ci-dessus.

Case 5 : **Limite de 1 000 propriétés du magasin Warm presque atteinte**

Si vous êtes un utilisateur du magasin Warm avec un grand nombre de propriétés et que vous pensez que cette modification pousserait votre environnement au-delà de la limite de nom de 1 000 propriétés WS, soumettez un ticket de support via le Portail Azure et mentionnez cette communication.

## <a name="next-steps"></a>Étapes suivantes

* Pour afficher la liste complète des types de données pris en charge, consultez [cet](concepts-supported-data-types.md) article.
