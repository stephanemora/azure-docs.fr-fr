---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2019
ms.openlocfilehash: c4abc5fa89b48b6fc55637e9ff3b259387d0d410
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796401"
---
Ce conteneur a les paramètres de configuration suivants :

|Obligatoire|Paramètre|Objectif|
|--|--|--|
|OUI|[ApiKey](#apikey-configuration-setting)|Utilisé pour le suivi des informations de facturation.|
|Non |[ApplicationInsights](#applicationinsights-setting)|Vous permet d’ajouter la prise en charge de la télémétrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) à votre conteneur.|
|OUI|[Billing](#billing-configuration-setting)|Spécifie l’URI de point de terminaison de la ressource de service sur Azure.|
|OUI|[Eula](#eula-setting)| Indique que vous avez accepté la licence pour le conteneur.|
|Non |[Fluentd](#fluentd-settings)|Écrire les données des journaux et, éventuellement, des métriques, sur un serveur Fluentd.|
|Non |Proxy HTTP|Configurer un proxy HTTP pour effectuer des requêtes sortantes.|
|Non |[Logging](#logging-settings)|Fournit la prise en charge de la journalisation ASP.NET Core pour votre conteneur. |
|Non |[Mounts](#mount-settings)|Lire et écrire des données de l’ordinateur hôte sur le conteneur, et du conteneur sur l’ordinateur hôte.|
