---
title: Installer et exécuter des conteneurs - Personalizer
titleSuffix: Azure Cognitive Services
description: Comment télécharger, installer et exécuter des conteneurs pour Personalizer.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/09/2019
ms.author: edjez
ms.openlocfilehash: a197531a7c78823271c0a5fa5413b76746f63a9a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507029"
---
# <a name="install-and-run-personalizer-containers"></a>Installer et exécuter des conteneurs Personalizer

Personalizer Service a le(s) conteneur(s) suivant(s) : 

|Fonction|Caractéristiques|
|-|-|
|Personalizer|Déterminez la meilleure action du contexte actuel du contenu et de l’utilisateur.|

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Vous devez respecter les prérequis suivants avant d’utiliser les conteneurs Personalizer Service :

|Obligatoire|Objectif|
|--|--|
|Moteur Docker| Vous avez besoin d’un moteur Docker installé sur un [ordinateur hôte](#the-host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. <br><br> **Sur Windows**, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.<br><br>|
|Bonne connaissance de Docker | Vous devez avoir une compréhension élémentaire des concepts Docker, notamment les registres, référentiels, conteneurs et images conteneurs, ainsi qu’une maîtrise des commandes `docker` de base.| 
|Ressource Personalizer Service |Pour pouvoir utiliser ces conteneurs, vous devez avoir :<br><br>Une ressource _Personalizer Service_ Azure permettant d’obtenir la clé de facturation et l’URI du point de terminaison de facturation associés. Les deux valeurs, disponibles dans les pages Vue d’ensemble de Personalizer Service et Clés du portail Azure, sont nécessaires au démarrage du conteneur.<br><br>**{BILLING_KEY}**  : clé de ressource<br><br>**{BILLING_ENDPOINT_URI}**  : exemple d’UURI de point de terminaison : `https://westus.api.cognitive.microsoft.com/`|

### <a name="the-host-computer"></a>L’ordinateur hôte

L’**hôte** est l’ordinateur qui exécute le conteneur docker. Il peut s’agir d’un ordinateur local ou d’un service d’hébergement docker dans Azure, notamment :

* [Azure Kubernetes Service](https://docs.microsoft.com/aks/index.yml)
* [Azure Container Instances](https://docs.microsoft.com/container-instances/index.yml)
* Cluster [Kubernetes](https://kubernetes.io/) déployé sur [Azure Stack](https://docs.microsoft.com/azure-stack/index.yml). Pour plus d’informations, consultez [Déployer Kubernetes sur Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).

### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

Le tableau suivant décrit la quantité minimale et recommandée de cœurs de processeur et de mémoire à allouer pour chaque conteneur Personalizer Service.

| Conteneur | Minimale | Recommandé |
|-----------|---------|-------------|
|Personalizer | 1 cœur, 4 Go de mémoire | 2 cœurs, 8 Go de mémoire |

Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

Le conteneur doit pouvoir se connecter à Azure EventHub pour relayer les informations des appels de classement et de récompense au serveur Personalizer dans Azure, et doit pouvoir se connecter à l’API Personalizer pour charger la configuration requise et les derniers modèles Machine Learning.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

Les images conteneur pour Personalizer Service sont disponibles. 

| Conteneur | Référentiel |
|-----------|------------|
| Personalizer | `mcr.microsoft.com/azure-cognitive-services/personalizer:latest` |

> [!TIP]
> Vous pouvez utiliser la commande [docker images](https://docs.docker.com/engine/reference/commandline/images/) pour lister vos images conteneurs téléchargées. Par exemple, la commande suivante liste l’ID, le référentiel et la balise de chaque image conteneur téléchargée dans un tableau :
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/personalizer                 latest
>  ``` 

### <a name="docker-pull-for-the-personalizer-service-container"></a>docker pull pour le conteneur Personalizer Service

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/personalizer:latest
```

## <a name="how-the-container-works"></a>Comment fonctionne le conteneur

Le conteneur Personalizer représente la partie client de Personalizer Service. Lorsque le conteneur s’exécute, il obtient les modèles et les paramètres de configuration de Personalizer Service dans le cloud Azure. Le service de conteneur utilise ces informations pour répondre aux demandes de **classement** et de **récompense**. Le conteneur envoie également ces demandes à la ressource Personalizer dans le cloud Azure. Ces informations sont ensuite utilisées pour entraîner votre modèle de boucle Personalizer selon le paramètre actuel de fréquence de mise à jour du modèle. Le modèle mis à jour est renvoyé au conteneur. 

![Architecture locale pour les clients du conteneur Personalizer](./media/personalizer-container-howto/personalizer-container-architecture.png)

## <a name="how-to-use-the-container"></a>Comment utiliser le conteneur

Une fois que le conteneur est sur l’[ordinateur hôte](#the-host-computer), appliquez la procédure suivante pour travailler avec le conteneur.

1. [Exécutez le conteneur](#run-the-container-with-docker-run) avec les paramètres de facturation requis. D’autres [exemples](personalizer-container-configuration.md#example-docker-run-commands) de commande `docker run` sont disponibles. 
1. [Interrogez le point de terminaison de prédiction du conteneur](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter l’un des trois conteneurs. La commande utilise les paramètres suivants :

| Placeholder | Valeur |
|-------------|-------|
|{BILLING_KEY} | Cette clé, qui permet de démarrer le conteneur, est disponible dans la page Clés de Personalizer Service du portail Azure.  |
|{BILLING_ENDPOINT_URI} | La valeur de l’URI de point de terminaison de facturation est disponible dans la page Vue d’ensemble de Personalizer Service du portail Azure.|

Remplacez ces paramètres par vos propres valeurs dans l’exemple de commande `docker run` suivant.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/personalizer\
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Cette commande :

* Exécute un conteneur Personalizer Service à partir de l’image conteneur.
* Alloue un cœur de processeur et 4 gigaoctets (Go) de mémoire.
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte. 

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).

### <a name="run-multiple-containers-on-the-same-host"></a>Exécuter plusieurs conteneurs sur le même hôte

Si vous envisagez d’exécuter plusieurs conteneurs avec les ports exposés, veillez à exécuter chaque conteneur avec un port différent. Par exemple, exécutez le premier conteneur sur le port 5000 et le second conteneur sur le port 5001.

Remplacez `<container-registry>` et `<container-name>` par les valeurs des conteneurs que vous utilisez. Ils n’ont pas besoin d’être le même conteneur. Vous pouvez avoir le conteneur Personalizer et le conteneur LUIS qui s’exécutent ensemble sur l’HÔTE, ou plusieurs conteneurs Personalizer qui s’exécutent. 

Exécutez le premier conteneur sur le port 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Exécutez le deuxième conteneur sur le port 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Tous les conteneurs suivants doivent être sur un port différent. 

## <a name="query-the-containers-prediction-endpoint"></a>Interroger le point de terminaison de prédiction du conteneur

Le conteneur fournit des API de point de terminaison de prédiction de requête basées sur REST. 

Utilisez l’hôte, https://localhost:5000, pour les API de conteneur.

## <a name="stop-the-container"></a>Arrêter le conteneur

Pour arrêter le conteneur, dans l’environnement de ligne de commande où le conteneur est en cours d’exécution, appuyez sur **Ctrl+C**.

## <a name="troubleshoot"></a>Résolution des problèmes

Si vous exécutez le conteneur avec un [montage](personalizer-container-configuration.md#mount-settings) de sortie et la journalisation activée, il génère des fichiers journaux qui sont utiles pour résoudre les problèmes qui se produisent lors du démarrage ou de l’exécution du conteneur. 

## <a name="container-api-documentation"></a>Documentation sur l’API de conteneur

Le conteneur fournit un ensemble complet de documentation pour les points de terminaison ainsi qu’une fonctionnalité `Try it now`. Cette fonctionnalité vous permet d’entrer vos paramètres dans un formulaire HTML basé sur le web et d’effectuer la requête sans avoir à écrire du code. Une fois la requête retournée, un exemple de commande CURL est fourni pour illustrer le format du corps et des en-têtes HTTP requis. 

> [!TIP]
> Consultez la [spécification OpenAPI](https://swagger.io/docs/specification/about/), qui décrit les opérations d’API prises en charge par le conteneur, à partir de l’URI relatif `/swagger`. Par exemple : 
>
>  ```http
>  http://localhost:5000/swagger
>  ```

## <a name="billing"></a>Facturation

Les conteneurs Personalizer Service envoient des informations de facturation à Azure à l’aide d’une ressource _Personalizer Service_ sur votre compte Azure. 

Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment.  

La commande `docker run` utilise les arguments suivants lors de la facturation :

| Option | Description |
|--------|-------------|
| `ApiKey` | Clé API de la ressource _Personalizer Service_ utilisée pour le suivi des informations de facturation. |
| `Billing` | Point de terminaison de la ressource _Personalizer Service_ utilisé pour le suivi des informations de facturation.|
| `Eula` | Indique que vous avez accepté la licence pour le conteneur.<br/>La valeur de cette option doit être `accept`. |

> [!IMPORTANT]
> Les trois options doivent être spécifiées avec des valeurs valides ; sinon, le conteneur ne démarre pas.

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](personalizer-container-configuration.md).

## <a name="summary"></a>Résumé

Dans cet article, vous avez appris les concepts et le workflow pour télécharger, installer et exécuter des conteneurs Personalizer Service. En résumé :

* Personalizer Service fournit des conteneurs Linux pour Docker, encapsulant la personnalisation.
* Les images conteneur sont téléchargées à partir de Microsoft Container Registry (MCR) dans Azure.
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le kit SDK pour appeler des opérations dans des conteneurs Personalizer Service en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
> Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Personalizer envoient également les données de demande au service correspondant dans Azure pour les entraîner en ligne et obtenir des modèles régulièrement mis à jour à partir d’Azure.

## <a name="next-steps"></a>Étapes suivantes

[Guide pratique pour configurer un conteneur Docker pour la commande `Docker Run`](personalizer-container-configuration.md)