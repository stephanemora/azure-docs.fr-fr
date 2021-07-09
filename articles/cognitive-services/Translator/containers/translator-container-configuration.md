---
title: Configurer des conteneurs - Translator
titleSuffix: Azure Cognitive Services
description: L’environnement d’exécution de conteneur Translator est configuré à l’aide des arguments de la commande `docker run`. Il s'agit de paramètres obligatoires et facultatifs.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: fa31cafb414792a5feef7207156ecfb2c0b68778
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110652858"
---
# <a name="configure-translator-docker-containers-preview"></a>Configurer des conteneurs Docker Translator (préversion)

Cognitive Services fournit une infrastructure de configuration commune à chaque conteneur.  Vous pouvez facilement configurer vos conteneurs Translator qui vous permettent de créer une architecture d’application Translator optimisée pour tirer parti des fonctionnalités robustes du cloud et de la localité en périphérie.

L’environnement d’exécution de conteneur **Translator** est configuré à l’aide des arguments de la commande `docker run`. Ce conteneur a plusieurs paramètres obligatoires et quelques paramètres facultatifs. Les paramètres propres aux conteneurs correspondent aux paramètres de facturation.

## <a name="configuration-settings"></a>Paramètres de configuration

Le conteneur a les paramètres de configuration suivants :

|Obligatoire|Paramètre|Objectif|
|--|--|--|
|Oui|[ApiKey](#apikey-configuration-setting)|Assure le suivi des informations de facturation.|
|Non|[ApplicationInsights](#applicationinsights-setting)|Permet d’ajouter la prise en charge de la télémétrie [Azure Application Insights](/azure/application-insights) à votre conteneur.|
|Oui|[Billing](#billing-configuration-setting)|Spécifie l’URI de point de terminaison de la ressource de service sur Azure.|
|Oui|[Eula](#eula-setting)| Indique que vous avez accepté la licence pour le conteneur.|
|Non|[Fluentd](#fluentd-settings)|Écrit les données des journaux et, éventuellement, des métriques, sur un serveur Fluentd.|
|Non|Proxy HTTP|Configure un proxy HTTP pour effectuer des requêtes sortantes.|
|Non|[Logging](#logging-settings)|Fournit la prise en charge de la journalisation ASP.NET Core pour votre conteneur. |
|Oui|[Mounts](#mount-settings)|Lit et écrit des données de l’ordinateur hôte sur le conteneur, et du conteneur sur l’ordinateur hôte.|

 > [!IMPORTANT]
> Les paramètres [**ApiKey**](#apikey-configuration-setting), [**Billing**](#billing-configuration-setting) et [**EULA**](#eula-setting) sont utilisés conjointement, et vous devez fournir des valeurs valides pour les trois ; à défaut, votre conteneur ne démarrera pas. Obtenez plus d’informations sur l’instanciation d’un conteneur à l’aide de ces paramètres de configuration.

## <a name="apikey-configuration-setting"></a>Paramètre de configuration ApiKey

Le paramètre `ApiKey` spécifie la clé de ressource Azure utilisée pour effectuer le suivi des informations de facturation pour le conteneur. Vous devez spécifier une valeur pour ApiKey, qui doit être une clé valide pour la ressource _Translator_ spécifiée dans le paramètre de configuration [`Billing`](#billing-configuration-setting).

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : Gestion des ressources **Translator**, sous **Clés**

## <a name="applicationinsights-setting"></a>Paramètre ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Paramètre de configuration Billing

Le paramètre `Billing` permet de spécifier l’URI de point de terminaison de la ressource _Translator_ sur Azure servant à mesurer les informations de facturation du conteneur. Vous devez affecter à ce paramètre de configuration une valeur correspondant à un URI de point de terminaison valide pour une ressource _Translator_ sur Azure. Le conteneur crée des rapports sur l’utilisation toutes les 10 à 15 minutes.

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : page Vue d’ensemble de **Translator**, avec l’étiquette `Endpoint`

| Obligatoire | Nom | Type de données | Description |
| -------- | ---- | --------- | ----------- |
| Oui | `Billing` | String | URI de point de terminaison de facturation. Pour plus d’informations sur la façon d’obtenir l’URI de facturation, consultez [Collecte des paramètres requis](translator-how-to-install-container.md#required-elements). Pour obtenir plus d’informations et une liste complète des points de terminaison régionaux, consultez [Noms de sous-domaines personnalisés pour Cognitive Services](../../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Paramètre Eula

[!INCLUDE [Container shared configuration eula settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Paramètres Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Paramètres des informations d’identification du proxy HTTP

[!INCLUDE [Container shared HTTP proxy settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Paramètres de journalisation

[!INCLUDE [Container shared configuration logging settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Paramètres de montage

Utilisez des montages de liaisons pour lire et écrire des données vers et à partir du conteneur. Vous pouvez spécifier un montage d’entrée ou de sortie en spécifiant l’option `--mount` dans la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les conteneurs Cognitive Services](../../cognitive-services-container-support.md)
