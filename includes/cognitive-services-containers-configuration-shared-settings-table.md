---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 57e54c4c3b8ae44d42051c87356f1feeaa5a3f10
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977206"
---
Ce conteneur a les paramètres de configuration suivants :

|Obligatoire|Paramètre|Objectif|
|--|--|--|
|Oui|[ApiKey](#apikey-setting)|Utilisé pour le suivi des informations de facturation.|
|Non |[ApplicationInsights](#applicationinsights-setting)|Vous permet d’ajouter la prise en charge de la télémétrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) à votre conteneur.|
|Oui|[Facturation](#billing-setting)|Spécifie l’URI de point de terminaison de la ressource de service sur Azure.|
|Oui|[Eula](#eula-setting)| Indique que vous avez accepté la licence pour le conteneur.|
|Non |[Fluentd](#fluentd-settings)|Écrire les données des journaux et, éventuellement, des métriques, sur un serveur Fluentd.|
|Non |[Journalisation](#logging-settings)|Fournit la prise en charge de la journalisation ASP.NET Core pour votre conteneur. |
|Non |[Mounts](#mount-settings)|Lire et écrire des données de l’ordinateur hôte sur le conteneur, et du conteneur sur l’ordinateur hôte.|
