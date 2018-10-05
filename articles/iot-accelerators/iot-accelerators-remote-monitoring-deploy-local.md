---
title: Déployer la solution de supervision à distance localement - Azure | Microsoft Docs
description: Ce guide pratique montre comment déployer l’accélérateur de solution de supervision à distance sur votre ordinateur local à des fins de test et de développement.
author: asdonald
manager: timlt
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/26/2018
ms.topic: conceptual
ms.openlocfilehash: 7007b1406dbcfab3af4700418ac2ce07b9e521c0
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407431"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>Déployer l’accélérateur de solution de supervision à distance localement

Cet article montre comment déployer l’accélérateur de solution de supervision à distance sur votre ordinateur local à des fins de test et de développement. L’approche décrite dans cet article déploie les microservices sur un conteneur Docker local et utilise les services IoT Hub, Cosmos DB et Azure Time Series Insights dans le cloud. Pour savoir comment exécuter l’accélérateur de solution de supervision à distance dans un IDE sur votre ordinateur local, consultez [Starting Microservices on local environment](https://github.com/Azure/remote-monitoring-services-java/blob/master/docs/LOCAL_DEPLOYMENT.md) sur GitHub.

## <a name="prerequisites"></a>Prérequis

Pour déployer les services Azure utilisés par l’accélérateur de solution de supervision à distance, vous avez besoin d’un abonnement Azure actif.

Si vous n’avez pas de compte, vous pouvez créer un compte d’essai gratuit en quelques minutes. Pour plus d’informations, consultez [Essai gratuit Azure](http://azure.microsoft.com/pricing/free-trial/).

Pour terminer le déploiement local, vous devez avoir installé les outils suivants sur votre machine de développement local :

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [Node.js v8](https://nodejs.org/) : Ce logiciel est un prérequis pour l’interface de ligne de commande PCS que les scripts utilisent pour créer des ressources Azure. N’utilisez pas Node.js v10.

> [!NOTE]
> Ces outils sont disponibles sur de nombreuses plateformes, notamment Windows, Linux et iOS.

### <a name="download-the-source-code"></a>Télécharger le code source

Le dépôt GitHub du code source de la supervision à distance contient les fichiers de configuration Docker dont vous avez besoin pour télécharger, configurer et exécuter les images Docker qui contiennent les microservices. Pour cloner le dépôt et en créer une version locale, utilisez votre environnement de ligne de commande pour accéder à un dossier approprié sur votre ordinateur local et exécutez l’une des commandes suivantes :

Pour télécharger la dernière version des implémentations de microservices Java, exécutez :

```cmd/sh
git clone https://github.com/Azure/remote-monitoring-services-java.git
```

Pour télécharger la dernière version des implémentations de microservices .NET, exécutez :

```cmd\sh
git clone https://github.com/Azure/remote-monitoring-services-dotnet.git
```

> [!NOTE]
> Ces commandes téléchargent le code source de tous les microservices en plus des scripts que vous utilisez pour exécuter les microservices localement. Même si vous n’avez pas besoin du code source pour exécuter les microservices dans Docker, vous pouvez l’utiliser si vous envisagez de modifier ultérieurement l’accélérateur de solution et de tester vos modifications localement.

## <a name="deploy-the-azure-services"></a>Déployer des services Azure

Bien que cet article explique comment exécuter les microservices localement, ceux-ci dépendent de l’exécution des services Azure exécutés dans le cloud. Vous pouvez déployer ces services Azure [manuellement via le portail Azure](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup) ou utiliser le script fourni. Les exemples de script suivants supposent que vous utilisez le référentiel .NET sur un ordinateur Windows. Si vous travaillez dans un autre environnement, adaptez les chemins, les extensions de fichier et les séparateurs de chemin en conséquence. Pour utiliser les scripts fournis pour :

### <a name="create-new-azure-resources"></a>Créer des ressources Azure

Si vous n’avez pas encore créé les ressources Azure nécessaires, procédez comme suit :

1. Dans votre environnement de ligne de commande, accédez au dossier **remote-monitoring-services-dotnet\scripts\local\launch** dans votre copie clonée du référentiel.

2. Exécutez le script **start.cmd** et suivez les invites. Le script vous invite à vous connecter à votre compte Azure et à redémarrer le script. Le script vous invite ensuite à entrer les informations suivantes :
    * Le nom de la solution.
    * Sélectionnez l’abonnement Azure à utiliser.
    * L’emplacement du centre de données Azure à utiliser.

    Le script crée un groupe de ressources dans Azure avec le nom de votre solution. Ce groupe de ressources contient les ressources Azure qu’utilise l’accélérateur de solution.

3. Lorsque le script a fini, il affiche une liste de variables d’environnement. Suivez les instructions dans la sortie de la commande pour enregistrer ces variables dans le fichier **remote-monitoring-services-dotnet\\scripts\\local\\.env**.

### <a name="use-existing-azure-resources"></a>Utiliser les ressources Azure existantes

Si vous avez déjà créé les ressources Azure nécessaires, modifiez les définitions de variable d’environnement dans le fichier **remote-monitoring-services-dotnet\\scripts\\local\\.env** avec les valeurs requises. Le fichier **.env** contient des informations détaillées sur où trouver les valeurs requises.

## <a name="run-the-microservices-in-docker"></a>Exécuter les microservices dans Docker

Les microservices exécutés dans les conteneurs Docker locaux doivent accéder aux services exécutés dans Azure. Vous pouvez tester la connexion Internet de votre environnement Docker à l’aide de la commande suivante qui démarre un petit conteneur et tente d’effectuer un test ping sur une adresse Internet :

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Pour exécuter l’accélérateur de solution, accédez au dossier **remote-monitoring-services-dotnet\\scripts\\local** dans votre environnement de ligne de commande et exécutez la commande suivante :

```cmd\sh
docker-compose up
```

La première fois que vous exécutez cette commande, Docker télécharge les images des microservices à partir de Docker Hub pour créer les conteneurs localement. Lors des exécutions suivantes, Docker exécute les conteneurs immédiatement.

Vous pouvez utiliser un interpréteur de commandes distinct pour afficher les journaux du conteneur. Tout d’abord, trouvez l’ID du conteneur à l’aide de la commande `docker ps -a`. Utilisez ensuite `docker logs {container-id} --tail 1000` pour afficher les 1 000 dernières entrées de journal pour le conteneur spécifié.

Pour accéder au tableau de bord de la solution de supervision à distance, accédez à [http://localhost:8080](http://localhost:8080) dans votre navigateur.

## <a name="clean-up"></a>Nettoyer

Pour éviter des frais superflus, lorsque vous avez terminé votre test, supprimez les services cloud de votre abonnement Azure. Pour supprimer les services, le plus simple consiste à accéder au [portail Azure](https://ms.portal.azure.com) et à supprimer le groupe de ressources créé quand vous avez exécuté le script **start.cmd**.

Utilisez la commande `docker-compose down --rmi all` pour supprimer les images Docker et libérer de l’espace sur votre machine locale. Vous pouvez également supprimer la copie locale du dépôt de supervision à distance créé quand vous avez cloné le code source à partir de GitHub.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Configurer un environnement de développement local
> * Configurer l’accélérateur de solution
> * Déployer l’accélérateur de solution
> * Établir une connexion à l’accélérateur de solution

La solution de supervision à distance étant déployée, l’étape suivante consiste à [explorer les fonctionnalités du tableau de bord des solutions](quickstart-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->
