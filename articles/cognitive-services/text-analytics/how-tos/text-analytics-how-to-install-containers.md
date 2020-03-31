---
title: Installer et exécuter des conteneurs – Analyse de texte
titleSuffix: Azure Cognitive Services
description: Ce tutoriel pas à pas décrit comment télécharger, installer et exécuter des conteneurs pour Analyse de texte.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 65033f9b6599d690b1097b4b78aa01148a40fc39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037503"
---
# <a name="install-and-run-text-analytics-containers"></a>Installer et exécuter les conteneurs Analyse de texte

Les conteneurs vous permettent d’exécuter les API d’analyse de texte dans votre propre environnement et sont très utiles pour répondre à vos besoins spécifiques en matière de sécurité et de gouvernance des données. Les conteneurs Analyse de texte fournissent un traitement en langage naturel avancé sur le texte brut et incluent trois fonctions principales : analyse des sentiments, extraction d’expressions clés et détection de la langue. Entity Linking n’est actuellement pas pris en charge dans un conteneur.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!IMPORTANT]
> Le compte gratuit est limité à 5 000 transactions par mois et seuls les niveaux tarifaires **Gratuit** et **Standard** <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">sont <span class="docon docon-navigate-external x-hidden-focus"></span></a> valides pour les conteneurs. Pour plus d’informations sur les taux de requête de transaction, consultez [Tarification - Transfert de données](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits).

## <a name="prerequisites"></a>Prérequis

Pour exécuter l’un des conteneurs Analyse de texte, vous devez disposer de l’ordinateur hôte et des environnements de conteneur.

## <a name="preparation"></a>Préparation

Vous devez respecter les prérequis suivants avant d’utiliser les conteneurs Analyse de texte :

|Obligatoire|Objectif|
|--|--|
|Moteur Docker| Vous avez besoin d’un moteur Docker installé sur un [ordinateur hôte](#the-host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. <br><br> **Sur Windows**, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.<br><br>|
|Bonne connaissance de Docker | Vous devez avoir une compréhension élémentaire des concepts Docker, notamment les registres, référentiels, conteneurs et images conteneurs, ainsi qu’une maîtrise des commandes `docker` de base.| 
|Ressource Analyse de texte |Pour pouvoir utiliser le conteneur, vous devez disposer des éléments suivants :<br><br>Ressource [Analyse de texte](../../cognitive-services-apis-create-account.md) d’Azure afin d’obtenir la clé API et l’URI du point de terminaison associés. Les deux valeurs, disponibles dans les pages Vue d’ensemble d’Analyse de texte et Clés du portail Azure, sont nécessaires au démarrage du conteneur.<br><br>**{API_KEY}**  : L’une des deux clés de ressource disponibles à la page **Clés**<br><br>**{ENDPOINT_URI}**  : Le point de terminaison tel qu'il est fourni à la page**Vue d’ensemble**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>L’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

Le tableau suivant décrit les cœurs de processeur minimum et recommandés, d’au moins 2,6 gigahertz (GHz) ou plus rapides, et la mémoire, en gigaoctets (Go), à allouer pour chaque conteneur Analyse de texte.

# <a name="key-phrase-extraction"></a>[Extraction d’expressions clés](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detection"></a>[Détection de la langue](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysis"></a>[Analyse des sentiments](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).
* TPS - transactions par seconde

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

Images conteneur pour Analyse de texte disponibles dans Microsoft Container Registry.

# <a name="key-phrase-extraction"></a>[Extraction d’expressions clés](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detection"></a>[Détection de la langue](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysis"></a>[Analyse des sentiments](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Docker pull pour les conteneurs Analyse de texte

# <a name="key-phrase-extraction"></a>[Extraction d’expressions clés](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Détection de la langue](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Analyse des sentiments](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>Comment utiliser le conteneur

Une fois que le conteneur est sur l’[ordinateur hôte](#the-host-computer), appliquez la procédure suivante pour travailler avec le conteneur.

1. [Exécutez le conteneur](#run-the-container-with-docker-run) avec les paramètres de facturation requis. D’autres [exemples](../text-analytics-resource-container-config.md#example-docker-run-commands) de commande `docker run` sont disponibles.
1. [Interrogez le point de terminaison de prédiction du conteneur](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter l’un des trois conteneurs. Consultez la section [Recueillir les paramètres requis](#gathering-required-parameters) pour plus d’informations sur la façon d’obtenir les valeurs `{ENDPOINT_URI}` et `{API_KEY}`.

[Exemples ](../text-analytics-resource-container-config.md#example-docker-run-commands) de la commande `docker run` sont disponibles.

# <a name="key-phrase-extraction"></a>[Extraction d’expressions clés](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Détection de la langue](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Analyse des sentiments](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Interroger le point de terminaison de prédiction du conteneur

Le conteneur fournit des API de point de terminaison de prédiction de requête basées sur REST.

Utilisez l’hôte, `http://localhost:5000`, pour les API de conteneur.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Dépannage

Si vous exécutez le conteneur avec un [montage](../text-analytics-resource-container-config.md#mount-settings) de sortie et la journalisation activée, il génère des fichiers journaux qui sont utiles pour résoudre les problèmes qui se produisent lors du démarrage ou de l’exécution du conteneur.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Facturation

Les conteneurs Analyse de texte envoient des informations de facturation à Azure à l’aide d’une ressource _Analyse de texte_ sur votre compte Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution des conteneurs Analyse de texte. En résumé :

* Analyse de texte fournit trois conteneurs Linux pour Docker, encapsulant diverses fonctionnalités :
   * *Extraction d’expressions clés*
   * *Détection de la langue*
   * *Analyse des sentiments*
* Les images conteneur sont téléchargées à partir de Microsoft Container Registry (MCR) dans Azure.
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le kit SDK pour appeler des opérations dans des conteneurs Analyse de texte en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
> Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas les données des clients (p. ex., l’image ou le texte analysés) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, passez en revue [Configurer des conteneurs](../text-analytics-resource-container-config.md).
* Reportez-vous à [Forum aux questions (FAQ)](../text-analytics-resource-faq.md) pour résoudre les problèmes liés à la fonctionnalité.
