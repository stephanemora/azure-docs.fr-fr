---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2019
ms.openlocfilehash: 4f9300ab1d688e1f5043f5b919e70c5a36c7c0e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064003"
---
Le conteneur a les paramètres de configuration suivants :

|Obligatoire|Paramètre|Objectif|
|--|--|--|
|OUI|[ApiKey](#apikey-configuration-setting)|Assure le suivi des informations de facturation.|
|Non|[ApplicationInsights](#applicationinsights-setting)|Permet d’ajouter la prise en charge de la télémétrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) à votre conteneur.|
|OUI|[Billing](#billing-configuration-setting)|Spécifie l’URI de point de terminaison de la ressource de service sur Azure.|
|OUI|[Eula](#eula-setting)| Indique que vous avez accepté la licence pour le conteneur.|
|Non|[Fluentd](#fluentd-settings)|Écrit les données des journaux et, éventuellement, des métriques, sur un serveur Fluentd.|
|Non|Proxy HTTP|Configure un proxy HTTP pour effectuer des requêtes sortantes.|
|Non|[Logging](#logging-settings)|Fournit la prise en charge de la journalisation ASP.NET Core pour votre conteneur. |
|Non|[Mounts](#mount-settings)|Lit et écrit des données de l’ordinateur hôte sur le conteneur, et du conteneur sur l’ordinateur hôte.|
