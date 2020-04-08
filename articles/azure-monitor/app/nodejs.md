---
title: Surveiller les services Node.js avec Azure Application Insights | Microsoft Docs
description: Analysez les performances et diagnostiquez les problèmes dans les services Node.js avec Application Insights.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 38336e3faf3764233dd94bffbfb24421e054496a
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411579"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Surveiller vos services et applications Node.js avec Application Insights

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) surveille vos services et composants principaux après leur déploiement pour vous permettre de détecter et diagnostiquer rapidement différents problèmes, notamment les problèmes de performances. Vous pouvez utiliser Application Insights pour les services Node.js hébergés dans votre centre de données, dans les machines virtuelles et applications web Azure, et même dans d’autres clouds publics.

Pour recevoir, stocker et explorer vos données d’analyse, incluez le kit de développement logiciel (SDK) dans votre code et configurez une ressource Application Insights correspondante dans Azure. Le kit de développement logiciel (SDK) envoie les données à cette ressource pour une analyse et une exploration plus approfondies.

Le kit de développement logiciel (SDK) Node.js peut automatiquement analyser les demandes HTTP entrantes et sortantes, les exceptions et certaines mesures du système. À partir de la version 0.20, le kit de développement logiciel (SDK) peut surveiller des [packages tiers](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules) communs comme MongoDB, MySQL et Redis. Tous les événements liés à une demande HTTP entrante sont mis en corrélation pour une résolution des problèmes plus rapide.

Vous pouvez utiliser l’API TelemetryClient pour instrumenter et surveiller manuellement des aspects supplémentaires de votre application et de votre système. L’API TelemetryClient est décrite plus en détail plus loin dans cet article.

## <a name="get-started"></a>Bien démarrer

Terminez les tâches suivantes pour configurer la surveillance d’une application ou d’un service.

### <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez d’un abonnement Azure ou [obtenez-en un gratuitement][azure-free-offer]. Si votre organisation possède déjà un abonnement Azure, un administrateur peut suivre [ces instructions][add-aad-user] pour vous y ajouter.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: https://docs.microsoft.com/azure/active-directory/active-directory-users-create-azure-portal


### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a> Configurer une ressource Application Insights


1. Connectez-vous au [portail Azure][portal].
2. Sélectionnez **Créer une ressource** > **Outils de développement** > **Application Insights**. La ressource inclut un point de terminaison permettant de recevoir les données de télémétrie, le stockage pour ces données, les rapports et tableaux de bord enregistrés, la configuration des règles et des alertes, etc.

3. Sur la page de création de ressource, sélectionnez **Application Node.js** dans la zone **Application Type**. Le type d’application détermine les tableaux de bord et rapports créés par défaut. (Toutes les ressources Application Insights peuvent collecter des données à partir de n’importe quel langage et n’importe quelle plateforme.)

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a> Configurer le kit de développement logiciel (SDK) Node.js

Incluez le kit de développement logiciel dans votre application afin qu’il collecte des données. 

1. Copiez la clé d’instrumentation de votre ressource (aussi appelée *iClé*) à partir du portail Azure. Application Insights utilise l’iClé pour mapper les données à votre ressource Azure. Avant que le kit de développement logiciel (SDK) ne puisse utiliser votre iClé, vous devez la spécifier dans une variable d’environnement ou dans votre code.  

   ![Copier la clé d’instrumentation](./media/nodejs/instrumentation-key-001.png)

2. Ajoutez la bibliothèque de kit de développement logiciel (SDK) Node.js aux dépendances de votre application par le biais de package.json. À partir du dossier racine de votre application, exécutez :

   ```bash
   npm install applicationinsights --save
   ```

3. Chargez explicitement la bibliothèque dans votre code. Étant donné que le kit de développement logiciel (SDK) injecte l’instrumentation dans beaucoup d’autres bibliothèques, chargez-la dès que possible, même avant les autres instructions `require`. 

   En haut de votre premier fichier .js, ajoutez le code suivant. La méthode `setup` configure l’iClé (et donc la ressource Azure) à utiliser par défaut pour tous les éléments suivis.

   ```javascript
   const appInsights = require("applicationinsights");
   appInsights.setup("<instrumentation_key>");
   appInsights.start();
   ```
   
   Vous pouvez également fournir une iClé par le biais de la variable d’environnement APPINSIGHTS\_INSTRUMENTATIONKEY au lieu de la transférer manuellement vers `setup()` ou `new appInsights.TelemetryClient()`. Cela vous permet de conserver les iClés en dehors du code source dédié et de spécifier des iClés différentes selon les environnements.

   Pour des options de configuration supplémentaires, consultez les sections suivantes.

   Vous pouvez essayer le kit de développement logiciel (SDK) sans envoyer de télémétrie en définissant `appInsights.defaultClient.config.disableAppInsights = true`.

### <a name="monitor-your-app"></a><a name="monitor"></a> Surveiller votre application

Le kit de développement logiciel (SDK) recueille automatiquement les données de télémétrie sur le runtime Node.js et certains modules tiers courants. Utilisez votre application pour générer certaines de ces données.

Ensuite, sur le [Portail Azure][portal], accédez à la ressource Application Insights que vous avez créée. Dans la **Vue d’ensemble de la chronologie**, cherchez vos premiers points de données. Pour voir plus de données détaillées, sélectionnez des composants différents dans les graphiques.

Pour consulter la topologie découverte pour votre application sélectionnez le bouton **Mappage d’application**. Sélectionnez les composants pour plus de détails.

![Mise en correspondance d’une application simple](./media/nodejs/application-map-002.png)

Pour en savoir plus sur votre application et pour résoudre d’éventuels problèmes, sélectionnez les autres vues disponibles dans la section **EXAMINER**.

![Section Examiner](./media/nodejs/007-investigate-pane.png)

#### <a name="no-data"></a>Pas de données ?

Étant donné que le kit de développement logiciel (SDK) regroupe les données à envoyer, l’affichage des éléments dans le portail peut prendre un certain temps. Si les données n’apparaissent pas dans votre ressource, essayez ce qui suit :

* Continuez d’utiliser l’application. Prenez plus de mesures pour générer davantage de données de télémétrie.
* Cliquez sur **Actualiser** dans l’affichage des ressources du portail. Les graphiques s’actualisent périodiquement et automatiquement. Les actualiser manuellement les force à s’actualiser immédiatement.
* Vérifiez que les [ports sortants requis](../../azure-monitor/app/ip-addresses.md) sont ouverts.
* Utilisez la fonction [Recherche](../../azure-monitor/app/diagnostic-search.md) pour chercher des événements spécifiques.
* Consultez les [FAQ][FAQ].


## <a name="sdk-configuration"></a>Configuration du kit de développement logiciel (SDK)

Les méthodes de configuration et les valeurs par défaut du kit de développement logiciel (SDK) sont répertoriées dans l’exemple de code suivant.

Pour mettre pleinement en corrélation les événements dans un service, veillez à définir `.setAutoDependencyCorrelation(true)`. Lorsque cette option est définie, le kit de développement logiciel (SDK) peut effectuer le suivi de contexte entre les rappels asynchrones dans Node.js.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .start();
```

## <a name="telemetryclient-api"></a>API TelemetryClient

Pour une description complète de l’API TelemetryClient, consultez [API Application Insights pour les événements et les mesures personnalisés](../../azure-monitor/app/api-custom-events-metrics.md).

Vous pouvez suivre les demandes, les événements, les mesures ou les exceptions à l’aide du kit de développement logiciel (SDK) Application Insights Node.js. L’exemple de code suivant présente certaines des API que vous pouvez utiliser :

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey is in env var
let client = appInsights.defaultClient;

client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Suivre les dépendances

Utilisez le code suivant pour effectuer le suivi de vos dépendances :

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.defaultClient;

var success = false;
let startTime = Date.now();
// Execute dependency call here...
let duration = Date.now() - startTime;
success = true;

client.trackDependency({dependencyTypeName: "dependency name", name: "command name", duration: duration, success: success});
```

### <a name="add-a-custom-property-to-all-events"></a>Ajouter une propriété personnalisée à tous les événements

Utilisez le code suivant pour ajouter une propriété personnalisée à tous les événements :

```javascript
appInsights.defaultClient.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Suivre les demandes HTTP GET

Utilisez le code suivant pour effectuer le suivi des demandes HTTP GET :

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.defaultClient.trackNodeHttpRequest({request: req, response: res});
    }
    // Other work here...
    res.end();
});
```

### <a name="track-server-startup-time"></a>Suivre le temps de démarrage du serveur

Utilisez le code suivant pour effectuer le suivi du temps de démarrage du serveur :

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

## <a name="next-steps"></a>Étapes suivantes

* [Navigation et tableaux de bord dans le portail Application Insights](../../azure-monitor/app/overview-dashboard.md)
* [Écrire des requêtes Analytics via vos données de télémétrie](../../azure-monitor/log-query/get-started-portal.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../../azure-monitor/app/troubleshoot-faq.md

