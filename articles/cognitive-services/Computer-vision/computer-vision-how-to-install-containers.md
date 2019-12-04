---
title: Guide pratique pour installer et exécuter des conteneurs – Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Ce tutoriel pas à pas décrit comment télécharger, installer et exécuter des conteneurs pour Vision par ordinateur.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: c65ed214747fd6a3729c2e9acff5489f5fa1b9d7
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74323628"
---
# <a name="install-and-run-read-containers-preview"></a>Installer et exécuter des conteneurs Lire (préversion)

Les conteneurs vous permettent d’exécuter les API Vision par ordinateur dans votre propre environnement. Les conteneurs conviennent particulièrement bien à certaines exigences de sécurité et de gouvernance des données. Dans cet article, vous allez apprendre à télécharger, installer et exécuter un conteneur Vision par ordinateur.

Un seul conteneur Docker, *Lire*, est disponible pour Vision par ordinateur. Le conteneur *Lire* permet de détecter et d’extraire du *texte imprimé* à partir d’images d’objets divers avec différents arrière-plans et surfaces, tels que des reçus, des affiches et des cartes de visite. De plus, le conteneur *Lire* détecte le *texte manuscrit* dans les images, et prend en charge les documents PDF, TIFF, multipage. Pour plus d’informations, consultez la documentation sur l’[API Lire](concept-recognizing-text.md#read-api).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Vous devez respecter les prérequis suivants avant d’utiliser les conteneurs :

|Obligatoire|Objectif|
|--|--|
|Moteur Docker| Vous avez besoin d’un moteur Docker installé sur un [ordinateur hôte](#the-host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. <br><br> **Sur Windows**, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.<br><br>|
|Bonne connaissance de Docker | Vous devez avoir une compréhension élémentaire des concepts Docker, notamment les registres, référentiels, conteneurs et images conteneurs, ainsi qu’une maîtrise des commandes `docker` de base.| 
|Ressource Vision par ordinateur |Pour pouvoir utiliser le conteneur, vous devez disposer des éléments suivants :<br><br>Une ressource **Vision par ordinateur** Azure, la clé d’API associée et l’URI de point de terminaison. Les deux valeurs, disponibles dans les pages Vue d’ensemble et Clés de la ressource, sont nécessaires au démarrage du conteneur.<br><br>**{API_KEY}**  : L’une des deux clés de ressource disponibles à la page **Clés**<br><br>**{ENDPOINT_URI}**  : le point de terminaison tel qu'il est fourni à la page **Vue d’ensemble**|

## <a name="request-access-to-the-private-container-registry"></a>Demander l’accès au registre de conteneurs privé

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>L’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

Des images conteneurs sont disponibles pour le conteneur Lire.

| Conteneur | Nom de registre de conteneurs / référentiel / image |
|-----------|------------|
| Lire | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

Utilisez la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur.

### <a name="docker-pull-for-the-read-container"></a>Commande docker pull du conteneur Lire

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Comment utiliser le conteneur

Une fois que le conteneur est sur l’[ordinateur hôte](#the-host-computer), appliquez la procédure suivante pour travailler avec le conteneur.

1. [Exécutez le conteneur](#run-the-container-with-docker-run) avec les paramètres de facturation requis. D’autres [exemples](computer-vision-resource-container-config.md) de commande `docker run` sont disponibles. 
1. [Interrogez le point de terminaison de prédiction du conteneur](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter le conteneur. Pour plus d’informations sur la façon d’obtenir les valeurs `{ENDPOINT_URI}` et `{API_KEY}`, consultez [Collecte des paramètres requis](#gathering-required-parameters).

[Exemples ](computer-vision-resource-container-config.md#example-docker-run-commands) de la commande `docker run` sont disponibles.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cette commande :

* Exécute un conteneur Lire à partir de l’image conteneur.
* Alloue 8 cœurs de processeur et 16 gigaoctets (Go) de mémoire.
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur.
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte.

D’autres [exemples](./computer-vision-resource-container-config.md#example-docker-run-commands) de commande `docker run` sont disponibles. 

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Interroger le point de terminaison de prédiction du conteneur

Le conteneur fournit des API de point de terminaison de prédiction de requête basées sur REST. 

Utilisez l’hôte, `http://localhost:5000`, pour les API de conteneur.

### <a name="asynchronous-read"></a>Lecture asynchrone

Vous pouvez utiliser conjointement les opérations `POST /vision/v2.0/read/core/asyncBatchAnalyze` et `GET /vision/v2.0/read/operations/{operationId}` pour lire de façon asynchrone une image, ce qui est similaire à la façon dont le service Vision par ordinateur utilise ces opérations REST correspondantes. La méthode POST asynchrone retourne un `operationId` qui est utilisé comme identificateur de la requête HTTP GET.

À partir de l’interface utilisateur Swagger, sélectionnez le `asyncBatchAnalyze` pour le développer dans le navigateur. Ensuite, sélectionnez **Faites un essai** > **Choisir un fichier**. Dans cet exemple, nous allons utiliser l’image suivante :

![Tabulations et espaces](media/tabs-vs-spaces.png)

Lorsque le POST asynchrone s’est correctement exécuté, il retourne le code d’état **HTTP 202**. Dans la réponse, un en-tête `operation-location` contient le point de terminaison de résultat de la requête.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location` est l’URL complète qui est accessible via HTTP GET. Voici la réponse JSON à l’exécution de l’URL `operation-location` à partir de l’image précédente :

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [
            56,
            39,
            317,
            50,
            313,
            134,
            53,
            123
          ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [
                90,
                43,
                243,
                53,
                243,
                123,
                94,
                125
              ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [
                259,
                55,
                313,
                62,
                313,
                122,
                259,
                123
              ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [
            221,
            148,
            417,
            146,
            417,
            206,
            227,
            218
          ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [
                230,
                148,
                416,
                141,
                419,
                211,
                232,
                218
              ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="synchronous-read"></a>Lecture synchrone

Vous pouvez utiliser l’opération `POST /vision/v2.0/read/core/Analyze` pour lire de façon synchrone une image. C’est uniquement lorsque l’image est lue dans son intégralité que l’API retourne une réponse JSON. La seule exception à ceci est si une erreur se produit. Lorsqu’une erreur se produit, le code JSON suivant est retourné :

```json
{
    status: "Failed"
}
```

L’objet de réponse JSON contient le même graphe d’objets que la version asynchrone. Si vous êtes un utilisateur JavaScript et visez la cohérence des types, vous pouvez utiliser les types suivants pour caster la réponse JSON en un objet `AnalyzeResult`.

```typescript
export interface AnalyzeResult {
    status: Status;
    recognitionResults?: RecognitionResult[] | null;
}

export enum Status {
    NotStarted = 0,
    Running = 1,
    Failed = 2,
    Succeeded = 3
}

export enum Unit {
    Pixel = 0,
    Inch = 1
}

export interface RecognitionResult {
    page?: number | null;
    clockwiseOrientation?: number | null;
    width?: number | null;
    height?: number | null;
    unit?: Unit | null;
    lines?: Line[] | null;
}

export interface Line {
    boundingBox?: number[] | null;
    text: string;
    words?: Word[] | null;
}

export enum Confidence {
    High = 0,
    Low = 1
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: Confidence | null;
}
```

Pour obtenir un exemple de cas d’utilisation, consultez <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">ce bac à sable TypeScript<span class="docon docon-navigate-external x-hidden-focus"></span></a>, puis sélectionnez **Exécuter** pour vous rendre compte de sa facilité d’utilisation.

## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous exécutez le conteneur avec un [montage](./computer-vision-resource-container-config.md#mount-settings) de sortie et la journalisation activée, il génère des fichiers journaux qui sont utiles pour résoudre les problèmes qui se produisent lors du démarrage ou de l’exécution du conteneur.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Facturation

Les conteneurs Cognitive Services envoient des informations de facturation à Azure à l’aide de la ressource correspondante dans votre compte Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution des conteneurs Vision par ordinateur. En résumé :

* Vision par ordinateur fournit un conteneur Linux pour Docker, et encapsule le conteneur Lire.
* Les images conteneur sont téléchargées à partir du registre de conteneurs « Conteneur (préversion) » dans Azure.
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou kit de développement logiciel (SDK) pour appeler des opérations dans des conteneurs Lire en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
> Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas de données relatives aux clients (par exemple, l’image ou le texte en cours d’analyse) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, passez en revue [Configurer des conteneurs](computer-vision-resource-container-config.md).
* Pour en savoir plus sur la reconnaissance du texte imprimé et manuscrit, passez en revue [Vue d’ensemble de Vision par ordinateur](Home.md).
* Pour plus d’informations sur les méthodes prises en charge par le conteneur, reportez-vous à l’[API Vision par ordinateur](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).
* Pour résoudre les problèmes liés à la fonctionnalité Vision par ordinateur, reportez-vous au [Forum aux questions (FAQ)](FAQ.md).
* Utiliser davantage de [conteneurs Cognitive Services](../cognitive-services-container-support.md)
