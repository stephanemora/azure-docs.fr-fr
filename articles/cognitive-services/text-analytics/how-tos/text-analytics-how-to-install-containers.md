---
title: Installer et exécuter des conteneurs
titleSuffix: Text Analytics - Cognitive Services - Azure
description: Ce tutoriel pas à pas décrit comment télécharger, installer et exécuter des conteneurs pour Analyse de texte.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 99bdb42d9a0d86d0d2acc4a6272e0c802042e6b5
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634906"
---
# <a name="install-and-run-containers"></a>Installer et exécuter des conteneurs

La mise en conteneur est une méthode de distribution de logiciels dans laquelle une application ou un service est packagé en tant qu’image conteneur. La configuration et les dépendances pour l’application ou le service sont inclus dans l’image conteneur. L’image conteneur peut ensuite être déployée sur un hôte conteneur avec peu ou pas de modifications. Les conteneurs sont isolés les uns des autres et l’encombrement du système d’exploitation sous-jacent est inférieur à celui d’une machine virtuelle. Vous pouvez instancier les conteneurs à partir d’images conteneur pour les tâches à court terme et les supprimer quand vous n’avez plus besoin.

Le conteneur Analyse de texte fournit l’ensemble suivant de conteneurs Docker, chacun contenant un sous-ensemble de fonctionnalités :

| Conteneur| Description |
|----------|-------------|
|Extraction d’expressions clés | Extraire des expressions clés pour identifier les points principaux. Par exemple, pour le texte d’entrée « Le repas était délicieux et le personnel adorable », l’API renvoie les principaux points de discussion : « repas » et « personnel adorable ». |
|Détection de la langue | Pour jusqu’à 120 langues maximum, détecte et signale la langue dans laquelle le texte d’entrée est rédigé. Le conteneur signale un code de langue unique pour chaque document qui est inclus dans la requête. Le code de langue est associé à un score indiquant la puissance de l’analyse. |
|Analyse des sentiments | Analyser le texte brut pour obtenir des indices sur un sentiment positif ou négatif. Cette API renvoie un score de sentiment de 0 à 1 pour chaque document, où 1 correspond au sentiment le plus positif. Les modèles d’analyse sont préformés à l’aide d’un corps complet de technologies de texte et de Langage naturel de Microsoft. Pour les [langues sélectionnées](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages.md), l’API peut analyser tout texte brut que vous fournissez, lui attribuer un score, et renvoyer directement les résultats à l’application appelante. |

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="preparation"></a>Préparation

Vous devez respecter les prérequis suivants avant d’utiliser les conteneurs Analyse de texte :

**Moteur Docker** : le moteur Docker doit être installé localement. Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) et [Windows](https://docs.docker.com/docker-for-windows/). Sur Windows, Docker doit être configuré pour prendre en charge les conteneurs Linux. Les conteneurs Docker peuvent également être déployés directement sur [Azure Kubernetes Service](/azure/aks/), sur [Azure Container Instances](/azure/container-instances/) ou sur un cluster [Kubernetes](https://kubernetes.io/) déployé sur [Azure Stack](/azure/azure-stack/). Pour plus d’informations sur le déploiement de Kubernetes sur Azure Stack, consultez [Déployer Kubernetes sur Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

Docker doit être configuré pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation.

**Connaissance de Microsoft Container Registry et de Docker** : vous devez avoir une compréhension élémentaire des concepts de Microsoft Container Registry et de Docker, tels que les registres, les référentiels, les conteneurs et les images de conteneur, ainsi qu’une maîtrise des commandes `docker` de base.  

Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).

### <a name="server-requirements-and-recommendations"></a>Configuration serveur requise et recommandations

Le tableau suivant décrit les cœurs de processeur minimum et recommandés, d’au moins 2,6 gigahertz (GHz) ou plus rapides, et la mémoire, en gigaoctets (Go), à allouer pour chaque conteneur Analyse de texte.

| Conteneur | Minimale | Recommandé |
|-----------|---------|-------------|
|Extraction d’expressions clés | 1 cœur, 2 Go de mémoire | 1 cœur, 4 Go de mémoire |
|Détection de la langue | 1 cœur, 2 Go de mémoire | 1 cœur, 4 Go de mémoire |
|Analyse des sentiments | 1 cœur, 8 Go de mémoire | 1 cœur, 8 Go de mémoire |

## <a name="download-container-images-from-microsoft-container-registry"></a>Télécharger des images conteneur à partir de Microsoft Container Registry

Images conteneur pour Analyse de texte disponibles à partir de Microsoft Container Registry. Le tableau suivant répertorie les référentiels disponibles à partir de Microsoft Container Registry pour les conteneurs Analyse de texte. Chaque référentiel contient une image conteneur, qui doit être téléchargée pour exécuter le conteneur localement.

| Conteneur | Référentiel |
|-----------|------------|
|Extraction d’expressions clés | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Détection de la langue | `mcr.microsoft.com/azure-cognitive-services/language` |
|Analyse des sentiments | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Utilisez la commande [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image de conteneur à partir d’un référentiel. Par exemple, pour télécharger la dernière image de conteneur Extraction de phrases clés à partir du référentiel, utilisez la commande suivante :

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

Pour obtenir une description complète des balises disponibles pour les conteneurs Analyse de texte, consultez les conteneurs suivants dans le Docker Hub :

* [Extraction de phrases clés](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Détection de la langue](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Analyse des sentiments](https://go.microsoft.com/fwlink/?linkid=2018654)

> [!TIP]
> Vous pouvez utiliser la commande [docker images](https://docs.docker.com/engine/reference/commandline/images/) pour répertorier vos images conteneur téléchargées. Par exemple, la commande suivante répertorie l’ID, le référentiel et la balise de chaque image conteneur téléchargée sous la forme d’un tableau :
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Instancier un conteneur à partir d’une image conteneur téléchargée

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour instancier un conteneur à partir d’une image conteneur téléchargée. Par exemple, la commande suivante :

* Instancie un conteneur à partir de l’image conteneur Analyse des sentiments
* Alloue un cœur de processeur et 8 gigaoctets (Go) de mémoire
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Supprime automatiquement le conteneur après sa fermeture

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789

```

> [!IMPORTANT]
> Les options de ligne de commande `Eula`, `Billing` et `ApiKey` doivent être spécifiées pour instancier le conteneur ; sinon, le conteneur ne démarre pas.  Pour plus d'informations, consultez [Facturation](#billing).

Une fois l’instanciation effectuée, vous pouvez appeler des opérations à partir du conteneur à l’aide de l’URI hôte du conteneur. Par exemple, l’URI hôte suivant représente le conteneur Analyse des sentiments qui a été instancié dans l’exemple précédent :

```http
http://localhost:5000/
```

> [!TIP]
> Vous pouvez accéder à la [spécification OpenAPI](https://swagger.io/docs/specification/about/) (ancienne spécification Swagger), qui décrit les opérations prises en charge par un conteneur instancié, à partir de l’URI relatif `/swagger` pour ce conteneur. Par exemple, l’URI suivant permet d’accéder à la spécification OpenAPI pour le conteneur Analyse des sentiments instancié dans l’exemple précédent :
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Vous pouvez soit [appeler les opérations d’API REST](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-call-api) disponibles à partir de votre conteneur, soit utiliser la bibliothèque client [SDK Analyse de texte Azure Cognitive Services](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics) pour appeler ces opérations.  
> [!IMPORTANT]
> Vous devez disposer du SDK Analyse de texte Azure Cognitive Services, version 2.1.0 ou ultérieure, si vous souhaitez utiliser la bibliothèque client avec votre conteneur.

La seule différence entre l’appel d’une opération donnée à partir de votre conteneur et l’appel de cette même opération à partir d’un service correspondant sur Azure est que vous allez utiliser l’URI hôte de votre conteneur au lieu de l’URI hôte d’une région Azure, pour appeler l’opération. Par exemple, si vous souhaitez utiliser une instance Analyse de texte en cours d’exécution dans la région Azure USA Ouest, vous appelez l’opération d’API REST suivante :

```http
POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
```

Si vous souhaitez utiliser un conteneur Extraction de phrases clés en cours d’exécution sur votre ordinateur local sous sa configuration par défaut, vous appelez l’opération d’API REST suivante :

```http
POST http://localhost:5000/text/analytics/v2.0/keyPhrases
```

### <a name="billing"></a>Facturation

Les conteneurs Analyse de texte envoient des informations de facturation à Azure à l’aide d’une ressource Analyse de texte correspondante sur votre compte Azure. Les options de ligne de commande suivantes sont utilisées par les conteneurs Analyse de texte à des fins de facturation :

| Option | Description |
|--------|-------------|
| `ApiKey` | La clé d’API de la ressource Analyse de texte utilisée pour effectuer le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur une clé API pour la ressource Analyse de texte Azure provisionnée, spécifiée dans `Billing`. |
| `Billing` | Le point de terminaison de la ressource Analyse de texte utilisée pour effectuer le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur l’URI de point de terminaison d’une ressource Analyse de texte Azure provisionnée.|
| `Eula` | Indique que vous avez accepté la licence pour le conteneur.<br/>La valeur de cette option doit être définie sur `accept`. |

> [!IMPORTANT]
> Les trois options doivent être spécifiées avec des valeurs valides, sinon le conteneur ne démarre pas.

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution des conteneurs Analyse de texte. En résumé :

* Analyse de texte fournit trois conteneurs Linux pour Docker, intégrant l’extraction de phrases clés, la détection de la langue et l’analyse des sentiments.
* Les images conteneur sont téléchargées à partir d’un registre de conteneurs privé dans Azure.
* Les images conteneur s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le kit SDK pour appeler des opérations dans des conteneurs Analyse de texte en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.
* ** Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent activer les conteneurs pour communiquer les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas de données clients (p. ex., l’image ou le texte analysés) à Microsoft.  

## <a name="next-steps"></a>Étapes suivantes

* Consulter [Configurer des conteneurs](../text-analytics-resource-container-config.md) pour les paramètres de configuration
* Consulter [Vue d’ensemble de l’Analyse de texte](../overview.md) pour en savoir plus sur la détection de phrases clés, la détection de la langue et l’analyse des sentiments  
* Reportez-vous à [l’API Analyse de texte](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) pour plus d’informations sur les méthodes prises en charge par le conteneur.
* Reportez-vous au [Forum aux questions (FAQ)](../text-analytics-resource-faq.md) pour résoudre les problèmes liés à la fonctionnalité Vision par ordinateur.