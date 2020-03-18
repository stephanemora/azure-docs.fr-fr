---
title: 'Démarrage rapide : Reconnaître la voix venant d’un microphone, Python – Service Speech'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour créer une application console de reconnaissance vocale qui utilise le kit SDK Speech pour Python. Lorsque vous avez terminé, vous pouvez utiliser le microphone de l’ordinateur pour une retranscription vocale en temps réel.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: a2d5614aeeedb88cd9b2c41baed317761443d17c
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925615"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Configurer votre environnement de développement et créer un projet vide](../../../../quickstarts/setup-platform.md)
> * Veiller à avoir accès à un microphone pour la capture audio

## <a name="support-and-updates"></a>Mises à jour et support technique

Les mises à jour du package Python du kit SDK Speech sont distribuées via PyPI et annoncées dans les [notes de publication](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Si une nouvelle version est disponible, vous pouvez effectuer la mise à jour avec la commande `pip install --upgrade azure-cognitiveservices-speech`.
Vérifiez la version actuellement installée en inspectant la variable `azure.cognitiveservices.speech.__version__`.

Si vous avez un problème ou si une fonctionnalité est manquante, consultez [Options d’aide et de support](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Créer une application Python qui utilise le kit SDK Speech

### <a name="run-the-sample"></a>Exécution de l'exemple

Vous pouvez copier l’[exemple de code](#sample-code) de ce guide de démarrage rapide dans un fichier source `quickstart.py` et l’exécuter dans votre environnement de développement intégré ou dans la console :

```sh
python quickstart.py
```

Vous pouvez également télécharger ce tutoriel de démarrage rapide en tant que notebook [Jupyter](https://jupyter.org) à partir du [référentiel d’exemples de kit SDK Speech](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) et l’exécuter en tant que notebook.

### <a name="sample-code"></a>Exemple de code

> [!NOTE]
> Le SDK Speech reconnaît par défaut l’utilisation de la langue en-US. Consultez [Spécifier la langue source pour la reconnaissance vocale](../../../../how-to-specify-source-language.md) pour plus d’informations sur le choix de la langue source.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installer et utiliser le SDK Speech avec Visual Studio Code

1. Téléchargez et installez une version 64 bits de [Python](https://www.python.org/downloads/) (versions 3.5 à 3.8) sur votre ordinateur.
1. Téléchargez et installez [Visual Studio Code](https://code.visualstudio.com/Download).
1. Ouvrez Visual Studio Code et installez l’extension Python. Sélectionnez **Fichier** > **Préférences** > **Extensions** dans le menu. Recherchez **Python**.

   ![Installer l’extension Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Créez un dossier pour y stocker le projet. Vous pouvez par exemple utiliser l’Explorateur Windows.
1. Dans Visual Studio Code, sélectionnez l’icône **Fichier**. Ouvrez ensuite le dossier que vous avez créé.

   ![Ouvrir un dossier](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Créez un nouveau fichier source Python, `speechsdk.py`, en sélectionnant l’icône de nouveau fichier.

   ![Créer un fichier](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Copiez, collez et enregistrez le [code Python](#sample-code) sur le fichier qui vient d’être créé.
1. Insérez les informations de votre abonnement au service Speech.
1. S’il est sélectionné, un interpréteur Python s’affiche sur le côté gauche de la barre d’état, en bas de la fenêtre.
   Sinon, affichez la liste des interpréteurs Python disponibles. Ouvrez la palette de commandes (Ctrl+Maj+P) et entrez **Python : Sélectionner l’interpréteur**. Choisissez un interpréteur approprié.
1. Vous pouvez installer le package Python du kit SDK Speech à partir de Visual Studio Code. Procédez ainsi s’il n'est pas encore installé pour l’interpréteur Python que vous avez sélectionné.
   Pour installer le package du kit SDK Speech, ouvrez un terminal. Faite réapparaître la palette de commandes (Ctrl+Maj+P) et entrez **Terminal : Créer un terminal intégré**.
   Dans le terminal qui s’ouvre, entrez la commande `python -m pip install azure-cognitiveservices-speech` ou la commande appropriée pour votre système.
1. Pour exécuter l’exemple de code, cliquez avec le bouton droit dans l’éditeur. Sélectionnez **Run Python File in Terminal** (Exécuter le fichier Python dans le terminal).
   Énoncez quelques mots lorsque vous y êtes invité. Le texte retranscrit s’affiche peu de temps après.

   ![Exécuter un exemple](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run.png)

Si vous rencontrez des problèmes pour suivre ces instructions, reportez-vous au [tutoriel Python plus complet sur Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]
