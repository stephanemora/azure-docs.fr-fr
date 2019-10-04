---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 46376477aae9f94c1c8f6e1dd6bc718d213cc373
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712528"
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
