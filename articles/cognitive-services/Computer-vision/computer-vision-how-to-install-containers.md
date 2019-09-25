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
ms.date: 09/18/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: d3a36615109383074833e9af634eb611fb863339
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103646"
---
# <a name="install-and-run-recognize-text-containers"></a>Installer et exécuter des conteneurs Reconnaître le texte

Le composant Reconnaître le texte de Vision par ordinateur est également disponible en tant que conteneur Docker. Il permet de détecter et d’extraire un texte imprimé à partir d’images d’objets divers avec différents arrière-plans et surfaces, tels que des reçus, des affiches et des cartes de visite.

> [!IMPORTANT]
> Le conteneur Reconnaître le texte ne fonctionne qu’en anglais pour le moment.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

L’utilisation des conteneurs Reconnaître le texte est soumise aux prérequis suivants :

|Obligatoire|Objectif|
|--|--|
|Moteur Docker| Vous avez besoin d’un moteur Docker installé sur un [ordinateur hôte](#the-host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. <br><br> **Sur Windows**, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.<br><br>|
|Bonne connaissance de Docker | Vous devez avoir une compréhension élémentaire des concepts Docker, notamment les registres, référentiels, conteneurs et images conteneurs, ainsi qu’une maîtrise des commandes `docker` de base.| 
|Ressource Vision par ordinateur |Pour pouvoir utiliser le conteneur, vous devez disposer des éléments suivants :<br><br>Une ressource **Vision par ordinateur** Azure, la clé d’API associée et l’URI de point de terminaison. Les deux valeurs, disponibles dans les pages Vue d’ensemble et Clés de la ressource, sont nécessaires au démarrage du conteneur.<br><br>**{API_KEY}**  : L’une des deux clés de ressource disponibles à la page **Clés**<br><br>**{ENDPOINT_URI}**  : le point de terminaison tel qu'il est fourni à la page **Vue d’ensemble**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Demander l’accès au registre de conteneurs privé

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>L’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

Des images conteneur sont disponibles pour Reconnaître le texte. 

| Conteneur | Référentiel |
|-----------|------------|
|Reconnaître le texte | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Utilisez la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur.


### <a name="docker-pull-for-the-recognize-text-container"></a>Commande docker pull du conteneur Reconnaître le texte

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Comment utiliser le conteneur

Une fois que le conteneur est sur l’[ordinateur hôte](#the-host-computer), appliquez la procédure suivante pour travailler avec le conteneur.

1. [Exécutez le conteneur](#run-the-container-with-docker-run) avec les paramètres de facturation requis. D’autres [exemples](computer-vision-resource-container-config.md) de commande `docker run` sont disponibles. 
1. [Interrogez le point de terminaison de prédiction du conteneur](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter le conteneur. Consultez la section [Recueillir les paramètres requis](#gathering-required-parameters) pour plus d’informations sur la façon d’obtenir les valeurs `{ENDPOINT_URI}` et `{API_KEY}`.

[Exemples ](computer-vision-resource-container-config.md#example-docker-run-commands) de la commande `docker run` sont disponibles.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cette commande :

* Exécute un conteneur Reconnaître le texte à partir de l’image conteneur.
* Alloue un cœur de processeur et 4 gigaoctets (Go) de mémoire.
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte. 

D’autres [exemples](./computer-vision-resource-container-config.md#example-docker-run-commands) de commande `docker run` sont disponibles. 

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Interroger le point de terminaison de prédiction du conteneur

Le conteneur fournit des API de point de terminaison de prédiction de requête basées sur REST. 

Utilisez l’hôte, `http://localhost:5000`, pour les API de conteneur.

### <a name="asynchronous-text-recognition"></a>Reconnaissance de texte asynchrone

Vous pouvez utiliser conjointement les opérations `POST /vision/v2.0/recognizeText` et `GET /vision/v2.0/textOperations/*{id}*` pour reconnaître de façon asynchrone le texte imprimé dans une image, ce qui est similaire à la façon dont le service Vision par ordinateur utilise ces opérations REST correspondantes. Le conteneur Reconnaître le texte reconnaît uniquement le texte imprimé pour le moment. Le texte manuscrit n’étant pas reconnu, le paramètre `mode` normalement spécifié pour l’opération de service Vision par ordinateur est ignoré par le conteneur Reconnaître le texte.

### <a name="synchronous-text-recognition"></a>Reconnaissance de texte synchrone

Vous pouvez utiliser l’opération `POST /vision/v2.0/recognizeTextDirect` pour reconnaître de façon synchrone le texte imprimé dans une image. Étant donné que cette opération est synchrone, le corps de la requête pour cette opération est identique l’opération `POST /vision/v2.0/recognizeText`. Toutefois, le corps de la requête pour cette opération est identique à celui retourné par l’opération `GET /vision/v2.0/textOperations/*{id}*`.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous exécutez le conteneur avec un [montage](./computer-vision-resource-container-config.md#mount-settings) de sortie et la journalisation activée, il génère des fichiers journaux qui sont utiles pour résoudre les problèmes qui se produisent lors du démarrage ou de l’exécution du conteneur. 


## <a name="billing"></a>Facturation

Les conteneurs Reconnaître le texte envoient des informations de facturation à Azure à l’aide d’une ressource _Reconnaître le texte_ sur votre compte Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution des conteneurs Reconnaître le texte. En résumé :

* Reconnaître le texte fournit un conteneur Linux pour Docker et encapsule la reconnaissance du texte.
* Les images conteneur sont téléchargées à partir de Microsoft Container Registry (MCR) dans Azure.
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le Kit de développement logiciel (SDK) pour appeler des opérations dans des conteneurs Reconnaître le texte en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
> Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas de données relatives aux clients (par exemple, l’image ou le texte en cours d’analyse) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, passez en revue [Configurer des conteneurs](computer-vision-resource-container-config.md).
* Pour en savoir plus sur la reconnaissance du texte imprimé et manuscrit, passez en revue [Vue d’ensemble de Vision par ordinateur](Home.md).  
* Pour plus d’informations sur les méthodes prises en charge par le conteneur, reportez-vous à l’[API Vision par ordinateur](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).
* Pour résoudre les problèmes liés à la fonctionnalité Vision par ordinateur, reportez-vous au [Forum aux questions (FAQ)](FAQ.md).
* Utiliser davantage de [conteneurs Cognitive Services](../cognitive-services-container-support.md)
