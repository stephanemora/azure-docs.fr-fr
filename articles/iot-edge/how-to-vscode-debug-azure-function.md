---
title: Modules de fonctions de débogage pour Azure IoT Edge | Microsoft Docs
description: Utiliser Visual Studio Code pour déboguer des fonctions Azure Functions C# avec Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 3/20/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: cd870d8f5c3fff87b121ab777a086f21df07cfbc
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Utiliser Visual Studio Code pour déboguer des fonctions Azure Functions avec Azure IoT Edge | Microsoft Docs

Cet article fournit des instructions détaillées pour l’utilisation de [Visual Studio (VS) Code](https://code.visualstudio.com/) pour déboguer vos fonctions Azure Functions sur IoT Edge.

## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Windows ou Linux comme machine de développement. Votre appareil IoT Edge peut être un autre appareil physique, ou vous pouvez simuler votre appareil IoT Edge sur votre machine de développement.

> [!NOTE]
> Vous pouvez uniquement déboguer des fonctions C# dans les conteneurs linux-amd64.

Avant de suivre les instructions de cet article, effectuez les étapes décrites dans [Développer une solution IoT Edge comportant plusieurs modules dans Visual Studio Code](tutorial-multiple-modules-in-vscode.md). Après cela, vous devez disposer des éléments suivants :
- Un registre Docker local en cours d’exécution sur votre machine de développement. Il est recommandé d’utiliser un registre Docker local pour le prototype et à des fins de test. Vous pouvez mettre à jour le registre de conteneurs dans le fichier `module.json` de chaque dossier du module.
- Un espace de travail du projet de la solution IoT Edge comportant un sous-dossier du module Azure Function.
- Le fichier `run.csx` avec votre code de fonction.
- Un runtime Edge en cours d’exécution sur votre machine de développement.

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Générer votre module de fonction IoT Edge pour le débogage
1. Pour démarrer le débogage, utilisez **Dockerfile.amd64.debug** afin de régénérer votre image docker et de redéployer votre solution Edge. Dans l’explorateur VS Code, accédez au fichier `deployment.template.json`. Mettez à jour l’URL de votre image de fonction en ajoutant `.debug` à la fin.

    ![Générer l’image de débogage](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Regénérez votre solution. Dans la palette de commandes de VS Code, saisissez et exécutez la commande **Edge : générer la solution IoT Edge**.
3. Dans l’explorateur des appareils Azure IoT Hub, cliquez avec le bouton droit sur un ID d’appareil IoT Edge, puis sélectionnez **Créer un déploiement pour un appareil Edge**. Sélectionnez le fichier `deployment.json` sous le dossier `config`. Vous pouvez alors constater la bonne création du déploiement avec un ID de déploiement dans le terminal intégré de VS Code.

Vous pouvez vérifier l’état de votre conteneur dans l’explorateur du Docker VS Code ou en exécutant la commande `docker images` dans le terminal.

## <a name="start-debugging-c-function-in-vs-code"></a>Démarrer le débogage de la fonction C# dans VS Code
1. VS Code conserve les informations de configuration du débogage dans un fichier `launch.json` situé dans un dossier `.vscode` de votre espace de travail. Ce fichier `launch.json` a été généré lorsque vous avez créé une nouvelle solution IoT Edge. Il se met à jour chaque fois que vous ajoutez un nouveau module qui prend en charge le débogage. Accédez à l’affichage du débogage et sélectionnez le fichier de configuration du débogage correspondant.
    ![Sélectionnez la configuration du débogage](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Accédez à `run.csx`. Ajoutez un point d’arrêt dans la fonction.
3. Cliquez sur le bouton **Démarrer le débogage** ou appuyez sur **F5**, puis sélectionnez le processus à attacher.
4. Dans l’affichage de débogage de VS Code, les variables apparaissent dans le volet situé à gauche. 


> [!NOTE]
> L’exemple ci-dessus montre comment déboguer la fonction .Net Core IoT Edge sur les conteneurs. Il repose sur la version de débogage de `Dockerfile.amd64.debug`, qui inclut VSDBG (le débogueur de lignes de commande .NET Core) dans votre image de conteneur lors de sa création. Nous vous recommandons d’utiliser ou de personnaliser directement le `Dockerfile` sans VSDBG pour la fonction IoT Edge prête à l’emploi une fois le débogage de votre fonction C# terminé.

## <a name="next-steps"></a>Étapes suivantes


[Utiliser Visual Studio Code pour déboguer un module C# avec Azure IoT Edge](how-to-vscode-debug-csharp-module.md)

