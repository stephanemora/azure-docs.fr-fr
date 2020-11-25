---
title: Déployer la solution de supervision à distance localement - Environnement de développement intégré VS - Azure | Microsoft Docs
description: Ce guide pratique montre comment déployer l’accélérateur de solution de supervision à distance sur votre ordinateur local en utilisant Visual Studio à des fins de test et de développement.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024144"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Déployer localement l’accélérateur de solution de supervision à distance - Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Cet article montre comment déployer l’accélérateur de solution de supervision à distance sur votre ordinateur local à des fins de test et de développement. Vous apprendrez à exécuter les microservices dans Visual Studio. Les déploiements de microservices locaux utilisent les services de cloud suivants : IoT Hub, Cosmos DB, Azure Stream Analytics et Azure Time Series Insights dans le cloud.

Si vous souhaitez exécuter l’accélérateur de solution de supervision à distance dans un Docker sur votre ordinateur local, consultez [Déployer localement l’accélérateur de solution de supervision à distance - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Conditions préalables requises

Pour déployer les services Azure utilisés par l’accélérateur de solution de supervision à distance, vous avez besoin d’un abonnement Azure actif.

Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez [Essai gratuit Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuration de la machine

Pour terminer le déploiement local, vous devez avoir installé les outils suivants sur votre machine de développement local :

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) : Ce logiciel est un prérequis pour l’interface de ligne de commande PCS que les scripts utilisent pour créer des ressources Azure. N’utilisez pas Node.js v10.

> [!NOTE]
> Visual Studio est disponible pour Windows et Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Exécuter les microservices

Dans cette section, vous exécutez les microservices de surveillance à distance. Vous exécutez l’interface utilisateur web en mode natif, le service Device Simulation dans Docker et les microservices dans Visual Studio.

### <a name="run-the-device-simulation-service"></a>Exécuter le service Device Simulation

Ouvrez une nouvelle fenêtre d’invite de commandes pour vérifier que vous avez bien accès aux variables d’environnement définies par le script **start.cmd** dans la section précédente.

Exécutez la commande suivante pour lancer le conteneur Docker pour le service Device Simulation. Le service simule des appareils pour la solution de supervision à distance.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Déployer toutes les autres microservices sur l’ordinateur local

Les étapes suivantes vous montrent comment exécuter les microservices de surveillance à distance dans Visual Studio :

1. Lancez Visual Studio.
1. Ouvrez le la solution **remote-monitoring.sln** dans le dossier **services** dans votre copie locale du référentiel.
1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur la solution, puis cliquez sur **Propriétés**.
1. Sélectionnez **Propriétés communes > Projet de démarrage**.
1. Sélectionnez **Plusieurs projets de démarrage** et définissez **Action** sur **Démarrer** pour les projets suivants :
    * WebService (asa-manager\WebService)
    * WebService (auth\WebService)
    * WebService (config\WebService)
    * WebService (device-telemetry\WebService)
    * WebService (iothub-manager\WebService)
    * WebService (storage-adapter\WebService)
1. Cliquez sur **OK** pour enregistrer vos choix.
1. Cliquez sur **Déboguer > Démarrer le débogage** pour générer et exécuter les services web sur l’ordinateur local.

Chaque service web ouvre une invite de commandes et une fenêtre de navigateur web. Dans l’invite de commandes, vous voyez le résultat de l’exécution du service et la fenêtre du navigateur vous permet d’en surveiller l’état. Ne fermez pas les invites de commandes ni les pages web, car cette action arrête le service web.

### <a name="start-the-stream-analytics-job"></a>Démarrage de la tâche Stream Analytics

Suivez ces étapes pour démarrer la tâche Stream Analytics :

1. Accédez au [portail Azure](https://portal.azure.com).
1. Accédez au **groupe de ressources** créé pour votre solution. Le nom du groupe de ressources est le nom que vous avez choisi pour votre solution lorsque vous avez exécuté le script **start.cmd**.
1. Cliquez sur la **tâche Stream Analytics** dans la liste des ressources.
1. Dans la **vue d’ensemble** de la tâche Stream Analytics, sélectionnez **Démarrer**. Cliquez ensuite sur **Démarrer** pour démarrer la tâche.

### <a name="run-the-web-ui"></a>Exécuter l’interface utilisateur web

Dans cette étape, vous démarrez l’interface utilisateur web. Ouvrez une nouvelle fenêtre d’invite de commandes pour vérifier que vous avez bien accès aux variables d’environnement définies par le script **start.cmd**. Accédez au dossier **webui** de votre copie locale du référentiel et exécutez les commandes suivantes :

```cmd
npm install
npm start
```

Une fois le démarrage terminé, votre navigateur affiche la page **http:\//localhost:3000/dashboard**. Les erreurs présentes sur cette page sont attendues. Pour afficher l’application sans erreur, procédez comme suit.

### <a name="configure-and-run-nginx"></a>Configurer et exécuter NGINX

Configurer un serveur proxy inverse pour lier l’application web et les microservices qui s’exécutent sur votre ordinateur local :

* Copiez le fichier **nginx.conf** à partir du dossier **webui\scripts\localhost** dans votre copie locale du dépôt vers le répertoire d’installation **nginx\conf**.
* Exécutez **nginx**.

Pour plus d’informations sur l’exécution de **nginx**, consultez [nginx pour Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Connexion au tableau de bord

Pour accéder au tableau de bord de la solution de supervision à distance, accédez à http:\//localhost:9000 dans votre navigateur.

## <a name="clean-up"></a>Nettoyer

Pour éviter des frais superflus, lorsque vous avez terminé votre test, supprimez les services cloud de votre abonnement Azure. Pour supprimer les services, accédez au [portail Azure](https://ms.portal.azure.com) et supprimez le groupe de ressources créé par le script **start.cmd**.

Vous pouvez également supprimer la copie locale du dépôt de surveillance à distance créé quand vous avez cloné le code source à partir de GitHub.

## <a name="next-steps"></a>Étapes suivantes

La solution de supervision à distance étant déployée, l’étape suivante consiste à [explorer les fonctionnalités du tableau de bord des solutions](quickstart-remote-monitoring-deploy.md).
