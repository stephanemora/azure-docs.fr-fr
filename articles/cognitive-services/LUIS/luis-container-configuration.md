---
title: Paramètres de conteneur Docker
titleSuffix: Language Understanding - Azure Cognitive Services
description: L’environnement d’exécution de conteneur LUIS est configuré à l’aide des arguments de la commande `docker run`. LUIS a plusieurs paramètres obligatoires et quelques paramètres facultatifs.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: dapine
ms.openlocfilehash: 4a9f7762b7960c74acad8203f70bc1e7c7cbd90f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063225"
---
# <a name="configure-language-understanding-docker-containers"></a>Configurer les conteneurs Docker Language Understanding 

L'environnement d'exécution de conteneur **Language Understanding** (LUIS) est configuré à l'aide des arguments de la commande `docker run`. LUIS a plusieurs paramètres obligatoires et quelques paramètres facultatifs. Plusieurs [exemples](#example-docker-run-commands) de commande sont disponibles. Les paramètres propres aux conteneurs sont les [paramètres de montage](#mount-settings) d’entrée et les paramètres de facturation. 

## <a name="configuration-settings"></a>Paramètres de configuration

Ce conteneur a les paramètres de configuration suivants :

|Obligatoire|Paramètre|Objectif|
|--|--|--|
|OUI|[ApiKey](#apikey-setting)|Utilisé pour le suivi des informations de facturation.|
|Non|[ApplicationInsights](#applicationinsights-setting)|Vous permet d’ajouter la prise en charge de la télémétrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) à votre conteneur.|
|OUI|[Billing](#billing-setting)|Spécifie l’URI de point de terminaison de la ressource de service sur Azure.|
|OUI|[Eula](#eula-setting)| Indique que vous avez accepté la licence pour le conteneur.|
|Non|[Fluentd](#fluentd-settings)|Écrire les données des journaux et, éventuellement, des métriques, sur un serveur Fluentd.|
|Non|[Proxy HTTP](#http-proxy-credentials-settings)|Configurer un proxy HTTP pour effectuer des requêtes sortantes.|
|Non|[Logging](#logging-settings)|Fournit la prise en charge de la journalisation ASP.NET Core pour votre conteneur. |
|OUI|[Mounts](#mount-settings)|Lire et écrire des données de l’ordinateur hôte sur le conteneur, et du conteneur sur l’ordinateur hôte.|

> [!IMPORTANT]
> Les paramètres [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting) et [`Eula`](#eula-setting) sont utilisés conjointement, et vous devez fournir des valeurs valides pour les trois ; à défaut, votre conteneur ne démarrera pas. Pour plus d’informations sur l’instanciation d’un conteneur à l’aide de ces paramètres de configuration, consultez [Facturation](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Paramètre ApiKey

Le paramètre `ApiKey` spécifie la clé de ressource Azure utilisée pour effectuer le suivi des informations de facturation pour le conteneur. Vous devez donner une valeur à ApiKey, qui doit être une clé valide pour la ressource _Cognitive Services_ spécifiée dans le paramètre de configuration [`Billing`](#billing-setting).

Vous trouverez ce paramètre aux emplacements suivants :

* Portail Azure : Gestion des ressources **Cognitive Services**, sous **Clés**
* Portail LUIS : Page **Keys and Endpoint settings** (Paramètres des clés et du point de terminaison). 

N’utilisez pas la clé de démarrage ou la clé de création. 

## <a name="applicationinsights-setting"></a>Paramètre ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Paramètre Billing

Le paramètre `Billing` permet de spécifier l’URI de point de terminaison de la ressource _Cognitive Services_ sur Azure servant à effectuer l’analyse des informations de facturation du conteneur. Vous devez donner une valeur à ce paramètre de configuration, qui doit être un URI de point de terminaison valide pour une ressource _Cognitive Services_ dans Azure. Le conteneur crée des rapports sur l’utilisation toutes les 10 à 15 minutes.

Vous trouverez ce paramètre aux emplacements suivants :

* Portail Azure : Vue d’ensemble **Cognitive Services**, étiquetée `Endpoint`
* Portail LUIS : Page **Keys and Endpoint settings** (Paramètres des clés et du point de terminaison), dans le cadre de l’URI de point de terminaison.

N’oubliez pas d’inclure le routage `luis/v2.0` dans l’URL, comme indiqué dans le tableau suivant :


|Obligatoire| Nom | Type de données | Description |
|--|------|-----------|-------------|
|OUI| `Billing` | Chaîne | URI du point de terminaison de facturation<br><br>Exemple :<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

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

Le conteneur LUIS n’utilise pas de montage d’entrée ou de sortie pour stocker des données d’entraînement ou de service. 

La syntaxe exacte de l’emplacement de montage d’hôte varie en fonction du système d’exploitation hôte. De plus, l’emplacement de montage de l’[ordinateur hôte](luis-container-howto.md#the-host-computer) peut ne pas être accessible en raison d’un conflit entre les autorisations utilisées par le compte de service docker et les autorisations de l’emplacement de montage de l’hôte. 

Le tableau suivant décrit les paramètres pris en charge.

|Obligatoire| Nom | Type de données | Description |
|-------|------|-----------|-------------|
|OUI| `Input` | Chaîne | Cible du montage d’entrée. La valeur par défaut est `/input`. Il s’agit de l’emplacement des fichiers de package LUIS. <br><br>Exemple :<br>`--mount type=bind,src=c:\input,target=/input`|
|Non| `Output` | Chaîne | Cible du montage de sortie. La valeur par défaut est `/output`. Il s’agit de l’emplacement des journaux d’activité. Cela comprend les journaux d’activité de requêtes LUIS et les journaux d’activité de conteneur. <br><br>Exemple :<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exemples de commandes docker run

Les exemples suivants utilisent les paramètres de configuration pour illustrer comment écrire et utiliser des commandes `docker run`.  Une fois en cours d’exécution, le conteneur continue à s’exécuter jusqu’à ce que vous l’[arrêtiez](luis-container-howto.md#stop-the-container).

* Ces exemples utilisent le répertoire du lecteur `c:` pour éviter tout conflit d’autorisation sur Windows. Si vous devez utiliser un répertoire spécifique en tant que répertoire d’entrée, vous devrez peut-être accorder au docker une autorisation de service. 
* Ne changez pas l’ordre des arguments, sauf si vous avez une connaissance approfondie des conteneurs docker.
* Si vous utilisez un autre système d’exploitation, utilisez la console et/ou le terminal approprié, la syntaxe de dossier pour les montages et le caractère de continuation de ligne pour votre système. Dans ces exemples, une console Windows avec un caractère de continuation de ligne `^` est censée être utilisée. Le conteneur étant un système d’exploitation Linux, le montage cible utilise une syntaxe de dossier de type Linux.

N’oubliez pas d’inclure le routage `luis/v2.0` dans l’URL, comme indiqué dans le tableau suivant.

Remplacez {_argument_name_} par vos propres valeurs :

| Placeholder | Valeur | Format ou exemple |
|-------------|-------|---|
|{ENDPOINT_KEY} | Clé de point de terminaison de l’application LUIS entraînée. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | La valeur de point de terminaison de facturation est disponible dans la page Vue d’ensemble Azure `Cognitive Services`. |https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](luis-container-howto.md#billing).
> La valeur ApiKey est la **clé** mentionnée dans la page Keys and Endpoints dans le portail LUIS. Elle est également disponible dans la page Clés de ressources d’Azure `Cognitive Services`. 

### <a name="basic-example"></a>Exemple de base

L’exemple suivant contient le moins d’arguments possible pour exécuter le conteneur :

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={BILLING_ENDPOINT} ^
ApiKey={ENDPOINT_KEY}
```

### <a name="applicationinsights-example"></a>Exemple ApplicationInsights

L’exemple suivant définit l’argument ApplicationInsights pour envoyer des données de télémétrie à Application Insights pendant l’exécution du conteneur :

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={BILLING_ENDPOINT} ^
ApiKey={ENDPOINT_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Exemple de journalisation 

La commande suivante définit le niveau de journalisation, `Logging:Console:LogLevel`, pour configurer le niveau de journalisation sur [`Information`](https://msdn.microsoft.com). 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={BILLING_ENDPOINT} ^
ApiKey={ENDPOINT_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Guide pratique pour installer et exécuter des conteneurs](luis-container-howto.md).
* Reportez-vous à la section [Résolution des problèmes](troubleshooting.md) pour résoudre les problèmes liés à la fonctionnalité LUIS.
* Utiliser davantage de [conteneurs Cognitive Services](../cognitive-services-container-support.md)
