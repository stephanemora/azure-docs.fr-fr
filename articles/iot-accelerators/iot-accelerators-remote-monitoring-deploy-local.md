---
title: Déployer la solution de surveillance à distance localement - Azure | Microsoft Docs
description: Ce tutoriel vous montre comment déployer l’accélérateur de solution de surveillance à distance sur votre ordinateur local pour le test et le développement.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/07/2018
ms.topic: conceptual
ms.openlocfilehash: 21bc8c27a44c940279b0c5bdcdbe04e579dc4bfa
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188659"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>Déployer l’accélérateur de solution de surveillance à distance localement

Cet article montre comment déployer l’accélérateur de solution de surveillance à distance sur votre ordinateur local à des fins de test et de développement. Cette approche déploie les microservices vers un conteneur Docker local et utilise les services de stockage Azure, IoT Hub, et Cosmos DB dans le cloud. Vous utilisez l’interface CLI des accélérateurs de solution (solutions préconfigurées) pour déployer les services cloud Azure.

## <a name="prerequisites"></a>Prérequis

Pour déployer les services Azure utilisés par l’accélérateur de solution de surveillance à distance, vous avez besoin d’un abonnement Azure actif.

Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).

Pour terminer le déploiement local, vous devez avoir installé les outils suivants sur votre machine de développement local :

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) -ce logiciel est requis pour les solutions préconfigurées CLI.
* Solutions préconfigurées CLI
* Dépôt local du code source

> [!NOTE]
> Ces outils sont disponibles sur de nombreuses plateformes, notamment Windows, Linux et iOS.

### <a name="install-the-pcs-cli"></a>Installez les solutions préconfigurées CLI

Pour installer l’interface CLI des solutions préconfigurées via npm, exécutez la commande suivante dans votre environnement de ligne de commande :

```cmd/sh
npm install iot-solutions -g
```

Pour plus d’informations sur l’interface CLI, consultez [How to use the CLI](https://github.com/Azure/pcs-cli/blob/master/README.md) (Comment utiliser l’interface CLI).

### <a name="download-the-source-code"></a>Télécharger le code source

 Le dépôt de code source de surveillance à distance inclut les fichiers de configuration Docker dont vous avez besoin pour télécharger, configurer et exécuter les images Docker qui contiennent les microservices. Pour cloner le dépôt et en créer une version locale, accédez à un dossier approprié sur votre ordinateur local via votre ligne de commande ou terminal préféré et exécutez l’une des commandes suivantes :

Pour installer les implémentations Java des microservices, exécutez :

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

Pour installer les implémentations .Net des microservices, exécutez :

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Dépôt et sous-modules des solutions préconfigurées de surveillance à distance [ [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) | [.Net](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) ]

> [!NOTE]
> Ces commandes téléchargent le code source pour tous les microservices. Bien que vous n’ayez pas besoin du code source pour exécuter les microservices dans Docker, le code source est utile si vous envisagez de modifier ultérieurement la solution préconfigurée et de tester vos modifications localement.

## <a name="deploy-the-azure-services"></a>Déployer des services Azure

Bien que cet article explique comment exécuter les microservices localement, ils dépendent de l’exécution de trois services Azure dans le cloud. Vous pouvez déployer ces services Azure [manuellement via le portail Azure](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup) ou utiliser l’interface CLI des solutions préconfigurées. Cet article explique comment utiliser l’outil `pcs`.

### <a name="sign-in-to-the-cli"></a>Se connecter à l’interface CLI

Avant de pouvoir déployer l’accélérateur de solution, vous devez vous connecter à votre abonnement Azure à l’aide de l’interface CLI comme suit :

```cmd/sh
pcs login
```

Suivez les instructions à l’écran pour effectuer le processus de connexion. Assurez-vous de ne cliquer nulle part à l’intérieur de l’interface CLI ou la connexion peut échouer. Vous verrez un message de connexion réussie dans l’interface CLI si vous êtes connecté. 

### <a name="run-a-local-deployment"></a>Exécuter un déploiement local

Utilisez la commande suivante pour démarrer le déploiement local. Vous créez ainsi les ressources Azure nécessaires et affichez les variables d’environnement dans la console. 

```cmd/pcs
pcs -s local
```

Le script vous invite à entrer les informations suivantes :

* Le nom de la solution.
* Sélectionnez l’abonnement Azure à utiliser.
* L’emplacement du centre de données Azure à utiliser.

> [!NOTE]
> Le script crée une instance IoT Hub, une instance Cosmos DB et un compte de stockage Azure dans un groupe de ressources dans votre abonnement Azure. Le nom du groupe de ressources est le nom de la solution que vous avez choisi quand vous avez exécuté l’outil `pcs` ci-dessus. 

> [!IMPORTANT]
> L’exécution du script prend plusieurs minutes. Lorsqu’elle est terminée, vous voyez un message `Copy the following environment variables to /scripts/local/.env file:`. Copiez les définitions des variables d’environnement à la suite du message ; vous les utiliserez dans une étape ultérieure.

## <a name="run-the-microservices-in-docker"></a>Exécuter les microservices dans Docker

Pour exécuter les microservices dans Docker, modifiez d’abord le fichier **scripts\\local\\.env** dans votre copie locale du dépôt que vous avez cloné lors d’une étape antérieure ci-dessus. Remplacez tout le contenu du fichier par les définitions des variables d’environnement que vous avez notées quand vous avez exécuté la commande `pcs` au cours de la dernière étape. Ces variables d’environnement permettent aux microservices dans le conteneur Docker de se connecter aux services Azure créés par l’outil `pcs`.

Pour exécuter l’accélérateur de solution, accédez au dossier **scripts\local** dans votre environnement de ligne de commande et exécutez la commande suivante :

```cmd\sh
docker-compose up
```

La première fois que vous exécutez cette commande, Docker télécharge les images des microservices à partir de Docker Hub pour créer les conteneurs localement. Lors des exécutions suivantes, Docker exécute les conteneurs immédiatement.

Vous pouvez utiliser un interpréteur de commandes distinct pour afficher les journaux du conteneur. Tout d’abord, trouvez l’ID du conteneur à l’aide de la commande `docker ps -a`. Utilisez ensuite `docker logs {container-id} --tail 1000` pour afficher les 1 000 dernières entrées de journal pour le conteneur spécifié.

Pour accéder au tableau de bord de la solution de surveillance à distance, accédez à [http://localhost:8080](http://localhost:8080) dans votre navigateur.

## <a name="clean-up"></a>Nettoyer

Pour éviter les coûts inutiles, lorsque vous avez terminé votre test, supprimez les services cloud de votre abonnement Azure. Pour supprimer les services, le plus simple consiste à accéder au [portail Azure](https://ms.portal.azure.com) et à supprimer le groupe de ressources créé par l’outil `pcs`.

Utilisez la commande `docker-compose down --rmi all` pour supprimer les images Docker et libérer de l’espace sur votre machine locale. Vous pouvez également supprimer la copie locale du dépôt de surveillance à distance créé quand vous avez cloné le code source à partir de GitHub.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Configurer un environnement de développement local
> * Configurer l’accélérateur de solution
> * Déployer l’accélérateur de solution
> * Établir une connexion à l’accélérateur de solution

La solution de surveillance à distance étant déployée, l’étape suivante consiste à [explorer les fonctionnalités du tableau de bord des solutions](quickstart-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->