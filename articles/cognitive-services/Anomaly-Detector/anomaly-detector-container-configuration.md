---
title: Configurer des conteneurs - détecteur d’anomalies
titleSuffix: Azure Cognitive Services
description: L’environnement d’exécution de détecteur d’anomalies conteneur est configuré à l’aide de le `docker run` arguments de la commande. Ce conteneur a plusieurs paramètres obligatoires et quelques paramètres facultatifs.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/07/2019
ms.author: aahi
ms.openlocfilehash: 0d09ce29aa5431de3eb82e5d9fe7440d4e3352e1
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026389"
---
# <a name="configure-anomaly-detector-containers"></a>Configurer des conteneurs de détecteur d’anomalies

Le **détecteur d’anomalies** environnement d’exécution de conteneur est configuré à l’aide de le `docker run` arguments de la commande. Ce conteneur a plusieurs paramètres obligatoires et quelques paramètres facultatifs. Plusieurs [exemples](#example-docker-run-commands) de commande sont disponibles. Les paramètres propres aux conteneurs correspondent aux paramètres de facturation. 

# <a name="configuration-settings"></a>Paramètres de configuration

Ce conteneur a les paramètres de configuration suivants :

|Obligatoire|Paramètre|Objectif|
|--|--|--|
|Oui|[ApiKey](#apikey-configuration-setting)|Utilisé pour le suivi des informations de facturation.|
|Non |[ApplicationInsights](#applicationinsights-setting)|Vous permet d’ajouter la prise en charge de la télémétrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) à votre conteneur.|
|Oui|[Billing](#billing-configuration-setting)|Spécifie l’URI de point de terminaison de la ressource de service sur Azure.|
|Oui|[Eula](#eula-setting)| Indique que vous avez accepté la licence pour le conteneur.|
|Non |[Fluentd](#fluentd-settings)|Écrire les données des journaux et, éventuellement, des métriques, sur un serveur Fluentd.|
|Non |[Proxy HTTP](#http-proxy-credentials-settings)|Configurer un proxy HTTP pour effectuer des requêtes sortantes.|
|Non |[Logging](#logging-settings)|Fournit la prise en charge de la journalisation ASP.NET Core pour votre conteneur. |
|Non |[Mounts](#mount-settings)|Lire et écrire des données de l’ordinateur hôte sur le conteneur, et du conteneur sur l’ordinateur hôte.|

> [!IMPORTANT]
> Les paramètres [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) et [`Eula`](#eula-setting) sont utilisés conjointement, et vous devez fournir des valeurs valides pour les trois ; à défaut, votre conteneur ne démarrera pas. Pour plus d’informations sur l’instanciation d’un conteneur à l’aide de ces paramètres de configuration, consultez [Facturation](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Paramètre de configuration ApiKey

Le paramètre `ApiKey` spécifie la clé de ressource Azure utilisée pour effectuer le suivi des informations de facturation pour le conteneur. Vous devez spécifier une valeur pour la clé API et la valeur doit être une clé valide pour le _détecteur d’anomalies_ ressource spécifiée pour le [ `Billing` ](#billing-configuration-setting) paramètre de configuration.

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : **Du détecteur d’anomalies** gestion des ressources, sous **clés**

## <a name="applicationinsights-setting"></a>Paramètre ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Paramètre de configuration Billing

Le `Billing` paramètre spécifie l’URI de point de terminaison de la _détecteur d’anomalies_ ressources sur Azure permet de contrôler les informations de facturation pour le conteneur. Vous devez spécifier une valeur pour ce paramètre de configuration, et la valeur doit être un URI de point de terminaison valide pour un _détecteur d’anomalies_ ressources sur Azure.

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : **Du détecteur d’anomalies** vue d’ensemble, étiqueté `Endpoint`

|Obligatoire| Nom | Type de données | Description |
|--|------|-----------|-------------|
|Oui| `Billing` | Chaîne | URI du point de terminaison de facturation<br><br>Exemple :<br>`Billing=https://westus2.api.cognitive.microsoft.com` |

## <a name="eula-setting"></a>Paramètre Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Paramètres Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Paramètres des informations d'identification du proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Paramètres de journalisation
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Paramètres de montage

Utilisez des montages de liaisons pour lire et écrire des données vers et à partir du conteneur. Vous pouvez spécifier un montage d’entrée ou de sortie en spécifiant l’option `--mount` dans la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Les conteneurs de détecteur d’anomalies n’utilisent pas d’entrée ou sortie monte à stocker des données de service ou de formation. 

La syntaxe exacte de l’emplacement de montage d’hôte varie en fonction du système d’exploitation hôte. De plus, l’emplacement de montage de l’[ordinateur hôte](anomaly-detector-container-howto.md#the-host-computer) peut ne pas être accessible en raison d’un conflit entre les autorisations utilisées par le compte de service Docker et les autorisations de l’emplacement de montage de l’hôte. 

|Facultatif| Nom | Type de données | Description |
|-------|------|-----------|-------------|
|Non autorisé| `Input` | String | Conteneurs de détecteur d’anomalies n’utilisent pas cela.|
|Facultatif| `Output` | Chaîne | Cible du montage de sortie. La valeur par défaut est `/output`. Il s’agit de l’emplacement des journaux d’activité. Les journaux d’activité de conteneur sont inclus. <br><br>Exemple :<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exemples de commandes docker run 

Les exemples suivants utilisent les paramètres de configuration pour illustrer comment écrire et utiliser des commandes `docker run`.  Une fois en cours d’exécution, le conteneur continue à s’exécuter jusqu’à ce que vous l’[arrêtiez](anomaly-detector-container-howto.md#stop-the-container).

* **Caractère de continuation de ligne** : Les commandes de Docker dans les sections suivantes utilisent la barre oblique inverse, `\`, comme un caractère de continuation de ligne pour un interpréteur de commandes bash. Remplacez-la ou supprimez-la en fonction des exigences de votre système d’exploitation hôte. Par exemple, le caractère de continuation de ligne pour windows est un point d’insertion, `^`. Remplacez la barre oblique inverse par le signe insertion. 
* **Ordre des arguments** : Ne changez pas l’ordre des arguments, sauf si vous avez une connaissance approfondie des conteneurs Docker.

Remplacez la valeur entre crochets, `{}`, avec vos propres valeurs :

| Placeholder | Valeur | Format ou exemple |
|-------------|-------|---|
|{BILLING_KEY} | La clé de point de terminaison de la ressource de détecteur d’anomalies. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Valeur de point de terminaison de facturation, région comprise.|`https://westus2.api.cognitive.microsoft.com`|

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](anomaly-detector-container-howto.md#billing).
> La valeur de clé API est la **clé** à partir de la page clés de ressource de détecteur d’anomalies Azure. 

## <a name="anomaly-detector-container-docker-examples"></a>Exemples de Docker de conteneur de détecteur d’anomalies

Les exemples suivants de Docker sont pour le conteneur de détecteur d’anomalies. 

### <a name="basic-example"></a>Exemple de base 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Exemple de journalisation avec arguments de ligne de commande

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```
