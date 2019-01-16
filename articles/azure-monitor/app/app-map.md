---
title: Mise en correspondance d’applications dans Azure Application Insights | Microsoft Docs
description: Surveiller des topologies d’applications complexes avec la mise en correspondance d’applications
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/17/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 238b75681ec525187ea27f60ac8b21b05b13954d
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063112"
---
# <a name="application-map-triage-distributed-applications"></a>Cartographie d’application : trier des applications distribuées

La mise en correspondance d’applications vous permet d’identifier les baisses de performances ou les défaillances sur l’ensemble des composants de votre application distribuée. Chaque nœud de cette mise en correspondance représente un composant d’application ou ses dépendances, avec des indicateurs de performance clés d’intégrité et l’état des alertes. Vous pouvez cliquer sur n’importe quel composant pour obtenir des diagnostics plus détaillés, par exemple des événements Application Insights. Si votre application utilise les services Azure, vous pouvez également accéder aux diagnostics Azure, et notamment aux recommandations de SQL Database Advisor.

## <a name="what-is-a-component"></a>Qu’est un composant ?

Les composants sont des parties pouvant être déployées de manière indépendante de votre application distribuée/de microservices. Les développeurs et équipes d’opérations disposent d’une visibilité au niveau du code ou d’un accès à la télémétrie générée par ces composants d’application. 

* Les composants sont différents des dépendances externes « observées » telles que SQL, EventHub, etc., auxquelles votre équipe/organisation peut ne pas avoir accès (code ou télémétrie).
* Les composants s’exécutent sur un nombre quelconque d’instances de serveur/rôle/conteneur.
* Les composants peuvent être des clés d’instrumentation Application Insights distinctes (même si les abonnements sont différents) ou des rôles différents rapportant à une clé d’instrumentation Application Insights unique. L’aperçu de mise en correspondance montre les composants, quelle que soit leur configuration.

## <a name="composite-application-map"></a>Cartographie d’application composite

Vous pouvez afficher la topologie complète des applications sur plusieurs niveaux des composants d’application associés. Les composants peuvent représenter différentes ressources Application Insights, ou différents rôles d’une seule ressource. La mise en correspondance d’applications trouve les composants en suivant les appels de dépendance HTTP effectués entre les serveurs sur lesquels le kit SDK Application Insights est installé. 

Cette expérience démarre la découverte progressive des composants. Lorsque vous chargez la cartographie d’application pour la première fois, un ensemble de requêtes est déclenché pour découvrir les composants liés à ce composant. Un bouton dans le coin supérieur gauche permet de mettre à jour le nombre de composants de votre application dès qu’ils sont détectés. 

Lorsque vous cliquez sur « Update map components » (Mettre à jour les composants de cartographie), la carte est actualisée avec tous les composants détectés. Selon la complexité de votre application, le chargement peut prendre une minute.

Si tous les composants sont des rôles au sein d’une seule ressource Application Insights, cette étape de découverte n’est pas requise. La charge initiale pour une telle application aura tous ses composants.

![Capture d’écran de la cartographie d’application](media/app-map/001.png)

L’un des principaux objectifs de cette expérience est de permettre de visualiser des topologies complexes incluant des centaines de composants.

Cliquez sur n’importe quel composant pour afficher des informations connexes ainsi que les performances et l’expérience de triage de défaillance de ce composant.

![Menu volant](media/app-map/application-map-001.png)

### <a name="investigate-failures"></a>Examiner les échecs

Pour lancer le panneau Échecs, sélectionnez **Examiner les échecs**.

![Capture d’écran du bouton Examiner les échecs](media/app-map/investigate-failures.png)

![Capture d’écran du panneau Échecs](media/app-map/failures.png)

### <a name="investigate-performance"></a>Examiner les performances

Pour résoudre les problèmes de performances, sélectionnez **Examiner les performances**.

![Capture d’écran du bouton Examiner les performances](media/app-map/investigate-performance.png)

![Capture d’écran du panneau Performances](media/app-map/performance.png)

### <a name="go-to-details"></a>Accéder aux détails

Pour explorer l’expérience de transaction de bout en bout pouvant présenter des vues générées au niveau de la pile des appels, sélectionnez **Accéder aux détails**.

![Capture d’écran du bouton Accéder aux détails](media/app-map/go-to-details.png)

![Capture d’écran des détails de transaction de bout en bout](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Afficher dans Analytics

Pour interroger et analyser vos données d’application de manière plus approfondie, cliquez sur **Afficher dans Analytics**.

![Capture d’écran du bouton Afficher dans Analytics](media/app-map/view-in-analytics.png)

![Capture d’écran de l’expérience d’analyse](media/app-map/analytics.png)

### <a name="alerts"></a>Alertes

Pour visualiser les alertes actives et les règles sous-jacentes qui entraînent le déclenchement d’alertes, sélectionnez **Alertes**.

![Capture d’écran du bouton Alertes](media/app-map/alerts.png)

![Capture d’écran de l’expérience d’analyse](media/app-map/alerts-view.png)

## <a name="set-cloudrolename"></a>Définir cloud_RoleName

La cartographie d’application utilise la propriété `cloud_RoleName` pour identifier les composants sur la carte. Le SDK Application Insights ajoute automatiquement la propriété `cloud_RoleName` aux données de télémétrie émises par les composants. Par exemple, le SDK ajoute un nom de site web ou un nom de rôle de service à la propriété `cloud_RoleName`. Toutefois, vous pouvez être amené à remplacer la valeur par défaut. Pour remplacer cloud_RoleName et changer ce qui s’affiche sur la cartographie d’application :

### <a name="net"></a>.NET

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "RoleName";
            }
        }
    }
}
```

**Charger votre initialiseur**

Dans ApplicationInsights.config :

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Une autre méthode consiste à instancier l’initialiseur dans le code, par exemple dans Global.aspx.cs :

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Autre méthode pour Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

Si vous utilisez Spring Boot avec le starter SpringBoot Application Insights, le seul changement requis consiste à définir votre nom personnalisé pour l’application dans le fichier application.properties.

`spring.application.name=<name-of-app>`

Le starter SpringBoot assigne automatiquement cloudRoleName à la valeur que vous entrez pour la propriété spring.application.name.

Pour plus d’informations sur la corrélation Java et sur la façon de configurer cloudRoleName pour les applications non-SpringBoot, consultez cette [section](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) sur la corrélation.

### <a name="clientbrowser-side-javascript"></a>JavaScript côté client/navigateur

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

Pour plus d’informations sur la substitution de la propriété cloud_RoleName avec des starters de télémétrie, consultez [Ajout de propriétés : ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Résolution de problèmes

Si la cartographie d’application ne fonctionne pas comme prévu, essayez ces étapes :

1. Veillez à utiliser un SDK officiellement pris en charge. Les SDK non pris en charge ou de la communauté ne prennent pas forcément en charge la corrélation.

    Reportez-vous à cet [article](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) pour obtenir la liste des SDK pris en charge.

2. Mettez à niveau tous les composants avec la dernière version du SDK.

3. Si vous utilisez Azure Functions avec C#, effectuez une mise à niveau vers [Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Vérifiez que [cloud_RoleName](app-map.md#Set-cloud-RoleName) est correctement configuré.

5. S’il vous manque une dépendance, vérifiez qu’elle figure dans la liste des [dépendances collectées automatiquement](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Sinon, vous pouvez toujours la suivre manuellement avec un [suivi d’appel de dépendance](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="portal-feedback"></a>Commentaires du portail
Pour envoyer des commentaires, utilisez l’option de commentaires.

![Image MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation de la corrélation](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
