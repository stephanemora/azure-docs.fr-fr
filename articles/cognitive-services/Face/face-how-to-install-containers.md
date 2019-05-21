---
title: Installer et exécuter des conteneurs
titlesuffix: Face - Azure Cognitive Services
description: Télécharger, installer et exécuter des conteneurs pour Face dans ce didacticiel de la procédure pas à pas.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: 9bc18157a6f60555ea18901796199a3fe9dc4c22
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65891223"
---
# <a name="install-and-run-face-containers"></a>Installer et exécuter des conteneurs de visage

Visage de Services cognitifs Azure fournit un conteneur de Linux standardisé pour Docker qui détecte les visages humains dans des images. Il identifie également les attributs, qui incluent des points de repère visage telles que noses et yeux, sexe, l’âge et autres fonctionnalités du visage prédite à la machine. En plus de détection, Face peut vérifier si deux visages dans la même image ou des images différentes sont identiques à l’aide d’un score de confiance. Face peut également comparer les visages par rapport à une base de données pour voir si un visage similaires ou identique existe déjà. Il peut également organiser les visages similaires dans des groupes à l’aide de traits visual partagés.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables

Vous devez respecter les conditions préalables suivantes avant d’utiliser les conteneurs de l’API visage.

|Obligatoire|Objectif|
|--|--|
|Moteur Docker| Le moteur Docker doit être installé sur un [ordinateur hôte](#the-host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. <br><br> Sur Windows, Docker doit également être configuré pour prendre en charge des conteneurs Linux.<br><br>|
|Bonne connaissance de Docker | Vous avez besoin d’une compréhension élémentaire des concepts Docker, telles que les registres, les dépôts, les conteneurs et les images de conteneur. Vous devez également les connaissances de base `docker` commandes.| 
|Azure `Cognitive Services` ressource |Pour utiliser le conteneur, vous devez disposer de :<br><br>Une ressource Azure Cognitive Services et la clé de facturation associée et l’URI de point de terminaison facturation. Les deux valeurs sont disponibles sur le **vue d’ensemble** et **clés** pages pour la ressource. Elles sont requises pour démarrer le conteneur. Ajouter le `face/v1.0` routage vers le point de terminaison URI, comme indiqué dans l’exemple BILLING_ENDPOINT_URI suivant : <br><br>**{BILLING_KEY}**  : clé de ressource<br><br>**{BILLING_ENDPOINT_URI}** : exemple d’URI de point de terminaison est `https://westus.api.cognitive.microsoft.com/face/v1.0`|

## <a name="request-access-to-the-private-container-registry"></a>Demander l’accès au registre de conteneurs privé

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>L’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

Le tableau suivant décrit la quantité minimale et recommandée de cœurs de processeur et de mémoire à allouer pour chaque conteneur de l’API Visage.

| Conteneur | Minimale | Recommandé | Transactions par seconde<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Face | 1 cœur, 2 Go de mémoire | 1 cœur, 4 Go de mémoire |10, 20|

* Chaque cœur doit être au moins 2,6 GHz ou plus rapide.
* Transactions par seconde (TPS).

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image de conteneur avec extraction de docker

Images de conteneur pour l’API visage sont disponibles. 

| Conteneur | Dépôt |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Commande docker pull du conteneur Visage

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Utilisez le conteneur

Une fois le conteneur se trouve sur le [ordinateur hôte](#the-host-computer), utilisez le processus suivant pour travailler avec le conteneur.

1. [Exécuter le conteneur](#run-the-container-with-docker-run) avec les paramètres de facturation. D’autres [exemples](./face-resource-container-config.md#example-docker-run-commands) de commande `docker run` sont disponibles. 
1. [Interrogez le point de terminaison de prédiction du conteneur](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec docker run

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter l’un des trois conteneurs. La commande utilise les paramètres suivants.

| Placeholder | Valeur |
|-------------|-------|
|{BILLING_KEY} | Cette clé est utilisée pour démarrer le conteneur et est disponible sur Azure `Cognitive Services` **clés** page. |
|{BILLING_ENDPOINT_URI} | La valeur de l’URI de point de terminaison facturation est disponible sur Azure `Cognitive Services` **vue d’ensemble** page. Par exemple `https://westus.api.cognitive.microsoft.com/face/v1.0`.|

Ajouter le `face/v1.0` routage vers le point de terminaison URI, comme indiqué dans l’exemple BILLING_ENDPOINT_URI précédent. 

Remplacez ces paramètres par vos propres valeurs dans l’exemple suivant `docker run` exemple de commande :

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Cette commande :

* Exécute un conteneur de visage à partir de l’image de conteneur.
* Alloue un cœur de processeur et 4 Go de mémoire.
* Expose le port TCP 5000 et alloue une pseudo TTY pour le conteneur.
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte. 

D’autres [exemples](./face-resource-container-config.md#example-docker-run-commands) de commande `docker run` sont disponibles. 

> [!IMPORTANT]
> Le `Eula`, `Billing`, et `ApiKey` options doivent être spécifiées pour exécuter le conteneur ou le conteneur ne démarre pas. Pour plus d'informations, consultez [Facturation](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Interroger le point de terminaison de prédiction du conteneur

Le conteneur fournit des API de point de terminaison de prédiction de requête basées sur REST. 

Utilisez l’hôte, `https://localhost:5000`, pour les API de conteneur.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous exécutez le conteneur avec une sortie [monter](./face-resource-container-config.md#mount-settings) et la journalisation est activée, le conteneur génère les fichiers journaux qui sont utiles résoudre les problèmes qui se produisent lors du démarrage ou d’exécuter le conteneur. 


## <a name="billing"></a>Facturation

Les conteneurs de l’API visage envoient les informations de facturation sur Azure à l’aide d’une ressource de l’API visage sur votre compte Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](./face-resource-container-config.md).

## <a name="summary"></a>Résumé

Dans cet article, vous avez appris des concepts et des flux de travail pour savoir comment télécharger, installer et exécuter des conteneurs de l’API visage. En résumé :

* L’API visage fournit trois conteneurs Linux pour Docker qui fournissent l’extraction de phrases clés, détection de la langue et l’analyse des sentiments.
* Images de conteneur sont téléchargées à partir du Registre de conteneur Azure.
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le Kit de développement pour appeler des opérations dans des conteneurs de l’API visage en spécifiant l’URI du conteneur hôte.
* Vous devez spécifier les informations de facturation lorsque vous instanciez un conteneur.

> [!IMPORTANT]
> Les conteneurs de Services cognitifs ne sont pas concédés sous licence pour s’exécuter sans être connecté à Azure pour le contrôle. Les clients doivent activer les conteneurs de communiquer des informations de facturation avec le service de contrôle à tout moment. Les conteneurs de Services cognitifs n’envoient des données client, telles que l’image ou le texte qui est en cours d’analyse, à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour les paramètres de configuration, consultez [configurer des conteneurs](face-resource-container-config.md).
* Pour en savoir plus sur comment détecter et identifier des visages, consultez [vue d’ensemble de la Face](Overview.md).
* Pour plus d’informations sur les méthodes prises en charge par le conteneur, consultez le [API visage](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Pour utiliser plus de conteneurs de Cognitive Services, consultez [conteneurs de Cognitive Services](../cognitive-services-container-support.md).
