---
title: Flux de métriques temps réel avec des métriques et des diagnostics personnalisés dans Azure Application Insights | Microsoft Docs
description: Surveillez votre application web en temps réel avec des métriques personnalisées, et diagnostiquez les problèmes avec un flux temps réel des échecs, des traces et des événements.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/28/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: f369eb6241a8eb3d44a0a38e243c533da47103e1
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104609"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Flux de métriques temps réel : Surveiller et diagnostiquer avec une latence de 1 seconde

Sondez le cœur de votre application web dynamique en production en utilisant les flux de métriques temps réel depuis [Application Insights](../../azure-monitor/app/app-insights-overview.md). Sélectionnez et filtrez les métriques et les compteurs de performances à surveiller en temps réel, sans aucune perturbation de votre service. Inspectez les traces de pile à partir d’échantillons de demandes en échec et d’exceptions. En combinaison avec le [profileur](../../azure-monitor/app/profiler.md), le [débogueur d’instantané](../../azure-monitor/app/snapshot-debugger.md) et les [tests de performances](../../azure-monitor/app/monitor-web-app-availability.md#performance-tests), les flux de métriques temps réel constituent un outil de diagnostic puissant et non invasif pour votre site web dynamique.

Avec les flux de métriques temps réel, vous pouvez :

* Valider un correctif lors de sa publication, en observant les performances et les nombres d’échecs.
* Observer l’effet des tests de charge et diagnostiquer les problèmes de façon dynamique. 
* Vous concentrer sur des sessions de tests particulières ou filtrer les problèmes connus, en sélectionnant et en filtrant les mesures que vous voulez observer.
* Obtenir les traces des exceptions quand elles se produisent.
* Faites des essais avec les filtres pour rechercher les indicateurs de performance clés les plus pertinents.
* Surveiller en temps réel des compteurs de performances Windows.
* Identifiez plus facilement un serveur qui rencontre des problèmes et filtrez tous les indicateurs de performance clés/flux en temps réel sur ce serveur uniquement.

[![Vidéo sur les flux de métriques temps réel](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Les métriques temps réel sont actuellement prises en charge pour les applications ASP.NET, ASP.NET Core, Azure Functions et Java.

## <a name="get-started"></a>Prise en main

1. Si vous n’avez pas encore [installé Application Insights](../../azure-monitor/azure-monitor-app-hub.md) dans votre application web, faites-le maintenant.
2. En plus des packages Application Insights standard, [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) est nécessaire pour activer le flux de métriques temps réel.
3. **Mettez à jour vers la dernière version** du package Application Insights. Dans Visual Studio, cliquez avec le bouton droit sur votre projet et choisissez **Gérer les packages NuGet**. Ouvrez l’onglet **Mises à jour** et sélectionnez tous les packages Microsoft.ApplicationInsights.*.

    Redéployez votre application.

3. Dans le [portail Azure](https://portal.azure.com), ouvrez la ressource Application Insights pour votre application puis ouvrez Flux temps réel.

4. [Sécurisez le canal de contrôle](#secure-the-control-channel) si vous utilisez des données sensibles comme des noms de clients dans vos filtres.

### <a name="no-data-check-your-server-firewall"></a>Pas de données ? Vérifier le pare-feu de votre serveur

Vérifiez que les [ports sortants pour le flux de métriques temps réel](../../azure-monitor/app/ip-addresses.md#outgoing-ports) sont ouverts dans le pare-feu de vos serveurs. 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>En quoi les flux de métriques temps réel diffèrent-ils de Metrics Explorer et d’Analytique ?

| |Flux temps réel | Metrics Explorer et Analytique |
|---|---|---|
|Latence|Données affichées en une seconde|Agrégé sur plusieurs minutes|
|Pas de conservation|Les données persistent tant qu’elles se trouvent sur le graphique puis elles sont abandonnées.|[Données conservées pendant 90 jours](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|À la demande|Les données sont diffusées dès que vous ouvrez les métriques temps réel|Les données sont envoyées quand le SDK est installé et activé|
|Gratuit|Pas de facturation pour les données du flux temps réel|Soumis à [tarification](../../azure-monitor/app/pricing.md)
|échantillonnage|Tous les compteurs et métriques sélectionnés sont transmis. Les échecs et les traces de pile sont échantillonnés. Les processeurs de télémétrie ne sont pas appliqués.|Les événements peuvent être [échantillonnés](../../azure-monitor/app/api-filtering-sampling.md)|
|Canal de contrôle|Les signaux de contrôle de filtre sont envoyés au SDK. Nous vous recommandons de [sécuriser ce canal](#secure-channel).|La communication est unidirectionnelle vers le portail|


## <a name="select-and-filter-your-metrics"></a>Sélectionner et filtrer vos métriques

(Disponible avec ASP.NET, ASP.NET Core et Azure Functions (v2).)

Vous pouvez surveiller un KPI personnalisé en temps réel en appliquant des filtres arbitraires sur les données de télémétrie Application Insights à partir du portail. Cliquez sur la commande de filtre qui s’affiche lorsque vous passez la souris sur un graphique. Le graphique suivant indique un KPI de nombre de demandes personnalisé avec des filtres sur les attributs d’URL et de durée. Vérifiez vos filtres grâce à la section Vue d’ensemble du flux qui affiche un flux en temps réel de télémétrie correspondant aux critères que vous avez spécifiés. 

![Indicateur de performance clé (KPI) de demande personnalisé](./media/live-stream/live-stream-filteredMetric.png)

Vous pouvez surveiller une valeur autre qu’un nombre. Les options varient selon le type de flux, qui peut être n’importe quelle télémétrie Application Insights : demandes, dépendances, exceptions, suivis, événements ou métriques. Il peut s’agir d’une [mesure personnalisée](../../azure-monitor/app/api-custom-events-metrics.md#properties) :

![Options de valeur](./media/live-stream/live-stream-valueoptions.png)

En plus des données de télémétrie Application Insights, vous pouvez également surveiller n’importe quel compteur de performances Windows en le sélectionnant dans les options de flux et en fournissant le nom du compteur de performances.

Les métriques temps réel sont agrégées à deux endroits : localement sur chaque serveur, puis sur tous les serveurs. Vous pouvez modifier la valeur par défaut en sélectionnant d’autres options dans les listes déroulantes respectives.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Exemple de télémétrie : Événements de diagnostic Azure personnalisés et en temps réel
Par défaut, le flux d’événements temps réel présente des exemples de demandes ayant échoué et d’appels de dépendance, d’exceptions, d’événements et de suivis. Cliquez sur l’icône de filtre pour afficher les critères appliqués. 

![Flux temps réel par défaut](./media/live-stream/live-stream-eventsdefault.png)

Comme avec les métriques, vous pouvez spécifier un critère arbitraire sur les types de données de télémétrie Application Insights. Dans cet exemple, nous sélectionnons des échecs de demande, des suivis et des événements spécifiques. Nous sélectionnons également toutes les exceptions et tous les échecs de dépendance.

![Flux temps réel personnalisé](./media/live-stream/live-stream-events.png)

Remarque : pour les critères basés sur un message d’exception, utilisez le message d’exception le plus à l’extérieur. Dans l’exemple précédent, pour éliminer l’exception sans gravité avec le message d’exception interne (après le délimiteur « <-- ») « Client déconnecté ». utilisez un critère de message ne contenant pas « Erreur de lecture du contenu de la demande ».

Consultez les informations détaillées d’un élément dans le flux temps réel en cliquant dessus. Vous pouvez interrompre le flux en cliquant sur **Suspendre**, en faisant simplement défiler ou en cliquant sur un élément. Le flux temps réel reprend lorsque vous refaites défiler vers le haut, ou en cliquant sur le compteur d’éléments collectés alors qu’il était suspendu.

![Échecs dynamiques échantillonnés](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrer par instance de serveur

Si vous voulez surveiller une instance de rôle serveur spécifique, vous pouvez appliquer un filtre par serveur.

![Échecs dynamiques échantillonnés](./media/live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Configuration requise du Kit de développement logiciel (SDK)
Le flux de métriques temps réel personnalisé est disponible avec la version 2.4.0-beta2 ou plus récente du [Kit de développement logiciel (SDK) Application Insights pour le web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). N’oubliez pas de sélectionner l’option « Inclure la version préliminaire » dans le gestionnaire de package NuGet.

## <a name="secure-the-control-channel"></a>Sécuriser le canal de contrôle
Les critères de filtres personnalisés que vous spécifiez sont renvoyés au composant de métriques temps réel dans le Kit de développement logiciel (SDK) Application Insights. Les filtres peuvent potentiellement contenir des informations sensibles telles que des ID clients. Vous pouvez sécuriser le canal avec une clé API secrète en plus de la clé d’instrumentation.
### <a name="create-an-api-key"></a>Création d’une clé API

![Créer une clé API](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Ajout d’une clé API à la configuration

### <a name="classic-aspnet"></a>ASP.NET classique

Dans le fichier applicationinsights.config, ajoutez AuthenticationApiKey à QuickPulseTelemetryModule :
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Ou dans le code, définissez-la sur QuickPulseTelemetryModule :

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="azure-function-apps"></a>Applications de fonction Azure

Pour Azure Function Apps (v2), la sécurisation du canal avec une clé API peut se faire avec une variable d’environnement. 

Créez une clé API à partir de votre ressource Application Insights et accédez à **Paramètres d’application** pour votre application de fonction. Sélectionnez **Ajouter un nouveau paramètre** et entrez le nom `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` et une valeur qui correspond à votre clé API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core (nécessite Application Insights ASP.NET Core SDK 2.3.0-beta ou version ultérieure)

Modifiez votre fichier startup.cs comme suit :

Tout d’abord ajouter

``` C#
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Ensuite, dans la méthode ConfigureServices, ajoutez :

``` C#
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Toutefois, si vous connaissez et faites confiance à tous les serveurs connectés, vous pouvez essayer les filtres personnalisés sans le canal authentifié. Cette option est disponible pour six mois. Ce remplacement est nécessaire à chaque nouvelle session, ou lorsqu’un nouveau serveur est en ligne.

![Options d’authentification de métriques temps réel](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Nous vous recommandons vivement de configurer le canal authentifié avant d’entrer des informations potentiellement sensibles comme un ID client dans les critères de filtre.
>

## <a name="generating-a-performance-test-load"></a>Génération d’un test de performances de charge

Si vous voulez observer l’effet d’une augmentation de la charge, utilisez le panneau Test de performances. Il simule des demandes à partir d’un certain nombre d’utilisateurs simultanés. Il peut s’exécuter des « tests manuels » (tests de ping) d’une URL unique, ou il peut exécuter un [test de performances web multi-étape](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) que vous chargez (de la même façon qu’un test de disponibilité).

> [!TIP]
> Après avoir créé le test de performances, ouvrez le test et le panneau Flux temps réel dans des fenêtres distinctes. Vous pouvez voir quand le test de performances en file d’attente démarre, et observer le flux dynamique en même temps.
>


## <a name="troubleshooting"></a>Résolution de problèmes

Pas de données ? Si votre application est dans un réseau protégé : Le flux de métriques temps réel utilise des adresses IP différentes de celles des autres données de télémétrie Application Insights. Assurez-vous que [ces adresses IP](../../azure-monitor/app/ip-addresses.md) sont ouvertes dans votre pare-feu.



## <a name="next-steps"></a>Étapes suivantes
* [Surveillance de l’utilisation avec Application Insights](../../azure-monitor/app/usage-overview.md)
* [Utilisation de Diagnostic Search](../../azure-monitor/app/diagnostic-search.md)
* [Profileur](../../azure-monitor/app/profiler.md)
* [Débogueur de capture instantanée](../../azure-monitor/app/snapshot-debugger.md)
