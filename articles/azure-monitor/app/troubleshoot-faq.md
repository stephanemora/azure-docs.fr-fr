---
title: Forum aux questions sur Azure Application Insights | Microsoft Docs
description: Questions fréquentes sur Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: mbullwin
ms.openlocfilehash: 115be0ad1b7dec44f036f6d50c2ac30ceba37ba7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457086"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights : Forum Aux Questions (FAQ)

## <a name="configuration-problems"></a>Problèmes de configuration
*J’ai des difficultés à configurer :*

* [Application .NET](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
* [Analyse d’une application déjà en cours d’exécution](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot)
* [Diagnostics Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Applications web Java](../../azure-monitor/app/java-troubleshoot.md)

*Je ne reçois aucune donnée de mon serveur*

* [Définir les exceptions de pare-feu](../../azure-monitor/app/ip-addresses.md)
* [Configurer un serveur ASP.NET](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Configurer un serveur Java](../../azure-monitor/app/java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Puis-je utiliser Application Insights avec... ?

* [Applications web sur un serveur IIS - en local ou sur une machine virtuelle](../../azure-monitor/app/asp-net.md)
* [Applications web Java](../../azure-monitor/app/java-get-started.md)
* [Applications Node.js](../../azure-monitor/app/nodejs.md)
* [Applications web sur Azure](../../azure-monitor/app/azure-web-apps.md)
* [Services cloud sur Azure](../../azure-monitor/app/cloudservices.md)
* [Serveurs d’applications exécutés dans Docker](../../azure-monitor/app/docker.md)
* [Applications web d’une seule page](../../azure-monitor/app/javascript.md)
* [SharePoint](sharepoint.md)
* [Applications de bureau Windows](../../azure-monitor/app/windows-desktop.md)
* [autres plateformes](../../azure-monitor/app/platforms.md)

## <a name="is-it-free"></a>Est-ce gratuit ?

Oui, pour une utilisation expérimentale. Dans le plan de tarification de base, votre application peut envoyer un certain quota de données chaque mois sans frais. Le quota gratuit est suffisamment grand pour couvrir le développement et la publication d’une application pour un petit nombre d’utilisateurs. Vous pouvez définir un plafond pour empêcher le traitement des données au-delà d’un certain seuil.

Les grands volumes de télémétrie sont facturés au Go. Nous vous offrons quelques conseils sur la façon de [limiter vos frais](../../azure-monitor/app/pricing.md).

Le plan Entreprise implique un coût journalier pour chaque nœud de serveur web qui envoie des données de télémétrie. Il est adapté si vous souhaitez utiliser l’exportation continue à grande échelle.

[Lire le plan de tarification](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Combien cela coûte ?

* Ouvrez la **page Utilisation et estimation des coûts** dans une ressource Application Insights. Cette page contient un graphique reflétant l’utilisation récente. Vous pouvez définir une limite de volume de données si vous le souhaitez.
* Ouvrez le panneau [Facturation Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) pour voir vos factures sur l’ensemble des ressources.

## <a name="q14"></a>Que modifie Application Insights dans mon projet ?
Cela dépend du type de projet. Pour une application web :

* Ajoute ces fichiers à votre projet :

  * ApplicationInsights.config.
  * ai.js
* Installe ces packages NuGet :

  * *Application Insights API* - API de base
  * *Application Insights API for Web Applications* - permet d'envoyer des données de télémétrie depuis le serveur
  * *Application Insights API for JavaScript Applications* - permet d'envoyer des données de télémétrie depuis le client

    Ces packages comprennent les assemblys suivants :
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Insère des éléments dans :

  * Web.config
  * packages.config
* (Nouveaux projets uniquement - si vous [ajoutez Application Insights à un projet existant][start], vous devez le faire manuellement). Insère des extraits de code dans le code du client et du serveur, afin de les initialiser avec l'ID de la ressource Application Insights. Par exemple, dans une application MVC, le code est inséré dans la page maître Views/Shared/_Layout.cshtml.

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Comment mettre à niveau à partir d'anciennes versions du Kit de développement logiciel (SDK) ?
Consultez les [notes de publication](../../azure-monitor/app/release-notes.md) du Kit de développement logiciel (SDK) adapté à votre type d'application.

## <a name="update"></a>Comment puis-je changer la ressource Azure à laquelle mon projet envoie des données ?
Dans l’Explorateur de solutions, cliquez avec le bouton droit sur `ApplicationInsights.config` , puis sélectionnez **Mettre à jour Application Insights**. Vous pouvez envoyer les données à une ressource existante ou à une nouvelle ressource dans Azure. L'Assistant Mise à jour modifie la clé d'instrumentation dans ApplicationInsights.config, qui détermine où le Kit de développement logiciel (SDK) du serveur envoie vos données. Cela modifie également la clé où de l'affichage dans vos pages web, sauf si vous désactivez l'option « Tout mettre à jour ».

## <a name="what-is-status-monitor"></a>Qu’est-ce que Status Monitor ?

Une application de bureau que vous pouvez utiliser dans votre serveur web IIS pour faciliter la configuration d’Application Insights dans les applications web. Cette application ne collecte pas de données de télémétrie : vous pouvez l’arrêter lorsque vous ne configurez pas une application. 

[Plus d’informations](../../azure-monitor/app/monitor-performance-live-website-now.md#questions)

## <a name="what-telemetry-is-collected-by-application-insights"></a>Quelles sont les données de télémétrie recueillies par Application Insights ?

À partir d’applications web serveur :

* Requêtes HTTP
* [Dépendances](../../azure-monitor/app/asp-net-dependencies.md). Appels passés aux instances SQL Database ; appels HTTP vers des services externes ; Azure Cosmos DB, table, stockage d’objets blob et file d’attente. 
* [Exceptions](../../azure-monitor/app/asp-net-exceptions.md) et arborescences des appels de procédure.
* [Compteurs de performances](../../azure-monitor/app/performance-counters.md) : si vous utilisez [Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md), [Supervision Azure](../../azure-monitor/app/azure-web-apps.md) ou le [writer collectd d’Application Insights](../../azure-monitor/app/java-collectd.md).
* [Événements et mesures personnalisés](../../azure-monitor/app/api-custom-events-metrics.md) que vous codez.
* [Journaux de suivi](../../azure-monitor/app/asp-net-trace-logs.md) si vous configurez le collecteur approprié.

À partir des [ pages web client](../../azure-monitor/app/javascript.md) :

* [Nombre de pages consultées](usage-overview.md)
* [Appels AJAX](../../azure-monitor/app/asp-net-dependencies.md). Requêtes transmises à partir d’un script en cours d’exécution.
* Données relatives au chargement de pages
* Nombre de sessions et d’utilisateurs
* [ID des utilisateurs authentifiés](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)

À partir d’autres sources, si vous les configurez :

* [Diagnostics Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Importer vers Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)
* [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)
* [Logstash](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Puis-je filtrer ou modifier des données de télémétrie ?

Oui, dans le serveur, vous pouvez écrire :

* Un processeur de télémétrie, pour filtrer ou ajouter des propriétés aux éléments de télémétrie sélectionnés avant de les envoyer à partir de votre application.
* Un initialiseur de télémétrie, pour ajouter des propriétés à tous les éléments de télémétrie.

En savoir plus pour [ASP.NET](../../azure-monitor/app/api-filtering-sampling.md) ou [Java](../../azure-monitor/app/java-filter-telemetry.md).

## <a name="how-are-city-country-and-other-geo-location-data-calculated"></a>Comment sont calculées les données relative à la ville, au pays et aux autres emplacements géographiques ?

Nous recherchons l’adresse IP (IPv4 ou IPv6) du client web à l’aide de [GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/).

* Télémétrie de navigateur : nous collectons l’adresse IP de l’expéditeur.
* Télémétrie de serveur : le module Application Insights collecte l’adresse IP du client. Elle n’est pas collectée si `X-Forwarded-For` est défini.

Vous pouvez configurer le `ClientIpHeaderTelemetryInitializer` pour récupérer l’adresse IP à partir d’un autre en-tête. Dans certains systèmes, par exemple, elle est déplacée vers `X-Originating-IP` par un proxy, un équilibreur de charge ou un CDN. [Plus d’informations](https://apmtips.com/blog/2016/07/05/client-ip-address/)

Vous pouvez [utiliser Power BI](../../azure-monitor/app/export-power-bi.md ) pour afficher les données de télémétrie de votre requête sur une carte.


## <a name="data"></a>Combien de temps les données sont-elles conservées dans le portail ? Sont-elles sécurisées ?
Voir [Rétention de données et confidentialité][data].

## <a name="could-personal-data-be-sent-in-the-telemetry"></a>Est-ce que des informations personnelles peuvent être envoyées dans les données de télémétrie ?

Cela est possible si votre code envoie ce type de données. Cela peut également se produire si les variables dans les arborescences des appels de procédure incluent des données personnelles. Votre équipe de développement doit mener une évaluation des risques afin de s’assurer que les données personnelles sont correctement gérées. [En savoir plus sur la rétention et la confidentialité des données](../../azure-monitor/app/data-retention-privacy.md).

**Tous** les octets de l’adresse web du client ont toujours la valeur 0 lors de la recherche des attributs d’emplacement géographique.

## <a name="my-ikey-is-visible-in-my-web-page-source"></a>Mon iKey est visible dans la source de ma page web. 

* Il s’agit d’une pratique courante dans les solutions de surveillance.
* Il ne peut pas être utilisé pour dérober vos données.
* Il peut servir à fausser vos alertes de données ou à déclencher des alertes.
* Aucun client ne nous a jamais signalé ce type de problème.

Vous pouvez :

* Utilisez deux iKeys distincts (ressources Application Insights distinctes) pour les données client et serveur. Ou
* Écrire un proxy qui s’exécute sur votre serveur et que le client web utilise pour envoyer des données.

## <a name="post"></a>Comment consulter les données POST dans la fonction Recherche de diagnostic ?
Les données POST ne sont pas automatiquement consignées, mais vous pouvez utiliser un appel TrackTrace ; placez les données dans le paramètre message. Ce dernier présente une limite de taille plus longue que les limites relatives aux propriétés de type chaîne, bien que vous ne puissiez pas lui appliquer de filtres.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Dois-je utiliser une ou plusieurs ressources Application Insights ?

Utilisez une ressource unique pour tous les composants ou rôles dans un système métier unique. Utilisez des ressources distinctes pour les versions de développement, de test et de publication, et pour les applications indépendantes.

* [Suivez la discussion ici](separate-resources.md)
* [Exemple : service cloud avec des rôles web et worker](../../azure-monitor/app/cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Comment modifier dynamiquement la clé d’instrumentation ?

* [Discussion ici](separate-resources.md)
* [Exemple : service cloud avec des rôles web et worker](../../azure-monitor/app/cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Que désignent les nombres d’utilisateurs et de sessions ?

* Le SDK JavaScript définit un cookie utilisateur sur le client web pour identifier les utilisateurs récurrents, ainsi qu’un cookie de session pour regrouper des activités.
* S’il n’existe aucun script côté client, vous pouvez [définir des cookies sur le serveur](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Si un utilisateur réel utilise votre site dans différents navigateurs, ou s’il utilise une navigation privée ou encore des ordinateurs différents, il sera comptabilisé plusieurs fois.
* Pour identifier un utilisateur connecté sur différents ordinateurs et navigateurs, ajoutez un appel à [setAuthenticatedUserContext()](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a> Comment savoir si j'ai activé tout ce qu'il faut pour utiliser Application Insights ?
| Ce qui suit doit s'afficher | Comment y accéder | Utilité |
| --- | --- | --- |
| Graphiques de disponibilité |[Tests web](../../azure-monitor/app/monitor-web-app-availability.md) |Savoir si votre application web est active |
| Performances des applications de serveur (temps de réponse, etc.) |[Ajout d’Application Insights à votre projet](../../azure-monitor/app/asp-net.md) ou [Installation d’AI Status Monitor sur le serveur](../../azure-monitor/app/monitor-performance-live-website-now.md) (ou écrivez votre propre code pour [suivre des dépendances](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)) |Détecter les problèmes de performances |
| Télémétrie des dépendances |[Installation d’AI Status Monitor sur le serveur](../../azure-monitor/app/monitor-performance-live-website-now.md) |Diagnostiquer les problèmes relatifs à des bases de données ou à d'autres composants externes |
| Obtention de l'arborescence des appels de procédure à partir des exceptions |[Insertion d’appels TrackException dans votre code](../../azure-monitor/app/asp-net-exceptions.md) (certains d’entre eux sont cependant signalés automatiquement) |Détecter et diagnostiquer les exceptions |
| Recherche des données de suivi des journaux |[Ajout d’un enregistreur de données](../../azure-monitor/app/asp-net-trace-logs.md) |Diagnostiquer les exceptions et problèmes de performances |
| Principes fondamentaux d'utilisation des clients (vues de page, sessions, etc.) |[Initialiseur JavaScript dans les pages web](../../azure-monitor/app/javascript.md) |Analyse de l'utilisation |
| Mesures personnalisées des clients |[Appels de suivi dans les pages web](../../azure-monitor/app/api-custom-events-metrics.md) |Améliorer l'expérience utilisateur |
| Mesures personnalisées des serveurs |[Appels de suivi dans le serveur](../../azure-monitor/app/api-custom-events-metrics.md) |Décisionnel |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Pourquoi les graphiques de recherche et de mesures contiennent-ils des valeurs différentes ?

[L’échantillonnage](../../azure-monitor/app/sampling.md) réduit le nombre d’éléments de télémétrie (demandes, événements personnalisés, etc.) qui sont réellement envoyés à partir de votre application sur le portail. Dans la Recherche, vous pouvez voir le nombre d’éléments réellement reçus. Dans les graphiques de mesure qui affichent un nombre d’événements, vous obtenez le nombre d’événements d’origine qui se sont produits. 

Chaque élément transmis comporte une propriété `itemCount` qui indique le nombre d’événements d’origine que représente cet élément. Pour observer l’échantillonnage en action, vous pouvez exécuter cette requête dans Analytics :

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automatisation

### <a name="configuring-application-insights"></a>Configuration d'Application Insights

Vous pouvez [écrire des scripts PowerShell](../../azure-monitor/app/powershell.md) à l’aide du moniteur de ressources Azure pour :

* Créer et mettre à jour des ressources Application Insights.
* Définir le plan de tarification.
* Récupérer la clé d’instrumentation.
* Ajouter une alerte métrique.
* Ajouter un test de disponibilité.

Vous ne peut pas définir un rapport état Metrics Explorer ou configurer une exportation continue.

### <a name="querying-the-telemetry"></a>Interrogation des données de télémétrie

Utilisez [l’API REST](https://dev.applicationinsights.io/) pour exécuter des requêtes [Analytics](../../azure-monitor/app/analytics.md).

## <a name="how-can-i-set-an-alert-on-an-event"></a>Comment puis-je définir une alerte sur un événement ?

Les alertes Azure portent uniquement sur les mesures. Créez une mesure personnalisée qui dépasse un seuil de valeur chaque fois que l’événement se produit. Puis définissez une alerte sur la mesure. Notez que vous recevez une notification chaque fois que la mesure dépasse le seuil dans les deux directions. Vous ne recevez pas de notification avant le dernier dépassement, que la valeur initiale soit élevée ou faible ; il y a toujours une latence de quelques minutes.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Existe-t-il des frais de transfert de données entre une application web Azure et Application Insights ?

* Si votre application web Azure est hébergée dans un centre de données qui comporte un point de terminaison de collecte Application Insights, aucuns frais ne sont appliqués. 
* S’il n’existe aucun point de terminaison de collecte dans votre centre de données hôte, les données de télémétrie de votre application généreront des [frais Azure de trafic sortant](https://azure.microsoft.com/pricing/details/bandwidth/).

Cela ne dépend pas de l’emplacement où est hébergée votre ressource Application Insights. Cela dépend simplement de la distribution de nos points de terminaison.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Puis-je envoyer des données de télémétrie au portail Application Insights ?

Nous vous recommandons d’utiliser nos Kits de développement logiciel (SDK) et [l’API du Kit SDK](../../azure-monitor/app/api-custom-events-metrics.md). Il existe des variantes du SDK pour les différentes [plateformes](../../azure-monitor/app/platforms.md). Ces kits de développement logiciel gèrent la mise en mémoire tampon, la compression, la limitation, les nouvelles tentatives, etc. Le [schéma d’ingestion](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) et le [protocole de point de terminaison](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) sont toutefois publics.

## <a name="can-i-monitor-an-intranet-web-server"></a>Puis-je surveiller un serveur web intranet ?

Oui, mais vous devez autoriser le trafic vers nos services à l’aide d’exceptions de pare-feu ou de redirections proxy.
- QuickPulse `rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Passez en revue notre liste de services et les adresses IP [ici](../../azure-monitor/app/ip-addresses.md).

### <a name="firewall-exception"></a>Exception de pare-feu

Permettez à votre serveur web d’envoyer la télémétrie à nos points de terminaison. 

### <a name="proxy-redirect"></a>Redirection proxy

Acheminez le trafic entre votre serveur et une passerelle sur votre intranet en remplaçant les points de terminaison dans votre configuration.
Si ces propriétés « Endpoint » ne sont pas présentes dans votre configuration, ces classes utiliseront les valeurs par défaut indiquées dans l’exemple ApplicationInsights.config ci-dessous. 

Votre passerelle doit acheminer le trafic vers l’adresse de base de notre point de terminaison. Dans votre configuration, remplacez les valeurs par défaut par `http://<your.gateway.address>/<relative path>`.


#### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Exemple ApplicationInsights.config avec les points de terminaison par défaut :
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector"/>
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

_Remarque : ApplicationIdProvider est disponible à partir de la version 2.6.0_


 

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Puis-je exécuter des tests web de disponibilité sur un serveur intranet ?

Nos [tests web](../../azure-monitor/app/monitor-web-app-availability.md) s’exécutent sur des points de présence qui sont répartis dans le monde entier. Il existe deux solutions :

* Pare-feu : autorisez les demandes envoyées à votre serveur parmi [la longue liste modifiable d’agents de test web](../../azure-monitor/app/ip-addresses.md).
* Écrivez votre propre code pour envoyer des demandes régulières à votre serveur depuis votre intranet. Vous pouvez exécuter des tests web Visual Studio à cet effet. Le testeur peut envoyer les résultats à Application Insights à l’aide de l’API TrackAvailability().

## <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Combien de temps dure la collecte de données de télémétrie ?

La plupart des données Application Insights ont une latence de moins de 5 minutes. Certaines données peuvent prendre plus de temps, en général les fichiers journaux plus volumineux. Pour plus d’informations, consultez le [contrat SLA Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).

## <a name="more-answers"></a>Réponses supplémentaires
* [Forum Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: ../../azure-monitor/app/data-retention-privacy.md
[platforms]: ../../azure-monitor/app/platforms.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[windows]: app-insights-windows-get-started.md
