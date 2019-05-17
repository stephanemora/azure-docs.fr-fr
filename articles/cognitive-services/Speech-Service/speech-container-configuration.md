---
title: Configurer des conteneurs de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Le conteneur de reconnaissance vocale
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: diberry
ms.openlocfilehash: e2ed29bb61f553f68b9f9802884169361d5d983f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65797923"
---
# <a name="configure-speech-service-containers"></a>Configurer des conteneurs de Service de reconnaissance vocale

Conteneurs de reconnaissance vocale permettent aux clients de créer une architecture d’application de reconnaissance vocale est optimisée pour tirer parti des fonctionnalités de cloud fiable et localité de bord. Les conteneurs de deux vocale que nous prenons en charge désormais sont **parole-texte** et **synthèse vocale**. 

Le **vocale** environnement d’exécution de conteneur est configuré à l’aide de le `docker run` arguments de la commande. Ce conteneur a plusieurs paramètres obligatoires et quelques paramètres facultatifs. Plusieurs [exemples](#example-docker-run-commands) de commande sont disponibles. Les paramètres propres aux conteneurs correspondent aux paramètres de facturation. 

# <a name="configuration-settings"></a>Paramètres de configuration

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Les paramètres [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) et [`Eula`](#eula-setting) sont utilisés conjointement, et vous devez fournir des valeurs valides pour les trois ; à défaut, votre conteneur ne démarrera pas. Pour plus d’informations sur l’instanciation d’un conteneur à l’aide de ces paramètres de configuration, consultez [Facturation](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Paramètre de configuration ApiKey

Le paramètre `ApiKey` spécifie la clé de ressource Azure utilisée pour effectuer le suivi des informations de facturation pour le conteneur. Vous devez spécifier une valeur pour la clé API et la valeur doit être une clé valide pour le _vocale_ ressource spécifiée pour le [ `Billing` ](#billing-configuration-setting) paramètre de configuration.

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : **De reconnaissance vocale** gestion des ressources, sous **clés**

## <a name="applicationinsights-setting"></a>Paramètre ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Paramètre de configuration Billing

Le `Billing` paramètre spécifie l’URI de point de terminaison de la _vocale_ ressources sur Azure permet de contrôler les informations de facturation pour le conteneur. Vous devez spécifier une valeur pour ce paramètre de configuration, et la valeur doit être un URI de point de terminaison valide pour un _vocale_ ressources sur Azure. La conteneur crée des rapports sur l'utilisation toutes les 10 à 15 minutes.

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : **De reconnaissance vocale** vue d’ensemble, étiqueté `Endpoint`

|Obligatoire| Nom | Type de données | Description |
|--|------|-----------|-------------|
|Oui| `Billing` | Chaîne | URI du point de terminaison de facturation<br><br>Exemple :<br>`Billing=https://westus.api.cognitive.microsoft.com/sts/v1.0` |

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

Les conteneurs de reconnaissance vocale n’utilisent pas d’entrée ou sortie monte à stocker des données de service ou de formation. 

La syntaxe exacte de l’emplacement de montage d’hôte varie en fonction du système d’exploitation hôte. De plus, l’emplacement de montage de l’[ordinateur hôte](speech-container-howto.md#the-host-computer) peut ne pas être accessible en raison d’un conflit entre les autorisations utilisées par le compte de service docker et les autorisations de l’emplacement de montage de l’hôte. 

|Facultatif| Nom | Type de données | Description |
|-------|------|-----------|-------------|
|Non autorisé| `Input` | Chaîne | Conteneurs de reconnaissance vocale ne l’utilisez pas.|
|Facultatif| `Output` | Chaîne | Cible du montage de sortie. La valeur par défaut est `/output`. Il s’agit de l’emplacement des journaux d’activité. Les journaux d’activité de conteneur sont inclus. <br><br>Exemple :<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exemples de commandes docker run 

Les exemples suivants utilisent les paramètres de configuration pour illustrer comment écrire et utiliser des commandes `docker run`.  Une fois en cours d’exécution, le conteneur continue à s’exécuter jusqu’à ce que vous l’[arrêtiez](speech-container-howto.md#stop-the-container).

* **Caractère de continuation de ligne** : Les commandes Docker dans les sections suivantes utilisent la barre oblique inverse, `\`, comme caractère de continuation de ligne. Remplacez-la ou supprimez-la en fonction des exigences de votre système d’exploitation hôte. 
* **Ordre des arguments** : Ne changez pas l’ordre des arguments, sauf si vous avez une connaissance approfondie des conteneurs Docker.

Remplacez {_argument_name_} par vos propres valeurs :

| Placeholder | Valeur | Format ou exemple |
|-------------|-------|---|
|{BILLING_KEY} | La clé de point de terminaison de la ressource de reconnaissance vocale. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Valeur de point de terminaison de facturation, région comprise.|`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing-configuration-setting).
> La valeur de clé API est la **clé** à partir de la page clés de ressource de reconnaissance vocale de Azure. 

## <a name="speech-container-docker-examples"></a>Exemples de reconnaissance vocale conteneur Docker

Les exemples suivants de Docker sont pour le conteneur de reconnaissance vocale. 

### <a name="basic-example-for-speech-to-text"></a>Exemple de base pour la voix en texte

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}   
```

### <a name="basic-example-for-text-to-speech"></a>Exemple de base pour la conversion de texte par synthèse vocale

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}  
```

### <a name="logging-example-for-speech-to-text"></a>Exemple de journalisation pour la voix en texte

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}   
  Logging:Console:LogLevel:Default=Information
```

### <a name="logging-example-for-text-to-speech"></a>Exemple de journalisation pour la conversion de texte par synthèse vocale

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}  
  Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Guide pratique pour installer et exécuter des conteneurs](speech-container-howto.md).