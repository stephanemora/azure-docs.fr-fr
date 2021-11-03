---
title: Utiliser des conteneurs Docker pour appeler Extraction de phrases clés localement
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser des conteneurs Docker pour appeler Extraction de phrases clés localement.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-key-phrase, ignite-fall-2021
keywords: localement, Docker, conteneur, traitement en langage naturel
ms.openlocfilehash: 51d7c53beaeabe57df5fe817cdb5c16c6d1d4252
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098166"
---
# <a name="install-and-run-key-phrase-extraction-containers"></a>Installer et exécuter des conteneurs Extraction de phrases clés


Les conteneurs vous permettent d’héberger l’API Extraction de phrases clés sur votre propre infrastructure. Si vous avez des exigences de sécurité ou de gouvernance des données qui ne peuvent pas être satisfaites en appelant Extraction de phrases clés à distance, les conteneurs peuvent être une bonne option.


> [!NOTE]
> * Le compte gratuit est limité à 5 000 enregistrements texte par mois et seuls les [niveaux tarifaires](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics) **Gratuit** et **Standard** sont valides pour les conteneurs. Pour plus d’informations sur les taux de requête de transaction, consultez [Tarification - Transfert de données](call-api.md#data-limits).

Les conteneurs vous permettent d’exécuter les API Extraction de phrases clés dans votre propre environnement et sont très utiles pour répondre à vos besoins spécifiques en matière de sécurité et de gouvernance des données. Les conteneurs Extraction de phrases clés fournissent un traitement en langage naturel avancé sur le texte brut et comprennent trois fonctions principales : analyse des sentiments, extraction de phrases clés et détection de la langue.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/).
* [Docker](https://docs.docker.com/) installé sur un ordinateur hôte. Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. 
    * Sous Windows, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.
    * Vous devez disposer d’une connaissance élémentaire des [concepts liés à Docker](https://docs.docker.com/get-started/overview/). 
* Une ressource de langue <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Créer une ressource de langue"  target="_blank"></a> avec le [niveau tarifaire](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) gratuit (F0) ou standard (S).

[!INCLUDE [Gathering required parameters](../../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>Spécifications et recommandations relatives à l’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../../../includes/cognitive-services-containers-host-computer.md)]

Le tableau suivant décrit les spécifications minimales et recommandées pour les conteneurs Extraction de phrases clés disponibles. Chaque processeur doit être cadencé à au moins 2,6 gigahertz (GHz). Les transactions par seconde (TPS) autorisées sont également indiquées.

|  | Configuration minimale pour l’hôte | Configuration recommandée pour l’hôte | TPS minimales | TPS maximales|
|---|---------|-------------|--|--|
| **Extraction d’expressions clés**   | 1 cœur, 2 Go de mémoire | 1 cœur, 4 Go de mémoire |15 | 30| 

Le cœur d’UC et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

Utilisez la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur à partir de Microsoft Container Registry.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```

[!INCLUDE [Tip for using docker list](../../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Une fois le conteneur sur l’ordinateur hôte, utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter les conteneurs. Chaque conteneur continuera à s’exécuter jusqu’à ce que vous l’arrêtiez.

> [!IMPORTANT]
> * les commandes docker dans les sections suivantes utilisent la barre oblique inverse, `\`, comme caractère de continuation de ligne. Remplacez-la ou supprimez-la en fonction des exigences de votre système d’exploitation hôte. 
> * Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).
> * Les conteneurs d’analyse des sentiments et de détection de la langue utilisent la version v3 de l’API et sont en disponibilité générale. Le conteneur Extraction de phrases clés utilise la version 2 de l’API et est en préversion.

Pour exécuter le conteneur *Extraction de phrases clés*, exécutez la commande `docker run` suivante. Remplacez les espaces réservés suivants par vos valeurs :

| Espace réservé | Valeur | Format ou exemple |
|-------------|-------|---|
| **{API_KEY}** | Clé de votre ressource Extraction de phrases clés. Cette information est disponible dans le portail Azure, sur la page **Key and endpoint** (Clé et point de terminaison) de votre ressource. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Point de terminaison pour accéder à l’API Extraction de phrases clés. Cette information est disponible dans le portail Azure, sur la page **Key and endpoint** (Clé et point de terminaison) de votre ressource. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cette commande :

* Exécute un conteneur *Extraction de phrases clés* à partir d’une image conteneur
* Alloue un cœur de processeur et 4 gigaoctets (Go) de mémoire.
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

Les conteneurs Extraction de phrases clés envoient des informations de facturation à Azure en utilisant une ressource _Extraction de phrases clés_ sur votre compte Azure. 

[!INCLUDE [Container's Billing Settings](../../../../../includes/cognitive-services-containers-how-to-billing-info.md)]
\
## <a name="summary"></a>Récapitulatif

Dans cet article, vous avez découvert les concepts et le flux de travail pour télécharger, installer et exécuter des conteneurs Extraction de phrases clés. En résumé :

* Extraction de phrases clés fournit des conteneurs Linux pour Docker.
* Les images conteneurs sont téléchargées à partir de Microsoft Container Registry (MCR).
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le SDK pour appeler des opérations dans les conteneurs Extraction de phrases clés en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
> Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services ne transmettent aucune donnée client (p. ex., le texte analysé) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, consultez [Configurer des conteneurs](../../concepts/configure-containers.md).
