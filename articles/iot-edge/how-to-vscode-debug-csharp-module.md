---
title: Utiliser Visual Studio Code pour déboguer un module C# avec Azure IoT Edge | Microsoft Docs
description: Déboguez un module C# avec Azure IoT Edge dans Visual Studio Code.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 65f2fb4526f1048ae88193f85a552a2202afa1d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Utiliser Visual Studio Code pour déboguer un module C# avec Azure IoT Edge
Cet article fournit des instructions détaillées pour l’utilisation de [Visual Studio Code](https://code.visualstudio.com/) comme principal outil de développement pour déboguer vos modules Azure IoT Edge.

## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Windows ou Linux comme machine de développement. Votre appareil IoT Edge peut être un autre appareil physique, ou vous pouvez simuler votre appareil IoT Edge sur votre machine de développement.

Avant de suivre les instructions de cet article, effectuez les étapes décrites dans [Développer une solution IoT Edge comportant plusieurs modules dans Visual Studio Code](tutorial-multiple-modules-in-vscode.md). Après cela, vous devez disposer des éléments suivants :
- Un registre Docker local en cours d’exécution sur votre machine de développement. Il est recommandé d’utiliser un registre Docker local pour le prototype et à des fins de test. Vous pouvez mettre à jour le registre de conteneurs dans le fichier `module.json` de chaque dossier du module.
- Un espace de travail de projet de la solution IoT Edge comportant un sous-dossier de module C#.
- Le fichier `Program.cs` avec la version la plus récente du code du module.
- Un runtime Edge en cours d’exécution sur votre machine de développement.

## <a name="build-your-iot-edge-c-module-for-debugging"></a>Générer un module C# IoT Edge pour le débogage
1. Pour commencer le débogage, utilisez **Dockerfile.amd64.debug** afin de régénérer votre image docker et de redéployer votre solution Edge. Dans l’explorateur VS Code, accédez au fichier `deployment.template.json`. Mettez à jour l’URL de votre image de fonction en ajoutant `.debug` à la fin.

2. Regénérez votre solution. Dans la palette de commandes de VS Code, saisissez et exécutez la commande **Edge : générer la solution IoT Edge**.

3. Dans l’explorateur des appareils Azure IoT Hub, cliquez avec le bouton droit sur un ID d’appareil IoT Edge, puis sélectionnez **Créer un déploiement pour un appareil Edge**. Sélectionnez `deployment.json` sous le dossier `config`. Vous pouvez alors constater la bonne création du déploiement avec un ID de déploiement dans le terminal intégré de VS Code.

> [!NOTE]
> Vous pouvez consulter l’état de votre conteneur dans l’explorateur du Docker VS Code ou en exécutant la commande `docker images` dans le terminal.

## <a name="start-debugging-c-module-in-vs-code"></a>Commencer le débogage du module C# dans VS Code
1. VS Code conserve les informations de configuration du débogage dans un fichier `launch.json` situé dans un dossier `.vscode` de votre espace de travail. Ce fichier `launch.json` a été généré lors de la création d’une nouvelle solution IoT Edge. Il sera mis à jour chaque fois que vous ajouterez un nouveau module prenant en charge le débogage. Accédez à l’affichage du débogage et sélectionnez le fichier de configuration du débogage correspondant.
    ![Sélectionnez la configuration du débogage](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Accédez à `program.cs`. Ajoutez un point d’arrêt dans ce fichier.

3. Cliquez sur le bouton Démarrer le débogage ou appuyez sur **F5**, puis sélectionnez le processus à attacher.

4. Dans l’affichage de débogage de VS Code, les variables apparaissent dans le volet situé à gauche. 

> [!NOTE]
> L’exemple précédent montre comment déboguer des modules .NET Core IoT Edge sur des conteneurs. Il repose sur la version de débogage de `Dockerfile.debug`, qui inclut VSDBG (le débogueur en ligne de commande .NET Core) dans votre image de conteneur lors de sa création. Une fois le débogage de vos modules C# terminé, nous vous recommandons d’utiliser ou de personnaliser directement `Dockerfile` sans VSDBG pour les modules IoT Edge prêts à l’emploi.

## <a name="next-steps"></a>Étapes suivantes

[Utiliser Visual Studio Code pour déboguer des fonctions Azure Functions avec Azure IoT Edge](how-to-vscode-debug-azure-function.md)

