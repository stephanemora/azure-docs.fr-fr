---
title: Azure Application Insights pour les applications web JavaScript
description: Obtenir les nombres de sessions et d’affichage de page, les données de client web, les applications monopages (SPA) et les modèles d’utilisation de suivi. Détection des problèmes de performances et des exceptions dans les pages Web JavaScript.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 50ce0d57ec7395c69bf65e41b67f0cb005a43cb8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854968"
---
# <a name="application-insights-for-web-pages"></a>Application Insights pour les pages web

Apprenez-en plus sur les performances et l’utilisation de votre page web ou de votre application. Ajoutez [Application Insights](app-insights-overview.md) à votre script de page pour obtenir le minutage des chargements de page et des appels AJAX, le nombre d’exceptions du navigateur et d’échecs d’AJAX et leurs détails, ainsi que les nombres d’utilisateurs et de sessions. Toutes ces données peuvent être segmentées par page, par version de système d’exploitation ou de navigateur client, par emplacement géographique et en fonction d’autres aspects. Vous pouvez définir des alertes en cas de dépassement d’un certain nombre d’échecs ou de ralentissement du chargement des pages. Et en insérant des suivis d’appel dans votre code JavaScript, vous pouvez suivre l’utilisation des différentes fonctionnalités de votre application de page web.

Vous pouvez utiliser Application Insights avec toutes les pages web ; il vous suffit pour cela d’ajouter un court extrait de code JavaScript. Si votre service web est [Java](java-get-started.md) ou [ASP.NET](asp-net.md), vous pouvez utiliser les kits SDK côté serveur conjointement au kit SDK JavaScript côté client pour acquérir une compréhension de bout en bout des performances de votre application.

## <a name="adding-the-javascript-sdk"></a>Ajout du SDK JavaScript

1. Tout d’abord, vous avez besoin d’une ressource Application Insights. Si vous ne disposez pas encore d’une ressource ni d’une clé d’instrumentation, suivez les [instructions permettant de créer une ressource](create-new-resource.md).
2. Copiez la clé d’instrumentation de la ressource à laquelle vous souhaitez envoyer vos données de télémétrie JavaScript.
3. Ajoutez le kit SDK JavaScript Application Insights à votre page web ou à votre application via l’une des deux options suivantes :
    * [Configuration basée sur npm](#npm-based-setup)
    * [Extrait de code JavaScript](#snippet-based-setup)

> [!IMPORTANT]
> N’utilisez qu’une seule méthode pour ajouter le SDK JavaScript à votre application. Si vous utilisez l’installation NPM, n’utilisez pas l’extrait de code, et inversement.

> [!NOTE]
> Le programme d’installation de NPM installe le SDK JavaScript en tant que dépendance à votre projet, en activant IntelliSense, tandis que l’extrait de code extrait le SDK au moment de l’exécution. Les deux prennent en charge les mêmes fonctionnalités. Cependant, les développeurs qui souhaitent des événements et une configuration plus personnalisés choisissent généralement l’installation NPM, tandis que les utilisateurs recherchant une activation rapide de l’analytique web prête à l’emploi choisissent l’extrait de code.

### <a name="npm-based-setup"></a>Configuration basée sur npm

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Configuration basée sur un extrait

Si votre application n’utilise pas npm, vous pouvez directement instrumenter vos pages web avec Application Insights en collant cet extrait de code en haut de chacune de vos pages. De préférence, il doit s’agir du premier script dans votre section `<head>` pour qu’il puisse surveiller tous les problèmes potentiels liés à l’ensemble de vos dépendances. Si vous utilisez l’application Blazor Server App, ajoutez l’extrait de code en haut du fichier `_Host.cshtml`, dans la section `<head>`.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Envoi de données de télémétrie au portail Azure

Par défaut, le kit SDK JavaScript Application Insights recueille un certain nombre d’éléments de télémétrie qui sont utiles pour déterminer l’intégrité de votre application et l’expérience utilisateur sous-jacente. notamment :

- **Exceptions non interceptées** dans votre application, y compris des informations sur
    - Arborescence des appels de procédure
    - Détails de l’exception et message accompagnant l’erreur
    - Numéros de ligne et de colonne de l’erreur
    - URL où l’erreur a été générée
- **Demandes de dépendance réseau** effectuées par vos demandes **XHR** et **Fetch** d’application (la collection de recherches est désactivée par défaut), y compris des informations sur
    - URL de la source de dépendance
    - Commande et méthode utilisées pour demander la dépendance
    - Durée de la demande
    - Code de résultat et état de réussite de la demande
    - ID (le cas échéant) de l’utilisateur qui effectue la demande
    - Contexte de corrélation (le cas échéant) dans lequel la demande est effectuée
- **Informations utilisateur** (par exemple, emplacement, réseau, adresse IP)
- **Informations sur l’appareil** (par exemple, navigateur, système d’exploitation, version, langue, modèle)
- **Informations de session**

### <a name="telemetry-initializers"></a>Initialiseurs de télémétrie
Les initialiseurs de télémétrie permettent de modifier le contenu des données de télémétrie collectées avant leur envoi à partir du navigateur de l’utilisateur. Ils peuvent également être utilisés pour empêcher l’envoi de certaines données de télémétrie, en retournant `false`. Plusieurs initialiseurs de télémétrie peuvent être ajoutés à votre instance d’Application Insights et ils seront exécutés dans l’ordre où ils sont ajoutés.

L’argument d’entrée pour `addTelemetryInitializer` est un rappel qui prend un [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) comme argument et retourne un `boolean` ou un `void`. Si `false` est retourné, l’élément de télémétrie n’est pas envoyé, sinon il passe à l’initialiseur de télémétrie suivant, le cas échéant, ou il est envoyé au point de terminaison de collecte de données de télémétrie.

Voici un exemple d’utilisation des initialiseurs de télémétrie :
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>Configuration
La plupart des champs de configuration sont nommés de façon à pouvoir avoir la valeur false par défaut. Tous les champs sont facultatifs à l’exception de `instrumentationKey`.

| Nom | Default | Description |
|------|---------|-------------|
| instrumentationKey | null | **Obligatoire**<br>Clé d’instrumentation que vous avez obtenue à partir du portail Azure. |
| accountId | null | ID de compte facultatif, si votre application regroupe les utilisateurs dans des comptes. Aucun espace, virgule, point-virgule, signe d’égalité ni barre verticale |
| sessionRenewalMs | 1800000 | Une session est consignée si l’utilisateur est inactif pendant ce laps de temps en millisecondes. La valeur par défaut est 30 minutes |
| sessionExpirationMs | 86400000 | Une session est consignée si elle s’est poursuivie pendant ce laps de temps en millisecondes. La valeur par défaut est 24 heures |
| maxBatchSizeInBytes | 10000 | Taille maximale du lot de données de télémétrie. Si un lot dépasse cette limite, il est immédiatement envoyé et un nouveau lot est démarré |
| maxBatchInterval | 15000 | Durée de mise en lot des données de télémétrie avant l’envoi (en millisecondes) |
| disableExceptionTracking | false | Si la valeur est true, les exceptions ne sont pas collectées automatiquement. La valeur par défaut est false. |
| disableTelemetry | false | Si la valeur est true, les données de télémétrie ne sont pas collectées ni envoyées. La valeur par défaut est false. |
| enableDebug | false | Si la valeur est true, les données de débogage **internes** sont levées en tant qu’exception **au lieu** d’être consignées, quels que soient les paramètres de journalisation du kit SDK. La valeur par défaut est false. <br>***Remarque :*** L’activation de ce paramètre entraîne la suppression de données de télémétrie chaque fois qu’une erreur interne se produit. Cela peut être utile pour identifier rapidement les problèmes liés à votre configuration ou utilisation du kit SDK. Pour ne pas perdre de données de télémétrie pendant le débogage, utilisez `consoleLoggingLevel` ou `telemetryLoggingLevel` à la place de `enableDebug`. |
| loggingLevelConsole | 0 | Consigne les erreurs **internes** d’Application Insights dans la console. <br>0 : désactivé, <br>1 : Erreurs critiques uniquement, <br>2 : Tout (erreurs et avertissements) |
| loggingLevelTelemetry | 1 | Envoie les erreurs **internes** d’Application Insights en tant que données de télémétrie. <br>0 : désactivé, <br>1 : Erreurs critiques uniquement, <br>2 : Tout (erreurs et avertissements) |
| diagnosticLogInterval | 10000 | (interne) Intervalle d’interrogation (en ms) pour la file d’attente de journalisation interne |
| samplingPercentage | 100 | Pourcentage d’événements qui seront envoyés. La valeur par défaut est 100, ce qui signifie que tous les événements sont envoyés. Définissez cette option si vous souhaitez conserver votre plafond de données pour les applications à grande échelle. |
| autoTrackPageVisitTime | false | Si la valeur est true, sur une consultation de page, la durée d’affichage de la page instrumentée précédente fait l’objet d’un suivi et est envoyée en tant que données de télémétrie, et un nouveau minuteur est démarré pour la consultation de page en cours. La valeur par défaut est false. |
| disableAjaxTracking | false | Si la valeur est true, les appels Ajax ne sont pas collectés automatiquement. La valeur par défaut est false. |
| disableFetchTracking | true | Si la valeur est true, les demandes Fetch ne sont pas collectées automatiquement. La valeur par défaut est true |
| overridePageViewDuration | false | Si la valeur est true, le comportement par défaut de trackPageView est modifié pour enregistrer la fin de l’intervalle de durée de consultation de page lorsque trackPageView est appelé. Si la valeur est false et qu’aucune durée personnalisée n’est fournie à trackPageView, les performances d’affichage de la page sont calculées à l’aide de l’API de minutage de la navigation. La valeur par défaut est false. |
| maxAjaxCallsPerView | 500 | Valeur par défaut 500 - contrôle le nombre d’appels Ajax qui seront supervisés par affichage de page. Affectez la valeur -1 pour superviser tous les appels Ajax (illimités) dans la page. |
| disableDataLossAnalysis | true | Si la valeur est false, les mémoires tampons d’expéditeur de données de télémétrie internes sont vérifiées au démarrage à la recherche d’éléments qui n’ont pas encore été envoyés. |
| disableCorrelationHeaders | false | Si la valeur est false, le kit SDK ajoute deux en-têtes ('Request-Id' et 'Request-Context') à toutes les demandes de dépendance pour les mettre en corrélation avec les demandes correspondantes côté serveur. La valeur par défaut est false. |
| correlationHeaderExcludedDomains |  | Désactiver les en-têtes de corrélation pour des domaines spécifiques |
| correlationHeaderDomains |  | Activer les en-têtes de corrélation pour des domaines spécifiques |
| disableFlushOnBeforeUnload | false | La valeur par défaut est false. Si la valeur est true, la méthode Flush n’est pas appelée lorsque l’événement onBeforeUnload est déclenché |
| enableSessionStorageBuffer | true | La valeur par défaut est true. Si la valeur est true, la mémoire tampon contenant toutes les données de télémétrie non envoyées est stockée dans le stockage de session. La mémoire tampon est restaurée lors du chargement de la page |
| isCookieUseDisabled | false | La valeur par défaut est false. Si la valeur est true, le kit SDK ne stocke pas ou ne lit pas les données des cookies.|
| cookieDomain | null | Domaine de cookie personnalisé. Cela est utile si vous souhaitez partager des cookies Application Insights entre les sous-domaines. |
| isRetryDisabled | false | La valeur par défaut est false. Si la valeur est false, réessayez avec 206 (succès partiel), 408 (délai d’expiration), 429 (trop de requêtes), 500 (erreur de serveur interne), 503 (service non disponible) et 0 (hors connexion, uniquement si détecté) |
| isStorageUseDisabled | false | Si la valeur est true, le kit SDK ne stocke pas ou ne lit pas les données à partir du stockage local et de session. La valeur par défaut est false. |
| isBeaconApiDisabled | true | Si la valeur est false, le kit SDK envoie toutes les données de télémétrie à l’aide de l’[API Beacon](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | false | La valeur par défaut est false. Quand l’onglet est fermé, le SDK envoie toutes les données de télémétrie restantes à l’aide de l’[API Beacon](https://www.w3.org/TR/beacon). |
| sdkExtension | null | Définit le nom de l’extension du kit SDK. Seuls les caractères alphabétiques sont autorisés. Le nom de l’extension est ajouté comme préfixe à la balise 'ai.internal.sdkVersion' (par exemple, 'ext_javascript:2.0.0'). La valeur par défaut est Null. |
| isBrowserLinkTrackingEnabled | false | La valeur par défaut est false. Si la valeur est true, le kit SDK effectue le suivi de toutes les demandes de [lien de navigateur](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink). |
| appId | null | AppId est utilisé pour la corrélation entre les dépendances AJAX qui se produisent côté client avec les demandes côté serveur. Lorsque l’API Beacon est activée, elle ne peut pas être utilisée automatiquement, mais peut être définie manuellement dans la configuration. La valeur par défaut est Null |
| enableCorsCorrelation | false | Si la valeur est true, le kit SDK ajoute deux en-têtes ('Request-Id' et 'Request-Context') à toutes les demandes CORS pour mettre en corrélation les dépendances AJAX sortantes avec les demandes correspondantes côté serveur. La valeur par défaut est false |
| namePrefix | non défini | Valeur facultative qui sera utilisée comme suffixe de nom pour localStorage et le nom du cookie.
| enableAutoRouteTracking | false | Effectuer le suivi automatique des modifications de route dans les applications monopages (SPA). Si la valeur est true, chaque modification de route envoie un nouveau Pageview à Application Insights. Les modifications des routes de hachage (`example.com/foo#bar`) sont également enregistrées en tant que nouveaux affichages de page.
| enableRequestHeaderTracking | false | Si la valeur est true, les en-têtes de requête d’extraction AJAX & Fetch sont suivis, la valeur par défaut est false.
| enableResponseHeaderTracking | false | Si la valeur est true, les en-têtes de réponse de requête d’extraction AJAX & Fetch sont suivis, la valeur par défaut est false.
| distributedTracingMode | `DistributedTracingModes.AI` | Définit le mode de traçage distribué. Si le mode AI_AND_W3C ou le mode W3C sont définis, les en-têtes de contexte de trace W3C (traceparent/tracestate) sont générés et inclus dans toutes les demandes sortantes. AI_AND_W3C est fourni à des fins de compatibilité descendante avec tous les services instrumentés Application Insights hérités.

## <a name="single-page-applications"></a>Applications monopages

Par défaut, ce kit SDK **ne gère pas** les modifications de route basées sur l’état qui se produisent dans les applications monopages. Pour activer le suivi automatique des modifications de route pour votre application monopage, vous pouvez ajouter `enableAutoRouteTracking: true` à la configuration de votre installation.

Actuellement, nous proposons un [plug-in React](#react-extensions) distinct que vous pouvez initialiser avec ce SDK. Il effectue également le suivi des modifications de route pour vous, ainsi que la collecte [d’autres données de télémétrie spécifiques à React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md).

> [!NOTE]
> Utilisez `enableAutoRouteTracking: true` uniquement si vous n’utilisez **pas** le plug-in REACT. Tous deux peuvent envoyer de nouveaux PageViews lorsque la route change. Si les deux sont activés, il est possible que des PageViews en double soient envoyés.

## <a name="configuration-autotrackpagevisittime"></a>Configuration : autoTrackPageVisitTime

En définissant `autoTrackPageVisitTime: true`, le temps que passe chaque utilisateur sur chaque page est suivi. Pour chaque nouveau PageView, le temps passé par l’utilisateur sur la page *précédente* est envoyée en tant que [métrique personnalisée](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-custom-overview) appelée `PageVisitTime`. Cette métrique personnalisée est affichable dans [Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) en tant que « métrique basée sur le journal ».

## <a name="react-extensions"></a>Extensions React

| Extensions |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [React Native](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Explorer les données côté navigateur/client

Vous pouvez afficher les données côté navigateur/client en accédant à **Métriques** et en ajoutant les métriques individuelles qui vous intéressent :

![](./media/javascript/page-view-load-time.png)

Vous pouvez également afficher vos données à partir du kit SDK JavaScript via l’expérience du navigateur dans le portail.

Sélectionnez **Navigateur**, puis choisissez **Échecs** ou **Performances**.

![](./media/javascript/browser.png)

### <a name="performance"></a>Performances

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Les dépendances

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analytics

Pour interroger vos données de télémétrie collectées par le kit SDK JavaScript, sélectionnez le bouton **Voir dans les journaux (analytique)** . En ajoutant une instruction `where` égale à `client_Type == "Browser"`, vous verrez uniquement les données du kit SDK JavaScript et toutes les données de télémétrie côté serveur collectées par d’autres kits SDK seront exclues.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Prise en charge du mappage de source

La pile d’appels minimisée de vos données de télémétrie d’exception peut être déminimisée dans le portail Azure. Toutes les intégrations existantes dans le panneau Détails des exceptions fonctionnent avec la pile d’appels nouvellement déminimisée.

#### <a name="link-to-blob-storage-account"></a>Lien vers un compte de stockage blob

Vous pouvez lier votre ressource Application Insights à votre propre conteneur de stockage blob Azure pour réduire automatiquement les piles d’appels. Pour commencer, consultez la [prise en charge automatique du mappage de source](./source-map-support.md).

### <a name="drag-and-drop"></a>Glisser-déposer

1. Sélectionnez un élément Télémétrie des exceptions dans le portail Azure pour afficher ses « détails de transaction de bout en bout ».
2. Identifiez les mappages de source qui correspondent à cette pile d’appels. Le mappage de source doit correspondre au fichier source d’un frame de pile, mais avec le suffixe `.map`
3. Glissez-déplacez les mappages de source sur la pile des appels dans le portail Azure ![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Version web de base d’Application Insights

Pour bénéficier d’une gestion allégée, vous pouvez installer à la place la version de base d’Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Cette version fournit un strict minimum de fonctionnalités et vous laisse la compléter à votre guise. Par exemple, elle n’effectue pas de collecte automatique (exceptions non interceptées, AJAX, etc.). Les API permettant d’envoyer certains types de données de télémétrie, comme `trackTrace`, `trackException`, etc., ne sont pas incluses dans cette version et vous devrez fournir votre propre wrapper. La seule API disponible est `track`. Vous trouverez un [exemple](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) ici.

## <a name="examples"></a>Exemples

Pour obtenir des exemples exécutables, consultez [Exemples du SDK JavaScript Application Insights](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Mise à niveau de l’ancienne version d’Application Insights

Dernières modifications dans la version V2 du kit SDK :
- Pour permettre de meilleures signatures d’API, certains appels d’API, tels que trackPageView et trackException, ont été mis à jour. L’exécution dans Internet Explorer 8 ou des versions antérieures du navigateur n’est pas prise en charge.
- L’enveloppe de télémétrie présente des modifications de structure et de nom des champs en raison de mises à jour du schéma de données.
- `context.operation` a été déplacé vers `context.telemetryTrace`. Certains champs ont également été modifiés (`operation.id` --> `telemetryTrace.traceID`).
  - Pour actualiser manuellement l’ID de consultation de page actuel (par exemple, dans les applications SPA), utilisez `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`.
    > [!NOTE]
    > Pour conserver l’ID de trace unique, dans lequel vous avez déjà utilisé `Util.newId()`, utilisez maintenant `Util.generateW3CId()`. Tous deux finissent par être l’ID d’opération.

Si vous utilisez le kit SDK de PRODUCTION d’Application Insights actuel (1.0.20) et que vous souhaitez voir si le nouveau kit SDK fonctionne dans le runtime, mettez à jour l’URL en fonction de votre scénario de chargement de kit SDK actuel.

- Scénario de téléchargement via CDN : Mettez à jour l’extrait de code que vous utilisez actuellement pour pointer vers l’URL suivante :
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- Scénario npm : Appelez `downloadAndSetup` pour télécharger le script ApplicationInsights complet à partir de CDN et l’initialiser avec la clé d’instrumentation :

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Effectuez un test dans l’environnement interne pour vérifier que la télémétrie de surveillance fonctionne comme prévu. Si tout fonctionne, mettez à jour vos signatures d’API conformément à la version V2 du kit SDK et procédez au déploiement dans vos environnements de production.

## <a name="sdk-performanceoverhead"></a>Surcharge/performances du kit SDK

Avec seulement 25 Ko compressés avec gzip et en mettant seulement ~15 ms pour s’initialiser, Application Insights ajoute un temps de chargement négligeable à votre site web. Avec cet extrait de code, les composants minimaux de la bibliothèque sont rapidement chargés. En attendant, le script complet est téléchargé en arrière-plan.

Pendant le téléchargement du script à partir de CDN, tout le suivi de votre page est mis en file d’attente. Une fois que le script téléchargé a fini de s’initialiser de manière asynchrone, tous les événements qui ont été mis en file d’attente font l’objet d’un suivi. Par conséquent, vous ne perdrez aucune donnée de télémétrie au cours du cycle de vie complet de votre page. Ce processus de configuration fournit à votre page un système d’analyse transparent, invisible à vos utilisateurs.

> Résumé :
> - **25 Ko** compressés avec gzip
> - Durée d’initialisation globale de **15 ms**
> - **Aucun** suivi manqué pendant le cycle de vie de la page

## <a name="browser-support"></a>Prise en charge des navigateurs

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome version la plus récente ✔ |  Firefox version la plus récente ✔ | IE 9+ et Edge ✔ | Opera version la plus récente ✔ | Safari version la plus récente ✔ |

## <a name="open-source-sdk"></a>Kit de développement logiciel (SDK) open source

Le kit SDK JavaScript Application Insights est proposé en open source. Pour afficher le code source ou contribuer au projet, visitez le [dépôt GitHub officiel](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next-steps"></a><a name="next"></a>Étapes suivantes
* [Suivi de l'utilisation](usage-overview.md)
* [Mesures et événements personnalisés](api-custom-events-metrics.md)
* [Développer-mesurer-apprendre](usage-overview.md)
