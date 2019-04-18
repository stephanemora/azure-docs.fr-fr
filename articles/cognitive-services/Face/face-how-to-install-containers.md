---
title: Installer et exécuter des conteneurs
titlesuffix: Face - Azure Cognitive Services
description: Ce tutoriel pas à pas décrit comment télécharger, installer et exécuter des conteneurs pour Visage.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: 5028a05fe74f1d19ed5e43ac797df87bbe3382e8
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680721"
---
# <a name="install-and-run-face-containers"></a>Installer et exécuter des conteneurs de visage

Visage fournit un conteneur Linux standardisé pour Docker, nommé Visage, qui détecte les visages humains sur des images et identifie les attributs, y compris les caractéristiques (par exemple, le nez et les yeux), le sexe, l’âge et d’autres caractéristiques du visage prévues par la machine. En plus de la détection, Visage peut vérifier si deux visages sur la même image ou sur des images différentes sont identiques à l’aide d’un score de confiance, ou de comparer des visages par rapport à une base de données pour voir si un visage similaire ou identique existe déjà. Il peut également regrouper des visages similaires à l’aide de caractéristiques visuelles partagées.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables

Vous devez respecter les prérequis suivants avant d’utiliser les conteneurs de l’API Visage :

|Obligatoire|Objectif|
|--|--|
|Moteur Docker| Vous avez besoin d’un moteur Docker installé sur un [ordinateur hôte](#the-host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. <br><br> **Sur Windows**, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.<br><br>|
|Bonne connaissance de Docker | Vous devez avoir une compréhension élémentaire des concepts Docker, notamment les registres, référentiels, conteneurs et images conteneurs, ainsi qu’une maîtrise des commandes `docker` de base.| 
|Azure `Cognitive Services` ressource |Pour pouvoir utiliser le conteneur, vous devez disposer des éléments suivants :<br><br>Un _Cognitive Services_ le point de terminaison facturation URI de clé de ressource Azure et la facturation associée. Les deux valeurs sont disponibles sur les pages de vue d’ensemble et des clés pour la ressource et sont nécessaires pour démarrer le conteneur. Vous devez ajouter le `face/v1.0` routage vers l’URI de point de terminaison, comme indiqué dans l’exemple BILLING_ENDPOINT_URI suivant. <br><br>**{BILLING_KEY}**  : clé de ressource<br><br>**{BILLING_ENDPOINT_URI}**  : exemple d’UURI de point de terminaison : `https://westus.api.cognitive.microsoft.com/face/v1.0`|


## <a name="request-access-to-the-private-container-registry"></a>Demander l’accès au registre de conteneurs privé

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>L’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

Le tableau suivant décrit la quantité minimale et recommandée de cœurs de processeur et de mémoire à allouer pour chaque conteneur de l’API Visage.

| Conteneur | Minimale | Recommandé | TPS<br>(Au minimum, Maximum)|
|-----------|---------|-------------|--|
|Face | 1 cœur, 2 Go de mémoire | 1 cœur, 4 Go de mémoire |10, 20|

* Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).
* Programmes transactionnels - transactions par seconde

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

Des images conteneur sont disponibles pour l’API Visage. 

| Conteneur | Référentiel |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Commande docker pull du conteneur Visage

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="how-to-use-the-container"></a>Comment utiliser le conteneur

Une fois que le conteneur est sur l’[ordinateur hôte](#the-host-computer), appliquez la procédure suivante pour travailler avec le conteneur.

1. [Exécutez le conteneur](#run-the-container-with-docker-run) avec les paramètres de facturation requis. D’autres [exemples](./face-resource-container-config.md#example-docker-run-commands) de commande `docker run` sont disponibles. 
1. [Interrogez le point de terminaison de prédiction du conteneur](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter l’un des trois conteneurs. La commande utilise les paramètres suivants :

| Placeholder | Valeur |
|-------------|-------|
|{BILLING_KEY} | Cette clé est utilisée pour démarrer le conteneur et est disponible sur Azure `Cognitive Services` page clés.  |
|{BILLING_ENDPOINT_URI} | La valeur de l’URI de point de terminaison facturation est disponible sur Azure `Cognitive Services` page Vue d’ensemble. Exemple est : `https://westus.api.cognitive.microsoft.com/face/v1.0`|

Vous devez ajouter le `face/v1.0` routage vers l’URI de point de terminaison, comme indiqué dans l’exemple BILLING_ENDPOINT_URI précédent. 

Remplacez ces paramètres par vos propres valeurs dans l’exemple de commande `docker run` suivant.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Cette commande :

* Exécute un conteneur Visage à partir de l’image conteneur.
* Alloue un cœur de processeur et 4 gigaoctets (Go) de mémoire.
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte. 

D’autres [exemples](./face-resource-container-config.md#example-docker-run-commands) de commande `docker run` sont disponibles. 

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Interroger le point de terminaison de prédiction du conteneur

Le conteneur fournit des API de point de terminaison de prédiction de requête basées sur REST. 

Utilisez l’hôte, `https://localhost:5000`, pour les API de conteneur.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous exécutez le conteneur avec un [montage](./face-resource-container-config.md#mount-settings) de sortie et la journalisation activée, il génère des fichiers journaux qui sont utiles pour résoudre les problèmes qui se produisent lors du démarrage ou de l’exécution du conteneur. 


## <a name="billing"></a>Facturation

Les conteneurs de l’API Visage envoient des informations de facturation à Azure à l’aide d’une ressource _API Visage_ sur votre compte Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](./face-resource-container-config.md).

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution des conteneurs de l’API Visage. En résumé :

* L’API Visage fournit trois conteneurs Linux pour Docker encapsulant l’extraction de phrases clés, la détection de la langue et l’analyse des sentiments.
* Les images conteneur sont téléchargées à partir de Microsoft Container Registry (MCR) dans Azure.
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le Kit de développement logiciel (SDK) pour appeler des opérations dans les conteneurs de l’API Visage en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
> Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas les données des clients (p. ex., l’image ou le texte analysés) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, passez en revue [Configurer des conteneurs](face-resource-container-config.md).
* Consulter [Vue d’ensemble de Visage](Overview.md) pour en savoir plus sur la détection et l’identification de visages  
* Reportez-vous à l’ [API Visage](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) pour plus d’informations sur les méthodes prises en charge par le conteneur.
* Reportez-vous à [Forum aux questions (FAQ)](FAQ.md) pour résoudre les problèmes liés à la fonctionnalité de Visage.
* Utiliser plus de [conteneurs Cognitive Services](../cognitive-services-container-support.md)
