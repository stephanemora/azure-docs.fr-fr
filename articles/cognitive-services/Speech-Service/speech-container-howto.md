---
title: Installez les conteneurs de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Installez et exécutez des conteneurs de reconnaissance vocale. La reconnaissance vocale transcrit en temps réel des flux audio en texte que vos applications, outils ou appareils peuvent utiliser ou afficher. La synthèse vocale convertit le texte d’entrée en parole synthétisée quasi humaine.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: diberry
ms.openlocfilehash: 7a4153dd382a901db21752dd3d55a01803431791
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388684"
---
# <a name="install-and-run-speech-service-containers"></a>Installer et exécuter des conteneurs de Service de reconnaissance vocale

Conteneurs de reconnaissance vocale permettent aux clients de créer une architecture d’application de reconnaissance vocale est optimisée pour tirer parti des fonctionnalités de cloud fiable et localité de bord. 

Les conteneurs de deux vocale sont **parole-texte** et **synthèse vocale**. 

|Fonction|Caractéristiques|Dernière|
|-|-|--|
|Reconnaissance vocale| <li>Retranscrit continu en temps réel vocale ou le traitement des enregistrements audio dans le texte avec des résultats intermédiaires.|1.1.1|
|Synthèse vocale| <li>Convertit le texte en paroles naturelles. avec l’entrée de texte brut ou Markup langage SSML (Speech Synthesis). |1.1.0|

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables

Vous devez remplir les conditions préalables suivantes avant d’utiliser des conteneurs de reconnaissance vocale :

|Obligatoire|Objectif|
|--|--|
|Moteur Docker| Vous avez besoin d’un moteur Docker installé sur un [ordinateur hôte](#the-host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. <br><br> **Sur Windows**, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.<br><br>|
|Bonne connaissance de Docker | Vous devez avoir une compréhension élémentaire des concepts Docker, notamment les registres, référentiels, conteneurs et images conteneurs, ainsi qu’une maîtrise des commandes `docker` de base.| 
|Ressources de la reconnaissance vocale |Pour pouvoir utiliser ces conteneurs, vous devez avoir :<br><br>Un _vocale_ des ressources Azure pour obtenir la clé de facturation associée et l’URI de point de terminaison facturation. Les deux valeurs sont disponibles sur le portail Azure **vocale** pages de vue d’ensemble et des clés et sont requises pour démarrer le conteneur.<br><br>**{BILLING_KEY}**  : clé de ressource<br><br>**{BILLING_ENDPOINT_URI}**  : exemple d’UURI de point de terminaison : `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="request-access-to-the-container-registry"></a>Demander l’accès au registre de conteneurs

Vous devez tout d’abord terminer et envoyer la [formulaire de demande de conteneurs de Cognitive Services Speech](https://aka.ms/speechcontainerspreview/) pour demander l’accès au conteneur. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>L’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Prise en charge de vecteur Extension avancé

L’**hôte** est l’ordinateur qui exécute le conteneur docker. L’hôte doit prendre en charge [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Vous pouvez vérifier cette prise en charge sur les hôtes Linux avec la commande suivante : 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

Le tableau suivant décrit les minimale et recommandée de cœurs de processeur et mémoire à allouer pour chaque conteneur de reconnaissance vocale.

| Conteneur | Minimale | Recommandé |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2 cœurs<br>2 Go de mémoire  | 4 cœurs<br>4 Go de mémoire  |
|cognitive-services-text-to-speech | 1 cœur, 0,5 Go de mémoire| 2 cœur, 1 Go de mémoire |

* Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).


Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

**Remarque**; La valeur minimale et recommandée sont basées sur les limites de Docker, *pas* ressources machine de l’hôte. Par exemple, les conteneurs de la parole-texte mémoire mapper des parties d’un modèle de langage volumineux et il est _recommandé_ que l’intégralité du fichier s’intègre dans la mémoire, ce qui est de 4 à 6 Go supplémentaires. En outre, la première exécution du conteneur soit peut prendre plus de temps, dans la mesure où les modèles sont en cours de pagination en mémoire.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

Images de conteneur pour la reconnaissance vocale sont disponibles. 

| Conteneur | Référentiel |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Paramètres régionaux de langue sont dans la balise de conteneur

Le `latest` balise extrait le `en-us` paramètres régionaux et `jessarus` voix. 

#### <a name="speech-to-text-locales"></a>Reconnaissance vocale pour les paramètres régionaux du texte

Toutes les balises, à l’exception de `latest` sont au format suivant, où le `<culture>` indique le conteneur de paramètres régionaux :

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

La balise suivante est un exemple du format :

```
1.0.0-amd64-en-us-preview
```

Le tableau suivant répertorie les paramètres régionaux pris en charge pour **parole-texte** dans le 1.1.1 version du conteneur :

|Paramètres régionaux de langue|Balises|
|--|--|
|Chinois|`zh-cn`|
|Anglais |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|Anglais |`fr-ca`<br>`fr-fr`|
|Allemand|`de-de`|
|Italien|`it-it`|
|Japonais|`ja-jp`|
|Coréen|`ko-kr`|
|Portugais|`pt-br`|
|Espagnol|`es-es`<br>`es-mx`|


#### <a name="text-to-speech-locales"></a>Paramètres régionaux de texte par synthèse vocale

Toutes les balises, à l’exception de `latest` sont au format suivant, où le `<culture>` indique les paramètres régionaux et la `<voice>` indique la voix du conteneur :

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

La balise suivante est un exemple du format :

```
1.0.0-amd64-en-us-jessarus-preview
```

Le tableau suivant répertorie les paramètres régionaux pris en charge pour **synthèse vocale** dans la version 1.1.0 version du conteneur :

|Paramètres régionaux de langue|Balises|Voix pris en charge|
|--|--|--|
|Chinois|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao-apollo|
|Anglais |`en-au`|Catherine<br>hayleyrus|
|Anglais |`en-gb`|George-apollo<br>hazelrus<br>Susan-apollo|
|Anglais |`en-in`|heera-apollo<br>priyarus<br>ravi-apollo<br>|
|Anglais |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|Anglais|`fr-ca`|Caroline<br>harmonierus|
|Anglais|`fr-fr`|hortenserus<br>julie-apollo<br>paul-apollo|
|Allemand|`de-de`|hedda<br>heddarus<br>stefan-apollo|
|Italien|`it-it`|Cosimo-apollo<br>luciarus|
|Japonais|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro-apollo|
|Coréen|`ko-kr`|heamirus|
|Portugais|`pt-br`|daniel-apollo<br>heloisarus|
|Espagnol|`es-es`|elenarus<br>laura-apollo<br>Pablo-apollo<br>|
|Espagnol|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Extraction de docker pour les conteneurs de reconnaissance vocale

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

1. [Exécutez le conteneur](#run-the-container-with-docker-run) avec les paramètres de facturation exigés mais non utilisés. D’autres [exemples](speech-container-configuration.md#example-docker-run-commands) de commande `docker run` sont disponibles. 
1. [Interrogez le point de terminaison de prédiction du conteneur](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter l’un des trois conteneurs. La commande utilise les paramètres suivants :

**Dans la version préliminaire**, les paramètres de facturation doivent être valides pour démarrer le conteneur, mais vous n’êtes pas facturé pour l’utilisation.

| Placeholder | Valeur |
|-------------|-------|
|{BILLING_KEY} | Cette clé est utilisée pour démarrer le conteneur et est disponible sur la page des clés de reconnaissance vocale du portail Azure.  |
|{BILLING_ENDPOINT_URI} | La valeur de l’URI de point de terminaison facturation est disponible sur la page de vue d’ensemble de la reconnaissance vocale du portail Azure.|

Remplacez ces paramètres par vos propres valeurs dans l’exemple de commande `docker run` suivant.

### <a name="text-to-speech"></a>Synthèse vocale

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} 
```

### <a name="speech-to-text"></a>Reconnaissance vocale

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} 
```

Cette commande :

* Exécute un conteneur de reconnaissance vocale à partir de l’image de conteneur
* Alloue 2 cœurs de processeur et de 2 gigaoctets (Go) de mémoire
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

Le conteneur fournit le point de terminaison de requête fondée sur un websocket API, qui sont accessibles via le [Speech SDK](index.yml).

Par défaut, le Speech SDK utilise les services de reconnaissance vocale en ligne. Pour utiliser le conteneur, vous devez changer la méthode d’initialisation. Consultez les exemples ci-dessous.

#### <a name="for-c"></a>Pour C#

Passer de l’utilisation de cet appel d’initialisation du cloud Azure :

```C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

à cet appel à l’aide du point de terminaison de conteneur :

```C#
var config = SpeechConfig.FromEndpoint("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Pour Python

Passer de l’utilisation de cet appel d’initialisation du cloud Azure :

```python
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

à cet appel à l’aide du point de terminaison de conteneur :

```python
speech_config = speechsdk.SpeechConfig(subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Synthèse vocale

Le conteneur fournit le point de terminaison REST API qui se trouve [ici](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) et vous pouvant trouver des exemples [ici](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Résolution de problèmes

Lorsque vous exécutez le conteneur, celui-ci utilise **stdout** et **stderr** pour produire des informations utiles à la résolution des problèmes qui surviennent lors du démarrage ou de l’exécution du conteneur. 

## <a name="billing"></a>Facturation

L’envoi de conteneurs vocale, informations de facturation pour Azure, en utilisant un _vocale_ ressource sur votre compte Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](speech-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Résumé

Dans cet article, vous avez appris des concepts et des flux de travail pour le téléchargement, l’installation et exécution des conteneurs de reconnaissance vocale. En résumé :

* Reconnaissance vocale fournit deux conteneurs Linux pour Docker, qui encapsule la parole-texte et texte par synthèse vocale.
* Les images conteneur sont téléchargées à partir du registre de conteneurs privé dans Azure.
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le Kit de développement logiciel pour appeler des opérations dans des conteneurs de reconnaissance vocale en spécifiant l’URI du conteneur hôte.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
>  Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas les données des clients (p. ex., l’image ou le texte analysés) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, passez en revue [Configurer des conteneurs](speech-container-configuration.md).
* Utiliser davantage de [conteneurs Cognitive Services](../cognitive-services-container-support.md)
