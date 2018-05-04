---
title: Plan de tarification Entreprise hérité pour Azure Application Insights | Microsoft Docs
description: Gérer les volumes de données de télémétrie et surveiller les coûts dans Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 6df013506e4541fee7850850776d26e5c69a799d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="enterprise-plan-details"></a>Détails du plan Entreprise

Il existe deux plans tarifaires pour Azure Application Insights : De base et Entreprise. Le plan tarifaire [De base](app-insights-pricing.md) est le plan par défaut. Il inclut toutes les fonctionnalités du plan Entreprise, sans frais supplémentaires. La facturation du plan De base repose essentiellement sur le volume de données ingéré. 

Le plan Entreprise donne lieu à une facturation par nœud, chaque nœud recevant une allocation de données quotidienne. Dans le plan tarifaire Entreprise, vous êtes facturé pour les données ingérées au-delà de l’allocation incluse. Si vous utilisez Operations Management Suite, vous devez choisir le plan Entreprise. 

Pour connaître les prix actuels dans votre devise et pour votre région, consultez la page [Tarification Application Insights](http://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-the-enterprise-plan-works"></a>Fonctionnement du plan Entreprise

* Vous payez pour chaque nœud qui envoie des données de télémétrie pour n’importe quelle application dans le plan Entreprise.
 * Un *nœud* correspond à une machine serveur virtuelle ou physique ou à une instance de rôle PaaS (Platform-as-a-Service) qui héberge votre application.
 * Les ordinateurs de développement, les navigateurs clients et les appareils mobiles ne sont pas considérés comme des nœuds.
 * Si votre application comporte plusieurs composants qui envoient des données de télémétrie, comme un service web et un Worker principal, ces composants sont comptabilisés séparément.
 * Les données des [Flux de métriques temps réel](app-insights-live-stream.md) ne sont pas comptabilisées dans la tarification. Dans un abonnement, vos frais sont calculés par nœud, et non par application. Si vous disposez de cinq nœuds envoyant des données de télémétrie pour 12 applications, les frais sont calculés pour cinq nœuds.
* Bien que les frais indiqués soient par mois, vous êtes facturé uniquement pour toutes les heures dans lesquelles un nœud envoie des données de télémétrie à partir d’une application. Le tarif horaire est le prix mensuel indiqué divisé par 744 (nombre d’heures dans un mois de 31 jours).
* Une allocation de volume de données de 200 Mo par jour est accordée pour chaque nœud détecté (avec une granularité par heure). Le volume de données alloué inutilisé n’est pas reporté de jour en jour.
 * Lorsque vous choisissez le plan tarifaire Entreprise, chaque abonnement reçoit un volume quotidien de données en fonction du nombre de nœuds qui envoient des données de télémétrie aux ressources Application Insights dans cet abonnement. Par conséquent, si vous disposez de cinq nœuds qui envoient des données toute la journée, une allocation groupée de 1 Go est appliquée à toutes les ressources Application Insights de cet abonnement. Le fait que certains nœuds envoient plus de données que d’autres a peu d’importance, car les données incluses sont partagées entre tous les nœuds. Si, un jour donné, les ressources d’Application Insights reçoivent plus de données que le volume quotidien alloué pour cet abonnement, les frais de données de dépassement par Go s’appliquent. 
 * Le volume de données quotidien alloué est calculé comme suit : nombre d’heures quotidiennes (UTC) pendant lesquelles chaque nœud envoie des données de télémétrie, divisé par 24 multiplié par 200 Mo. Par conséquent, si quatre nœuds envoient des données de télémétrie pendant 15 heures sur les 24 heures de la journée, les données incluses pour le jour en question sont calculées comme suit : ((4 &#215; 15) / 24) &#215; 200 Mo = 500 Mo. Au prix de 2,30 USD par Go pour le dépassement de données, les frais s'élèvent à 1,15 USD si les nœuds envoient 1 Go de données dans cette journée.
 * Le volume de données quotidien alloué du plan Entreprise n’est pas partagé entre les applications pour lesquelles vous avez choisi le plan tarifaire De base. Le volume alloué inutilisé n’est pas reporté de jour en jour. 

## <a name="examples-of-how-to-determine-distinct-node-count"></a>Exemples de détermination du nombre de nœuds distincts

| Scénario                               | Nombre total de nœuds quotidien |
|:---------------------------------------|:----------------:|
| 1 application utilisant 3 instances Azure App Service et 1 serveur virtuel | 4 |
| 3 applications s’exécutant sur 2 machines virtuelles ; ressources Application Insights pour ces applications figurant dans le même abonnement et dans le plan Entreprise | 2 | 
| 4 applications dont les ressources Applications Insights figurent dans le même abonnement ; chaque application exécutant 2 instances pendant 16 heures creuses, ainsi que 4 instances pendant 8 heures de pointe | 13.33 | 
| Services cloud avec 1 rôle de travail et 1 rôle web, chacune exécutant 2 instances | 4 | 
| Cluster Azure Service Fabric de 5 nœuds exécutant 50 microservices ; chaque microservice exécutant 3 instances | 5.|

* Le comptage de nœuds précis varie selon le Kit de développement logiciel (SDK) Application Insights que votre application utilise. 
  * Dans les versions 2.2 et ultérieures de ce Kit de développement logiciel, le [Kit de développement logiciel (SDK) principal](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) et le [Kit de développement logiciel (SDK) web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) Application Insights signalent tous deux chaque hôte d’application en tant que nœud, par exemple le nom d’ordinateur pour le serveur physique et les hôtes de machine virtuelle ou le nom d’instance dans le cas de services cloud.  La seule exception concerne une application qui utilise uniquement [.NET Core](https://dotnet.github.io/) et le Kit de développement logiciel (SDK) principal Application Insights, auquel cas un seul nœud est signalé pour tous les hôtes, car le nom d’hôte n’est pas disponible. 
  * Pour les versions antérieures du Kit de développement logiciel (SDK), le [Kit de développement logiciel (SDK) web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) se comporte comme les nouvelles versions du Kit de développement logiciel (SDK), mais le [Kit de développement logiciel (SDK) principal](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) ne signale qu’un seul nœud, quel que soit le nombre d’hôtes d’application. 
  * Si votre application utilise le Kit de développement logiciel (SDK) pour définir l’instance de rôle (**roleInstance**) sur une valeur personnalisée, cette même valeur est utilisée par défaut pour déterminer le nombre de nœuds. 
  * Si vous utilisez une nouvelle version du Kit de développement logiciel (SDK) avec une application qui s’exécute à partir d’ordinateurs clients ou d’appareils mobiles, le nombre de nœuds renvoyé peut se révéler très important (en raison du grand nombre d’ordinateurs clients ou d’appareils mobiles). 
