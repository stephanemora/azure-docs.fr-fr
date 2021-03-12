---
title: Chaînes de connexion dans Azure Application Insights | Microsoft Docs
description: Guide pratique pour utiliser des chaînes de connexion.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: df87b060423aeff9fa5f83f21634395fe30e0bbb
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486282"
---
# <a name="connection-strings"></a>Chaînes de connexion

## <a name="overview"></a>Vue d’ensemble

Les chaînes de connexion fournissent aux utilisateurs Application Insights un seul paramètre de configuration, ce qui évite d’avoir à utiliser plusieurs paramètres de proxy. Elles s’avèrent très utiles aux serveurs web intranet et aux environnements cloud souverains ou hybrides qui cherchent à envoyer des données au service de supervision.

Les paires clé-valeur permettent aux utilisateurs de définir facilement une combinaison préfixe-suffixe pour chaque service/produit Application Insights (AI).

> [!IMPORTANT]
> Nous déconseillons de définir à la fois une chaîne de connexion et une clé d’instrumentation. Au cas où un utilisateur définirait les deux, la valeur définie en dernier prévaut. 


## <a name="scenario-overview"></a>Présentation du scénario 

Scénarios client dans lesquels nous visualisons l’impact le plus important :

- Exceptions de pare-feu ou redirections de proxy 

    Quand la supervision d’un serveur web intranet est nécessaire, notre solution antérieure demandait aux clients d’ajouter des points de terminaison de service individuels à leur configuration. Vous pourrez trouver plus d’informations [ici](../faq.md#can-i-monitor-an-intranet-web-server). 
    Les chaînes de connexion offrent une meilleure alternative en réduisant cet effort à un seul paramètre. La simple modification d’un préfixe et d’un suffixe permet de renseigner tous les points de terminaison et de les rediriger automatiquement vers les services appropriés. 

- Environnements cloud souverains ou hybrides

    Les utilisateurs peuvent envoyer des données à une [région Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) définie.
    Les chaînes de connexion vous permettent de définir des paramètres de point de terminaison pour vos serveurs intranet ou des paramètres cloud hybrides. 

## <a name="getting-started"></a>Prise en main

### <a name="finding-my-connection-string"></a>Où trouver ma chaîne de connexion ?

Votre chaîne de connexion s’affiche dans le panneau Vue d’ensemble de votre ressource Application Insights.

![Chaîne de connexion dans le panneau Vue d’ensemble](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>schéma

#### <a name="max-length"></a>Longueur maximale

La longueur maximale prise en charge pour la connexion s’élève à 4 096 caractères.

#### <a name="key-value-pairs"></a>Paires clé-valeur

La chaîne de connexion se compose d’une liste de paramètres représentés sous forme de paires clé-valeur séparées par un point-virgule : `key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Syntaxe

- `InstrumentationKey` (exemple : 00000000-0000-0000-0000-000000000000) La chaîne de connexion est un champ **obligatoire**.
- `Authorization` (exemple : ikey) (Ce paramètre est facultatif car, pour le moment, nous ne prenons en charge que l’autorisation ikey.)
- `EndpointSuffix` (exemple : applicationinsights.azure.cn) La définition du suffixe du point de terminaison indique au SDK à quel cloud Azure se connecter. Le SDK assemble le reste du point de terminaison pour les services individuels.
- Points de terminaison explicites.
  Tout service peut être substitué explicitement dans la chaîne de connexion.
   - `IngestionEndpoint` (exemple : `https://dc.applicationinsights.azure.com`)
   - `LiveEndpoint` (exemple : `https://live.applicationinsights.azure.com`)
   - `ProfilerEndpoint` (exemple : `https://profiler.monitor.azure.com`)
   - `SnapshotEndpoint` (exemple : `https://snapshot.monitor.azure.com`)

#### <a name="endpoint-schema"></a>Schéma de point de terminaison

`<prefix>.<suffix>`
- Préfixe : Définit un service. 
- Suffixe : Définit le nom de domaine commun.

##### <a name="valid-suffixes"></a>Suffixes valides

Voici la liste des suffixes valides. 
- applicationinsights.azure.cn
- applicationinsights.us


Voir aussi : https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Préfixes valides

- [Ingestion de données de télémétrie](./app-insights-overview.md) : `dc`
- [Métriques temps réel](./live-stream.md) : `live`
- [Profiler](./profiler-overview.md): `profiler`
- [Capture instantanée](./snapshot-debugger.md) : `snapshot`



## <a name="connection-string-examples"></a>Exemples de chaînes de connexion courantes


### <a name="minimal-valid-connection-string"></a>Chaîne de connexion valide minimale

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

Dans cet exemple, seule la clé d’instrumentation a été définie.

- Le schéma d’autorisation est défini par défaut sur « ikey » 
- Clé d’instrumentation : 00000000-0000-0000-0000-000000000000
- Les URI de services régionaux se basent sur les [valeurs par défaut du SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs) et se connectent au service Azure global public :
   - Ingestion : `https://dc.services.visualstudio.com/`
   - Métriques temps réel : `https://rt.services.visualstudio.com/`
   - Profiler : `https://profiler.monitor.azure.com/`
   - Débogueur : `https://snapshot.monitor.azure.com/`



### <a name="connection-string-with-endpoint-suffix"></a>Chaîne de connexion avec suffixe de point de terminaison

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

Dans cet exemple, cette chaîne de connexion spécifie le suffixe du point de terminaison et le SDK construit les points de terminaison de service.

- Le schéma d’autorisation est défini par défaut sur « ikey » 
- Clé d’instrumentation : 00000000-0000-0000-0000-000000000000
- Les URI de services régionaux se basent sur le suffixe de point de terminaison fourni : 
   - Ingestion : `https://dc.ai.contoso.com`
   - Métriques temps réel : `https://live.ai.contoso.com`
   - Profiler : `https://profiler.ai.contoso.com`
   - Débogueur : `https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Chaîne de connexion avec des substitutions de points de terminaison explicites 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

Dans cet exemple, cette chaîne de connexion spécifie des substitutions explicites pour chaque service. Le SDK utilise strictement les points de terminaison fournis sans apporter aucune modification.

- Le schéma d’autorisation est défini par défaut sur « ikey » 
- Clé d’instrumentation : 00000000-0000-0000-0000-000000000000
- Les URI de services régionaux se basent sur les valeurs de remplacement explicites : 
   - Ingestion : `https://custom.com:111/`
   - Métriques temps réel : `https://custom.com:222/`
   - Profiler : `https://custom.com:333/`
   - Débogueur : `https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>Comment définir une chaîne de connexion

Les chaînes de connexion sont prises en charge dans les versions de SDK suivantes :
- .NET et .NET Core v2.12.0
- Java v2.5.1 et Java 3.0
- JavaScript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

Une chaîne de connexion peut être définie dans du code, une variable d’environnement ou un fichier de configuration.



### <a name="environment-variable"></a>Variable d’environnement

- Chaîne de connexion : `APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="code-samples"></a>Exemples de code

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

Définissez la propriété [TelemetryConfiguration.ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274) ou [ApplicationInsightsServiceOptions.ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/81288f26921df1e8e713d31e7e9c2187ac9e6590/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs#L66-L69)

.NET défini explicitement :
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

Fichier de configuration .NET :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

NetCore défini explicitement :
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore config.json : 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (v2.5.x) défini explicitement :
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Important : JavaScript ne prend pas en charge l’utilisation de variables d’environnement.

Utilisation de l’extrait de code :

L’extrait de code actuel (répertorié ci-dessous) est la version « 5 », la version est encodée dans l’extrait de code sous la forme sv : « # » et la [version actuelle est également disponible sur GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}});
</script>
```

> [!NOTE]
> Pour une meilleure lisibilité et pour réduire les erreurs JavaScript possibles, toutes les options de configuration possibles sont répertoriées sur une nouvelle ligne dans l’extrait de code ci-dessus, si vous ne souhaitez pas modifier la valeur d’une ligne commentée, elle peut être supprimée.

Installation manuelle :
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

Nous recommandons aux utilisateurs de définir la variable d’environnement.

Pour définir explicitement la chaîne de connexion :

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```

---

## <a name="next-steps"></a>Étapes suivantes

Prise en main lors de l’exécution avec :

* [Applications hébergées par IIS sur une machine virtuelle Azure et un groupe de machines virtuelles identiques Azure](./azure-vm-vmss-apps.md)
* [Serveur IIS](./monitor-performance-live-website-now.md)
* [Azure Web Apps](./azure-web-apps.md)

Prise en main pendant le développement avec :

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)

