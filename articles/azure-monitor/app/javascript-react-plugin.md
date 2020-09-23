---
title: Plug-in React pour le Kit de développement logiciel (SDK) JavaScript Application Insights
description: Comment installer et utiliser le plug-in React pour le Kit de développement logiciel (SDK) JavaScript Application Insights.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 3a11f77384c520bed9824841269be4ad998adba4
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056198"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Plug-in React pour le Kit de développement logiciel (SDK) JavaScript Application Insights

Le plug-in React pour le Kit de développement logiciel (SDK) JavaScript Application Insights permet :

- le suivi des modifications d’itinéraires ;
- le calcul des statistiques d’utilisation des composants React.

## <a name="getting-started"></a>Prise en main

Installez le package npm :

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Utilisation de base

```javascript
import React from 'react';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin, withAITracking } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from "history";

const browserHistory = createBrowserHistory({ basename: '' });
var reactPlugin = new ReactPlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin,appInsights, MyComponent);

```

## <a name="configuration"></a>Configuration

| Name    | Default | Description                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | null    | Historique du routeur React. Pour plus d’informations, consultez la [documentation du package react-router](https://reactrouter.com/web/api/history). Pour savoir comment accéder à l’objet d’historique en dehors des composants, consultez la [FAQ relative à react-router](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components).    |

### <a name="react-components-usage-tracking"></a>Suivi de l’utilisation des composants React

Pour instrumenter le suivi de l’utilisation des composants React, appliquez la fonction de composant d’ordre supérieur `withAITracking`.

Elle mesurera le temps à partir de l’événement `ComponentDidMount` par le biais de l’événement `ComponentWillUnmount`. Toutefois, pour plus de précision, le temps pendant lequel l’utilisateur était inactif sera soustrait : `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time`.

Pour afficher cette métrique dans le portail Azure, vous devez accéder à la ressource Application Insights, sélectionner l’onglet « Métriques » et configurer les graphiques vides pour qu’ils affichent le nom de métrique personnalisé « Temps d’engagement du composant React (secondes) », sélectionner l’agrégation (somme, moyenne, etc.) de votre métrique et appliquer la division par « Nom du composant ».

![Capture d’écran du graphique qui affiche la métrique personnalisée « Temps d’engagement du composant React (secondes) » divisée par « Nom du composant »](./media/javascript-react-plugin/chart.png)

Vous pouvez également exécuter des requêtes personnalisées pour diviser les données d’Application Insights afin de générer des rapports et des visualisations selon vos besoins. Dans le portail Azure, accédez à la ressource Application Insights, sélectionnez « Analyse » dans le menu supérieur de l’onglet Vue d’ensemble et exécutez votre requête.

![Capture d’écran des résultats de la requête de métrique personnalisée.](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Il peut s’écouler jusqu’à 10 minutes avant que de nouvelles mesures personnalisées n’apparaissent dans le portail Azure.

## <a name="sample-app"></a>Exemple d'application

Consultez la [démonstration de React d’Application Insights](https://github.com/Azure-Samples/application-insights-react-demo).

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le Kit de développement logiciel (SDK) JavaScript, consultez la [documentation du Kit de développement logiciel (SDK) JavaScript Application Insights](javascript.md).
- Pour en savoir plus sur le langage de requête Kusto et l’interrogation des données dans Log Analytics, consultez la page [Vue d’ensemble des requêtes de journal](../../azure-monitor/log-query/log-query-overview.md).
