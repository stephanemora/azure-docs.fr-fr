---
title: Plug-in Angular pour le Kit de développement logiciel (SDK) JavaScript Application Insights
description: Comment installer et utiliser le plug-in Angular pour le Kit de développement logiciel (SDK) JavaScript Application Insights.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: d45d8bed328dc91dfeeabd6ce878074fa1218623
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737016"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Plug-in Angular pour le Kit de développement logiciel (SDK) JavaScript Application Insights

Le plug-in Angular pour le Kit de développement logiciel (SDK) JavaScript Application Insights permet :

- le suivi des modifications d’itinéraires ;

> [!WARNING]
> Le plug-in Angular n’est pas compatible avec ECMAScript 3 (ES3).

## <a name="getting-started"></a>Prise en main

Installez le package npm :

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Utilisation de base

Configurez une instance d’Application Insights dans le composant d’entrée de votre application :

```js
import { Component } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(
        private router: Router
    ){
        var angularPlugin = new AngularPlugin();
        const appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
        appInsights.loadAppInsights();
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le Kit de développement logiciel (SDK) JavaScript, consultez la [documentation du Kit de développement logiciel (SDK) JavaScript Application Insights](javascript.md)
- [Plug-in Angular sur GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
