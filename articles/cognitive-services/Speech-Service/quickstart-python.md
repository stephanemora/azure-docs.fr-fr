---
title: 'Démarrage rapide : Reconnaissance vocale dans Python avec le Kit de développement logiciel (SDK) du service Speech'
titleSuffix: Azure Cognitive Services
description: Découvrir comment effectuer une reconnaissance vocale dans Python avec le Kit de développement logiciel (SDK) du service Speech
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: chlandsi
ms.openlocfilehash: 7610b12b351b2652df7ade603711d4d92e587292
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723907"
---
# <a name="quickstart-using-the-speech-service-from-python"></a>Démarrage rapide : Utilisation du service Speech dans Python

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Cet article explique comment utiliser le service Speech via le Kit de développement logiciel (SDK) Speech pour Python. Il décrit comment effectuer une reconnaissance vocale à partir d’une entrée micro.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, voici une liste de prérequis :

* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).
* [Python 3.5 (64 bits)](https://www.python.org/downloads/) ou version ultérieure.
* Le package de Kit de développement logiciel (SDK) Speech Python est disponible pour Windows (x64), Mac (macOS X version 10.12 ou ultérieure) et Linux (Ubuntu 16.04 ou 18.04 sur x64).
* Sur Ubuntu, exécutez les commandes suivantes pour installer les packages requis :

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

* Sur Windows, vous avez également besoin de [Microsoft Redistributable Visual C++ pour Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pour votre plateforme.

## <a name="get-the-speech-sdk-python-package"></a>Obtenir le package de Kit de développement logiciel (SDK) Speech Python

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Le package de Kit de développement logiciel (SDK) Cognitive Services Speech Python peut être installé à partir de [PyPI](https://pypi.org/) à l’aide de cette commande sur la ligne de commande :

```sh
pip install azure-cognitiveservices-speech
```

La version actuelle du kit SDK de reconnaissance vocale Cognitive Services est `1.2.0`.

## <a name="support-and-updates"></a>Mises à jour et support technique

Les mises à jour du package de Kit de développement logiciel (SDK) Speech Python sont distribuées via PyPI et sont annoncées sur la page [Notes de publication](./releasenotes.md).
Si une nouvelle version est disponible, vous pouvez effectuer la mise à jour avec la commande `pip install --upgrade azure-cognitiveservices-speech`.
Vous pouvez vérifier la version actuellement installée en inspectant la variable `azure.cognitiveservices.speech.__version__`.

Si vous avez un problème ou si une fonctionnalité est manquante, consultez notre [page de support](./support.md).

## <a name="create-a-python-application-using-the-speech-sdk"></a>Créer une application Python à l’aide du Kit de développement logiciel (SDK) Speech

### <a name="running-the-sample-in-a-terminal"></a>Exécution de l’exemple dans un terminal

Vous pouvez copier le [code](#quickstart-code) de ce démarrage rapide sur un fichier source `quickstart.py` et l’exécuter dans votre IDE ou dans la console

```sh
python quickstart.py
```

Vous pouvez également télécharger ce didacticiel de démarrage rapide en tant que notebook [Jupyter](https://jupyter.org) dans le [référentiel d’exemples Speech Cognitive Services](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) et l’exécuter en tant que notebook.

### <a name="quickstart-code"></a>Code de démarrage rapide

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="installing-the-speech-sdk-python-package-and-running-the-sample-in-visual-studio-code"></a>Installation du package de Kit de développement logiciel (SDK) Speech Python et exécution de l’exemple dans Visual Studio Code

1. [Téléchargez](https://www.python.org/downloads/) et installez une version 64 bits (3.5 ou version ultérieure) de Python sur votre ordinateur.
1. [Téléchargez](https://code.visualstudio.com/Download) et installez Visual Studio Code.
1. Ouvrez Visual Studio Code et installez l’extension Python en sélectionnant **Fichier** > **Préférences** > **Extensions** dans le menu, puis recherchez « Python ».
   ![Installer une extension Python](media/sdk/qs-python-vscode-python-extension.png)
1. Créez un dossier pour stocker le projet, par exemple à l’aide de l’Explorateur Windows.
1. Dans Visual Studio Code, cliquez sur l’icône **Fichier**, puis ouvrez le dossier que vous avez créé.
   ![Ouvrir un dossier](media/sdk/qs-python-vscode-python-open-folder.png)
1. Créez un fichier source Python `speechsdk.py` en cliquant sur l’icône de nouveau fichier.
   ![Créer un fichier](media/sdk/qs-python-vscode-python-newfile.png)
1. Copiez, collez et enregistrez le [code Python](#quickstart-code) sur le fichier qui vient d’être créé.
1. Insérez les informations de votre abonnement au service Speech.
1. Si un interpréteur Python a déjà été sélectionné, il est affiché sur le côté gauche de la barre d’état, en bas de la fenêtre.
   Sinon, vous pouvez faire apparaître une liste des interpréteurs Python en ouvrant la **Palette de commandes** (`Ctrl+Shift+P`) et en tapant **Python: Select Interpreter** (Python : Sélectionner un interpréteur), puis choisissez un interpréteur adapté.
1. Si le package de Kit de développement logiciel (SDK) Speech Python n’est pas encore installé pour l’interpréteur Python que vous avez sélectionné, vous pouvez facilement le faire dans Visual Studio Code.
   Pour installer le package de Kit de développement logiciel (SDK) Speech, ouvrez un terminal en affichant une nouvelle fois la Palette de commandes (`Ctrl+Shift+P`) et en tapant **Terminal : Créer un terminal intégré**.
   Dans le terminal qui s’ouvre, entrez la commande `python -m pip install azure-cognitiveservices-speech`, ou la commande appropriée pour votre système.
1. Pour exécuter l’exemple de code, cliquez avec le bouton droit dans l’éditeur et sélectionnez **Run Python File in Terminal** (Exécuter le fichier Python dans le terminal).
   Dites quelques mots quand vous y êtes invité ; le texte retranscrit doit s’afficher peu de temps après.
   ![Exécuter un exemple](media/sdk/qs-python-vscode-python-run.png)

Si vous rencontrez des problèmes pour suivre ces instructions, reportez-vous au [didacticiel plus complet sur Python Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Python sur GitHub](https://aka.ms/csspeech/samples)
