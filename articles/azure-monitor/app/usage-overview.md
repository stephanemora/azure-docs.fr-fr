---
title: Analyse de l’usage avec Azure Application Insights | Microsoft Docs
description: Comprenez vos utilisateurs et ce qu’ils font avec votre application.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/10/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: ba29688958ee11aa9906a820f7a3d2bf41223743
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798181"
---
# <a name="usage-analysis-with-application-insights"></a>Analyse de l'utilisation avec Application Insights

Quelles sont les fonctionnalités de votre application web ou mobile les plus populaires ? Vos utilisateurs atteignent-ils leurs objectifs avec votre application ? Disparaissent-ils à des stades spécifiques, et reviennent-ils plus tard ?  [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) vous permet d’obtenir des insights utiles sur l’utilisation de votre application. Chaque fois que vous mettez à jour votre application, vous pouvez évaluer son bon fonctionnement pour les utilisateurs. Grâce à ces informations, vous pouvez prendre des décisions basées sur des données sur les cycles de développement suivants.

## <a name="send-telemetry-from-your-app"></a>Envoyer des données de télémétrie à partir de votre application

La meilleure expérience est obtenue en installant Application Insights à la fois dans votre code serveur d’applications et dans vos pages web. Les composants client et serveur de votre application envoient la télémétrie au portail Azure pour analyse.

1. **Code serveur :** installez le module approprié pour votre [ASP.NET](../../azure-monitor/app/asp-net.md), [Azure](../../azure-monitor/app/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/app/nodejs.md) ou une [autre](../../azure-monitor/app/platforms.md) application.

    * *Vous ne voulez pas installer de code serveur ? Vous pouvez simplement [créer une ressource Azure Application Insights](../../azure-monitor/app/create-new-resource.md ).*

2. **Code de page web :** ajoutez le script suivant à votre page web avant la balise ``</head>`` de fermeture. Remplacez la clé d’instrumentation par la valeur appropriée pour votre ressource Application Insights :

   ```javascript
      <script type="text/javascript">
        var appInsights=window.appInsights||function(a){
            function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
        }({
            instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
        });
        
        window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
    </script>
    ```
    Pour en savoir plus les configurations plus complexes pour la supervision des sites web, consultez la [Documentation de référence de l’API du Kit de développement logiciel (SDK) JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

3. **Code de l’application mobile :** utilisez le SDK App Center pour collecter les événements à partir de votre application, puis envoyer des copies de ces événements à Application Insights pour analyse en [suivant ce guide](../../azure-monitor/learn/mobile-center-quickstart.md).

4. **Obtenir la télémétrie :** exécutez votre projet en mode débogage pendant quelques minutes, puis examinez les résultats dans le panneau Vue d’ensemble dans Application Insights.

    Publiez votre application pour surveiller les performances de votre application et découvrir ce que vos utilisateurs font avec votre application.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Inclure l’ID d’utilisateur et l’ID de session dans votre télémétrie
Pour effectuer le suivi des utilisateurs au fil du temps, Application Insights nécessite un moyen de les identifier. L’outil Événements est le seul outil d’utilisation qui ne nécessite pas d’ID d’utilisateur ni d’ID de session.

Démarrer l’envoi des ID d’utilisateur et de session à l’aide de [ce processus](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Explorer des données démographiques et des statistiques de l’utilisation
Découvrez quand des personnes utilisent votre application, les pages qui les intéressent le plus, où vos utilisateurs se trouvent, les navigateurs et les systèmes d’exploitation qu’ils utilisent. 

Les rapports Utilisateurs et sessions filtrent vos données par pages ou événements personnalisés, et les segmentent par propriétés telles que l’emplacement, l’environnement et la page. Vous pouvez également ajouter vos propres filtres.

![Utilisateurs](./media/usage-overview/users.png)  

Aperçu des modèles intéressants appropriés dans le jeu de données.  

* Le rapport **Utilisateurs** compte le nombre d’utilisateurs uniques qui accèdent à vos pages dans les délais que vous avez choisis. Pour les applications web, les utilisateurs sont comptés avec les cookies. Si un utilisateur accède à votre site avec différents navigateurs ou ordinateurs clients, ou efface ses cookies, il est compté plusieurs fois.
* Le rapport **Sessions** compte le nombre de sessions utilisateur qui accèdent à votre site. Une session est une période d’activité d’un utilisateur, qui se termine par une période d’inactivité de plus d’une demi-heure.

[En savoir plus sur les outils Utilisateurs, Sessions et Événements](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Rétention - Combien d’utilisateurs reviennent ?

La rétention vous permet de comprendre la fréquence à laquelle vos utilisateurs reviennent utiliser leur application, en fonction des cohortes d’utilisateurs qui ont effectué une action pendant une période donnée. 

- Comprendre quelles fonctionnalités spécifiques font revenir les utilisateurs plus que d’autres 
- Formuler des hypothèses en fonction des données utilisateur réel 
- Déterminer si la rétention est un problème dans votre produit 

![Rétention](./media/usage-overview/retention.png) 

Les commandes de rétention en haut vous permettent de définir des événements spécifiques et un intervalle de temps pour calculer la rétention. Le graphique au centre fournit une représentation visuelle du pourcentage de rétention globale sur l’intervalle de temps spécifié. Le graphique en bas représente la rétention sur une période de temps donnée. Ce niveau de détail vous permet de comprendre de manière plus approfondie ce que font vos utilisateurs et ce qui les peut amener à revenir.  

[En savoir plus sur l’outil de rétention](usage-retention.md)

## <a name="custom-business-events"></a>Événements personnalisés

Pour bien comprendre ce que font les utilisateurs avec votre application, il est utile d’insérer des lignes de code pour enregistrer des événements personnalisés. Ces événements permettent de suivre toute activité, des actions détaillées des utilisateurs comme un clic sur un bouton, aux événements plus significatifs comme un de faire un achat ou de gagner à un jeu. 

Bien que les pages consultées puissent représenter des événements utiles, cela n’est en général pas le cas. Un utilisateur peut ouvrir une page produit sans acheter le produit. 

Grâce aux événements spécifiques, vous pouvez représenter la progression de vos utilisateurs sur votre site. Vous pouvez connaître leurs préférences sur les différentes options et où ils abandonnent ou rencontrent des difficultés. Grâce à ces informations, vous pouvez prendre des décisions avisées sur les priorités de vos travaux de développement en souffrance.

Les événements peuvent être enregistrés à partir du côté client de l’application :

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Ou du côté serveur :

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Vous pouvez joindre des valeurs de propriété à ces événements, afin de pouvoir filtrer ou fractionner les événements lorsque vous les étudiez dans le portail. Un ensemble standard de propriétés est également associé à chaque événement, comme des ID d’utilisateur anonymes, vous permettant ainsi de suivre la séquence d’activités d’un utilisateur.

En savoir plus sur les [événements personnalisés](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) et les [propriétés](../../azure-monitor/app/api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Segmenter et traiter les événements

Dans les outils Utilisateurs, Sessions et Événements, vous pouvez segmenter et traiter des événements personnalisés par utilisateur, nom d’événement et propriétés.
![Utilisateurs](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Concevoir la télémétrie avec l’application

Lorsque vous concevez chaque fonctionnalité de votre application, prenez en compte comment vous allez mesurer son succès auprès de vos utilisateurs. Choisissez les événements à enregistrer et coder les appels de suivi de ces événements dans votre application dès le début.

## <a name="a--b-testing"></a>Test A | B
Si vous ne savez pas quelle variante de la fonctionnalité sera la plus efficace, publiez les deux versions et mettez-les à disposition de différents utilisateurs. Mesurer le taux de réussite de chaque version et créez-en une version unifiée.

Pour cette technique, vous joignez des valeurs de propriétés distinctes à toute la télémétrie envoyée par chaque version de votre application. Pour cela, vous définissez des propriétés dans le contexte TelemetryContext actif. Ces propriétés par défaut sont ajoutées à chaque message de télémétrie que l'application envoie, non seulement vos messages personnalisés, mais aussi la télémétrie standard.

Dans le portail Application Insights, filtrez et segmentez vos données sur les valeurs de propriétés, afin de comparer les différentes versions.

Pour ce faire, [configurez un initialiseur de télémétrie](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer) :

**Applications ASP.NET**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

Dans l’initialiseur de l’application web, par exemple Global.asax.cs :

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**Applications ASP.NET Core**

> [!NOTE]
> Ajouter l’initialiseur en utilisant `ApplicationInsights.config` ou `TelemetryConfiguration.Active` n’est pas valide pour les applications ASP.NET Core. 

Pour les applications [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers), l’ajout d’un nouveau `TelemetryInitializer` se fait en l’ajoutant au conteneur d’injection de dépendance, comme indiqué ci-dessous. Ceci est fait dans la méthode `ConfigureServices` de votre classe `Startup.cs`.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Tous les nouveaux TelemetryClients ajoutent automatiquement la valeur de propriété que vous spécifiez. Les événements de télémétrie individuels peuvent remplacer les valeurs par défaut.

## <a name="next-steps"></a>Étapes suivantes
   - [Utilisateurs, sessions, événements](usage-segmentation.md)
   - [Entonnoirs](usage-funnels.md)
   - [Rétention](usage-retention.md)
   - [Flux d’utilisateurs](usage-flows.md)
   - [Classeurs](../../azure-monitor/app/usage-workbooks.md)
   - [Ajouter du contexte utilisateur](usage-send-user-context.md)
