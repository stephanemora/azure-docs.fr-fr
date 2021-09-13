---
title: Installer et exécuter des conteneurs Docker pour l’API Analyse de texte
titleSuffix: Azure Cognitive Services
description: Utilisez les conteneurs Docker pour l’API Analyse de texte pour effectuer un traitement en langage naturel, tel que l’analyse des sentiments, en local.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020, devx-track-azurecli
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/21/2021
ms.author: aahi
keywords: local, Docker, conteneur, analyse des sentiments, traitement en langage naturel
ms.openlocfilehash: 82e247d51351417a987205b2c65ea26be737ec5c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473419"
---
# <a name="install-and-run-text-analytics-containers"></a>Installer et exécuter les conteneurs Analyse de texte

Les conteneurs vous permettent d’exécuter les API d’analyse de texte dans votre propre environnement et sont très utiles pour répondre à vos besoins spécifiques en matière de sécurité et de gouvernance des données. Les conteneurs Analyse de texte suivants sont disponibles :

* analyse des sentiments
* détection de la langue
* Extraction de phrases clés (préversion)
* Analyse de texte pour la santé 

> [!NOTE]
> * Les fonctionnalités Liaison d’entités et Reconnaissance d'entité nommée ne sont actuellement pas disponibles en tant que conteneurs.
> * Les emplacements de l’image du conteneur peuvent avoir été modifiés récemment. Lisez cet article pour voir l’emplacement mis à jour de ce conteneur.
> * Le compte gratuit est limité à 5 000 enregistrements texte par mois et seuls les [niveaux tarifaires](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics) **Gratuit** et **Standard** sont valides pour les conteneurs. Pour plus d’informations sur les taux de requête de transaction, consultez [Tarification - Transfert de données](../concepts/data-limits.md).

Les conteneurs vous permettent d’exécuter les API d’analyse de texte dans votre propre environnement et sont très utiles pour répondre à vos besoins spécifiques en matière de sécurité et de gouvernance des données. Les conteneurs Analyse de texte fournissent un traitement en langage naturel avancé sur le texte brut et incluent trois fonctions principales : analyse des sentiments, extraction d’expressions clés et détection de la langue.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Vous devez respecter les prérequis suivants avant d’utiliser les conteneurs Analyse de texte. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer.

* [Docker](https://docs.docker.com/) installé sur un ordinateur hôte. Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. 
    * Sous Windows, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.
    * Vous devez disposer d’une connaissance élémentaire des [concepts liés à Docker](https://docs.docker.com/get-started/overview/). 
* Une <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Créer une ressource Analyse de texte"  target="_blank">ressource Analyse de texte </a> avec le [niveau tarifaire](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) Gratuit (F0) ou Standard (S).

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>Spécifications et recommandations relatives à l’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

Le tableau suivant décrit les spécifications minimales et recommandées pour les conteneurs Analyse de texte disponibles. Chaque processeur doit être cadencé à au moins 2,6 gigahertz (GHz). Les transactions par seconde (TPS) autorisées sont également indiquées.

|  | Configuration minimale pour l’hôte | Configuration recommandée pour l’hôte | TPS minimales | TPS maximales|
|---|---------|-------------|--|--|
| **Détection de la langue**   | 1 cœur, 2 Go de mémoire | 1 cœur, 4 Go de mémoire |15 | 30| 
| **Extraction de phrases clés (préversion)**   | 1 cœur, 2 Go de mémoire | 1 cœur, 4 Go de mémoire |15 | 30| 
| **Analyse des sentiments**   | 1 cœur, 2 Go de mémoire | 4 cœurs, 8 Go de mémoire |15 | 30|
| **Analyse de texte pour l’intégrité – 1 document/demande**   |  4 cœurs, 10 Go de mémoire | 6 cœurs, 12 Go de mémoire |15 | 30|
| **Analyse de texte pour l’intégrité – 10 documents/demande**   |  6 cœurs, 16 Go de mémoire | 8 cœurs, 20 Go de mémoire |15 | 30|

Le cœur d’UC et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

# <a name="sentiment-analysis"></a>[Analyse des sentiments ](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extraction de phrases clés (préversion)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Détection de la langue](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health"></a>[Analyse de texte pour la santé](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Une fois le conteneur sur l’ordinateur hôte, utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter les conteneurs. Chaque conteneur continuera à s’exécuter jusqu’à ce que vous l’arrêtiez.

> [!IMPORTANT]
> * les commandes docker dans les sections suivantes utilisent la barre oblique inverse, `\`, comme caractère de continuation de ligne. Remplacez-la ou supprimez-la en fonction des exigences de votre système d’exploitation hôte. 
> * Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).
>   * Si vous utilisez le conteneur Analyse de texte pour la santé, l’accusé de réception [IA responsable ](/legal/cognitive-services/text-analytics/transparency-note-health) (RAI) doit également être présent avec la valeur `accept`.
> * Les conteneurs d’analyse des sentiments et de détection de la langue utilisent la version v3 de l’API et sont en disponibilité générale. Le conteneur d’extraction de phrases clés utilise la version 2 de l’API et est en préversion.

# <a name="sentiment-analysis"></a>[Analyse des sentiments](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extraction de phrases clés (préversion)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Détection de la langue](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health"></a>[Analyse de texte pour la santé](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

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

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution des conteneurs Analyse de texte. En résumé :

* Analyse de texte fournit trois conteneurs Linux pour Docker, encapsulant diverses fonctionnalités :
   * *Analyse des sentiments*
   * *Extraction de phrases clés (préversion)* 
   * *Détection de la langue*
   * *Analyse de texte pour la santé*
* Les images conteneurs sont téléchargées à partir de Microsoft Container Registry (MCR).
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le kit SDK pour appeler des opérations dans des conteneurs Analyse de texte en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
> Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services ne transmettent aucune donnée client (p. ex., le texte analysé) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, consultez [Configurer des conteneurs](../text-analytics-resource-container-config.md).
