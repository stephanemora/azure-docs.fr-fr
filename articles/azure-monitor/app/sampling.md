---
title: Échantillonnage de données de télémétrie dans Azure Application Insights | Microsoft Docs
description: Comment maintenir sous contrôle le volume de télémétrie.
services: application-insights
documentationcenter: windows
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.author: cithomas
ms.openlocfilehash: d88de2bf660165022b39aaa0321ff5c62ea81cd3
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231851"
---
# <a name="sampling-in-application-insights"></a>Échantillonnage dans Application Insights

L’échantillonnage est une fonctionnalité dans [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Il s’agit de la méthode recommandée pour réduire le trafic et le stockage des données de télémétrie, tout en conservant une analyse statistiquement correcte des données d’application. Le filtre sélectionne les éléments associés afin que vous puissiez naviguer entre les éléments lorsque vous effectuez un diagnostic.
Lors de la mesure est présentées dans le portail, ils sont renormalisés pour prendre en compte l’échantillonnage. Cette opération réduit aucun effet sur les statistiques.

L’échantillonnage réduit les coûts du trafic et des données, et vous aide à éviter les limitations.

## <a name="in-brief"></a>En bref :

* L’échantillonnage conserve 1 enregistrement sur *n* et ignore le reste. Par exemple, il peut conserver un dans cinq événements, un taux d’échantillonnage de 20 %. 
* Échantillonnage adaptatif est activé par défaut dans la dernière version d’ASP.NET et les Kits de développement logiciel ASP.NET Core (SDK).
* Vous pouvez également définir l’échantillonnage manuellement. Cela peut être configuré dans le portail sur la *page utilisation et estimation des coûts*, dans le Kit de développement ASP.NET dans le fichier ApplicationInsights.config dans le Kit de développement logiciel ASP.NET Core via le code ou dans le SDK Java dans le fichier ApplicationInsights.xml de fichiers.
* Si vous consignez des événements personnalisés et doivent s’assurer qu’un jeu d’événements est conservé ou ignoré conjointement, les événements doivent avoir la même valeur OperationId.
* Le diviseur d’échantillonnage *n* est signalé dans chaque enregistrement de la propriété `itemCount`, qui dans la recherche s’affiche sous le nom convivial « nombre de demandes » ou « nombre d’événements ». `itemCount==1`Lorsque l’échantillonnage n’est pas dans l’opération.
* Si vous écrivez des requêtes Analytics, vous devez [tenir compte de l’échantillonnage](../../azure-monitor/log-query/aggregations.md). En particulier, au lieu de compter simplement les enregistrements, vous devez utiliser `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Types d’échantillonnage

Il existe trois autres méthodes d’échantillonnage :

* **L’échantillonnage ADAPTATIF** ajuste automatiquement le volume de données de télémétrie envoyées à partir du SDK dans votre application ASP.NET/ASP.NET Core. Il s’agit de l’échantillonnage par défaut à partir d’ASP.NET Web SDK v 2.0.0-beta3 et versions ultérieures et Microsoft.ApplicationInsights.AspNetCore SDK v 2.2.0-beta1 et versions ultérieures.  L’échantillonnage adaptatif n’est disponible que pour la télémétrie ASP.NET côté serveur.

* **Échantillonnage à débit fixe** réduit le volume de données de télémétrie envoyées à partir de votre serveur ASP.NET ou ASP.NET Core ou Java et de navigateurs de vos utilisateurs. Vous définissez le débit. Le client et le serveur synchronisent leur échantillonnage de façon à ce que vous puissiez naviguer entre les demandes et les affichages de pages associés dans Search.

* **L’échantillonnage d’ingestion** fonctionne dans le portail Azure. Il ignore certaines données de télémétrie provenant de votre application, à une fréquence d’échantillonnage que vous définissez. Le trafic des données de télémétrie reçu de votre application n’est pas réduit, mais cela vous permet de respecter votre quota mensuel. Le principal avantage de l’échantillonnage d’ingestion est que vous pouvez définir le taux d’échantillonnage sans avoir à redéployer votre application. L’échantillonnage d’ingestion fonctionne uniformément pour tous les clients et serveurs.

Si un échantillonnage de débit adaptatif ou fixe est en cours d’utilisation, l’échantillonnage d’ingestion est désactivé.


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>Échantillonnage adaptatif dans vos Applications Web de Core ASP.NET/ASP.NET

L’échantillonnage adaptatif est disponible pour le SDK Application Insights pour ASP.NET v 2.0.0-beta3 et versions ultérieures, Microsoft.ApplicationInsights.AspNetCore SDK v 2.2.0-beta1 et versions ultérieures et est activé par défaut.

L’échantillonnage adaptatif a une incidence sur le volume de données de télémétrie envoyées à partir de votre application de serveur web au point de terminaison de service Application Insights. Le volume est automatiquement ajusté pour conserver au sein d’un débit de trafic maximal spécifié et est contrôlé via le paramètre `MaxTelemetryItemsPerSecond`. Si l’application génère une faible quantité de données de télémétrie, comme lors du débogage ou en raison d’une faible utilisation, les éléments ne sont pas obtenir échantillonnées tant que volume est inférieur à `MaxTelemetryItemsPerSecond`. En tant que volume de données de télémétrie augmente, le taux d’échantillonnage est ajustée afin d’obtenir le volume cible.

Pour atteindre le volume cible, certaines des données de télémétrie générées sont ignorées. Toutefois, comme d’autres types d’échantillonnage, l’algorithme conserve les éléments de télémétrie associés. Par exemple, quand vous inspectez les données de télémétrie dans Search, vous pouvez trouver la demande liée à une exception spécifique.

Les données de mesure telles que le taux de demandes et le taux d’exceptions sont ajustées pour compenser le taux d’échantillonnage, afin qu’elles affichent des valeurs approximativement correctes dans Metrics Explorer.

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Configuration de l’échantillonnage adaptatif pour les Applications ASP.NET

[En savoir plus](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) sur la configuration de l’échantillonnage adaptatif pour les Applications ASP.NET Core. 

Dans [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), vous pouvez ajuster plusieurs paramètres dans le nœud `AdaptiveSamplingTelemetryProcessor`. Les chiffres indiqués correspondent aux valeurs par défaut :

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Taux cible que l’algorithme adaptatif tente d’atteindre **sur chaque hôte du serveur**. Si votre application web s’exécute sur plusieurs hôtes, réduisez cette valeur afin de ne pas dépasser votre débit de trafic cible sur le portail Application Insights.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    L’intervalle auquel le taux actuel de télémétrie est réévalué. L’évaluation est effectuée sous forme de moyenne mobile. Vous souhaiterez peut-être raccourcir cet intervalle si vos données de télémétrie sont soumises à des pics soudains.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    En cas de modification du pourcentage d’échantillonnage, délai avant que le pourcentage d’échantillonnage puisse de nouveau être réduit pour capturer moins de données.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    En cas de modification du pourcentage d’échantillonnage, délai avant que le pourcentage d’échantillonnage puisse de nouveau être augmenté pour capturer plus de données.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Lors des variations du pourcentage d’échantillonnage, valeur minimale autorisée.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Lors des variations du pourcentage d’échantillonnage, valeur maximale autorisée.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Lors du calcul de la moyenne mobile, poids affecté à la valeur la plus récente. Utilisez une valeur inférieure ou égale à 1. Plus les valeurs sont petites, moins l’algorithme est réactif en cas de modifications brusques.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Valeur affectée lorsque l’application vient de démarrer. Ne réduisez pas valeur pendant que vous déboguez.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Une liste délimitée par des points-virgules des types que vous ne souhaitez pas voir échantillonnés. Les types reconnus sont : Dependency, Event, Exception, PageView, Request et Trace. Toutes les instances des types spécifiés sont transmises ; les types qui ne sont pas spécifiés sont échantillonnés.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Une liste délimitée par des points-virgules des types que vous souhaitez échantillonner. Les types reconnus sont : Dependency, Event, Exception, PageView, Request et Trace. Les types spécifiés sont échantillonnés ; toutes les instances des autres types sont toujours transmises.


**Pour désactiver** ADAPTATIF d’échantillonnage, de supprimer l’ou les nœuds AdaptiveSamplingTelemetryProcessor d’applicationinsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Solution alternative : configurer l’échantillonnage adaptatif dans le code

Au lieu de définir le paramètre d’échantillonnage dans le fichier .config, vous pouvez définir ces valeurs par programmation.

1. Supprimez tout le `AdaptiveSamplingTelemetryProcessor` nœud (s) à partir du fichier .config.
2. L’extrait de code suivant permet de configurer l’échantillonnage adaptatif.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([En savoir plus sur les processeurs de télémétrie](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Vous pouvez également ajuster le taux d’échantillonnage pour chaque type de données de télémétrie individuellement, ou pourrez même exclure certains types échantillonnées à tous les. 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Configuration de l’échantillonnage adaptatif pour les Applications ASP.NET Core.

Il existe aucune `ApplicationInsights.Config` pour les Applications ASP.NET Core, par conséquent, chaque configuration est effectuée par le biais de code.
L’échantillonnage adaptatif est activé par défaut pour toutes les applications ASP.NET Core. Vous pouvez désactiver ou personnaliser le comportement d’échantillonnage.

### <a name="turning-off-adaptive-sampling"></a>Désactivation de l’échantillonnage adaptatif

La fonctionnalité d’échantillonnage par défaut peut être désactivée lors de l’ajout du service Application Insights, dans la méthode ```ConfigureServices```, à l’aide ```ApplicationInsightsServiceOptions``` au sein de la `Startup.cs` fichier :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
    //...
}
```

Le code ci-dessus va désactiver la fonctionnalité d’échantillonnage. Pour ajouter un échantillonnage avec davantage d’options de personnalisation, procédez comme suit.

### <a name="configure-sampling-settings"></a>Configurer les paramètres d’échantillonnage

Pour personnaliser le comportement de l’échantillonnage, utilisez des méthodes d’extension de ```TelemetryProcessorChainBuilder```, comme indiqué ci-dessous.

> [!IMPORTANT]
> Si vous utilisez cette méthode pour configurer l’échantillonnage, veillez à utiliser les paramètres aiOptions.EnableAdaptiveSampling = false; de AddApplicationInsightsTelemetry().

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

    var builder = configuration .TelemetryProcessorChainBuilder;
    // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
    // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    builder.Build();

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    // ...
}

```

**Si l’utilisation de la méthode ci-dessus pour configurer l’échantillonnage, veillez à utiliser ```aiOptions.EnableAdaptiveSampling = false;``` paramètres avec AddApplicationInsightsTelemetry().**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-and-java-websites"></a>Échantillonnage à débit fixe pour les sites Web ASP.NET, ASP.NET Core et Java

L’échantillonnage à débit fixe réduit le trafic envoyé depuis votre serveur web et les navigateurs web. À la différence de l’échantillonnage adaptatif, il réduit les données de télémétrie à un débit fixe choisi par vos soins. En outre, il synchronise l’échantillonnage client et serveur afin que les éléments associés soient conservés ; par exemple, quand vous examinez un affichage de page dans Recherche, vous pouvez trouver sa demande associée.

Comme les autres techniques d’échantillonnage, il conserve également les éléments associés. Pour chaque événement de requête HTTP, la requête et ses événements associés sont ignorés ou transmis ensemble.

Dans Metrics Explorer, les taux tels que le nombre de demandes et d’exceptions sont multipliés par un facteur pour compenser le taux d’échantillonnage, afin qu’ils soient approximativement corrects.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Configurer l’échantillonnage à débit fixe avec ASP.NET

1. **Désactivez l’échantillonnage adaptatif** : dans [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), supprimez ou commentez le nœud `AdaptiveSamplingTelemetryProcessor`.

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **Activez le module d’échantillonnage à débit fixe.** Ajoutez cet extrait de code à [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Solution alternative : activez l’échantillonnage à taux fixe dans le code serveur
    
    Au lieu de définir le paramètre d’échantillonnage dans le fichier .config, vous pouvez définir ces valeurs par programmation. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```
([En savoir plus sur les processeurs de télémétrie](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>Configurer l’échantillonnage à taux fixe dans ASP.NET Core

1. **Désactivez l’échantillonnage adaptatif** :  Modifications peuvent être apportées dans la méthode ```ConfigureServices```, à l’aide ```ApplicationInsightsServiceOptions```:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    //...
    }
    ```

2. **Activez le module d’échantillonnage à débit fixe.** Modifications peuvent être apportées dans la méthode ```Configure``` comme illustré ci-dessous extrait de code :

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.TelemetryProcessorChainBuilder;
        // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
        // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }

    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>Configurer l’échantillonnage à débit fixe avec Java ###

1. Téléchargez et configurez votre application web avec la dernière version du [Kit SDK Java Application Insights](../../azure-monitor/app/java-get-started.md).

2. **Activez le module d’échantillonnage à débit fixe** en ajoutant l’extrait de code suivant au fichier ApplicationInsights.xml.

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        <TelemetryProcessors/>
    ```

3. Vous pouvez inclure ou exclure certains types de données de télémétrie de l’échantillonnage en utilisant les balises suivantes au sein de la balise Processor « FixedRateSamplingTelemetryProcessor » :
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

Les types de données de télémétrie qui peuvent être inclus ou exclus de l’échantillonnage sont : Dépendance, Event, Exception, PageView, demande et Trace.

> [!NOTE]
> Pour le pourcentage d’échantillonnage, choisissez un pourcentage proche de 100/N, où N est un nombre entier.  L’échantillonnage ne prend actuellement en charge aucune autre valeur.
> 
> 

<a name="other-web-pages"></a>



## <a name="ingestion-sampling"></a>échantillonnage d’ingestion

Cette forme d’échantillonnage fonctionne au niveau où les données de télémétrie issues de votre serveur web, des navigateurs et des appareils atteignent le point de terminaison du service Application Insights. Bien qu’elle ne réduise pas le trafic des données de télémétrie envoyées à partir de votre application, elle réduit la quantité traitée et conservée (et facturée) par Application Insights.

Utilisez ce type d’échantillonnage si votre application dépasse souvent son quota mensuel et que vous ne pouvez recourir à aucun type d’échantillonnage basé sur le Kit de développement logiciel (SDK). 

Définissez le taux d’échantillonnage dans la page Utilisation et estimation des coûts :

![Dans le panneau Vue d’ensemble de l’application, cliquez sur Paramètres, Quota + tarification, Échantillons conservés, sélectionnez un taux d’échantillonnage, puis cliquez sur Mettre à jour.](./media/sampling/04.png)

Comme d’autres types d’échantillonnage, l’algorithme conserve les éléments de télémétrie associés. Par exemple, quand vous inspectez les données de télémétrie dans Search, vous pouvez trouver la demande liée à une exception spécifique. Les données de mesure telles que les taux de demandes et le taux d’exceptions sont correctement conservées.

Les points de données ignorés par l’échantillonnage ne sont disponibles dans aucune fonctionnalité Application Insights, par exemple l’ [exportation continue](../../azure-monitor/app/export-telemetry.md).

L’échantillonnage d’ingestion ne fonctionne pas pendant qu’une opération d’échantillonnage adaptatif ou taux fixe basé sur un Kit de développement logiciel (SDK) est en cours d’exécution. L’échantillonnage adaptatif est activé par défaut lorsque ASP.NET/ASP.NET Core SDK est activé dans Visual Studio ou activé dans les extensions d’application Web Azure ou à l’aide de Status Monitor, et l’échantillonnage d’ingestion est désactivé. Si le taux d’échantillonnage dans le Kit de développement est inférieure à 100 % (ex.) les éléments sont échantillonnées) alors le taux d’échantillonnage d’ingestion que vous définissez est ignoré.

> [!WARNING]
> La valeur affichée sur la vignette indique la valeur que vous définissez pour l’échantillonnage d’ingestion. Il ne représente pas le taux d’échantillonnage réel si l’échantillonnage du kit de développement logiciel (SDK) est effectué.
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>Échantillonnage pour les pages web avec JavaScript
Vous pouvez configurer des pages Web pour l’échantillonnage à débit fixe à partir de n’importe quel serveur. 

Quand vous [configurez les pages web pour Application Insights](../../azure-monitor/app/javascript.md), modifiez l’extrait de code JavaScript que vous obtenez à partir du portail Application Insights. (Dans les applications ASP.NET, l’extrait de code passe généralement dans _Layout.cshtml.)  Insérez une ligne de type `samplingPercentage: 10,` avant la clé d’instrumentation :

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

Pour le pourcentage d’échantillonnage, choisissez un pourcentage proche de 100/N, où N est un nombre entier.  L’échantillonnage ne prend actuellement en charge aucune autre valeur.

Si vous avez activé l’échantillonnage à débit fixe sur le serveur, les clients et le serveur se synchronisent de façon à ce que, dans Recherche, vous puissiez naviguer entre les demandes et les affichages de pages associés.

## <a name="when-to-use-sampling"></a>Quand utiliser l’échantillonnage ?

Échantillonnage adaptatif est automatiquement activé dans les dernières .NET et les kits SDK .NET Core. Quelle que soit la version du SDK que vous utilisez, vous pouvez activer l’échantillonnage d’ingestion pour autoriser Application Insights à échantillonner les données collectées.

Par défaut, aucun échantillonnage n’est activé dans le Kit SDK Java, qui ne gère à l’heure actuelle que l’échantillonnage à débit fixe. L’échantillonnage adaptatif n’est pas pris en charge dans le Kit SDK Java.

En général, pour la plupart des applications de taille petite à moyenne, vous n’avez pas besoin d’échantillonnage. Pour obtenir les informations de diagnostic les plus utiles et les statistiques les plus précises, le mieux est de collecter les données sur toutes vos activités utilisateur. 

Les principaux avantages de l’échantillonnage sont les suivants :

* Si le service Application Insights supprime (« limite ») des points de données lorsque votre application envoie un taux de télémétrie très élevé dans un court laps de temps. 
* Pour respecter le [quota](../../azure-monitor/app/pricing.md) de points de données pour votre niveau tarifaire. 
* Pour réduire le trafic réseau généré par la collecte de données de télémétrie. 

### <a name="which-type-of-sampling-should-i-use"></a>Quel type d’échantillonnage dois-je utiliser ?

**Utiliser l’échantillonnage d’ingestion dans les situations suivantes :**

* Vous dépassez souvent votre quota mensuel de données de télémétrie.
* Vous utilisez une version du Kit SDK qui ne prend pas en charge l’échantillonnage, par exemple, une version d’ASP.NET antérieure à la version 2.
* Vous obtenez trop télémétrie à partir de navigateurs web de vos utilisateurs.

**Utilisez l’échantillonnage à débit fixe si :**

* Vous utilisez la version 2.0.0 ou une version ultérieure du Kit SDK Application Insights pour les services web ASP.NET ou la version 2.0.1 ou une version ultérieure du Kit SDK Java, et
* Vous voulez disposer d’un échantillonnage synchronisé entre le client et le serveur afin de pouvoir naviguer entre les événements connexes sur le client et le serveur (tels que les affichages de pages et les requêtes http) lorsque vous étudiez des événements dans [Search](../../azure-monitor/app/diagnostic-search.md).
* Vous êtes sûr du pourcentage d’échantillonnage approprié pour votre application. Il doit être suffisamment élevé pour obtenir des mesures précises, mais inférieur au pourcentage engendrant le dépassement de votre quota de tarification et des limites de limitation. 

**Utilisez l’échantillonnage adaptatif :**

Si les conditions d’utilisation des autres formes d’échantillonnage ne s’appliquent pas, nous vous recommandons l’échantillonnage adaptatif. Ce paramètre est activé par défaut dans le Kit de développement logiciel ASP.NET/ASP.NET minimale. Elle ne réduit le trafic que si un débit minimal donné est atteint ; ainsi, les sites peu utilisés ne sont pas affectés.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Comment savoir si l’échantillonnage est utilisé ?

Pour découvrir le taux d’échantillonnage réel, indépendamment de l’endroit où il a été appliqué, utilisez une [requête Analytics](../../azure-monitor/app/analytics.md) telle que celle-ci :

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Si RetainedPercentage pour n’importe quel type est inférieure à 100, cet élément est échantillonné.

**Application Insights n’échantillonne pas de session, mesures et compteurs de performances des types de données de télémétrie dans n’importe quel techniques d’échantillonnage décrites ci-dessus. Ces types sont toujours exclus de l’échantillonnage comme la réduction de la précision peut être souhaitable pour ces types de données de télémétrie**

## <a name="how-does-sampling-work"></a>Comment fonctionne l’échantillonnage ?

La fonctionnalité d’échantillonnage à débit fixe du Kit SDK ASP.NET à partir de la version 2.0.0 et du Kit SDK Java à partir de la version 2.0.1. L’échantillonnage adaptatif est une fonctionnalité du Kit SDK dans ASP.NET version 2.0.0 et ultérieures. L’échantillonnage d’ingestion est une fonctionnalité du service Application Insights et peut fonctionner si le Kit de développement logiciel (SDK) n’effectue pas d’échantillonnage.

L’algorithme d’échantillonnage sélectionne les éléments de télémétrie à supprimer et ceux à conserver (qu’ils se trouvent dans le Kit de développement logiciel ou le service Application Insights). La décision d’échantillonnage est fondée sur plusieurs règles visant à préserver l’intégrité de tous les points de données reliés entre eux, en conservant dans Application Insights une expérience de diagnostic qui demeure exploitable et fiable, même avec un jeu de données réduit. En cas d’échec d’une requête, par exemple, si votre application envoie d’autres éléments de télémétrie (tels que les exceptions et les traces enregistrées à partir de cette requête), l’échantillonnage ne fractionnera ni cette requête ni les autres éléments de télémétrie. Il les conservera ou supprimera ensemble. C’est pourquoi, lorsque vous examinez les détails de la requête dans Application Insights, la requête s’affichera toujours avec les éléments de télémétrie qui lui sont associés. 

La décision d’échantillonnage repose sur l’ID d’opération de la demande, ce qui signifie que tous les éléments de télémétrie appartenant à une opération particulière est conservé ou supprimé. Pour les éléments de télémétrie qui n’ont pas d’opération ID ensemble (par exemple les éléments de télémétrie signalés à partir de threads asynchrones sans contexte http) échantillonnage capturera simplement un pourcentage d’éléments de télémétrie de chaque type. Avant la 2.5.0-beta2 du kit SDK .NET et 2.2.0-beta3 du Kit de développement ASP.NET Core, la décision d’échantillonnage était basée sur le hachage de l’ID d’utilisateur pour les applications qui définissent le « utilisateur » (autrement dit, les applications web plus typique). Pour les types d’applications qui n’a pas de définir les utilisateurs (par exemple, les services web), la décision d’échantillonnage était basée sur l’ID d’opération de la demande.

Lorsque les données de télémétrie vous sont restituées, le service Application Insights ajuste les mesures en fonction du même pourcentage d’échantillonnage que celui utilisé au moment de la collecte, de manière à compenser les points de données manquants. Par conséquent, lorsqu’ils consultent les données de télémétrie dans Application Insights, les utilisateurs constatent des approximations correctes d’un point de vue statistique et très proches des chiffres réels.

La précision de l’approximation dépend en grande partie du pourcentage d’échantillonnage configuré. Elle est également plus précise pour les applications qui gèrent un grand nombre de requêtes globalement similaires générées par un grand nombre d’utilisateurs. En revanche, pour les applications qui ne sont pas soumises à une charge importante, l’échantillonnage n’est pas nécessaire, car ces applications peuvent généralement envoyer toutes leurs données de télémétrie sans dépasser leur quota ni entraîner de perte de données liée à une limitation de la bande passante. 

> [!WARNING]
> Application Insights n’échantillonne pas les exemples de données de télémétrie de type métriques et sessions. La précision réduite n’est pas du tout souhaitable pour ces types de données de télémétrie.
> 

### <a name="adaptive-sampling"></a>échantillonnage adaptatif

L’échantillonnage adaptatif ajoute un composant qui contrôle la vitesse de transmission actuelle à partir du Kit de développement logiciel (SDK) et ajuste le pourcentage d’échantillonnage afin de le maintenir en dessous du taux maximal cible. L’ajustement est recalculé à intervalles réguliers en fonction d’une moyenne mobile de la vitesse de transmission sortante.

## <a name="sampling-and-the-javascript-sdk"></a>Échantillonnage et kit de développement logiciel JavaScript

Le kit de développement logiciel (SDK) côté client (JavaScript) participe à l’échantillonnage à taux fixe parallèlement au kit de développement logiciel (SDK) côté serveur. Les pages instrumentées envoient uniquement les données de télémétrie côté client provenant des utilisateurs que le SDK côté serveur a décidé d’échantillonner. Cette logique est conçue pour préserver l’intégrité de la session utilisateur côté client et côté serveur. En partant de n’importe quel élément de télémétrie dans Application Insights, vous retrouverez donc tous les autres éléments de télémétrie associés à l’utilisateur ou à la session en question. 

*Mes données de télémétrie côté client et côté serveur n’affichent pas les échantillons coordonnés que vous décrivez ci-dessus.*

* Vérifiez que vous avez activé l’échantillonnage à débit fixe à la fois sur le serveur et sur le client.
* Assurez-vous que vous utilisez bien le kit de développement logiciel version 2.0 ou ultérieure.
* Vérifiez que vous définissez le même pourcentage d’échantillonnage dans le client et dans le serveur.

## <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)

*Qu’est le comportement d’échantillonnage par défaut dans ASP.NET et ASP.NET Core SDK ?*

* Si vous utilisez l’une des dernières versions du kit SDK ci-dessus, l’échantillonnage adaptatif est activé par défaut avec cinq éléments de télémétrie par seconde.
  Il existe 2 AdaptiveSamplingTelemetryProcessors ajoutées par défaut, un contient le type d’événement dans l’échantillonnage et l’autre exclut un type d’événement d’échantillonnage. Cette configuration signifie que le SDK tente de limiter les éléments de télémétrie à cinq éléments de télémétrie de types d’événements et les cinq éléments de télémétrie de tous les autres types combinées, ce qui garantit que les événements sont échantillonnées séparément des autres types de données de télémétrie. Les événements sont généralement utilisés pour la télémétrie métier et ne doivent probablement pas être affectées par les volumes de télémétrie de diagnostic.
  
  Voici le fichier ApplicationInsights.Config par défaut généré. Comme décrit, il existe deux nœuds AdaptiveSamplingTelemetryProcessor distincts ont été ajoutés, un à l’exclusion de types d’événements et l’autre son inclusion. Dans ASP.NET Core, exacte même comportement par défaut est activée dans le code. Pour modifier ce comportement par défaut, utilisez les exemples dans la section précédente du document.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*Données de télémétrie peuvent être échantillonnées plusieurs fois ?*

* Non. SamplingTelemetryProcessors ignorer les éléments d’échantillonnage des considérations si l’élément est a déjà été échantillonnée. Vaut également pour l’échantillonnage d’Ingestion en tant que, qui ne s’appliquent pas d’échantillonnage à ces éléments échantillonnés déjà dans le Kit de développement. »

*Pourquoi l’échantillonnage ne permet-il pas simplement de « collecter X % de chaque type de télémétrie » ?*

* Bien que cette approche d’échantillonnage pouvait fournir un haut niveau de précision des approximations métriques, il ne fonctionneraient pas la possibilité de mettre en corrélation les données de diagnostic par utilisateur, session et la demande, ce qui est essentiel pour les diagnostics. L’échantillonnage est donc plus efficace lorsqu’il revient à « collecter tous les éléments de télémétrie pour X % des utilisateurs de l’application » ou à « collecter tous les éléments de télémétrie pour X % des requêtes de l’application ». Pour les éléments de télémétrie que ne pas associés aux requêtes (par exemple, le traitement asynchrone en arrière-plan), la procédure de secours doit « collecter X % de tous les éléments pour chaque type de données de télémétrie. » 

*Le pourcentage d’échantillonnage peut-il évoluer au fil du temps ?*

* Oui, l’échantillonnage adaptatif modifie progressivement le pourcentage d’échantillonnage en fonction du volume de données de télémétrie constaté.

*Si j’utilise l’échantillonnage à débit fixe, comment déterminer le pourcentage d’échantillonnage optimal pour mon application ?*

* Pour cela, vous pouvez commencer par l’échantillonnage adaptatif, identifier le taux obtenu (voir la question précédente) et passer à l’échantillonnage à taux fixe en utilisant ce taux. 
  
    Autrement, vous devez procéder par tâtonnements. Analysez votre utilisation actuelle des données de télémétrie dans Application Insights, observez les limitations qui s’appliquent, puis estimez le volume de données de télémétrie collectées. Ces trois entrées, combinées au niveau tarifaire sélectionné, vous indiquent dans quelle mesure vous devrez réduire le volume de données de télémétrie collectées. Toutefois, une augmentation du nombre d’utilisateurs ou toute autre modification au niveau du volume des données de télémétrie peut invalider votre estimation.

*Que se passe-t-il si je configure le pourcentage d’échantillonnage à un niveau trop faible ?*

* Un pourcentage d’échantillonnage excessivement faible (échantillonnage trop agressif) a pour effet de réduire la précision des approximations lorsqu’Application Insights tente de compenser la visualisation des données pour tenir compte de la réduction du volume de données. Il peut également affecter l’expérience de diagnostic puisque l’échantillonnage peut inclure certaines requêtes rarement sujettes à des problèmes d’échec ou de ralentissement.

*Que se passe-t-il si je configure un pourcentage d’échantillonnage trop élevé ?*

* Si vous configurez un pourcentage d’échantillonnage trop élevé (c’est-à-dire pas assez agressif), le volume de données de télémétrie collectées n’est pas suffisamment réduit. Vous risquez de perdre des données de télémétrie sous l’effet de la limitation de bande passante et de supporter des coûts plus élevés que prévu pour l’utilisation d’Application Insights en raison des frais de dépassement.

*Sur quelles plates-formes puis-je utiliser l’échantillonnage ?*

* L’échantillonnage d’ingestion peut se produire automatiquement pour toute télémétrie au-dessus d’un certain volume, si le Kit de développement logiciel (SDK)n’effectue pas d’échantillonnage. Cette configuration est acceptées, par exemple, si vous utilisez une version antérieure du kit SDK ASP.NET ou une version précédente de Java SDK(1.0.10 or before).
* Si vous utilisez des versions SDK ASP.NET version 2.0.0 et versions ultérieures ou ASP.NET CORE SDK version 2.2.0 et versions ultérieures (hébergé dans Azure ou sur votre propre serveur), vous obtenez échantillonnage adaptatif par défaut, mais vous pouvez basculer à taux fixe, comme décrit ci-dessus. Avec l’échantillonnage à taux fixe, le Kit de développement logiciel (SDK) du navigateur se synchronise automatiquement pour échantillonner les événements connexes. 
* Si vous utilisez le Kit de développement logiciel Java version 2.0.1 ou ultérieur, vous pouvez configurer applicationinsights.XML de façon à activer sur l’échantillonnage à débit fixe. L’échantillonnage est désactivé par défaut. Avec l’échantillonnage à taux fixe, le Kit de développement logiciel (SDK) du navigateur se synchronise automatiquement pour échantillonner les événements connexes.

*Je souhaite que certains événements rares soient toujours affichés. Comment faire en sorte qu’ils soient disponibles hors du module d’échantillonnage ?*

* La meilleure façon d’y parvenir consiste à écrire un personnalisé [TelemetryProcessor](../../azure-monitor/app/api-filtering-sampling.md#filtering), qui affecte le `SamplingPercentage` à 100 sur l’élément de télémétrie souhaitées retenue, comme indiqué ci-dessous. Cela garantit que toutes les techniques d’échantillonnage ignore cet élément à partir de toutes les considérations d’échantillonnage.

```csharp
    if(somecondition)
    {
        ((ISupportSampling)item).SamplingPercentage = 100;
    }
```

## <a name="next-steps"></a>Étapes suivantes

* [filtrage](../../azure-monitor/app/api-filtering-sampling.md) peut fournir un contrôle plus strict de ce que le Kit de développement logiciel (SDK) envoie.