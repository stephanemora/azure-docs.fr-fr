---
title: Adresses IP utilisées par Azure Monitor
description: Exceptions de pare-feu de serveur requises par Application Insights
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 6e98419c805b7012a20ef08090c8cf3025baf30e
ms.sourcegitcommit: ef950cf37f65ea7a0f583e246cfbf13f1913eb12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111420821"
---
# <a name="ip-addresses-used-by-azure-monitor"></a>Adresses IP utilisées par Azure Monitor

[Azure Monitor](../overview.md) utilise un certain nombre d’adresses IP. Azure Monitor est constitué de métriques de plateforme de base et d’un journal, en plus de Log Analytics et d’Application Insights. Vous devrez peut-être connaître ces adresses si l’application ou l’infrastructure que vous supervisez est hébergée derrière un pare-feu.

> [!NOTE]
> Bien que ces adresses soient statiques, il est possible que nous devions les modifier de temps à autre. Tout le trafic d’Application Insights représente le trafic sortant, à l’exception de la surveillance de la disponibilité et des webhooks qui nécessitent des règles de pare-feu entrantes.

> [!TIP]
> Vous pouvez utiliser les [balises de service réseau](../../virtual-network/service-tags-overview.md) Azure pour gérer l’accès si vous utilisez des groupes de sécurité réseau Azure. Si vous gérez l’accès pour les ressources hybrides/locales, vous pouvez télécharger les listes d’adresses IP équivalentes en tant que [fichiers JSON](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) qui sont mis à jour chaque semaine. Pour couvrir toutes les exceptions de cet article, vous devez utiliser les étiquettes de service : `ActionGroup`, `ApplicationInsightsAvailability` et `AzureMonitor`.

Sinon, vous pouvez vous abonner à cette page comme flux RSS en ajoutant https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom à votre lecteur RSS/ATOM favori pour être informé des dernières modifications.


## <a name="outgoing-ports"></a>Ports sortants

Vous devez ouvrir des ports sortants dans le pare-feu de votre serveur pour autoriser le Kit de développement logiciel (SDK) Application Insights et/ou Status Monitor à envoyer des données sur le portail :

| Objectif | URL | IP | Ports |
| --- | --- | --- | --- |
| Télémétrie |dc.applicationinsights.azure.com<br/>dc.applicationinsights.microsoft.com<br/>dc.services.visualstudio.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.155.50<br/>52.162.110.67<br/>191.233.204.248<br/>13.69.66.140<br/>13.77.52.29<br/>51.107.59.180<br/>40.71.12.235<br/>20.44.8.10<br/>40.71.13.169<br/>13.66.141.156<br/>40.71.13.170<br/>13.69.65.23<br/>20.44.17.0<br/>20.36.114.207 <br/>51.116.155.246 <br/>51.107.155.178 <br/>51.140.212.64 <br/>13.86.218.255 <br/>20.37.74.240 <br/>65.52.250.236 <br/>13.69.229.240 <br/>52.236.186.210<br/>52.167.107.65<br/>40.71.12.237<br/>40.78.229.32<br/>40.78.229.33<br/>51.105.67.161<br/>40.124.64.192<br/>20.44.12.194<br/>20.189.172.0<br/>13.69.106.208<br/>40.78.253.199<br/>40.78.253.198<br/>40.78.243.19 | 443 |
| Live Metrics Stream (Flux continu de mesures) | live.applicationinsights.azure.com<br/>rt.applicationinsights.microsoft.com<br/>rt.services.visualstudio.com|23.96.28.38<br/>13.92.40.198<br/>40.112.49.101<br/>40.117.80.207<br/>157.55.177.6<br/>104.44.140.84<br/>104.215.81.124<br/>23.100.122.113| 443 |

## <a name="status-monitor"></a>Status Monitor

Configuration de Status Monitor (nécessaire uniquement pour apporter des modifications).

| Objectif | URL | IP | Ports |
| --- | --- | --- | --- |
| Configuration |`management.core.windows.net` | |`443` |
| Configuration |`management.azure.com` | |`443` |
| Configuration |`login.windows.net` | |`443` |
| Configuration |`login.microsoftonline.com` | |`443` |
| Configuration |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| Configuration |`auth.gfx.ms` | |`443` |
| Configuration |`login.live.com` | |`443` |
| Installation | `globalcdn.nuget.org`, `packages.nuget.org` ,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org`, `dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>Tests de disponibilité

Voici la liste des adresses à partir desquelles les [tests web de disponibilité](./monitor-web-app-availability.md) sont exécutés. Si vous souhaitez exécuter des tests web sur votre application, mais que votre serveur web est limité au service de clients spécifiques, vous devrez alors autoriser le trafic entrant depuis nos serveurs de test de disponibilité.


> [!NOTE]
> Pour les ressources situées dans des réseaux virtuels privés qui ne peuvent pas autoriser une communication entrante directe avec les agents de test de disponibilité dans Azure public, la seule option consiste [à créer et à héberger vos propres tests de disponibilité personnalisés](availability-azure-functions.md).

### <a name="service-tag"></a>Balise du service

Si vous utilisez des groupes de sécurité réseau Azure, ajoutez simplement une **règle de port d’entrée** pour autoriser le trafic à partir de tests de disponibilité Application Insights en sélectionnant **Balise de service** en tant que **Source**, et **ApplicationInsightsAvailability** en tant que **Balise du service source**.

>[!div class="mx-imgBorder"]
>![Sous Paramètres, sélectionnez Règles de sécurité de trafic entrant, puis Ajouter en haut de l’onglet ](./media/ip-addresses/add-inbound-security-rule.png)

>[!div class="mx-imgBorder"]
>![Onglet Ajouter une règle de sécurité de trafic entrant](./media/ip-addresses/add-inbound-security-rule2.png)

Ouvrez les ports 80 (http) et 443 (https) pour le trafic entrant à partir de ces adresses (les adresses IP sont regroupées par emplacement) :

### <a name="ip-addresses"></a>Adresses IP

Si vous recherchez les adresses IP réelles, vous pouvez les ajouter à la liste des adresses IP autorisées dans votre pare-feu, téléchargez le fichier JSON décrivant les plages d’adresses IP Azure. Ces fichiers contiennent les informations les plus récentes.

Après avoir téléchargé le fichier approprié, ouvrez-le à l’aide de votre éditeur de texte préféré et recherchez « ApplicationInsightsAvailability » pour accéder directement à la section du fichier décrivant la balise de service pour les tests de disponibilité.

> [!NOTE]
> Ces adresses sont répertoriées à l'aide de la notation CIDR (Classless Inter-Domain Routing). Cela signifie qu’une entrée comme `51.144.56.112/28` équivaut à 16 adresses IP entre `51.144.56.112` et `51.144.56.127`.

#### <a name="azure-public-cloud"></a>Cloud public Azure
Téléchargez des [adresses IP de cloud public](https://www.microsoft.com/download/details.aspx?id=56519).

#### <a name="azure-us-government-cloud"></a>Cloud Azure US Government
Télécharger des [adresses IP de cloud gouvernemental](https://www.microsoft.com/download/details.aspx?id=57063).

#### <a name="azure-china-cloud"></a>Cloud Azure Chine
Télécharger des [adresses IP de cloud chinois](https://www.microsoft.com/download/details.aspx?id=57062).

### <a name="discovery-api"></a>API Discovery
Vous pourriez vouloir [récupérer par programmation](../../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) la liste actuelle des balises de service ainsi que les informations relatives aux plages d’adresses IP.

## <a name="application-insights--log-analytics-apis"></a>API Log Analytics et Application Insights

| Objectif | URI |  IP | Ports |
| --- | --- | --- | --- |
| API |`api.applicationinsights.io`<br/>`api1.applicationinsights.io`<br/>`api2.applicationinsights.io`<br/>`api3.applicationinsights.io`<br/>`api4.applicationinsights.io`<br/>`api5.applicationinsights.io`<br/>`dev.applicationinsights.io`<br/>`dev.applicationinsights.microsoft.com`<br/>`dev.aisvc.visualstudio.com`<br/>`www.applicationinsights.io`<br/>`www.applicationinsights.microsoft.com`<br/>`www.aisvc.visualstudio.com`<br/>`api.loganalytics.io`<br/>`*.api.loganalytics.io`<br/>`dev.loganalytics.io`<br>`docs.loganalytics.io`<br/>`www.loganalytics.io` |20.37.52.188 <br/> 20.37.53.231 <br/> 20.36.47.130 <br/> 20.40.124.0 <br/> 20.43.99.158 <br/> 20.43.98.234 <br/> 13.70.127.61 <br/> 40.81.58.225 <br/> 20.40.160.120 <br/> 23.101.225.155 <br/> 52.139.8.32 <br/> 13.88.230.43 <br/> 52.230.224.237 <br/> 52.242.230.209 <br/> 52.173.249.138 <br/> 52.229.218.221 <br/> 52.229.225.6 <br/> 23.100.94.221 <br/> 52.188.179.229 <br/> 52.226.151.250 <br/> 52.150.36.187 <br/> 40.121.135.131 <br/> 20.44.73.196 <br/> 20.41.49.208 <br/> 40.70.23.205 <br/> 20.40.137.91 <br/> 20.40.140.212 <br/> 40.89.189.61 <br/> 52.155.118.97 <br/> 52.156.40.142 <br/> 23.102.66.132 <br/> 52.231.111.52 <br/> 52.231.108.46 <br/> 52.231.64.72 <br/> 52.162.87.50 <br/> 23.100.228.32 <br/> 40.127.144.141 <br/> 52.155.162.238 <br/> 137.116.226.81 <br/> 52.185.215.171 <br/> 40.119.4.128 <br/> 52.171.56.178 <br/> 20.43.152.45 <br/> 20.44.192.217 <br/> 13.67.77.233 <br/> 51.104.255.249 <br/> 51.104.252.13 <br/> 51.143.165.22 <br/> 13.78.151.158 <br/> 51.105.248.23 <br/> 40.74.36.208 <br/> 40.74.59.40 <br/> 13.93.233.49 <br/> 52.247.202.90 |80,443 |
| Extension Annotations de pipeline Azure | aigs1.aisvc.visualstudio.com |dynamique|443 | 

## <a name="application-insights-analytics"></a>Application Insights Analytics

| Objectif | URI | IP | Ports |
| --- | --- | --- | --- |
| Portail Analytics | analytics.applicationinsights.io | dynamique | 80,443 |
| CDN | applicationanalytics.azureedge.net | dynamique | 80,443 |
| CDN multimédia | applicationanalyticsmedia.azureedge.net | dynamique | 80,443 |

Remarque : Le domaine *.applicationinsights.io appartient à l’équipe Application Insights.

## <a name="log-analytics-portal"></a>Portail Log Analytics

| Objectif | URI | IP | Ports |
| --- | --- | --- | --- |
| Portail | portal.loganalytics.io | dynamique | 80,443 |
| CDN | applicationanalytics.azureedge.net | dynamique | 80,443 |

Remarque : le domaine *.loganalytics.io appartient à l’équipe Log Analytics.

## <a name="application-insights-azure-portal-extension"></a>Extension du Portail Azure Application Insights

| Objectif | URI | IP | Ports |
| --- | --- | --- | --- |
| Extension Application Insights | stamp2.app.insightsportal.visualstudio.com | dynamique | 80,443 |
| CDN de l’extension Application Insights | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | dynamique | 80,443 |

## <a name="application-insights-sdks"></a>SDK Application Insights

| Objectif | URI | IP | Ports |
| --- | --- | --- | --- |
| CDN du SDK JS Application Insights | az416426.vo.msecnd.net<br/>js.monitor.azure.com | dynamique | 80,443 |

## <a name="action-group-webhooks"></a>Webhooks de groupe d’actions

Vous pouvez interroger la liste des adresses IP utilisées par les groupes d’actions à l’aide de la [commande PowerShell Get-AzNetworkServiceTag](/powershell/module/az.network/Get-AzNetworkServiceTag).

### <a name="action-groups-service-tag"></a>Étiquette de service des groupes d’actions
La gestion des modifications apportées aux adresses IP sources peut prendre beaucoup de temps. L’utilisation d’**étiquettes de service** élimine la nécessité de mettre à jour votre configuration. Une balise de service représente un groupe de préfixes d’adresses IP d’un service Azure donné. Microsoft gère les adresses IP et met à jour automatiquement l’étiquette de service au fur et à mesure que les adresses changent, ce qui évite d’avoir à mettre à jour les règles de sécurité réseau pour un groupe d’actions.

1. Dans le portail Azure, sous Services Azure, recherchez *Groupe de sécurité réseau*.
2. Cliquez sur **Ajouter**, puis créez un groupe de sécurité réseau.

   1. Ajoutez le nom du groupe de ressources, puis entrez les *Détails de l’instance*.
   1. Cliquez sur **Vérifier + créer**, puis sur *Créer*.
   
   :::image type="content" source="../alerts/media/action-groups/action-group-create-security-group.png" alt-text="Exemple de création d’un groupe de sécurité réseau."border="true":::

3. Accédez à un Groupe de ressources, puis cliquez sur le *groupe de sécurité réseau* que vous avez créé.

    1. Sélectionnez *Règles de sécurité de trafic entrant*.
    1. Cliquez sur **Ajouter**.
    
    :::image type="content" source="../alerts/media/action-groups/action-group-add-service-tag.png" alt-text="Exemple d’ajout d’étiquette de service." border="true":::

4. Une nouvelle fenêtre s’ouvre dans le volet de droite.
    1.  Sélectionnez Source : **Étiquette de service**
    1.  Étiquette de service source : **ActionGroup**
    1.  Cliquez sur **Add**.
    
    :::image type="content" source="../alerts/media/action-groups/action-group-service-tag.png" alt-text="Exemple d’ajout d’étiquette de service." border="true":::


## <a name="profiler"></a>Profileur

| Objectif | URI | IP | Ports |
| --- | --- | --- | --- |
| Agent | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portail | gateway.azureserviceprofiler.net | dynamique | 443
| Stockage | *.core.windows.net | dynamique | 443

## <a name="snapshot-debugger"></a>Débogueur de capture instantanée

> [!NOTE]
> Le profileur et le débogueur de capture instantanée partagent le même jeu d’adresses IP.

| Objectif | URI | IP | Ports |
| --- | --- | --- | --- |
| Agent | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portail | gateway.azureserviceprofiler.net | dynamique | 443
| Stockage | *.core.windows.net | dynamique | 443
