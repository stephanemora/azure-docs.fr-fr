---
title: Présentation d’Azure SignalR | Microsoft Docs
description: Présentation du service Azure SignalR.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/17/2018
ms.author: zhshang
ms.openlocfilehash: c574a3dd26b36b656cc931a0801cbf0ef23cf362
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43668239"
---
# <a name="what-is-azure-signalr-service"></a>Présentation du service Azure SignalR

Le service Microsoft Azure SignalR est actuellement disponible en [version préliminaire publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le service Azure SignalR est un service Azure qui repose sur [ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction), une [bibliothèque open source](https://github.com/aspnet/signalr) qui simplifie le processus d’ajout de fonctionnalités web en temps réel aux applications par HTTP. Ces fonctionnalités en temps réel permettent au serveur web de transmettre des mises à jour de contenu aux clients connectés. Par conséquent, les clients sont mis à jour sans avoir à interroger le serveur ni à envoyer de nouvelles requêtes HTTP de mise à jour.

Cet article donne une vue d’ensemble du service Azure SignalR. Si vous voulez démarrer, commencez par le [Guide de démarrage rapide ASP.NET Core](signalr-quickstart-dotnet-core.md).

## <a name="what-is-signalr-service-used-for"></a>À quoi sert le service SignalR ? 

Il existe de nombreux types d’applications qui nécessitent des mises à jour de contenu en temps réel. Ceux-ci, par exemple, sont de bons candidats pour le service Azure SignalR :

* Les applications ayant besoin de mises à jour fréquentes auprès du serveur. Exemples : jeux, réseaux sociaux, scrutin, enchères, cartes et applications GPS.
* Les tableaux de bord et les applications de monitoring. Exemples : tableaux de bord des entreprises, mises à jour instantanées des ventes et alertes de voyage.
* Les applications de collaboration. Exemples : applications de tableau blanc et logiciels de réunion d’équipe.
* Les applications qui envoient des notifications. Exemples : réseaux sociaux, messagerie, conversation instantanée, jeux, alertes de voyage, etc.

En interne, SignalR est une abstraction sur de nombreuses techniques utilisées pour créer des applications web en temps réel. [WebSocket](https://wikipedia.org/wiki/WebSocket) est le mécanisme de transport optimal, mais d’autres techniques comme [SSE (Server-Sent Events)](https://wikipedia.org/wiki/Server-sent_events) et le Long Polling sont utilisées lorsque les autres options ne sont pas disponibles. SignalR détecte automatiquement et initialise le mode de transport adapté en fonction des fonctionnalités prises en charge sur le serveur et le client.

## <a name="developing-signalr-apps"></a>Développer des applications SignalR

Il existe actuellement deux versions de SignalR que vous pouvez utiliser avec vos applications web : SignalR pour ASP.NET et ASP.NET Core SignalR (la plus récente). Le service Azure SignalR est un service géré par Azure qui repose sur ASP.NET Core SignalR. 

ASP.NET Core SignalR est une nouvelle version entièrement réécrite, qui par conséquent n’offre pas de compatibilité descendante avec la version antérieure de SignalR. Les API et les comportements sont différents. Le Kit de développement logiciel (SDK) ASP.NET Core SignalR est .NET Standard ; il est donc utilisable avec .NET Framework. Il oblige toutefois à utiliser les nouvelles API, plutôt que les anciennes. Si vous utilisez SignalR et que vous souhaitez passer à ASP.NET Core SignalR ou au service Azure SignalR, vous devrez modifier votre code de façon à gérer les différences d’API.

Avec le service Azure SignalR, le composant côté serveur d’ASP.NET Core SignalR est hébergé dans Azure. Il est toutefois possible, dans la mesure où la technologie repose sur ASP.NET Core, d’exécuter l’application web proprement dite sur différentes plateformes (Windows, Linux et macOS) tout en conservant un hébergement [Azure App Service](../app-service/app-service-web-overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache) ou [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index). Vous pouvez également utiliser l’auto-hébergement dans votre propre processus.

Si votre application répond aux objectifs suivants : prendre en charge les dernières fonctionnalités pour fournir des mises à jour de contenu en temps réel aux clients, gérer plusieurs plateformes (Azure, Windows, Linux et macOS) et maintenir un hébergement dans différents environnements, la meilleure solution est peut-être d’utiliser le service Azure SignalR.


## <a name="why-not-deploy-signalr-myself"></a>Pourquoi ne pas déployer SignalR soi-même ?

Vous avez toujours la possibilité de déployer votre application web Azure, compatible avec ASP.NET Core SignalR, sous la forme d’un composant principal de votre application web globale.

L’une des principales raisons d’utiliser le service Azure SignalR est la simplicité. Avec le service Azure SignalR, vous n’avez pas besoin de gérer les problèmes de performances, d’évolutivité ou de disponibilité. Le contrat de niveau de service de 99,9 % s’en occupe pour vous.

Par ailleurs, WebSocket est généralement la technique privilégiée pour prendre en charge des mises à jour de contenu en temps réel. Toutefois, plus le nombre de connexions WebSocket persistantes augmente, plus l’équilibrage de charge devient problématique. Il existe différentes solutions : l’équilibrage de charge DNS, les équilibreurs de charge matériels et l’équilibrage de charge logiciel. Le service Azure SignalR gère ce problème à votre place.

Il se peut également que vous n’ayez pas du tout besoin d’héberger réellement une application web. La logique de votre application web peut tirer parti de [l’informatique sans serveur](https://azure.microsoft.com/overview/serverless-computing/). Par exemple, votre code n’est hébergé et ne s’exécute qu’à la demande avec des déclencheurs [Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Ce scénario peut être complexe, car votre code ne conserve pas longtemps les connexions avec les clients. Le service Azure SignalR est en mesure de gérer cette situation, car il gère déjà les connexions à votre place.

## <a name="how-does-it-scale"></a>Comment fonctionne la mise à l’échelle ?

Il est courant de mettre à l’échelle SignalR avec SQL Server, Azure Service Bus ou le Cache Redis. Le service Azure SignalR gère l’approche de mise à l’échelle à votre place. Si les performances et le coût sont comparables à ces méthodes, il vous épargne la complexité associée à la gestion de ces services. Il suffit de mettre à jour le nombre d’unités du service. Chaque unité de service prend en charge jusqu’à 1 000 connexions clientes.

## <a name="next-steps"></a>Étapes suivantes
* [Guide de démarrage rapide : Créer une salle de conversation avec Azure SignalR](signalr-quickstart-dotnet-core.md)  
  

