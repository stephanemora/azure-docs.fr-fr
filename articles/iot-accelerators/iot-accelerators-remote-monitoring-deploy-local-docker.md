---
title: Déployer localement la solution de supervision à distance – Docker – Azure | Microsoft Docs
description: Ce guide pratique montre comment déployer l’accélérateur de solution de supervision à distance sur votre ordinateur local en utilisant Docker à des fins de test et de développement.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "73888838"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Déployer localement l’accélérateur de solution de supervision à distance - Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Cet article montre comment déployer l’accélérateur de solution de supervision à distance sur votre ordinateur local à des fins de test et de développement. Vous apprendrez à déployer les microservices dans des conteneurs Docker locaux. Les déploiements de microservices locaux utilisent les services de cloud suivants : IoT Hub, Cosmos DB, Azure Stream Analytics et Azure Time Series Insights dans le cloud.

Si vous souhaitez exécuter l’accélérateur de solution de supervision à distance dans un IDE sur votre ordinateur local, consultez [Déployer localement l’accélérateur de solution de supervision à distance - Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Conditions préalables requises

Pour déployer les services Azure utilisés par l’accélérateur de solution de supervision à distance, vous avez besoin d’un abonnement Azure actif.

Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez [Essai gratuit Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuration de la machine

Pour terminer le déploiement local, vous devez avoir installé les outils suivants sur votre machine de développement local :

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) : si vous envisagez d’apporter des changements aux microservices.
* [Node.js v8](https://nodejs.org/) : Ce logiciel est un prérequis pour l’interface de ligne de commande PCS que les scripts utilisent pour créer des ressources Azure. N’utilisez pas Node.js v10.

> [!NOTE]
> Ces outils sont disponibles sur de nombreuses plateformes, notamment Windows, Linux et iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Exécuter les microservices dans Docker

Ouvrez une nouvelle invite de commandes pour vous assurer d’avoir accès aux variables d’environnement définie par le script **start.cmd**. Sur Windows, vous pouvez vérifier que les variables d’environnement sont définies en exécutant la commande suivante :

```cmd
set PCS
```

Cette commande affiche toutes les variables d’environnement définies par le script **start.cmd**.

Assurez-vous que Docker est en cours d’exécution sur votre ordinateur local.
> [!NOTE]
> Docker doit exécuter des [conteneurs Linux](https://docs.docker.com/docker-for-windows/) s’il s’exécute sur Windows.

Les microservices exécutés dans les conteneurs Docker locaux doivent accéder aux services cloud Azure. Vous pouvez tester la connexion Internet de votre environnement Docker à l’aide de la commande suivante, pour envoyer un ping à une adresse Internet depuis un conteneur :

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Pour exécuter l’accélérateur de solution, accédez au dossier **services\\scripts\\local** dans votre environnement de ligne de commande et exécutez la commande suivante :

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Assurez-vous de [partager un lecteur local](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) avec Docker avant d’exécuter `docker-compose up`.

La première fois que vous exécutez cette commande, Docker télécharge les images des microservices à partir de Docker Hub pour créer les conteneurs localement. Lors des exécutions suivantes, Docker exécute les conteneurs immédiatement.

> [!TIP]
> Microsoft publie fréquemment de nouvelles images Docker avec de nouvelles fonctionnalités. Vous pouvez utiliser le jeu de commandes suivant pour nettoyer vos conteneurs Docker locaux et les images correspondantes avant d’extraire les plus récents :

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Vous pouvez utiliser un interpréteur de commandes distinct pour afficher les journaux d’activité du conteneur. Tout d’abord, trouvez l’ID du conteneur à l’aide de la commande `docker ps`. Utilisez ensuite `docker logs {container-id} --tail 1000` pour afficher les 1000 dernières entrées pour le conteneur spécifié.

### <a name="start-the-stream-analytics-job"></a>Démarrage de la tâche Stream Analytics

Suivez ces étapes pour démarrer la tâche Stream Analytics :

1. Accédez au [portail Azure](https://portal.azure.com).
1. Accédez au **groupe de ressources** créé pour votre solution. Le nom du groupe de ressources est le nom que vous avez choisi pour votre solution lorsque vous avez exécuté le script **start.cmd**.
1. Cliquez sur la **tâche Stream Analytics** dans la liste des ressources.
1. Dans la **vue d’ensemble** de la tâche Stream Analytics, sélectionnez **Démarrer**. Cliquez ensuite sur **Démarrer** pour démarrer la tâche.

### <a name="connect-to-the-dashboard"></a>Connexion au tableau de bord

Pour accéder au tableau de bord de la solution de supervision à distance, accédez à `http://localhost:8080` dans votre navigateur. Vous pouvez maintenant utiliser l’interface utilisateur web et les microservices locaux.

## <a name="clean-up"></a>Nettoyer

Pour éviter des frais superflus, lorsque vous avez terminé votre test, supprimez les services cloud de votre abonnement Azure. Pour supprimer les services, accédez au [portail Azure](https://ms.portal.azure.com) et supprimez le groupe de ressources créé par le script **start.cmd**.

Utilisez la commande `docker-compose down --rmi all` pour supprimer les images Docker et libérer de l’espace sur votre machine locale. Vous pouvez également supprimer la copie locale du dépôt de surveillance à distance créé quand vous avez cloné le code source à partir de GitHub.

## <a name="next-steps"></a>Étapes suivantes

La solution de supervision à distance étant déployée, l’étape suivante consiste à [explorer les fonctionnalités du tableau de bord des solutions](quickstart-remote-monitoring-deploy.md).
