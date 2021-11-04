---
title: Installer et exécuter des conteneurs Docker pour Analyse des sentiments
titleSuffix: Azure Cognitive Services
description: Utilisez les conteneurs Docker pour l’API Analyse des sentiments pour effectuer localement un traitement en langage naturel, tel que l’analyse des sentiments.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
keywords: local, Docker, conteneur, analyse des sentiments, traitement en langage naturel
ms.openlocfilehash: 0239280045cab18627ba6e888cd75b03de0d54e4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097638"
---
# <a name="install-and-run-sentiment-analysis-containers"></a>Installer et exécuter des conteneurs Analyse des sentiments

Les conteneurs vous permettent d’héberger l’API Analyse des sentiments sur votre propre infrastructure. Si vous avez des exigences de sécurité ou de gouvernance des données qui ne peuvent pas être satisfaites en appelant Analyse des sentiments à distance, les conteneurs peuvent être une bonne option.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Vous devez respecter les prérequis suivants avant d’utiliser des conteneurs Analyse des sentiments. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer.

* [Docker](https://docs.docker.com/) installé sur un ordinateur hôte. Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. 
    * Sous Windows, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.
    * Vous devez disposer d’une connaissance élémentaire des [concepts liés à Docker](https://docs.docker.com/get-started/overview/). 
* Une ressource de langue <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Créer une ressource de langue"  target="_blank"></a> avec le [niveau tarifaire](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) gratuit (F0) ou standard (S).

[!INCLUDE [Gathering required parameters](../../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>Spécifications et recommandations relatives à l’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../../../includes/cognitive-services-containers-host-computer.md)]

Le tableau suivant décrit les spécifications minimales et recommandées pour le conteneur disponible. Chaque processeur doit être cadencé à au moins 2,6 gigahertz (GHz). Les transactions par seconde (TPS) autorisées sont également indiquées.

|  | Configuration minimale pour l’hôte | Configuration recommandée pour l’hôte | TPS minimales | TPS maximales|
|---|---------|-------------|--|--|
| **Analyse des sentiments**   | 1 cœur, 2 Go de mémoire | 4 cœurs, 8 Go de mémoire |15 | 30|

Le cœur d’UC et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

Le conteneur Analyse des sentiments v3 est disponible dans plusieurs langues. Pour télécharger la version anglaise du conteneur, utilisez la commande ci-dessous. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Pour télécharger le conteneur dans une autre langue, remplacez `en` par l’un des codes de langue ci-dessous. 

| Conteneur Analyse des sentiments | Code langue |
|--|--|
| Chinois simplifié    |   `zh-hans`   |
| Chinois traditionnel   |   `zh-hant`   |
| Néerlandais                 |     `nl`      |
| Anglais               |     `en`      |
| Français                |     `fr`      |
| Allemand                |     `de`      |
| Hindi                 |    `hi`       |
| Italien               |     `it`      |
| Japonais              |     `ja`      |
| Coréen                |     `ko`      |
| Norvégien (Bokmål)   |     `no`      |
| Portugais (Brésil)   |    `pt-BR`    |
| Portugais (Portugal) |    `pt-PT`    |
| Espagnol               |     `es`      |
| Turc               |     `tr`      |

[!INCLUDE [Tip for using docker list](../../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Une fois le conteneur sur l’ordinateur hôte, utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter les conteneurs. Chaque conteneur continuera à s’exécuter jusqu’à ce que vous l’arrêtiez.

> [!IMPORTANT]
> * les commandes docker dans les sections suivantes utilisent la barre oblique inverse, `\`, comme caractère de continuation de ligne. Remplacez-la ou supprimez-la en fonction des exigences de votre système d’exploitation hôte. 
> * Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).

Pour exécuter le conteneurAnalyse des sentiments, exécutez la commande `docker run` suivante. Remplacez les espaces réservés suivants par vos valeurs :

| Espace réservé | Valeur | Format ou exemple |
|-------------|-------|---|
| **{API_KEY}** | Clé pour votre ressource Language. Cette information est disponible dans le portail Azure, sur la page **Key and endpoint** (Clé et point de terminaison) de votre ressource. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Point de terminaison pour accéder à l’API. Cette information est disponible dans le portail Azure, sur la page **Key and endpoint** (Clé et point de terminaison) de votre ressource. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{LANGUAGE}** | Langue du conteneur vous voulez exécuter. Veillez à que ceci corresponde à la commande `docker pull` que vous avez utilisée. Notez le trait d’union (`-`) utilisé avant la langue dans l’exemple ci-dessous. | `en` |

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment-{LANGUAGE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cette commande :

* Exécute un conteneur *Analyse des sentiments* à partir de l’image conteneur
* Alloue un cœur de processeur et 8 gigaoctets (Go) de mémoire
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte.

[!INCLUDE [Running multiple containers on the same host](../../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Interroger le point de terminaison de prédiction du conteneur

Le conteneur fournit des API de point de terminaison de prédiction de requête basées sur REST.

Utilisez l’hôte, `http://localhost:5000`, pour les API de conteneur.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Dépannage

Si vous exécutez le conteneur avec un [montage](../../concepts/configure-containers.md#mount-settings) de sortie et la journalisation activée, il génère des fichiers journaux qui sont utiles pour résoudre les problèmes qui se produisent lors du démarrage ou de l’exécution du conteneur.

[!INCLUDE [Cognitive Services FAQ note](../../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Facturation

Les conteneurs Analyse des sentiments envoient des informations de facturation à Azure à l’aide d’une ressource _Language_ sur votre compte Azure.

[!INCLUDE [Container's Billing Settings](../../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](../../concepts/configure-containers.md).

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution du conteneur Analyse des sentiments. En résumé :

* Analyse des sentiments fournit des conteneurs Linux pour Docker
* Les images conteneurs sont téléchargées à partir de Microsoft Container Registry (MCR).
* Les images conteneurs s’exécutent dans Docker.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
> Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services ne transmettent aucune donnée client (p. ex., le texte analysé) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, consultez [Configurer des conteneurs](../../concepts/configure-containers.md).
