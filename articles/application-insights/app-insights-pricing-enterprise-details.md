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
ms.openlocfilehash: 6e7591ccf0f21099474a08dda088422c377135f6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="enterprise-plan-details"></a>Détails du plan Entreprise

Application Insights a deux plans de tarification. Le plan par défaut dit [De base](app-insights-pricing.md) intègre les mêmes fonctionnalités que le plan Entreprise sans frais supplémentaires. Il est facturé principalement d’après le volume de données consommé. Si vous utilisez Operations Management Suite, vous devez choisir le plan Entreprise. Ce dernier est facturé d’après le nombre de nœuds et les dotations de données quotidiennes. Vous êtes ensuite facturé pour toutes les données consommées en supplément de la dotation choisie.

Pour connaître les prix actuels dans votre devise et pour votre région, consultez la [page de tarification d’Application Insights](http://azure.microsoft.com/pricing/details/application-insights/).

## <a name="heres-how-the-enterprise-plan-works"></a>Voici comment le plan Entreprise fonctionne

* Vous payez pour chaque nœud qui envoie des données de télémétrie pour n'importe quelle application dans le plan Entreprise.
 * Un *nœud* correspond à une machine serveur virtuelle ou physique ou à une instance de rôle Platform-as-a-Service qui héberge votre application.
 * Les ordinateurs de développement, les navigateurs clients et les appareils mobiles ne sont pas comptés comme nœuds.
 * Si votre application comporte plusieurs composants qui envoient des données de télémétrie, comme un service web et un Worker back-end, ces composants sont comptés séparément.
 * Les données [Flux de métriques en temps réel](app-insights-live-stream.md) ne sont pas comptabilisées dans la tarification.* Dans un abonnement, vos frais sont calculés par nœud et non par application. Si vous avez cinq nœuds envoyant des données de télémétrie pour 12 applications, les frais sont calculés pour cinq nœuds.
* Bien que les frais indiqués soient par mois, vous êtes facturé uniquement pour toutes les heures dans lesquelles un nœud envoie des données de télémétrie à partir d’une application. Le tarif horaire est le prix mensuel indiqué / 744 (le nombre d’heures dans un mois de 31 jours).
* Une allocation de volume de données de 200 Mo par jour est accordée pour chaque nœud détecté (avec une granularité par heure). L’allocation des données inutilisées n'est pas reportée d’un jour à l’autre.
 * Lorsque vous choisissez l’option de tarification Entreprise, chaque abonnement reçoit un volume quotidien de données selon le nombre de nœuds qui envoient des données de télémétrie aux ressources Application Insights dans cet abonnement. Par conséquent, si vous avez 5 nœuds qui envoient des données toute la journée, une allocation groupée de 1 Go est appliquée à toutes les ressources Application Insights dans cet abonnement. Le fait que certains nœuds envoient plus de données que d’autres a peu d'importance, car les données incluses sont partagées entre tous les nœuds. Si, un jour donné, les ressources d’Application Insights reçoivent plus de données que le volume quotidien alloué pour cet abonnement, les frais de données de dépassement par Go s’appliquent. 
 * Le volume de données quotidien alloué est calculé comme suit : nombre d’heures quotidiennes (UTC) pendant lesquelles chaque nœud envoie des données de télémétrie, divisé par 24 fois 200 Mo. Par conséquent, si 4 nœuds envoient des données de télémétrie pendant 15 heures sur les 24 heures de la journée, les données incluses pour le jour en question sont calculées comme suit : ((4 x 15)/24) x 200 Mo = 500 Mo. Au prix de 2,30 USD par Go pour le dépassement de données, les frais s'élèvent à 1,15 USD si les nœuds envoient 1 Go de données dans cette journée.
 * Le plan Entreprise n’est pas partagé avec les applications pour lesquelles vous avez choisi l’option De base et le volume alloué inutilisé n’est pas reporté de jour en jour. 

## <a name="here-are-some-examples-of-determining-distinct-node-count"></a>Voici quelques exemples de détermination du nombre de nœuds distincts :

| Scénario                               | Nombre total de nœuds quotidien |
|:---------------------------------------|:----------------:|
| 1 application utilise 3 instances d'Azure App Service et 1 serveur virtuel | 4 |
| 3 applications s’exécutant sur 2 machines virtuelles et les ressources Application Insights pour ces applications sont dans le même abonnement et dans le plan Entreprise | 2 | 
| 4 applications dont les ressources Application Insights sont dans le même abonnement. Chaque application exécute 2 instances pendant 16 heures creuses et 4 instances pendant 8 heures de pointe. | 13.33 | 
| Services cloud avec 1 rôle de travail et 1 rôle web, chacune exécutant 2 instances | 4 | 
| Cluster de 5 nœuds Service Fabric exécutant 50 micro-services, chaque micro-service exécutant 3 instances | 5.|

* La méthode de comptage de nœuds précise varie selon le Kit de développement logiciel (SDK) Application Insights que votre application utilise. 
  * Dans les versions du Kit de développement logiciel 2.2 et ultérieures, le [Kit de développement logiciel (SDK) principal](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) ou [le Kit de développement logiciel (SDK) web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) Application Insights signale chaque hôte d’application en tant que nœud, par exemple le nom d’ordinateur pour le serveur physique et les hôtes de machine virtuelle ou le nom d’instance dans le cas de services cloud.  La seule exception concerne les applications qui utilisent uniquement [.NET Core](https://dotnet.github.io/) et le Kit de développement logiciel (SDK) principal Application Insights, auquel cas un seul nœud sera signalé pour tous les hôtes, car le nom d’hôte n’est pas disponible. 
  * Pour les versions antérieures du Kit de développement logiciel (SDK), le [Kit de développement logiciel (SDK) web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) se comporte comme les nouvelles versions du Kit de développement logiciel (SDK). Toutefois, le [Kit de développement logiciel (SDK) principal](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) ne signale qu’un seul nœud, quel que soit le nombre d’hôtes d’application actuel. 
  * Si votre application utilise le Kit de développement logiciel (SDK) pour définir l’instance de rôle (roleInstance) sur une valeur personnalisée, cette même valeur est utilisée par défaut pour déterminer le nombre de nœuds. 
  * Si vous utilisez une nouvelle version du Kit de développement logiciel (SDK) avec une application exécutée à partir d’ordinateurs clients ou d'appareils mobiles, il est possible que le nombre de nœuds retourné soit très important (en raison du grand nombre d’ordinateurs clients ou d'appareils mobiles). 
