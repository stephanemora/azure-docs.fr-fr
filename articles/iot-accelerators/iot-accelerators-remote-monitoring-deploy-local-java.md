---
title: Déployer localement la solution de supervision à distance (via l’IDE IntelliJ) - Azure | Microsoft Docs
description: Ce guide pratique montre comment déployer l’accélérateur de solution de supervision à distance sur votre ordinateur local en utilisant IntelliJ à des fins de test et de développement.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 2b55fea69fe1affb6cab5d360f1e8355c3bb720d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66015438"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Déployer localement l’accélérateur de solution de supervision à distance - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Cet article montre comment déployer l’accélérateur de solution de supervision à distance sur votre ordinateur local à des fins de test et de développement. Vous apprendrez à exécuter les microservices dans IntelliJ. Un déploiement de microservices local utilise les services cloud suivants : IoT Hub, Cosmos DB, Azure Stream Analytics et Azure Time Series Insights dans le cloud.

Si vous souhaitez exécuter l’accélérateur de solution de supervision à distance dans un Docker sur votre ordinateur local, consultez [Déployer localement l’accélérateur de solution de supervision à distance - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Prérequis

Pour déployer les services Azure utilisés par l’accélérateur de solution de supervision à distance, vous avez besoin d’un abonnement Azure actif.

Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuration de la machine

Pour terminer le déploiement local, vous devez avoir installé les outils suivants sur votre machine de développement local :

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [Plug-in IntelliJ Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [Plug-in IntelliJ SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Plug-in IntelliJ SBT Executor](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) : Ce logiciel est un prérequis pour l’interface de ligne de commande PCS que les scripts utilisent pour créer des ressources Azure. N’utilisez pas Node.js v10.

> [!NOTE]
> L’IDE IntelliJ est disponible pour Windows et Mac.

## <a name="download-the-source-code"></a>Télécharger le code source

Les dépôts de code source de supervision à distance incluent le code source et les fichiers config Docker nécessaires à l’exécution des images Docker des microservices.

Pour cloner le référentiel et en créer une version locale, utilisez votre environnement de ligne de commande afin d'accéder à un dossier approprié sur votre ordinateur local. Exécutez ensuite l’un des ensembles de commandes suivants pour cloner le dépôt Java :

Pour télécharger la dernière version des implémentations de microservices java, exécutez :


```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

> [!NOTE]
> Ces commandes téléchargent le code source de tous les microservices en plus des scripts que vous utilisez pour exécuter les microservices localement. Même si vous n’avez pas besoin du code source pour exécuter les microservices dans Docker, vous pouvez l’utiliser si vous envisagez de modifier ultérieurement l’accélérateur de solution et de tester vos modifications localement.

## <a name="deploy-the-azure-services"></a>Déployer des services Azure

Bien que cet article explique comment exécuter les microservices localement, ceux-ci dépendent de l’exécution des services Azure exécutés dans le cloud. Utilisez le script suivant pour déployer les services Azure. Les exemples de scripts suivants supposent que vous utilisez le dépôt java sur un ordinateur Windows. Si vous travaillez dans un autre environnement, adaptez les chemins, les extensions de fichier et les séparateurs de chemin en conséquence.

### <a name="create-new-azure-resources"></a>Créer des ressources Azure

Si vous n’avez pas encore créé les ressources Azure nécessaires, procédez comme suit :

1. Dans votre environnement de ligne de commande, accédez au dossier **\services\scripts\local\launch** de votre copie clonée du référentiel.

1. Exécutez les commandes suivantes pour installer l'outil CLI **pcs** et vous connecter à votre compte Azure :

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Exécutez le script **start.cmd**. Le script vous invite à entrer les informations suivantes :
   * Le nom de la solution.
   * Sélectionnez l’abonnement Azure à utiliser.
   * L’emplacement du centre de données Azure à utiliser.

     Le script crée un groupe de ressources dans Azure avec le nom de votre solution. Ce groupe de ressources contient les ressources Azure qu’utilise l’accélérateur de solution. Vous pouvez supprimer ce groupe de ressources dès que vous n’avez plus besoin des ressources correspondantes.

     Le script ajoute également un ensemble de variables d'environnement dotées du préfixe **PCS** sur votre ordinateur local. Ces variables d’environnement fournissent les détails de supervision à distance nécessaires pour lire à partir d’une ressource Azure Key Vault. La supervision à distance lira ses valeurs de configuration à partir de cette ressource Key Vault.

     > [!TIP]
     > Quand le script s’exécute, il enregistre également les variables d’environnement dans un fichier appelé **\<votre dossier de base\>\\.pcs\\\<nom de la solution\>.env**. Vous pouvez les utiliser pour de futurs déploiements d’accélérateurs de solutions. Notez que l'ensemble de variables d'environnement de votre ordinateur local se substitue aux valeurs du fichier **services\\scripts\\local\\.env** lorsque vous exécutez **docker-compose**.

1. Quittez votre environnement de ligne de commande.

### <a name="use-existing-azure-resources"></a>Utiliser les ressources Azure existantes

Si vous avez déjà créé les ressources Azure requises, créez les variables d'environnement correspondantes sur votre ordinateur local.
Définissez les variables d’environnement suivantes :
* **PCS_KEYVAULT_NAME** : nom de la ressource Azure Key Vault
* **PCS_AAD_APPID** : ID d’application AAD
* **PCS_AAD_APPSECRET** : secret d’application AAD

Les valeurs de configuration seront lues à partir de cette ressource Azure Key Vault. Vous pouvez enregistrer ces variables d’environnement dans le fichier **\<votre dossier de base\>\\.pcs\\\<nom de la solution\>.env** à partir du déploiement. Notez que l'ensemble de variables d'environnement de votre ordinateur local se substitue aux valeurs du fichier **services\\scripts\\local\\.env** lorsque vous exécutez **docker-compose**.

Certaines configurations requises par le microservice sont stockées dans une instance de **Key Vault** qui a été créée lors du déploiement initial. Les variables correspondantes dans keyvault doivent être modifiées en fonction des besoins.

## <a name="run-the-microservices"></a>Exécuter les microservices

Dans cette section, vous exécutez les microservices de surveillance à distance. Vous exécutez l’interface utilisateur web en mode natif, la simulation d’appareil, le service d’authentification et ASA Manager dans Docker et les microservices dans IntelliJ.

### <a name="run-the-device-simulation-service"></a>Exécuter le service Device Simulation

Ouvrez une nouvelle fenêtre d’invite de commandes pour vérifier que vous avez bien accès aux variables d’environnement définies par le script **start.cmd** dans la section précédente.

Exécutez la commande suivante pour lancer le conteneur Docker pour le service Device Simulation. Le service simule des appareils pour la solution de supervision à distance.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Exécuter le service d’authentification

Ouvrez une nouvelle fenêtre d’invite de commandes et exécutez la commande suivante afin de lancer le conteneur Docker pour le service d’authentification. Le service permet de gérer les utilisateurs autorisés à accéder à des solutions Azure IoT.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-asa-manager-service"></a>Exécuter le service ASA Manager

Ouvrez une nouvelle fenêtre d’invite de commandes et exécutez la commande suivante pour lancer le conteneur Docker pour le service ASA Manager. Le service permet la gestion des travaux Azure Stream Analytics (ASA), notamment la définition de la configuration et du démarrage, l’arrêt et la supervision des états.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Déployer toutes les autres microservices sur l’ordinateur local

Les étapes suivantes vous montrent comment exécuter les microservices de supervision à distance dans IntelliJ :

#### <a name="import-project"></a>Importer un projet

1. Lancez l’IDE IntelliJ
1. Sélectionnez **Importer un projet** et choisissez **azure-iot-pcs-remote-monitoring-java\services\build.sbt**

#### <a name="create-run-configurations"></a>Créer des configurations d’exécution

1. Sélectionnez **Exécuter > Modifier les configurations**
1. Sélectionnez **Ajouter une nouvelle configuration > sbt task (tâche sbt)** 
1. Entrez un **nom** et **Tâches** comme exécution 
1. Sélectionnez le **répertoire de travail** selon le service que vous souhaitez exécuter
1. Cliquez sur **Appliquer > OK** pour enregistrer vos choix.
1. Créez des configurations d’exécution pour les services suivants :
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

À titre d’exemple, l’image suivante illustre l’ajout d’une configuration pour un service :

[![Add-Configuration](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>Créer une configuration composée

1. Pour exécuter tous les services, sélectionnez **Ajouter une nouvelle configuration > Compound (Composée)**
1. Entrez le **nom** et **add sbt tasks (ajouter des tâches sbt)**
1. Cliquez sur **Appliquer > OK** pour enregistrer vos choix.

À titre d’exemple, l’image suivante illustre l’ajout de toutes les tâches sbt à une configuration unique :

[![Add-All-Services](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Cliquez sur **Exécuter** pour générer et exécuter les services web sur l’ordinateur local.

Chaque service web ouvre une invite de commandes et une fenêtre de navigateur web. Dans l’invite de commandes, vous voyez le résultat de l’exécution du service et la fenêtre du navigateur vous permet d’en surveiller l’état. Ne fermez pas les invites de commandes ni les pages web, car cette action arrête le service web.


Pour obtenir l’état des services, vous pouvez accéder aux URL suivantes :
* IoT-Hub Manager [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Device Telemetry [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* config [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* storage-adapter [http://localhost:9022/v1/status](http://localhost:9022/v1/status)


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
