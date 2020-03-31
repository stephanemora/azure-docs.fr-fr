---
title: Déployer localement la solution de supervision à distance – Visual Studio Code – Azure | Microsoft Docs
description: Ce guide pratique montre comment déployer l’accélérateur de solution de supervision à distance sur votre ordinateur local en utilisant Visual Studio Code à des fins de test et de développement.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890958"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Déployer localement la solution de supervision à distance - Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Cet article montre comment déployer l’accélérateur de solution de supervision à distance sur votre ordinateur local à des fins de test et de développement. Vous apprendrez à exécuter les microservices dans Visual Studio Code. Un déploiement de microservices local utilise les services cloud suivants : IoT Hub, Cosmos DB, Azure Stream Analytics et Azure Time Series Insights.

## <a name="prerequisites"></a>Prérequis

Pour déployer les services Azure utilisés par l’accélérateur de solution de supervision à distance, vous avez besoin d’un abonnement Azure actif.

Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez [Essai gratuit Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuration de la machine

Pour terminer le déploiement local, vous devez avoir installé les outils suivants sur votre machine de développement local :

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Extension C# de VS Code](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) : Ce logiciel est un prérequis pour l’interface de ligne de commande PCS que les scripts utilisent pour créer des ressources Azure. Ne pas utiliser Node.js v10

> [!NOTE]
> Visual Studio Code est disponible pour Windows, Mac et Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Exécuter les microservices

Dans cette section, vous exécutez les microservices de surveillance à distance. Vous exécutez l’interface utilisateur web en mode natif, le service Device Simulation dans Docker et les microservices dans Visual Studio Code.

### <a name="build-the-code"></a>Générer le code

Accédez à azure-iot-pcs-remote-monitoring-dotnet\services dans l’invite de commandes et exécutez les commandes suivantes pour générer le code.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Déployer toutes les autres microservices sur l’ordinateur local

Les étapes suivantes vous montrent comment exécuter les microservices de surveillance à distance dans Visual Studio Code :

1. Lancez Visual Studio Code.
1. Ouvrez le dossier **azure-iot-pcs-remote-monitoring-dotnet** dans Visual Studio Code.
1. Créez un dossier nommé **vscode** dans le dossier **azure-iot-pcs-remote-monitoring-dotnet**.
1. Copiez les fichiers **launch.json** et **tasks.json** à partir de services\scripts\local\launch\idesettings\vscode dans le dossier **vscode** que vous venez de créer.
1. Ouvrez le **panneau de débogage** dans Visual Studio Code et exécutez la configuration **Exécuter tous les microservices**. Cette configuration exécute le microservice de simulation d’appareil dans Docker et les autres microservices dans le débogueur.

La sortie obtenue avec l’exécution de la configuration **Exécuter tous les microservices** dans la console de débogage ressemble à ce qui suit :

[![Deploy-Local-Microservices](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Exécuter l’interface utilisateur web

Dans cette étape, vous démarrez l’interface utilisateur web. Accédez au dossier **azure-iot-pcs-remote-monitoring-dotnet\webui** dans votre copie locale et exécutez les commandes suivantes :

```cmd
npm install
npm start
```

Une fois le démarrage terminé, votre navigateur affiche la page **http:\//localhost:3000/dashboard**. Les erreurs présentes sur cette page sont attendues. Pour afficher l’application sans erreur, procédez comme suit.

### <a name="configure-and-run-nginx"></a>Configurer et exécuter NGINX

Configurer un serveur proxy inverse pour lier l’application web et les microservices qui s’exécutent sur votre ordinateur local :

* Copiez le fichier **nginx.conf** du dossier **webui\scripts\localhost** vers le répertoire d’installation **nginx\conf**.
* Exécutez **nginx**.

Pour plus d’informations sur l’exécution de **nginx**, consultez [nginx pour Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Connexion au tableau de bord

Pour accéder au tableau de bord de la solution de supervision à distance, accédez à http:\//localhost:9000 dans votre navigateur.

## <a name="clean-up"></a>Nettoyer

Pour éviter des frais superflus, lorsque vous avez terminé votre test, supprimez les services cloud de votre abonnement Azure. Pour supprimer les services, accédez au [portail Azure](https://ms.portal.azure.com) et supprimez le groupe de ressources créé par le script **start.cmd**.

Vous pouvez également supprimer la copie locale du dépôt de surveillance à distance créé quand vous avez cloné le code source à partir de GitHub.

## <a name="next-steps"></a>Étapes suivantes

La solution de supervision à distance étant déployée, l’étape suivante consiste à [explorer les fonctionnalités du tableau de bord des solutions](quickstart-remote-monitoring-deploy.md).
