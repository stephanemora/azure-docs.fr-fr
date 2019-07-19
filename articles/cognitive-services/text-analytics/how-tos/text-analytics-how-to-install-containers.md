---
title: Installer et exécuter des conteneurs
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Ce tutoriel pas à pas décrit comment télécharger, installer et exécuter des conteneurs pour Analyse de texte.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: c4ef58f35b3d038f360ff962c70e92711bc205ce
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446506"
---
# <a name="install-and-run-text-analytics-containers"></a>Installer et exécuter les conteneurs Analyse de texte

Les conteneurs Analyse de texte fournissent un traitement en langage naturel avancé sur le texte brut et incluent trois fonctions principales : analyse des sentiments, extraction d’expressions clés et détection de la langue. Entity Linking n’est actuellement pas pris en charge dans un conteneur.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour exécuter l’un des conteneurs Analyse de texte, vous devez disposer de l’ordinateur hôte et des environnements de conteneur.

## <a name="preparation"></a>Préparation

Vous devez respecter les prérequis suivants avant d’utiliser les conteneurs Analyse de texte :

|Obligatoire|Objectif|
|--|--|
|Moteur Docker| Vous avez besoin d’un moteur Docker installé sur un [ordinateur hôte](#the-host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. <br><br> **Sur Windows**, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.<br><br>|
|Bonne connaissance de Docker | Vous devez avoir une compréhension élémentaire des concepts Docker, notamment les registres, référentiels, conteneurs et images conteneurs, ainsi qu’une maîtrise des commandes `docker` de base.| 
|Resource `Cognitive Services` |Pour pouvoir utiliser le conteneur, vous devez disposer des éléments suivants :<br><br>Une ressource [_Cognitive Services_](text-analytics-how-to-access-key.md) Azure permettant d’obtenir la clé de facturation et l’URI du point de terminaison de facturation associés. Les deux valeurs, disponibles dans les pages Vue d’ensemble de Cognitive Services et Clés du portail Microsoft Azure, sont nécessaires au démarrage du conteneur. Vous devez ajouter le routage `text/analytics/v2.0` à l’URI de point de terminaison, comme dans l’exemple BILLING_ENDPOINT_URI suivant.<br><br>**{BILLING_KEY}**  : clé de ressource<br><br>**{BILLING_ENDPOINT_URI}**  : exemple d’UURI de point de terminaison : `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

### <a name="the-host-computer"></a>L’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

Le tableau suivant décrit les cœurs de processeur minimum et recommandés, d’au moins 2,6 gigahertz (GHz) ou plus rapides, et la mémoire, en gigaoctets (Go), à allouer pour chaque conteneur Analyse de texte.

| Conteneur | Minimale | Recommandé | TPS<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
|Extraction d’expressions clés | 1 cœur, 2 Go de mémoire | 1 cœur, 4 Go de mémoire |15, 30|
|Détection de la langue | 1 cœur, 2 Go de mémoire | 1 cœur, 4 Go de mémoire |15, 30|
|Analyse des sentiments | 1 cœur, 2 Go de mémoire | 1 cœur, 4 Go de mémoire |15, 30|

* Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).
* TPS - transactions par seconde

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

Images conteneur pour Analyse de texte disponibles à partir de Microsoft Container Registry. 

| Conteneur | Référentiel |
|-----------|------------|
|Extraction d’expressions clés | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Détection de la langue | `mcr.microsoft.com/azure-cognitive-services/language` |
|Analyse des sentiments | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Utilisez la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur à partir de Microsoft Container Registry.

Pour obtenir une description complète des balises disponibles pour les conteneurs Analyse de texte, consultez les conteneurs suivants dans le Docker Hub :

* [Extraction de phrases clés](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Détection de la langue](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Analyse des sentiments](https://go.microsoft.com/fwlink/?linkid=2018654)

Utilisez la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur.


### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker pull du conteneur d’extraction de phrases clés

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>Docker pull du conteneur de détection de langue

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>Docker pull du conteneur de sentiments

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>Comment utiliser le conteneur

Une fois que le conteneur est sur l’[ordinateur hôte](#the-host-computer), appliquez la procédure suivante pour travailler avec le conteneur.

1. [Exécutez le conteneur](#run-the-container-with-docker-run) avec les paramètres de facturation requis. D’autres [exemples](../text-analytics-resource-container-config.md#example-docker-run-commands) de commande `docker run` sont disponibles. 
1. [Interrogez le point de terminaison de prédiction du conteneur](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter l’un des trois conteneurs. La commande utilise les paramètres suivants :

| Placeholder | Valeur |
|-------------|-------|
|{BILLING_KEY} | Cette clé, qui permet de démarrer le conteneur, est disponible dans la page Clés `Cognitive Services` sur le portail Microsoft Azure.  |
|{BILLING_ENDPOINT_URI} | La valeur d’URI de point de terminaison de facturation est disponible sur la page Vue d’ensemble Azure `Cognitive Services`. <br><br>Exemple :<br>`Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

Vous devez ajouter le routage `text/analytics/v2.0` à l’URI de point de terminaison, comme dans l’exemple BILLING_ENDPOINT_URI précédent.

Remplacez ces paramètres par vos propres valeurs dans l’exemple de commande `docker run` suivant.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Cette commande :

* Exécute un conteneur de phrases clés à partir d’une image conteneur
* Alloue un cœur de processeur et 4 gigaoctets (Go) de mémoire.
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte. 

D’autres [exemples](../text-analytics-resource-container-config.md#example-docker-run-commands) de commande `docker run` sont disponibles. 

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Interroger le point de terminaison de prédiction du conteneur

Le conteneur fournit des API de point de terminaison de prédiction de requête basées sur REST. 

Utilisez l’hôte, `https://localhost:5000`, pour les API de conteneur.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous exécutez le conteneur avec un [montage](../text-analytics-resource-container-config.md#mount-settings) de sortie et la journalisation activée, il génère des fichiers journaux qui sont utiles pour résoudre les problèmes qui se produisent lors du démarrage ou de l’exécution du conteneur. 

## <a name="billing"></a>Facturation

Les conteneurs Analyse de texte envoient des informations de facturation à Azure à l’aide d’une ressource _Cognitive Services_ sur votre compte Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](../text-analytics-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution des conteneurs Analyse de texte. En résumé :

* Analyse de texte fournit trois conteneurs Linux pour Docker, intégrant l’extraction de phrases clés, la détection de la langue et l’analyse des sentiments.
* Les images conteneur sont téléchargées à partir de Microsoft Container Registry (MCR) dans Azure.
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le kit SDK pour appeler des opérations dans des conteneurs Analyse de texte en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
> Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas les données des clients (p. ex., l’image ou le texte analysés) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, passez en revue [Configurer des conteneurs](../text-analytics-resource-container-config.md).
* Reportez-vous à [Forum aux questions (FAQ)](../text-analytics-resource-faq.md) pour résoudre les problèmes liés à la fonctionnalité.

