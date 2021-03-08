---
title: Analyse de l’usage avec Azure Application Insights | Microsoft Docs
description: Comprenez vos utilisateurs et ce qu’ils font avec votre application.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 32c817fea00cfd28a3e0187cc7c581d828412c69
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726153"
---
# <a name="usage-analysis-with-application-insights"></a>Analyse de l'utilisation avec Application Insights

Quelles sont les fonctionnalités de votre application web ou mobile les plus populaires ? Vos utilisateurs atteignent-ils leurs objectifs avec votre application ? Disparaissent-ils à des stades spécifiques, et reviennent-ils plus tard ?  [Azure Application Insights](./app-insights-overview.md) vous permet d’obtenir des insights utiles sur l’utilisation de votre application. Chaque fois que vous mettez à jour votre application, vous pouvez évaluer son bon fonctionnement pour les utilisateurs. Grâce à ces informations, vous pouvez prendre des décisions basées sur des données sur les cycles de développement suivants.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>Envoyer des données de télémétrie à partir de votre application

La meilleure expérience est obtenue en installant Application Insights à la fois dans votre code serveur d’applications et dans vos pages web. Les composants client et serveur de votre application envoient la télémétrie au portail Azure pour analyse.

1. **Code serveur :** installez le module approprié pour votre [ASP.NET](./asp-net.md), [Azure](./app-insights-overview.md), [Java](./java-get-started.md), [Node.js](./nodejs.md) ou une [autre](./platforms.md) application.

    * *Vous ne voulez pas installer de code serveur ? Vous pouvez simplement [créer une ressource Azure Application Insights](./create-new-resource.md).*

2. **Code de page web :** ajoutez le script suivant à votre page web avant la balise ``</head>`` de fermeture. Remplacez la clé d’instrumentation par la valeur appropriée pour votre ressource Application Insights :
    
    L’extrait de code actuel (répertorié ci-dessous) est la version « 5 », la version est encodée dans l’extrait de code sous la forme sv : « # » et la [version actuelle est également disponible sur GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

    ```html
    <script type="text/javascript">
    !function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{nConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
    src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
    // name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
    // ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
    // useXhr: 1, // Use XHR instead of fetch to report failures (if available),
    crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
    // onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
    cfg: { // Application Insights Configuration
      instrumentationKey:"INSTRUMENTATION_KEY"
    }});
    </script>
    ```

    Pour en savoir plus les configurations plus complexes pour la supervision des sites web, consultez l’[article de référence du SDK JavaScript](./javascript.md).

3. **Code de l’application mobile :** utilisez le SDK App Center pour collecter les événements à partir de votre application, puis envoyer des copies de ces événements à Application Insights pour analyse en [suivant ce guide](../app/mobile-center-quickstart.md).

4. **Obtenir la télémétrie :** exécutez votre projet en mode débogage pendant quelques minutes, puis examinez les résultats dans le panneau Vue d’ensemble dans Application Insights.

    Publiez votre application pour surveiller les performances de votre application et découvrir ce que vos utilisateurs font avec votre application.

## <a name="explore-usage-demographics-and-statistics"></a>Explorer des données démographiques et des statistiques de l’utilisation
Découvrez quand des personnes utilisent votre application, les pages qui les intéressent le plus, où vos utilisateurs se trouvent, les navigateurs et les systèmes d’exploitation qu’ils utilisent. 

Les rapports Utilisateurs et sessions filtrent vos données par pages ou événements personnalisés, et les segmentent par propriétés telles que l’emplacement, l’environnement et la page. Vous pouvez également ajouter vos propres filtres.

![Capture d’écran montrant la page Vue d’ensemble des utilisateurs pour une société fictive.](./media/usage-overview/users.png)  

Aperçu des modèles intéressants appropriés dans le jeu de données.  

* Le rapport **Utilisateurs** compte le nombre d’utilisateurs uniques qui accèdent à vos pages dans les délais que vous avez choisis. Pour les applications web, les utilisateurs sont comptés avec les cookies. Si un utilisateur accède à votre site avec différents navigateurs ou ordinateurs clients, ou efface ses cookies, il est compté plusieurs fois.
* Le rapport **Sessions** compte le nombre de sessions utilisateur qui accèdent à votre site. Une session est une période d’activité d’un utilisateur, qui se termine par une période d’inactivité de plus d’une demi-heure.

[En savoir plus sur les outils Utilisateurs, Sessions et Événements](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Rétention - Combien d’utilisateurs reviennent ?

La rétention vous permet de comprendre la fréquence à laquelle vos utilisateurs reviennent utiliser leur application, en fonction des cohortes d’utilisateurs qui ont effectué une action pendant une période donnée. 

- Comprendre quelles fonctionnalités spécifiques font revenir les utilisateurs plus que d’autres 
- Formuler des hypothèses en fonction des données utilisateur réel 
- Déterminer si la rétention est un problème dans votre produit 

![Capture d’écran montrant la page Vue d’ensemble de la rétention avec des informations sur la fréquence à laquelle les utilisateurs reviennent pour utiliser leur application.](./media/usage-overview/retention.png) 

Les commandes de rétention en haut vous permettent de définir des événements spécifiques et un intervalle de temps pour calculer la rétention. Le graphique au centre fournit une représentation visuelle du pourcentage de rétention globale sur l’intervalle de temps spécifié. Le graphique en bas représente la rétention sur une période de temps donnée. Ce niveau de détail vous permet de comprendre de manière plus approfondie ce que font vos utilisateurs et ce qui les peut amener à revenir.  

[En savoir plus sur l’outil de rétention](usage-retention.md)

## <a name="custom-business-events"></a>Événements personnalisés

Pour bien comprendre ce que font les utilisateurs avec votre application, il est utile d’insérer des lignes de code pour enregistrer des événements personnalisés. Ces événements permettent de suivre toute activité, des actions détaillées des utilisateurs comme un clic sur un bouton, aux événements plus significatifs comme un de faire un achat ou de gagner à un jeu.

Vous pouvez également utiliser le [plug-in Collecte automatique de l’analytique de clics](javascript-click-analytics-plugin.md) pour collecter des événements personnalisés.

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

En savoir plus sur les [événements personnalisés](./api-custom-events-metrics.md#trackevent) et les [propriétés](./api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Segmenter et traiter les événements

Dans les outils Utilisateurs, Sessions et Événements, vous pouvez segmenter et traiter des événements personnalisés par utilisateur, nom d’événement et propriétés.
![Capture d’écran montrant la page Vue d’ensemble des utilisateurs pour une société fictive.](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Concevoir la télémétrie avec l’application

Lorsque vous concevez chaque fonctionnalité de votre application, prenez en compte comment vous allez mesurer son succès auprès de vos utilisateurs. Choisissez les événements à enregistrer et coder les appels de suivi de ces événements dans votre application dès le début.

## <a name="a--b-testing"></a>Test A | B
Si vous ne savez pas quelle variante de la fonctionnalité sera la plus efficace, publiez les deux versions et mettez-les à disposition de différents utilisateurs. Mesurer le taux de réussite de chaque version et créez-en une version unifiée.

Pour cette technique, vous joignez des valeurs de propriétés distinctes à toute la télémétrie envoyée par chaque version de votre application. Pour cela, vous définissez des propriétés dans le contexte TelemetryContext actif. Ces propriétés par défaut sont ajoutées à chaque message de télémétrie que l'application envoie, non seulement vos messages personnalisés, mais aussi la télémétrie standard.

Dans le portail Application Insights, filtrez et segmentez vos données sur les valeurs de propriétés, afin de comparer les différentes versions.

Pour ce faire, [configurez un initialiseur de télémétrie](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) :

**Applications ASP.NET**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
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
   - [Classeurs](../visualize/workbooks-overview.md)
