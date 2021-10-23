---
title: Plug-in Angular pour le Kit de développement logiciel (SDK) JavaScript Application Insights
description: Comment installer et utiliser le plug-in Angular pour le Kit de développement logiciel (SDK) JavaScript Application Insights.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: dc5b7e3f1e452bbbf4e65442cbf683b4cb1a8816
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129811"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Plug-in Angular pour le Kit de développement logiciel (SDK) JavaScript Application Insights

Le plug-in Angular pour le Kit de développement logiciel (SDK) JavaScript Application Insights permet :

- le suivi des modifications d’itinéraires ;
- le suivi des exceptions non interceptées.

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

Pour suivre les exceptions non interceptées, configurez ApplicationinsightsAngularpluginErrorService dans `app.module.ts` :

```js
import { ApplicationinsightsAngularpluginErrorService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
  ...
  providers: [
    {
      provide: ErrorHandler,
      useClass: ApplicationinsightsAngularpluginErrorService
    }
  ]
  ...
})
export class AppModule { }
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le Kit de développement logiciel (SDK) JavaScript, consultez la [documentation du Kit de développement logiciel (SDK) JavaScript Application Insights](javascript.md)
- [Plug-in Angular sur GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
