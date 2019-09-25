---
title: Installer des conteneurs Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Installez et exécutez des conteneurs Speech. La reconnaissance vocale transcrit en temps réel des flux audio en texte que vos applications, outils ou appareils peuvent utiliser ou afficher. La synthèse vocale convertit le texte d’entrée en parole synthétisée quasi humaine.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 4b8ea102c7acc55acec05234303ff4c215a4bc0f
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105159"
---
# <a name="install-and-run-speech-service-containers"></a>Installer et exécuter des conteneurs de service Speech

Les conteneurs Speech permettent aux clients de créer une architecture d’application vocal optimisée pour tirer parti des fonctionnalités robustes du cloud et de la localité en périphérie. 

Les deux conteneurs Speech sont la **reconnaissance vocale** et **synthèse vocale**. 

|Fonction|Caractéristiques|Latest|
|-|-|--|
|Reconnaissance vocale| <li>Transcrit en continu de la parole en temps réel ou des enregistrements audio en texte, avec des résultats intermédiaires.|1.2.0|
|Synthèse vocale| <li>Convertit le texte en paroles naturelles. Avec entrée de texte brut ou Langage de balisage de synthèse vocale (SSML). |1.2.0|

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

L’utilisation des conteneurs Speech est soumise aux conditions préalables suivantes :

|Obligatoire|Objectif|
|--|--|
|Moteur Docker| Vous avez besoin d’un moteur Docker installé sur un [ordinateur hôte](#the-host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. <br><br> **Sur Windows**, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.<br><br>|
|Bonne connaissance de Docker | Vous devez avoir une compréhension élémentaire des concepts Docker, notamment les registres, référentiels, conteneurs et images conteneurs, ainsi qu’une maîtrise des commandes `docker` de base.| 
|Ressource Speech |Pour pouvoir utiliser ces conteneurs, vous devez avoir :<br><br>Une ressource _Speech_ d’Azure afin d’obtenir la clé API et l’URI du point de terminaison associés. Les deux valeurs sont disponibles sur les pages Vue d’ensemble et Clés de la ressource **Speech** du portail Azure. Elles sont nécessaires au démarrage du conteneur.<br><br>**{API_KEY}**  : L’une des deux clés de ressource disponibles à la page **Clés**<br><br>**{ENDPOINT_URI}**  : le point de terminaison tel qu'il est fourni à la page **Vue d’ensemble**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Demander l’accès au registre de conteneurs

Vous devez d’abord compléter et envoyer le [formulaire de demande de conteneurs Cognitive Services Speech](https://aka.ms/speechcontainerspreview/) pour demander l’accès au conteneur. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>L’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Prise en charge d’Advanced Vector Extension

L’**hôte** est l’ordinateur qui exécute le conteneur docker. L’hôte doit prendre en charge [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Vous pouvez vérifier cette prise en charge sur les hôtes Linux avec la commande suivante : 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

Le tableau suivant décrit la quantité minimale et recommandée de cœurs de processeur et de mémoire à allouer pour chaque conteneur Speech.

| Conteneur | Minimale | Recommandé |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2 cœurs<br>Mémoire 2 Go  | 4 cœurs<br>Mémoire 4 Go  |
|cognitive-services-text-to-speech | 1 cœur, 0,5 Go de mémoire| 2 cœurs, 1 Go de mémoire |

* Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

**Remarque** : Les valeurs minimales et recommandées sont basées sur les limites Docker, et *pas* sur les ressources de la machine hôte. Par exemple, les conteneurs de reconnaissance vocale contenant des portions de mappage de mémoire d’un modèle linguistique volumineux, il est _recommandé_ que l’intégralité du fichier tienne dans la mémoire, ce qui correspond à 4 à 6 Go supplémentaires. En outre, la première exécution d’un conteneur peut prendre beaucoup de temps perce que les modèles sont paginés en mémoire.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

Des images conteneurs pour Speech sont disponibles.

| Conteneur | Référentiel |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Les paramètres régionaux de langue figurent dans une balise de conteneur

La balise `latest` extrait les paramètres régionaux `en-us` et la voix `jessarus`.

#### <a name="speech-to-text-locales"></a>Paramètres régionaux de reconnaissance vocale

Toutes les balises, à l’exception de `latest`, sont au format suivant, où `<culture>` indique le conteneur des paramètres régionaux :

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

La balise suivante illustre le format :

```
1.2.0-amd64-en-us-preview
```

Le tableau suivant répertorie les paramètres régionaux pris en charge pour la **reconnaissance vocale** dans la version 1.2.0 du conteneur :

|Paramètres régionaux de langue|Balises|
|--|--|
|Chinois|`zh-cn`|
|Anglais |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|Français |`fr-ca`<br>`fr-fr`|
|Allemand|`de-de`|
|Italien|`it-it`|
|Japonais|`ja-jp`|
|Coréen|`ko-kr`|
|Portugais|`pt-br`|
|Espagnol|`es-es`<br>`es-mx`|

#### <a name="text-to-speech-locales"></a>Paramètres régionaux de synthèse vocale

Toutes les balises, à l’exception de `latest`, sont au format suivant, où `<culture>` indique les paramètres régionaux et `<voice>` la voix du conteneur :

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

La balise suivante illustre le format :

```
1.2.0-amd64-en-us-jessarus-preview
```

Le tableau suivant répertorie les paramètres régionaux pris en charge pour la **synthèse vocale** dans la version 1.2.0 du conteneur :

|Paramètres régionaux de langue|Balises|Voix prises en charge|
|--|--|--|
|Chinois|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao-apollo|
|Anglais |`en-au`|catherine<br>hayleyrus|
|Anglais |`en-gb`|george-apollo<br>hazelrus<br>susan-apollo|
|Anglais |`en-in`|heera-apollo<br>priyarus<br>ravi-apollo<br>|
|Anglais |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|Français|`fr-ca`|caroline<br>harmonierus|
|Français|`fr-fr`|hortenserus<br>julie-apollo<br>paul-apollo|
|Allemand|`de-de`|hedda<br>heddarus<br>stefan-apollo|
|Italien|`it-it`|cosimo-apollo<br>luciarus|
|Japonais|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro-apollo|
|Coréen|`ko-kr`|heamirus|
|Portugais|`pt-br`|daniel-apollo<br>heloisarus|
|Espagnol|`es-es`|elenarus<br>laura-apollo<br>pablo-apollo<br>|
|Espagnol|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Docker pull pour les conteneurs Speech

#### <a name="speech-to-text"></a>Reconnaissance vocale

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Synthèse vocale

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>Comment utiliser le conteneur

Une fois que le conteneur est sur l’[ordinateur hôte](#the-host-computer), appliquez la procédure suivante pour travailler avec le conteneur.

1. [Exécutez le conteneur](#run-the-container-with-docker-run) avec les paramètres de facturation requis. D’autres [exemples](speech-container-configuration.md#example-docker-run-commands) de commande `docker run` sont disponibles.
1. [Interrogez le point de terminaison de prédiction du conteneur](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter le conteneur. Pour plus d’informations sur la façon d’obtenir les valeurs `{ENDPOINT_URI}` et `{API_KEY}`, consultez [Collecte des paramètres requis](#gathering-required-parameters).

[Exemples ](speech-container-configuration.md#example-docker-run-commands) de la commande `docker run` sont disponibles.

> [!NOTE]
> **Durant la phase de préversion**, les paramètres de facturation doivent être valides pour démarrer le conteneur, mais vous n’êtes pas facturés pour son utilisation.

### <a name="text-to-speech"></a>Synthèse vocale

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="speech-to-text"></a>Reconnaissance vocale

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cette commande :

* Exécute un conteneur Speech à partir de l’image conteneur
* Alloue 2 cœurs de processeur et 2 gigaoctets (Go) de mémoire
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte.

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Interroger le point de terminaison de prédiction du conteneur

|Conteneur|Point de terminaison|
|--|--|
|Reconnaissance vocale|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Synthèse vocale|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Reconnaissance vocale

Le conteneur fournit des API de point de terminaison de requête s’appuyant sur WebSocket, qui sont accessibles par le biais du [Kit de développement logiciel (SDK) Speech](index.yml).

Par défaut, le Kit de développement logiciel (SDK) Speech utilise des services vocaux en ligne. Pour utiliser le conteneur, vous devez changer la méthode d’initialisation. Consultez les exemples ci-dessous.

#### <a name="for-c"></a>Pour C#

Passer de l’utilisation de cet appel d’initialisation du cloud Azure :

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

à cet appel à l’aide du point de terminaison de conteneur :

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"),
    "YourSubscriptionKey");
```

#### <a name="for-python"></a>Pour Python

Passer de l’utilisation de cet appel d’initialisation du cloud Azure :

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

à cet appel à l’aide du point de terminaison de conteneur :

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Synthèse vocale

Le conteneur fournit des API de point de terminaison REST, qui se trouvent [ici](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech), et des exemples [ici](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Résolution de problèmes

Lorsque vous exécutez le conteneur, celui-ci utilise **stdout** et **stderr** pour produire des informations utiles à la résolution des problèmes qui surviennent lors du démarrage ou de l’exécution du conteneur.

## <a name="billing"></a>Facturation

Les conteneurs Speech envoient des informations de facturation à Azure à l’aide d’une ressource _Speech_ sur votre compte Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](speech-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution des conteneurs Speech. En résumé :

* Speech fournit deux conteneurs Linux pour Docker, qui encapsulent la reconnaissance vocale et la synthèse vocale.
* Les images conteneur sont téléchargées à partir du registre de conteneurs privé dans Azure.
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou kit de développement logiciel (SDK) pour appeler des opérations dans des conteneurs Speech en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
>  Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas les données des clients (p. ex., l’image ou le texte analysés) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, passez en revue [Configurer des conteneurs](speech-container-configuration.md).
* Utiliser davantage de [conteneurs Cognitive Services](../cognitive-services-container-support.md)
