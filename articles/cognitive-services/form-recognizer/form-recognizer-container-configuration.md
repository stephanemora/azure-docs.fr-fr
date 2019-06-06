---
title: Configurer le conteneur - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Découvrez comment configurer le conteneur Form Recognizer pour analyser des données de formulaire et de table.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/31/2019
ms.author: pafarley
ms.openlocfilehash: 28acc2d1eafacb9e53fac3e3cce092738401f838
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475388"
---
# <a name="configure-form-recognizer-containers"></a>Configurer des conteneurs Form Recognizer

Les conteneurs Form Recognizer permettent aux clients de créer une architecture d’application optimisée pour tirer parti des fonctionnalités robustes du cloud et de la localité en périphérie.

L’environnement d’exécution de conteneur **Form Recognizer** est configuré à l’aide des arguments de la commande `docker run`. Ce conteneur a plusieurs paramètres obligatoires et quelques paramètres facultatifs. Plusieurs [exemples](#example-docker-run-commands) de commande sont disponibles. Les paramètres propres aux conteneurs correspondent aux paramètres de facturation.

## <a name="configuration-settings"></a>Paramètres de configuration

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Les paramètres [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) et [`Eula`](#eula-setting) sont utilisés conjointement, et vous devez fournir des valeurs valides pour les trois ; à défaut, votre conteneur ne démarrera pas. Pour plus d’informations sur l’instanciation d’un conteneur à l’aide de ces paramètres de configuration, consultez [Facturation](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Paramètre de configuration ApiKey

Le paramètre `ApiKey` spécifie la clé de ressource Azure utilisée pour effectuer le suivi des informations de facturation pour le conteneur. Vous devez spécifier une valeur pour ApiKey, il doit s’agir d’une clé valide de la ressource _Form Recognizer_ spécifiée pour le paramètre de configuration [`Billing`](#billing-configuration-setting).

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : Gestion des ressources de **Form Recognizer**, sous **Clés**

## <a name="applicationinsights-setting"></a>Paramètre ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Paramètre de configuration Billing

Le paramètre `Billing` permet de spécifier l’URI du point de terminaison de la ressource _Form Recognizer_ sur Azure servant à mesurer les informations de facturation du conteneur. Vous devez affecter à ce paramètre de configuration une valeur correspondant à un URI de point de terminaison valide pour une ressource _Form Recognizer_ sur Azure. Le conteneur crée des rapports sur l’utilisation toutes les 10 à 15 minutes.

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : Vue d’ensemble de **Form Recognizer**, étiqueté `Endpoint`

|Obligatoire| Nom | Type de données | Description |
|--|------|-----------|-------------|
|OUI| `Billing` | Chaîne | URI du point de terminaison de facturation<br><br>Exemple :<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

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

Le conteneur Form Recognizer nécessite un montage d’entrée et de sortie. Le montage d’entrée peut être en lecture seule. Il est indispensable pour accéder aux données qui sont utilisées dans l’entraînement et le scoring. Le montage de sortie doit être accessible en écriture. Il sert à stocker les modèles et les données temporaires.

La syntaxe exacte de l’emplacement de montage d’hôte varie en fonction du système d’exploitation hôte. De plus, l’emplacement de montage de l’[ordinateur hôte](form-recognizer-container-howto.md#the-host-computer) peut ne pas être accessible en raison d’un conflit entre les autorisations utilisées par le compte de service Docker et les autorisations de l’emplacement de montage de l’hôte.

|Facultatif| Nom | Type de données | Description |
|-------|------|-----------|-------------|
|Obligatoire| `Input` | Chaîne | Cible du montage d’entrée. La valeur par défaut est `/input`.    <br><br>Exemple :<br>`--mount type=bind,src=c:\input,target=/input`|
|Obligatoire| `Output` | Chaîne | Cible du montage de sortie. La valeur par défaut est `/output`.  <br><br>Exemple :<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exemples de commandes docker run

Les exemples suivants utilisent les paramètres de configuration pour illustrer comment écrire et utiliser des commandes `docker run`.  Une fois en cours d’exécution, le conteneur continue à s’exécuter jusqu’à ce que vous l’[arrêtiez](form-recognizer-container-howto.md#stop-the-container).

* **Caractère de continuation de ligne** : Les commandes Docker dans les sections suivantes utilisent la barre oblique inverse, `\`, comme caractère de continuation de ligne. Remplacez-la ou supprimez-la en fonction des exigences de votre système d’exploitation hôte.
* **Ordre des arguments** : Ne changez pas l’ordre des arguments, sauf si vous avez une connaissance approfondie des conteneurs Docker.

Remplacez {_argument_name_} par vos propres valeurs :

| Placeholder | Valeur |
|-------------|-------|
|{BILLING_KEY} | Cette clé, qui permet de démarrer le conteneur, est disponible dans la page Clés de Form Recognizer du portail Azure.  |
|{BILLING_ENDPOINT_URI} | La valeur de l’URI du point de terminaison de facturation est disponible dans la page Vue d’ensemble de Form Recognizer du portail Azure.|
|{COMPUTER_VISION_API_KEY}| La clé est disponible dans la page Clés de l’API Vision par ordinateur du portail Azure.|
|{COMPUTER_VISION_ENDPOINT_URI}|Le point de terminaison de facturation. Si vous utilisez une ressource informatique Vision par ordinateur, la valeur de l’URI est disponible dans la page Vue d’ensemble de l’API Vision par ordinateur du portail Azure. Si vous utilisez un conteneur `cognitive-services-recognize-text`, utilisez l’URL du point de terminaison de facturation passée au conteneur dans la commande `docker run`.|

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing-configuration-setting).
> La valeur ApiKey est la **Clé** présente dans la page des clés de la ressource Azure Form Recognizer.

## <a name="form-recognizer-container-docker-examples"></a>Exemples Docker de conteneurs Form Recognizer

Les exemples Docker suivants s’appliquent au conteneur Form Recognizer.

### <a name="basic-example-for-form-recognizer"></a>Exemple de base pour Form Recognizer

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Exemple de journalisation pour Form Recognizer

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>Étapes suivantes

* Consultez [Guide pratique pour installer et exécuter des conteneurs](form-recognizer-container-howto.md).
