---
title: Installer et exécuter des conteneurs - détecteur d’anomalies
titleSuffix: Azure Cognitive Services
description: Utiliser des algorithmes avancés de l’API de détecteur d’anomalies pour identifier les anomalies dans vos données de série chronologique.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/07/2019
ms.author: aahi
ms.openlocfilehash: cc82e091ae4c033bda7f1d91c9aed36bb081de88
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233620"
---
# <a name="install-and-run-anomaly-detector-containers"></a>Installer et exécuter des conteneurs de détecteur d’anomalies

Le détecteur d’anomalies a le conteneur suivant : 

|Fonction|Caractéristiques|
|-|-|
|Détecteur d’anomalies| <li> Détecte les anomalies qu’ils se produisent en temps réel. <li> Détecte les anomalies tout au long de votre jeu de données en tant que lot. <li> Déduit la plage normale attendue de vos données. <li> Réglage de la sensibilité de détection des anomalies prend en charge afin de mieux ajuster vos données. |

Pour plus d’informations sur les API, consultez le site :
* [En savoir plus sur le service de l’API de détecteur d’anomalies](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables

Vous devez remplir les conditions préalables suivantes avant d’utiliser des conteneurs de détecteur d’anomalies :

|Obligatoire|Objectif|
|--|--|
|Moteur Docker| Vous avez besoin d’un moteur Docker installé sur un [ordinateur hôte](#the-host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. <br><br> **Sur Windows**, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.<br><br>|
|Bonne connaissance de Docker | Vous devez avoir une compréhension élémentaire des concepts Docker, notamment les registres, référentiels, conteneurs et images conteneurs, ainsi qu’une maîtrise des commandes `docker` de base.| 
|Ressources de détecteur d’anomalies |Pour pouvoir utiliser ces conteneurs, vous devez avoir :<br><br>Un _détecteur d’anomalies_ des ressources Azure pour obtenir la clé de facturation associée et l’URI de point de terminaison facturation. Les deux valeurs sont disponibles sur les pages de vue d’ensemble du détecteur d’anomalies et les clés du portail Azure et sont nécessaires pour démarrer le conteneur.<br><br>**{BILLING_KEY}**  : clé de ressource<br><br>**{BILLING_ENDPOINT_URI}**  : exemple d’UURI de point de terminaison : `https://westus2.api.cognitive.microsoft.com`|

## <a name="request-access-to-the-container-registry"></a>Demander l’accès au registre de conteneurs

Vous devez tout d’abord terminer et envoyer la [formulaire de demande de conteneur de détecteur d’anomalies](https://aka.ms/adcontainer) pour demander l’accès au conteneur.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>L’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

Le tableau suivant décrit les minimale et recommandée de cœurs de processeur et mémoire à allouer pour le conteneur de détecteur d’anomalies.

| Requêtes par seconde (requêtes par seconde) | Minimale | Recommandé |
|-----------|---------|-------------|
| 10 REQUÊTES PAR SECONDE | 4 cœur, 1 Go de mémoire | 8 cœurs 2 Go de mémoire |
| 20 REQUÊTES PAR SECONDE | 8 cœurs, 2 Go de mémoire | 16 cœurs 4 Go de mémoire |

Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

Utilisez la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur.

| Conteneur | Dépôt |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-anomaly-detector-container"></a>Extraction de docker pour le conteneur de détecteur d’anomalies

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Comment utiliser le conteneur

Une fois que le conteneur est sur l’[ordinateur hôte](#the-host-computer), appliquez la procédure suivante pour travailler avec le conteneur.

1. [Exécutez le conteneur](#run-the-container-with-docker-run) avec les paramètres de facturation requis. D’autres [exemples](anomaly-detector-container-configuration.md#example-docker-run-commands) de commande `docker run` sont disponibles. 
1. [Interrogez le point de terminaison de prédiction du conteneur](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter l’un des trois conteneurs. La commande utilise les paramètres suivants :

| Placeholder | Valeur |
|-------------|-------|
|{BILLING_KEY} | Cette clé est utilisée pour démarrer le conteneur et est disponible sur la page des clés de détecteur d’anomalies du portail Azure.  |
|{BILLING_ENDPOINT_URI} | La valeur de l’URI de point de terminaison facturation est disponible sur la page de vue d’ensemble du détecteur d’anomalies du portail Azure.|

Remplacez ces paramètres par vos propres valeurs dans l’exemple de commande `docker run` suivant.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Cette commande :

* Exécute un conteneur de détecteur d’anomalies à partir de l’image de conteneur
* Alloue un cœur de processeur et 4 gigaoctets (Go) de mémoire.
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte. 

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Plusieurs conteneurs en cours d’exécution sur le même hôte

Si vous envisagez d’exécuter plusieurs conteneurs avec les ports exposés, veillez à exécuter chaque conteneur avec un port différent. Par exemple, exécutez le premier conteneur sur le port 5000 et le second conteneur sur le port 5001.

Remplacez `<container-registry>` et `<container-name>` par les valeurs des conteneurs que vous utilisez. Ils n’ont pas besoin d’être le même conteneur. Vous pouvez avoir le conteneur de détecteur d’anomalies et le conteneur de LUIS qui exécutent ensemble sur l’ordinateur hôte, ou vous pouvez avoir plusieurs conteneurs de détecteur d’anomalies en cours d’exécution. 

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

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous exécutez le conteneur avec un [montage](anomaly-detector-container-configuration.md#mount-settings) de sortie et la journalisation activée, il génère des fichiers journaux qui sont utiles pour résoudre les problèmes qui se produisent lors du démarrage ou de l’exécution du conteneur. 

## <a name="billing"></a>Facturation

L’envoi de conteneurs détecteur d’anomalies, informations de facturation pour Azure, en utilisant un _détecteur d’anomalies_ ressource sur votre compte Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](anomaly-detector-container-configuration.md).

## <a name="summary"></a>Résumé

Dans cet article, vous avez appris des concepts et des flux de travail pour le téléchargement, l’installation et exécution des conteneurs de détecteur d’anomalies. En résumé :

* Détecteur d’anomalies fournit un conteneur Linux pour Docker, qui encapsule la détection d’anomalie avec Visual Studio batch de diffusion en continu, l’inférence de la plage attendue et le réglage de la sensibilité.
* Images de conteneur sont téléchargés à partir d’un Azure Container Registry privé dédié à la version préliminaire de conteneurs.
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le Kit de développement logiciel pour appeler des opérations dans des conteneurs de détecteur d’anomalies en spécifiant l’URI du conteneur hôte.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
> Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs de Services cognitifs ne pas envoient des données client (par exemple, les données de série chronologique qui sont en cours d’analyse) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, passez en revue [Configurer des conteneurs](anomaly-detector-container-configuration.md).
* [En savoir plus sur le service de l’API de détecteur d’anomalies](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
