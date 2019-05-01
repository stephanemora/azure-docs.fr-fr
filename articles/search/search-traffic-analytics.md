---
title: Implémenter la fonctionnalité Analytique du trafic des recherches - Recherche Azure
description: Activez l’analytique du trafic des recherches pour la Recherche Azure afin d’ajouter des données de télémétrie et des événements lancés par l’utilisateur à des fichiers journaux.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c30c8bae3e76778a31cdd0695acde52b5b1c6b02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749568"
---
# <a name="implement-search-traffic-analytics-in-azure-search"></a>Implémenter la fonctionnalité Analytique du trafic des recherches dans la Recherche Azure
La recherche de l’analyse du trafic est un modèle qui implémente une boucle de rétroaction pour votre service de recherche. Ce modèle décrit les données nécessaires et la manière de les collecter à l’aide d’Application Insights, l’un des leaders du secteur dans le domaine de la surveillance de services multi-plateforme.

La recherche de l’analyse du trafic vous permet de gagner en visibilité dans votre service de recherche et de dévoiler des informations sur les utilisateurs et leur comportement. En recueillant des données sur les choix de vos utilisateurs, vous êtes en mesure de prendre des décisions qui améliorent votre expérience de recherche et de vous interrompre lorsque les résultats ne répondent pas à vos attentes.

La Recherche Azure offre une solution de télémétrie qui intègre Azure Application Insights et Power BI pour assurer une surveillance et un suivi approfondis. Étant donné que l’interaction avec Azure Search passe uniquement par les API, la télémétrie doit être implémentée par les développeurs à l’aide de la recherche, en suivant les instructions contenues sur cette page.

## <a name="identify-relevant-search-data"></a>Identifier les données de recherche pertinentes

Pour disposer de mesures de recherche utiles, il est nécessaire de consigner quelques signaux provenant des utilisateurs de l’application de recherche. Ces signaux indiquent le contenu qui intéresse les utilisateurs et qu’ils estiment répondre à leurs besoins.

La fonctionnalité Rechercher l’analyse du trafic repose sur deux signaux :

1. Événements de recherche générés par l’utilisateur : ce signal se concentre uniquement sur les requêtes de recherche lancées par un utilisateur. Les requêtes de recherche utilisées pour remplir des facettes, du contenu supplémentaire ou des informations internes ne sont pas importantes ; elles ont également tendance à biaiser vos résultats.

2. Événements de clic générés par l’utilisateur : dans ce document, le terme « clic»  fait référence à un utilisateur qui sélectionne un résultat de recherche spécifique retourné par une requête de recherche. Un clic signifie généralement qu’un document est un résultat pertinent pour une requête de recherche spécifique.

En liant la recherche et les événements de clic par un ID de corrélation, il est possible d’analyser les comportements des utilisateurs de votre application. Il est impossible d’obtenir ces informations de recherche en se basant uniquement sur les journaux d’activité de trafic de recherche.

## <a name="add-search-traffic-analytics"></a>Ajouter la fonctionnalité Analytique du trafic des recherches

Les signaux mentionnés dans la section précédente doivent être collectés à partir de l’application de recherche lorsque l’utilisateur interagit avec cette application. Application Insights est une solution de supervision extensible, disponible pour plusieurs plateformes et qui intègre des options d’instrumentation flexibles. L’utilisation d’Application Insights vous permet de tirer parti des rapports de recherche Power BI créés par Azure Search afin de faciliter l’analyse des données.

Sur la page du [portail](https://portal.azure.com) de votre service Recherche Azure, le panneau Rechercher l’analyse du trafic contient un aide-mémoire pour suivre ce modèle de télémétrie. Vous pouvez également sélectionner ou créer une ressource Application Insights et consulter toutes les données nécessaires au même endroit.

![Instructions relatives à la recherche de l’analyse du trafic][1]

## <a name="1---select-a-resource"></a>1 - Sélectionner une ressource

Vous devez sélectionner une ressource Application Insights ou en créer une si vous n’en n’avez pas. Vous pouvez utiliser une ressource qui est déjà en cours d’utilisation pour consigner les événements personnalisés requis.

Lorsque vous créez une nouvelle ressource Application Insights, tous les types d’application sont valides pour ce scénario. Sélectionnez celle qui convient le mieux à la plate-forme que vous utilisez.

Vous avez besoin de la clé d’instrumentation pour créer le client de télémétrie pour votre application. Vous pouvez l’obtenir à partir du tableau de bord du portail Application Insights, ou encore de la page Rechercher l’analyse du trafic, en sélectionnant l’instance que vous souhaitez utiliser.

## <a name="2---add-instrumentation"></a>2 - Ajouter la fonctionnalité d’instrumentation

Cette phase consiste à instrumenter votre propre application de recherche, à l’aide de la ressource Application Insights que vous avez créée à l’étape précédente. Ce processus se décompose en quatre étapes :

**Étape 1 : Créer un client de télémétrie** Il s’agit de l’objet qui envoie les événements à la ressource Application Insights.

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

Pour d’autres langages et plates-formes, consultez la [liste](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) complète.

**Étape 2 : Demander un ID de recherche pour la corrélation** Pour mettre en corrélation les requêtes de recherche avec les clics, il est nécessaire de disposer d’un ID de corrélation qui lie ces deux événements distincts. La Recherche Azure vous fournit un ID de recherche avec un en-tête :

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<ServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**Étape 3 : Consigner les événements de recherche**

Chaque fois qu’une requête de recherche est émise par un utilisateur, vous devez la consigner en tant qu’événement de recherche en respectant le schéma suivant sur un événement personnalisé Application Insights :

**ServiceName** : (chaîne) nom du service de recherche **SearchId** : (GUID) identificateur unique de la requête de recherche (qui est fourni dans la réponse de recherche) **IndexName** : (chaîne) index du service de recherche à interroger **QueryTerms** : (chaîne) termes de recherche entrés par l’utilisateur **ResultCount** : (entier) nombre de documents qui ont été renvoyés (fourni dans la réponse de recherche) **ScoringProfile** : (chaîne) nom du profil de score utilisé, le cas échéant

> [!NOTE]
> Demandez le nombre de requêtes générées par l’utilisateur en ajoutant $count=true à votre requête de recherche. Plus d’informations, cliquez [ici](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> N’oubliez pas d’enregistrer uniquement les requêtes de recherche qui sont générées par les utilisateurs.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**Étape 4 : Consigner les événements de clic**

Chaque fois qu’un utilisateur clique sur un document, vous obtenez un signal qui doit être consigné afin d’analyser la recherche. Utilisez les événements personnalisés d’Application Insights pour consigner ces événements avec le schéma suivant :

**ServiceName** : (chaîne) nom du service de recherche **SearchId** : (GUID) identificateur unique de la requête de recherche associée **DocId** : (chaîne) identificateur du document **Position** : (entier) classement du document dans la page des résultats de recherche

> [!NOTE]
> La position fait référence à la commande cardinale dans votre application. Vous êtes libre de définir ce nombre, tant qu’il reste toujours le même, pour faciliter la comparaison.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

## <a name="3---analyze-in-power-bi"></a>3 - Analyser dans Power BI

Après avoir instrumenté votre application et vérifié que votre application est correctement connectée à Application Insights, vous pouvez utiliser un modèle prédéfini créé par la Recherche Azure pour Power BI Desktop. 

La Recherche Azure fournit un [pack de contenu Power BI](https://app.powerbi.com/getdata/services/azure-search) de supervision pour vous permettre d’analyser les données de journal. Le pack de contenu ajoute des graphiques et des tableaux prédéfinis utiles pour analyser les données supplémentaires capturées pour l’analytique du trafic des recherches. Pour plus d’informations, consultez la [Page d’aide du pack de contenu](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/). 

1. Dans le volet de navigation de gauche du tableau de bord Recherche Azure, sous **Paramètres**, cliquez sur **Analytique du trafic des recherches**.

2. Dans la page **Analytique du trafic des recherches**, à l’étape 3, cliquez sur **Obtenir Power BI Desktop** pour installer Power BI.

   ![Obtenir des rapports Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Obtenir des rapports Power BI")

2. Dans la même page, cliquez sur **Télécharger un rapport PowerBI**.

3. Le rapport s’ouvre dans Power BI Desktop et vous invite à vous connecter à Application Insights. Ces informations sont disponibles dans les pages du portail Azure relatives à votre ressource Application Insights.

   ![Se connecter à Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Se connecter à Application Insights")

4. Cliquez sur **Charger**.

Ce rapport contient des graphiques et des tableaux qui vous aident à prendre des décisions plus éclairées pour améliorer les performances et la pertinence de vos recherches.

Les métriques incluaient les éléments suivants :

* Taux de clic (CTR) : rapport entre les utilisateurs qui cliquent sur un document spécifique et le nombre total de recherches.
* Recherches sans clic : termes renvoyant aux principales requêtes qui n’enregistrent aucun clic
* Documents ayant reçu le plus de clics : documents les plus consultés, classés par ID, au cours des 24 dernières heures, des 7 derniers jours et des 30 derniers jours.
* Paires terme-document populaires : termes qui amènent l’utilisateur à cliquer sur le même document, classés par clics.
* Temps de clic : clics compartimentés en fonction du délai écoulé depuis la requête de recherche

La capture d’écran suivante montre les rapports et graphiques intégrés permettant d’analyser l’analytique du trafic des recherches.

![Tableau de bord Power BI pour la Recherche Azure](./media/search-traffic-analytics/AzureSearch-PowerBI-Dashboard.png "Tableau de bord Power BI pour la Recherche Azure")

## <a name="next-steps"></a>Étapes suivantes
Instrumentez votre application de recherche pour obtenir des données puissantes et détaillées sur votre service de recherche.

Des informations supplémentaires sur [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) sont disponibles. Vous pouvez également visiter la [page des tarifs](https://azure.microsoft.com/pricing/details/application-insights/) pour en savoir plus sur les différents niveaux de service.

En savoir plus sur la création de rapports exceptionnels. Pour plus d’informations, consultez [Prise en main de Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/).

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
