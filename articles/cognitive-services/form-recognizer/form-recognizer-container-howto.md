---
title: Installer et exécuter le conteneur - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser le conteneur Form Recognizer pour analyser des données de formulaire et de table.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: pafarley
ms.openlocfilehash: c7d5d9421ec89f1d75723d3538ee9a73e56dc6a3
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143037"
---
# <a name="install-and-run-form-recognizer-containers"></a>Installer et exécuter des conteneurs Form Recognizer
Form Recognizer applique la technologie de machine learning pour identifier et extraire des tables et des paires clé-valeur à partir de formulaires. En leur associant des valeurs et des entrées de table, cette ressource restitue des données structurées qui incluent les relations du fichier d’origine. Vous pouvez appeler votre modèle personnalisé Form Recognizer au moyen d’une API REST simple, afin de réduire la complexité et facilement l’intégrer à votre processus d’automatisation de workflow ou à une autre application. Et comme cinq documents (ou un formulaire vide) seulement sont nécessaires, vous obtenez des résultats rapidement, avec précision et en adéquation avec votre contenu spécifique, sans intervention manuelle lourde ni recours à des compétences approfondies en science des données. Aucun étiquetage ni aucune annotation de données ne sont exigés.

|Fonction|Caractéristiques|
|-|-|
|Form Recognizer| <li>Traite les fichiers de type PDF, PNG et JPG.<li>Entraîne des modèles personnalisés avec un minimum de 5 formulaires présentant la même disposition. <li>Extrait des informations de table et des paires clé-valeur. <li>Utilise la fonction Reconnaissance de texte de l’API Vision par ordinateur de Cognitive Service pour détecter et extraire du texte imprimé à partir d’images dans des formulaires.<li>N’exige ni étiquetage ni annotation.|

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

L’utilisation des conteneurs Form Recognizer est soumise aux prérequis suivants :

|Obligatoire|Objectif|
|--|--|
|Moteur Docker| Vous avez besoin d’un moteur Docker installé sur un [ordinateur hôte](#the-host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. <br><br> **Sur Windows**, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.<br><br>|
|Bonne connaissance de Docker | Vous devez avoir une compréhension élémentaire des concepts Docker, notamment les registres, référentiels, conteneurs et images conteneurs, ainsi qu’une maîtrise des commandes `docker` de base.|
|Azure CLI| Vous devez installer l’interface [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) sur votre hôte.|
|Ressource API Vision par ordinateur| Pour pouvoir traiter documents et images numérisés, une **ressource Vision par ordinateur** est nécessaire. Vous pouvez accéder à la fonctionnalité **Reconnaître le texte** en tant que ressource Azure (API REST ou kit SDK) ou en tant que `cognitive-services-recognize-text`[conteneur](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull). Les frais de facturation habituels s’appliquent. <br><br>Vous devez transmettre la clé et le point de terminaison de facturation de votre ressource Vision par ordinateur spécifique (cloud Azure ou conteneur Cognitive Services). Utilisez cette clé et le point de terminaison de facturation en tant que {COMPUTER_VISION_API_KEY} et {COMPUTER_VISION_BILLING_ENDPOINT_URI}.<br><br> Si vous utilisez le **conteneur `cognitive-services-recognize-text`**, assurez-vous que :<br><br>* Votre clé Vision par ordinateur du conteneur Form Recognizer est la clé spécifiée dans la commande `docker run` de Vision par ordinateur du conteneur `cognitive-services-recognize-text`.<br>* Votre point de terminaison de facturation est le point de terminaison du conteneur, par exemple `https://localhost:5000`. Si vous utilisez un conteneur Vision par ordinateur et un conteneur Form Recognizer ensemble sur le même hôte, ils ne peuvent pas être tous les deux démarrés avec le port par défaut `5000`.  |  
|Ressource Form Recognizer |Pour pouvoir utiliser ces conteneurs, vous devez disposer des éléments suivants :<br><br>Ressource Azure _Form Recognizer_ afin d’obtenir la clé de facturation et l’URI du point de terminaison de facturation associés. Les deux valeurs, disponibles dans les pages Clés et Vue d’ensemble de **Form Recognizer** du portail Azure, sont nécessaires au démarrage du conteneur.<br><br>**{BILLING_KEY}**  : clé de ressource<br><br>**{BILLING_ENDPOINT_URI}**  : exemple d’UURI de point de terminaison : `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>Demander l’accès au registre de conteneurs

Vous devez d’abord compléter et envoyer le [formulaire de demande de conteneurs Form Recognizer de Cognitive Services](https://aka.ms/FormRecognizerRequestAccess) pour solliciter l’accès au conteneur. Vous serez également inscrit à la Vision par ordinateur. Il n’est pas nécessaire de vous inscrire séparément à l’aide du formulaire de demande de la Vision par ordinateur. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>L’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

Le tableau suivant décrit la quantité minimale et recommandée de cœurs de processeur et de mémoire à allouer pour chaque conteneur Form Recognizer.

| Conteneur | Minimale | Recommandé |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 cœurs, 4 Go de mémoire | 4 cœurs, 8 Go de mémoire |

* Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).
* TPS - transactions par seconde

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

> [!Note]
> Les valeurs minimales et recommandées sont basées sur les limites Docker et *pas* sur les ressources de la machine hôte.

## <a name="get-the-container-image-with-docker-pull-command"></a>Obtenir l’image conteneur avec la commande docker pull

Des images conteneur sont disponibles pour Form Recognizer.

| Conteneur | Référentiel |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

Si vous prévoyez d’utiliser le `cognitive-services-recognize-text` [conteneur](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull) plutôt que le service Form Recognizer, veillez à utiliser la commande `docker pull` avec le nom de conteneur approprié : 

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-form-recognizer-container"></a>Commande docker pull du conteneur Form Recognizer

#### <a name="form-recognizer"></a>Form Recognizer

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Comment utiliser le conteneur

Une fois que le conteneur est sur l’[ordinateur hôte](#the-host-computer), appliquez la procédure suivante pour travailler avec le conteneur.

1. [Exécutez le conteneur](#run-the-container-with-docker-run) avec les paramètres de facturation exigés mais non utilisés. D’autres [exemples](form-recognizer-container-configuration.md#example-docker-run-commands) de commande `docker run` sont disponibles.
1. [Interrogez le point de terminaison de prédiction du conteneur](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter l’un des trois conteneurs. La commande utilise les paramètres suivants :

| Placeholder | Valeur |
|-------------|-------|
|{BILLING_KEY} | Cette clé, qui permet de démarrer le conteneur, est disponible dans la page Clés de Form Recognizer du portail Azure.  |
|{BILLING_ENDPOINT_URI} | La valeur de l’URI du point de terminaison de facturation est disponible dans la page Vue d’ensemble de Form Recognizer du portail Azure.|
|{COMPUTER_VISION_API_KEY}| La clé est disponible dans la page Clés de l’API Vision par ordinateur du portail Azure.|
|{COMPUTER_VISION_ENDPOINT_URI}|Le point de terminaison de facturation. Si vous utilisez une ressource informatique Vision par ordinateur, la valeur de l’URI est disponible dans la page Vue d’ensemble de l’API Vision par ordinateur du portail Azure. Si vous utilisez un conteneur `cognitive-services-recognize-text`, utilisez l’URL du point de terminaison de facturation passée au conteneur dans la commande `docker run`.|

Remplacez ces paramètres par vos propres valeurs dans l’exemple de commande `docker run` suivant.

### <a name="form-recognizer"></a>Form Recognizer

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Cette commande :

* Exécute un conteneur Form Recognizer à partir de l’image conteneur
* Alloue 2 cœurs de processeur et 8 gigaoctets (Go) de mémoire
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte.
* Monte un volume /d’entrée et un volume /de sortie sur le conteneur

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Exécuter des conteneurs distincts sous forme de commandes docker run distinctes

Pour la combinaison Form Recognizer avec Reconnaissance de texte, hébergée localement sur le même hôte, deux exemples de commandes d’interface Docker CLI suivent.

Exécutez le premier conteneur sur le port 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Exécutez le deuxième conteneur sur le port 5001.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Chaque conteneur suivant doit être sur un port différent. 

### <a name="run-separate-containers-with-docker-compose"></a>Exécuter des conteneurs distincts avec Docker Compose

Pour la combinaison Form Recognizer avec Reconnaissance de texte, hébergée localement sur le même hôte, un exemple de fichier YAML de Docker Compose suit. `{COMPUTER_VISION_API_KEY}` de la Reconnaissance de texte doit être identique pour les deux conteneurs `formrecognizer` et `ocr`. Seul le conteneur `ocr` se sert de `{COMPUTER_VISION_ENDPOINT_URI}`, car le conteneur `formrecognizer` utilise le port et le nom `ocr`. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: {COMPUTER_VISION_API_KEY}  

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{BILLING_ENDPOINT_URI}"
      apikey: {BILLING_KEY}
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"  
```


> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` ainsi que `FormRecognizer:ComputerVisionApiKey` et `FormRecognizer:ComputerVisionEndpointUri` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Interroger le point de terminaison de prédiction du conteneur

|Conteneur|Point de terminaison|
|--|--|
|form-recognizer|http://localhost:5000


### <a name="form-recognizer"></a>Form Recognizer

Le conteneur fournit des API de point de terminaison de requête s’appuyant sur WebSocket, qui sont accessibles par le biais de la [documentation du kit SDK des services Form Recognizer](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Par défaut, le Kit SDK Form Recognizer utilise les services en ligne. Pour utiliser le conteneur, vous devez changer la méthode d’initialisation. Consultez les exemples ci-dessous.

#### <a name="for-c"></a>Pour C#

Passer de l’utilisation de cet appel d’initialisation du cloud Azure :

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

à cet appel à l’aide du point de terminaison de conteneur :

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Pour Python

Passer de l’utilisation de cet appel d’initialisation du cloud Azure :

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

à cet appel à l’aide du point de terminaison de conteneur :

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Form Recognizer

Le conteneur fournit des API de point de terminaison REST, qui se trouvent [ici](https://docs.microsoft.com/azure/cognitive-services/formrecognizer-service/rest-apis#formrecognier-api), et des exemples [ici](https://azure.microsoft.com/resources/samples/cognitive-formrecognizer).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Résolution de problèmes

Lorsque vous exécutez le conteneur, celui-ci utilise **stdout** et **stderr** pour produire des informations utiles à la résolution des problèmes qui surviennent lors du démarrage ou de l’exécution du conteneur.

## <a name="billing"></a>Facturation

Les conteneurs Form Recognizer envoient des informations de facturation à Azure par l’intermédiaire d’une ressource _Form Recognizer_ sur votre compte Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](form-recognizer-container-configuration.md).

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert les concepts et le workflow du téléchargement, de l’installation et de l’exécution des conteneurs Form Recognizer. En résumé :

* Form Recognizer fournit un conteneur Linux pour Docker.
* Les images conteneur sont téléchargées à partir du registre de conteneurs privé dans Azure.
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le kit SDK pour appeler des opérations dans le conteneur Form Recognizer en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
>  Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas les données des clients (p. ex., l’image ou le texte analysés) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, passez en revue [Configurer des conteneurs](form-recognizer-container-configuration.md).
* Utiliser davantage de [conteneurs Cognitive Services](../cognitive-services-container-support.md)
