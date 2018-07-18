---
title: Déboguer plusieurs modules pour Azure IoT Edge dans VS Code | Microsoft Docs
description: Utiliser Visual Studio Code pour déboguer plusieurs modules C# avec Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 38c66129ac8244d18b0f94c1485c785015e29ab4
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049589"
---
# <a name="use-visual-studio-code-to-debug-multiple-modules-with-azure-iot-edge"></a>Utiliser Visual Studio Code pour déboguer plusieurs modules C# avec Azure IoT Edge
Cet article fournit des instructions détaillées pour l’utilisation de [Visual Studio (VS) Code](https://code.visualstudio.com/) afin de déboguer plusieurs modules sur IoT Edge.

## <a name="prerequisites"></a>Prérequis
Effectuez le tutoriel [Développer une solution IoT Edge comportant plusieurs modules dans Visual Studio Code](tutorial-multiple-modules-in-vscode.md) et vérifiez que vous disposez d’au moins deux modules s’exécutant sur votre appareil IoT Edge.

## <a name="multi-target-and-remote-debugging-in-vs-code"></a>Débogage à distance et multi-cible dans VS Code
Avec l’extension Azure IoT Edge et VS Code, vous pouvez attacher le processus de module dans un conteneur, que celui-ci s’exécute sur votre machine de développement ou dans un appareil IoT Edge physique distant. Le débogage de plusieurs modules s’exécutant dans des conteneurs consiste à attacher plusieurs processus dans des conteneurs distincts. Vous pouvez utiliser le [débogage multi-cible](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging) VS Code quand vous déboguez plusieurs modules.

   > [!TIP]
   > Si votre conteneur de module s’exécute dans un appareil IoT Edge physique distant, vous pouvez être amené à configurer [Docker Machine](https://docs.docker.com/machine/overview/) afin que le moteur Docker sur votre machine de développement puisse communiquer avec les hôtes Docker distants.

### <a name="build-your-iot-edge-modules-for-debugging-purpose"></a>Générer vos modules IoT Edge pour le débogage
1. Pour démarrer le débogage multi-module, utilisez **Dockerfile.amd64.debug** afin de regénérer vos images docker et de redéployer votre solution Edge. Dans l’explorateur VS Code, accédez au fichier `deployment.template.json`. Mettez à jour les URL de vos images en ajoutant `.debug` à la fin. Vous avez besoin de deux images de modules comportant au moins `.debug`. Si vous travaillez sur la solution issue du tutoriel précédent, vous devez disposer d’un module de fonctions C# et d’un module C#. Mettez à jour ces deux URL d’image en ajoutant `.debug` à la fin et enregistrez ce fichier. 
2. Regénérez votre solution. Dans la palette de commandes de VS Code, tapez et exécutez la commande **Azure IoT Edge : générer la solution IoT Edge**.
3. Dans l’explorateur des appareils Azure IoT Hub, cliquez avec le bouton droit sur un ID d’appareil IoT Edge, puis sélectionnez **Créer un déploiement pour un appareil Edge**. Sélectionnez le fichier `deployment.json` sous le dossier `config`. Vous pouvez alors constater la bonne création du déploiement avec un ID de déploiement dans le terminal intégré de VS Code.

Vous pouvez vérifier l’état de votre conteneur dans l’explorateur du Docker VS Code ou en exécutant la commande `docker ps` dans le terminal.

### <a name="start-debugging-c-function-in-vs-code"></a>Démarrer le débogage de la fonction C# dans VS Code
1. VS Code conserve les informations de configuration du débogage dans un fichier `launch.json` situé dans un dossier `.vscode` de votre espace de travail. Ce fichier `launch.json` a été généré lorsque vous avez créé une nouvelle solution IoT Edge. Il se met à jour chaque fois que vous ajoutez un nouveau module qui prend en charge le débogage. Accédez à l’affichage du débogage et sélectionnez le fichier de configuration du débogage correspondant pour le débogage à distance des modules de fonctions C#.
2. Accédez à `run.csx`. Ajoutez un point d’arrêt dans la fonction.
3. Cliquez sur le bouton **Démarrer le débogage** ou appuyez sur **F5**, puis sélectionnez le processus à attacher.
4. Dans l’affichage de débogage de VS Code, les variables apparaissent dans le volet situé à gauche. 

### <a name="start-debugging-c-module-at-the-same-time-in-vs-code"></a>Commencer le débogage du module C# en même temps dans VS Code
1. Dans la palette de commandes de VS Code, tapez et exécutez la commande « Espace de travail : dupliquer l’espace de travail dans une nouvelle fenêtre ». Une nouvelle fenêtre VS Code démarre avec le même espace de travail.
2. Accédez à l’affichage du débogage et sélectionnez le fichier de configuration du débogage correspondant pour le débogage à distance du module C#.
3. Accédez à `program.cs`. Ajoutez un point d’arrêt dans le module C#.
4. Cliquez sur le bouton **Démarrer le débogage** ou appuyez sur **F5**, puis sélectionnez le processus à attacher.
5. Dans l’affichage de débogage de VS Code, les variables apparaissent dans le volet situé à gauche. 

### <a name="see-variables-in-multiple-debugging-windows"></a>Voir les variables dans plusieurs fenêtres de débogage
1. Vous avez maintenant au moins deux sessions de débogage en cours d’exécution dans deux fenêtres VS Code. Un des points d’arrêt doit être atteint.
2. Appuyez sur `F10` ou cliquez sur le bouton d’exécution pas à pas dans la **barre d’outils de débogage**.
3. Le point d’arrêt dans une autre fenêtre VS Code doit être atteint. 
4. Répétez les deux étapes précédentes ; vous pouvez voir les variables de plusieurs modules dans plusieurs fenêtres de débogage VS Code.

> [!NOTE]
> L’exemple ci-dessus montre comment déboguer plusieurs modules avec Azure IoT Edge. Il repose sur la version de débogage de `Dockerfile.amd64.debug`, qui inclut VSDBG (le débogueur de lignes de commande .NET Core) dans votre image de conteneur lors de sa création. Nous vous recommandons d’utiliser ou de personnaliser directement le `Dockerfile` sans VSDBG pour la fonction IoT Edge prête à l’emploi une fois le débogage de votre fonction C# terminé.

## <a name="next-steps"></a>Étapes suivantes

Une fois votre module généré, découvrez comment [déployer des modules Azure IoT Edge à partir de Visual Studio Code](how-to-deploy-modules-vscode.md).
