---
title: Télémétrie pour l’analyse du trafic de recherche
titleSuffix: Azure Cognitive Search
description: Activez l’analytique du trafic des recherches pour la Recherche cognitive Azure, collectez les données de télémétrie et les événements initiés par l’utilisateur à l’aide d’Application Insights, puis analysez les résultats dans un rapport Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/29/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 89d067162dacb7a0ca25de826630e1986f1035e1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102485467"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Collecter les données de télémétrie pour l’analyse du trafic de recherche

L’analyse du trafic de recherche est un modèle pour la collecte des données de télémétrie concernant les interactions de l’utilisateur avec votre application Recherche cognitive Azure, comme les événements de clic initiés par l’utilisateur et les saisies au clavier. À l’aide de ces informations, vous pouvez déterminer l’efficacité de votre solution de recherche, en vous intéressant notamment aux termes de recherche populaires, aux taux de clics et aux entrées de requête qui ne produisent aucun résultat.

Ce modèle dépend d’[Application Insights](../azure-monitor/app/app-insights-overview.md) (fonctionnalité d’[Azure Monitor](../azure-monitor/index.yml)) pour collecter les données utilisateur. Vous devrez également ajouter l’instrumentation à votre code client, comme le décrit cet article. Enfin, vous aurez besoin d’un mécanisme de création de rapports pour analyser les données. Nous vous recommandons Power BI, mais vous pouvez utiliser le tableau de bord de l'application ou tout autre outil qui se connecte à Application Insights.

> [!NOTE]
> Le modèle décrit dans cet article est destiné aux scénarios avancés et aux données parcours générées par le code que vous ajoutez à votre client. En revanche, les journaux de service sont faciles à configurer, fournissent une variété de métriques et peuvent être gérés dans le portail sans code requis. L’activation de la journalisation est recommandée pour tous les scénarios. Pour plus d’informations, consultez [Collecter et analyser les données de journal](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Identifier les données de recherche pertinentes

Pour obtenir des mesures utiles pour l’analyse du trafic de recherche, il est nécessaire d’enregistrer certains signaux auprès des utilisateurs de votre application de recherche. Ces signaux indiquent le contenu qui intéresse les utilisateurs et qu’ils estiment pertinent. Pour l’analyse du trafic de recherche, il s’agit des éléments suivants :

+ Événements de recherche générés par l’utilisateur : Ce signal se concentre uniquement sur les requêtes de recherche lancées par un utilisateur. Les autres requêtes de recherche, comme celles utilisées pour renseigner des facettes ou récupérer des informations internes, ne sont pas importantes. Veillez à n'instrumenter que les événements initiés par l'utilisateur afin d'éviter que vos résultats ne soient faussés ou biaisés.

+ Événements de clic générés par l’utilisateur : Sur une page de résultats de recherche, un événement de clic signifie généralement qu’un document est un résultat pertinent pour une requête de recherche spécifique.

En liant les événements de recherche et de clic avec un ID de corrélation, vous aurez une meilleure compréhension de la façon dont la fonctionnalité de recherche de votre application fonctionne.

## <a name="add-search-traffic-analytics"></a>Ajouter la fonctionnalité Analytique du trafic des recherches

Sur la page du [portail](https://portal.azure.com) de votre service Recherche cognitive Azure, ouvrez la page Analytique du trafic des recherches pour accéder à un aide-mémoire permettant de suivre ce modèle de télémétrie. À partir de cette page, vous pouvez sélectionner ou créer une ressource Application Insights, obtenir la clé d’instrumentation, copier des extraits de code que vous pouvez adapter à votre solution et télécharger un rapport Power BI qui est créé sur le schéma reflété dans le modèle.

![Page Analyse du trafic de recherche dans le portail](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Page Analyse du trafic de recherche dans le portail")

## <a name="1---set-up-application-insights"></a>1 - Configurer Application Insights

Sélectionnez une ressource Application Insights ou [en créer une](../azure-monitor/app/create-new-resource.md) si vous n’en n’avez pas. Si vous utilisez la page Analyse du trafic de recherche, vous pouvez copier la clé d’instrumentation dont votre application a besoin pour se connecter à Application Insights.

Une fois que vous disposez d’une ressource Application Insights, vous pouvez suivre les [instructions relatives aux plateformes et langages pris en charge](../azure-monitor/app/platforms.md) pour inscrire votre application. L’inscription consiste simplement à ajouter la clé d’instrumentation d’Application Insights à votre code, ce qui configure l’association. Vous pouvez trouver la clé dans le portail ou à partir de la page Analyse du trafic de recherche lorsque vous sélectionnez une ressource existante.

Un raccourci qui fonctionne pour certains types de projets Visual Studio est reflété dans les étapes suivantes. Il crée une ressource et inscrit votre application en quelques clics.

1. Pour Visual Studio et le développement ASP.NET, ouvrez votre solution et sélectionnez **Projet** > **Ajouter Application Insights Telemetry**.

1. Cliquez sur **Mise en route**.

1. Inscrivez votre application en fournissant un compte Microsoft, un abonnement Azure et une ressource Application Insights (une nouvelle ressource par défaut). Cliquez sur **S'inscrire**.

À ce stade, votre application est configurée pour l’analyse des applications, ce qui signifie que tous les chargements de page sont suivis avec les métriques par défaut. Pour plus d’informations sur les étapes précédentes, consultez [Activer la télémétrie Application Insights côté serveur](../azure-monitor/app/asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 - Ajouter la fonctionnalité d’instrumentation

Cette étape consiste à instrumenter votre propre application de recherche, à l’aide de la ressource Application Insights que vous avez créée à l’étape précédente. Il y a quatre étapes pour ce processus, en commençant par la création d’un client de télémétrie.

### <a name="step-1-create-a-telemetry-client"></a>Étape 1 : Créer un client de télémétrie

Créez un objet qui envoie des événements à Application Insights. Vous pouvez ajouter l’instrumentation à votre code d’application côté serveur ou au code côté client s’exécutant dans un navigateur, exprimé ici en variantes C# et JavaScript (pour les autres langages, consultez la liste complète des [plateformes et frameworks pris en charge](../azure-monitor/app/platforms.md). Choisissez l’approche qui vous donne la profondeur d’informations souhaitée.

La télémétrie côté serveur capture les métriques au niveau de la couche application, par exemple dans les applications qui s’exécutent en tant que service web dans le cloud, ou en tant qu’application locale sur un réseau d’entreprise. La télémétrie côté serveur capture les événements de recherche et de clic, la position d’un document dans les résultats et les informations sur les requêtes, mais votre collecte de données est limitée aux informations disponibles au niveau de cette couche.

Sur le client, vous pouvez avoir du code supplémentaire qui manipule les entrées de requête, ajoute la navigation ou inclut le contexte (par exemple, les requêtes lancées à partir d’une page d’accueil et celles lancées depuis une page de produit). Si cela décrit votre solution, vous pouvez opter pour l’instrumentation côté client afin que vos données de télémétrie reflètent ces détails supplémentaires. La façon dont ces détails supplémentaires sont collectés dépasse le cadre de ce modèle, mais vous pouvez consulter [Application Insights pour les pages web](../azure-monitor/app/javascript.md#explore-browserclient-side-data) pour plus d’informations. 

**Utiliser C#**

Pour C#, **InstrumentationKey** doit être défini dans la configuration de votre application, par exemple appsettings.json si vous avez un projet ASP.NET. Reportez-vous aux instructions d’enregistrement si vous avez des doutes sur l’emplacement de la clé.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
{
    _telemetryClient = telemetry;
}
```

**Utiliser JavaScript**

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
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
}});
</script>
```

> [!NOTE]
> Pour une meilleure lisibilité et pour réduire les erreurs JavaScript possibles, toutes les options de configuration possibles sont répertoriées sur une nouvelle ligne dans l’extrait de code ci-dessus, si vous ne souhaitez pas modifier la valeur d’une ligne commentée, elle peut être supprimée.


### <a name="step-2-request-a-search-id-for-correlation"></a>Étape 2 : Demander un ID de recherche pour la corrélation

Pour mettre en corrélation les requêtes de recherche avec les clics, il est nécessaire de disposer d’un ID de corrélation qui lie ces deux événements distincts. La Recherche cognitive Azure vous fournit un ID de recherche avec un en-tête HTTP.

Le fait de disposer de l’ID de recherche permet de corréler les métriques émises par la Recherche cognitive Azure pour la requête elle-même avec les métriques personnalisées que vous consignez dans Application Insights.

**Utiliser C# (SDK v11 plus récent)**

Le SDK le plus récent requiert l'utilisation d'un pipeline HTTP pour définir l'en-tête comme détaillé dans cet [exemple](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Pipeline.md#implementing-a-syncronous-policy).

```csharp
// Create a custom policy to add the correct headers
public class SearchIdPipelinePolicy : HttpPipelineSynchronousPolicy
{
    public override void OnSendingRequest(HttpMessage message)
    {
        message.Request.Headers.SetValue("x-ms-azs-return-searchid", "true");
    }
}
```

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Azure.Search.Documents

SearchClientOptions clientOptions = new SearchClientOptions();
clientOptions.AddPolicy(new SearchIdPipelinePolicy(), HttpPipelinePosition.PerCall);

var client = new SearchClient("<SearchServiceName>", "<IndexName>", new AzureKeyCredential("<QueryKey>"), options: clientOptions);

Response<SearchResults<SearchDocument>> response = await client.SearchAsync<SearchDocument>(searchText: searchText, searchOptions: options);
string searchId = string.Empty;
if (response.GetRawResponse().Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Utiliser C# (SDK v10 plus ancien)**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>));

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Utiliser JavaScript (appel à des API REST)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Étape 3 : Consigner les événements de recherche

Chaque fois qu’une requête de recherche est émise par un utilisateur, vous devez la consigner en tant qu’événement de recherche en respectant le schéma suivant sur un événement personnalisé Application Insights. N’oubliez pas de consigner uniquement les requêtes de recherche générées par l’utilisateur.

+ **SearchServiceName** : (string) nom du service de recherche
+ **SearchId** : (guid) identificateur unique de la requête de recherche (fourni dans la réponse de recherche)
+ **IndexName** : (string) index du service de recherche à interroger
+ **QueryTerms** : (string) termes de recherche entrés par l’utilisateur
+ **ResultCount** : (int) nombre de documents qui ont été retournés (dans la réponse de recherche)
+ **ScoringProfile** : (string) nom du profil de scoring utilisé, le cas échéant

> [!NOTE]
> Demandez le nombre de requêtes générées par l’utilisateur en ajoutant $count=true à votre requête de recherche. Pour en savoir plus, consultez la section relative à la [recherche de documents (REST)](/rest/api/searchservice/search-documents#query-parameters).
>

**Utiliser C#**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
};
_telemetryClient.TrackEvent("Search", properties);
```

**Utiliser JavaScript**

```javascript
appInsights.trackEvent("Search", {
  SearchServiceName: <service name>,
  SearchId: <search id>,
  IndexName: <index name>,
  QueryTerms: <search terms>,
  ResultCount: <results count>,
  ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>Étape 4 : Consigner les événements de clic

Chaque fois qu’un utilisateur clique sur un document, vous obtenez un signal qui doit être consigné afin d’analyser la recherche. Utilisez les événements personnalisés d’Application Insights pour consigner ces événements avec le schéma suivant :

+ **ServiceName** : (string) nom du service de recherche
+ **SearchId** : (guid) identificateur unique de la requête de recherche associée
+ **DocId** : (string) identificateur du document
+ **Position** : (int) rang du document dans la page des résultats de la recherche

> [!NOTE]
> La position fait référence à la commande cardinale dans votre application. Vous êtes libre de définir ce nombre, tant qu’il reste toujours le même, pour faciliter la comparaison.
>

**Utiliser C#**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
};
_telemetryClient.TrackEvent("Click", properties);
```

**Utiliser JavaScript**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 - Analyser dans Power BI

Après avoir instrumenté votre application et vérifié qu’elle est correctement connectée à Application Insights, vous pouvez télécharger un modèle de rapport prédéfini pour analyser les données dans Power BI Desktop. Le rapport contient des graphiques et des tableaux prédéfinis utiles pour analyser les données supplémentaires capturées pour l’analytique du trafic des recherches.

1. Dans le volet de navigation de gauche du tableau de bord de la Recherche cognitive Azure, sous **Paramètres**, cliquez sur **Analytique du trafic des recherches**.

1. Dans la page **Analytique du trafic des recherches**, à l’étape 3, cliquez sur **Obtenir Power BI Desktop** pour installer Power BI.

   ![Obtenir des rapports Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Obtenir des rapports Power BI")

1. Dans la même page, cliquez sur **Télécharger un rapport Power BI**.

1. Le rapport s’ouvre dans Power BI Desktop et vous invite à vous connecter à Application Insights et à fournir des informations d’identification. Des informations de connexion sont disponibles dans les pages du portail Azure relatives à votre ressource Application Insights. Pour les informations d’identification, indiquez les mêmes nom d’utilisateur et mot de passe que vous utilisez pour vous connecter au portail.

   ![Se connecter à Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Se connecter à Application Insights")

1. Cliquez sur **Charger**.

Ce rapport contient des graphiques et des tableaux qui vous aident à prendre des décisions plus éclairées pour améliorer les performances et la pertinence de vos recherches.

Les métriques incluaient les éléments suivants :

+ Volume de recherche et paires terme-document les plus populaires : termes qui amènent l’utilisateur à cliquer sur le même document, classés par clics.
+ Recherches sans clic : termes renvoyant aux principales requêtes qui n’enregistrent aucun clic

La capture d’écran suivante montre à quoi peut ressembler un rapport intégré si vous avez utilisé tous les éléments du schéma.

![Tableau de bord Power BI pour la Recherche cognitive Azure](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Tableau de bord Power BI pour la Recherche cognitive Azure")

## <a name="next-steps"></a>Étapes suivantes

Instrumentez votre application de recherche pour obtenir des données puissantes et détaillées sur votre service de recherche.

Des informations supplémentaires sur [Application Insights](../azure-monitor/app/app-insights-overview.md) sont disponibles. Vous pouvez également visiter la [page des tarifs](https://azure.microsoft.com/pricing/details/application-insights/) pour en savoir plus sur les différents niveaux de service.

En savoir plus sur la création de rapports exceptionnels. Pour plus d’informations, consultez [Bien démarrer avec Power BI Desktop](/power-bi/fundamentals/desktop-getting-started).
