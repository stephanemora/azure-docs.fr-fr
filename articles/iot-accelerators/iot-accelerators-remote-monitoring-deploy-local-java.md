---
title: Déployer la solution de supervision à distance localement – Environnement de développement intégré IntelliJ – Azure | Microsoft Docs
description: Ce guide pratique montre comment déployer l’accélérateur de solution de supervision à distance sur votre ordinateur local en utilisant IntelliJ à des fins de test et de développement.
author: v-krghan
manager: dominicbetts
ms.custom: devx-track-java
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: f7554843db247ade1cddff78902430a5d84debe1
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319165"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Déployer localement l’accélérateur de solution de supervision à distance - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Cet article montre comment déployer l’accélérateur de solution de supervision à distance sur votre ordinateur local à des fins de test et de développement. Vous allez découvrir comment exécuter les microservices dans IntelliJ. Un déploiement de microservices locaux utilise les services cloud suivants : IoT Hub, Azure Cosmos DB, Azure Stream Analytics et Azure Time Series Insights.

Si vous souhaitez exécuter l’accélérateur de solution de supervision à distance dans un Docker sur votre ordinateur local, consultez [Déployer localement l’accélérateur de solution de supervision à distance - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Prérequis

Pour déployer les services Azure utilisés par l’accélérateur de solution de supervision à distance, vous avez besoin d’un abonnement Azure actif.

Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez [Essai gratuit Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuration de la machine

Pour terminer le déploiement local, vous devez avoir installé les outils suivants sur votre machine de développement local :

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [Plug-in Scala IntelliJ](https://plugins.jetbrains.com/plugin/1347-scala)
* [Plug-in SBT IntelliJ](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Plug-in SBT Executor IntelliJ](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/)

Node.js v8 est un prérequis pour l’interface CLI PCS que les scripts utilisent pour créer des ressources Azure. N’utilisez pas Node.js v10.

> [!NOTE]
> L’IDE IntelliJ est disponible pour Windows et Mac.

## <a name="download-the-source-code"></a>Télécharger le code source

Les dépôts de code source de supervision à distance incluent le code source et les fichiers config Docker nécessaires à l’exécution des images Docker des microservices.

Pour cloner le dépôt et en créer une version locale, utilisez votre environnement de ligne de commande pour accéder à un dossier approprié sur votre ordinateur local. Exécutez ensuite un des ensembles de commandes suivants pour cloner le dépôt Java :

* Pour télécharger la dernière version des implémentations de microservices Java, exécutez la commande suivante :

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* Pour récupérer les sous-modules les plus récents, exécutez les commandes suivantes :

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Ces commandes téléchargent le code source de tous les microservices en plus des scripts que vous utilisez pour exécuter les microservices localement. Vous n’avez pas besoin du code source pour exécuter les microservices dans Docker. Le code source est cependant utile si vous prévoyez de modifier ultérieurement l’accélérateur de solution et de tester vos modifications localement.

## <a name="deploy-the-azure-services"></a>Déployer des services Azure

Bien que cet article explique comment exécuter les microservices localement, ceux-ci dépendent de l’exécution des services Azure exécutés dans le cloud. Utilisez le script suivant pour déployer les services Azure. Les exemples de scripts suivants supposent que vous utilisez le dépôt Java sur une machine Windows. Si vous travaillez dans un autre environnement, adaptez les chemins, les extensions de fichier et les séparateurs de chemin en conséquence.

### <a name="create-new-azure-resources"></a>Créer des ressources Azure

Si vous n’avez pas encore créé les ressources Azure nécessaires, procédez comme suit :

1. Dans votre environnement de ligne de commande, accédez au dossier **\services\scripts\local\launch** de votre copie clonée du dépôt.

1. Exécutez les commandes suivantes pour installer l'outil CLI **pcs** et vous connecter à votre compte Azure :

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Exécutez le script **start.cmd**. Le script vous invite à entrer les informations suivantes :

   * Le nom de la solution.
   * Sélectionnez l’abonnement Azure à utiliser.
   * L’emplacement du centre de données Azure à utiliser.

   Le script crée un groupe de ressources dans Azure qui a le nom de votre solution. Ce groupe de ressources contient les ressources Azure qu’utilise l’accélérateur de solution. Vous pouvez supprimer ce groupe de ressources dès que vous n’avez plus besoin des ressources correspondantes.

   Le script ajoute également un ensemble de variables d’environnement à votre machine locale. Chaque nom de variable est préfixé de **PCS**. Ces variables d’environnement fournissent des détails permettant à la supervision à distance de lire ses valeurs de configuration dans une ressource Azure Key Vault.

   > [!TIP]
   > Une fois le script terminé, il enregistre les variables d’environnement dans un fichier appelé **\<your home folder\>\\. PCs\\\<solution name\>. env**. Vous pouvez les utiliser pour des déploiements ultérieurs de l’accélérateur de solution. Notez que les ensembles de variables d’environnement sur votre machine locale remplace les valeurs du fichier **services\\scripts\\local\\.env** quand vous exécutez **docker-compose**.

1. Fermez votre environnement de ligne de commande.

### <a name="use-existing-azure-resources"></a>Utiliser les ressources Azure existantes

Si vous avez déjà créé les ressources Azure nécessaires, définissez les variables d’environnement correspondantes sur votre machine locale :
* **PCS_KEYVAULT_NAME** : Nom de la ressource Key Vault.
* **PCS_AAD_APPID** : ID d’application Azure Active Directory (Azure AD).
* **PCS_AAD_APPSECRET**: Secret de l’application Azure AD.

Les valeurs de configuration sont lues à partir de cette ressource Key Vault. Ces variables d’environnement peuvent être enregistrées dans le fichier **\<your home folder\>\\.pcs\\\<solution name\>.env** à partir du déploiement. Notez que l'ensemble de variables d'environnement de votre ordinateur local se substitue aux valeurs du fichier **services\\scripts\\local\\.env** lorsque vous exécutez **docker-compose**.

Certaines des configurations nécessaires au microservice sont stockées dans une instance de Key Vault qui a été créée lors du déploiement initial. Les variables correspondantes dans le coffre de clés doivent être modifiées en fonction des besoins.

## <a name="run-the-microservices"></a>Exécuter les microservices

Dans cette section, vous exécutez les microservices de surveillance à distance. Vous exécutez :

* L’interface utilisateur web en mode natif.
* Les services Simulation d’appareil IoT Azure, Authentification et Azure Stream Analytics Manager dans Docker.
* Les microservices dans IntelliJ.

### <a name="run-the-device-simulation-service"></a>Exécuter le service Simulation d’appareil

Ouvrez une nouvelle fenêtre d’invite de commandes. Vérifiez que vous avez accès aux variables d’environnement définies par le script **start.cmd** de la section précédente.

Exécutez la commande suivante pour lancer le conteneur Docker pour le service Simulation d’appareil. Le service simule des appareils pour la solution de supervision à distance.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Exécuter le service d’authentification

Ouvrez une nouvelle fenêtre d’invite de commandes, puis exécutez la commande suivante afin d’ouvrir le conteneur Docker pour le service Authentification. Avec ce service, vous pouvez gérer les utilisateurs autorisés à accéder à des solutions Azure IoT.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Exécuter le service Stream Analytics Manager

Ouvrez une nouvelle fenêtre d’invite de commandes, puis exécutez la commande suivante afin d’ouvrir le conteneur Docker pour le service Stream Analytics Manager. Avec ce service, vous pouvez gérer les tâches Stream Analytics. Cette gestion comprend la définition de la configuration des tâches, et le démarrage, l’arrêt et la supervision de l’état des tâches.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Déployer tous les autres microservices sur la machine locale

Les étapes suivantes vous montrent comment exécuter les microservices de supervision à distance dans IntelliJ.

#### <a name="import-a-project"></a>Importer un projet

1. Ouvrez l’IDE IntelliJ.
1. Sélectionnez **Importer un projet**.
1. Choisissez **azure-iot-pcs-remote-monitoring-java\services\build.sbt**.

#### <a name="create-run-configurations"></a>Créer des configurations d’exécution

1. Sélectionnez **Exécuter** > **Modifier les configurations**.
1. Sélectionnez **Ajouter une nouvelle configuration** > **tâche sbt**.
1. Entrez un **Nom**, puis **Tâches** avec la valeur **exécuter**.
1. Sélectionnez le **Répertoire de travail** selon le service que vous souhaitez exécuter.
1. Cliquez sur **Appliquer** > **OK** pour enregistrer vos choix.
1. Créez des configurations d’exécution pour les services web suivants :
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

À titre d’exemple, l’image suivante montre comment ajouter une configuration pour un service :

[![Capture d’écran de la fenêtre Exécution de l’IDE IntelliJ/Configurations de débogage, montrant l’option storageAdapter mise en surbrillance dans la liste des tâches sbt dans le volet gauche, et les entrées des zones Nom, Tâches, Répertoire de travail et Paramètres de la machine virtuelle dans le volet droit.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Créer une configuration composée

1. Pour exécuter tous les services ensemble, sélectionnez **Ajouter une nouvelle configuration** > **Composition**.
1. Entrez un **Nom**, puis sélectionnez **ajouter des tâches sbt**.
1. Cliquez sur **Appliquer** > **OK** pour enregistrer vos choix.

À titre d’exemple, l’image suivante montre comment ajouter toutes les tâches sbt à une même configuration :

[![Capture d’écran de la fenêtre Exécution de l’IDE IntelliJ/Configurations de débogage, montrant l’option AllServices mise en surbrillance dans la liste Composition du volet gauche et l’option « deviceTelemetry » de la tâche sbt mise en surbrillance dans le volet droit.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Cliquez sur **Exécuter** pour générer et exécuter les services web sur la machine locale.

Chaque service web ouvre une fenêtre d’invite de commandes et une fenêtre de navigateur web. À l’invite de commandes, vous voyez la sortie du service en cours d’exécution. La fenêtre du navigateur vous permet de superviser l’état. Ne fermez pas les fenêtres d’invite de commandes ni les pages web, car ces actions arrêtent le service web.

Pour accéder à l’état des services, accédez aux URL suivantes :

* Gestionnaire IoT-Hub : `http://localhost:9002/v1/status`
* Télémétrie des appareils : `http://localhost:9004/v1/status`
* config : `http://localhost:9005/v1/status`
* storage-adapter : `http://localhost:9022/v1/status`

### <a name="start-the-stream-analytics-job"></a>Démarrage de la tâche Stream Analytics

Suivez ces étapes pour démarrer la tâche Stream Analytics :

1. Accédez au [portail Azure](https://portal.azure.com).
1. Accédez au **Groupe de ressources** créé pour votre solution. Le nom du groupe de ressources est le nom que vous avez choisi pour votre solution lorsque vous avez exécuté le script **start.cmd**.
1. Sélectionnez la **tâche Stream Analytics** dans la liste des ressources.
1. Dans la page **Vue d’ensemble** de la tâche Stream Analytics , sélectionnez le bouton **Démarrer**, puis sélectionnez **Démarrer** pour démarrer la tâche.

### <a name="run-the-web-ui"></a>Exécuter l’interface utilisateur web

Dans cette étape, vous démarrez l’interface utilisateur web. Ouvrez une nouvelle fenêtre d’invite de commandes. Veillez à avoir accès aux variables d’environnement définies par le script **start.cmd**. Accédez au dossier **webui** de votre copie locale du dépôt, puis exécutez les commandes suivantes :

```cmd
npm install
npm start
```

Quand la commande **start** est terminée, votre navigateur affiche la page à l’adresse `http://localhost:3000/dashboard`. Les erreurs présentes sur cette page sont attendues. Pour voir l’application sans erreur, effectuez les étapes suivantes.

### <a name="configure-and-run-nginx"></a>Configurer et exécuter Nginx

Configurez un serveur proxy inverse qui lie l’application web aux microservices qui s’exécutent sur votre machine locale :

1. Copiez le fichier **nginx.conf** à partir du dossier **webui\scripts\localhost** de votre copie locale du dépôt vers le répertoire d’installation **nginx\conf**.
1. Exécutez Nginx.

Pour plus d’informations sur l’exécution de Nginx, consultez [nginx pour Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Connexion au tableau de bord

Pour accéder au tableau de bord de la solution de supervision à distance, accédez à `http://localhost:9000` dans votre navigateur.

## <a name="clean-up"></a>Nettoyer

Pour éviter des frais superflus, supprimez les services cloud de votre abonnement Azure quand vous avez terminé votre test. Pour supprimer les services, accédez au [portail Azure](https://ms.portal.azure.com) et supprimez le groupe de ressources créé par le script **start.cmd**.

Vous pouvez également supprimer la copie locale du dépôt de supervision à distance créé quand vous avez cloné le code source à partir de GitHub.

## <a name="next-steps"></a>Étapes suivantes

La solution de supervision à distance étant déployée, l’étape suivante consiste à [explorer les fonctionnalités du tableau de bord des solutions](quickstart-remote-monitoring-deploy.md).
