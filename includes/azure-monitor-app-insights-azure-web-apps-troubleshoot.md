---
ms.topic: include
ms.date: 09/03/2021
ms.author: lagayhar
author: lgayhardt
services: azure-monitor
ms.subservice: application-insights
ms.openlocfilehash: 327ab19750615c7912eeffdd520efa896314f1d4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602681"
---
### <a name="connection-string-and-instrumentation-key"></a>Chaîne de connexion et clé d’instrumentation

Lorsque l’analyse sans code est utilisée, seule la chaîne de connexion est requise. Toutefois, nous vous recommandons quand même de définir la clé d’instrumentation pour préserver la compatibilité descendante avec les versions antérieures du SDK lorsque l’instrumentation manuelle est exécutée.

### <a name="difference-between-standard-metrics-from-application-insights-vs-azure-app-service-metrics"></a>Quelles sont les différence entre les métriques standard d’Application Insights et les métriques d’Azure App Service ?

Application Insights collecte des données de télémétrie pour les requêtes qui les ont transmises à l’application. Si la défaillance s’est produite dans WebApps/WebServer et que la requête n’a pas atteint l’application utilisateur, Application Insights n’aura pas de télémétrie à son sujet.

La durée de `serverresponsetime` calculée par Application Insights ne correspond pas nécessairement au temps de réponse du serveur observé par Web Apps. Cela est dû au fait qu’Application Insights compte uniquement la durée pendant laquelle la requête réelle atteint l’application utilisateur. Si la requête est bloquée/mise en file d’attente dans WebServer, le temps d’attente sera inclus dans les métriques de WebApps, mais pas dans celles d’Application Insights.


