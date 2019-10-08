---
title: 'Démarrage rapide : Synthèse vocale, Python - Service Speech'
titleSuffix: Azure Cognitive Services
description: Découvrez comment effectuer la synthèse vocale en Python avec le SDK Speech
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/14/2019
ms.author: yulili
ms.openlocfilehash: fed0dea1a562f5ddea05516125a38c690069f446
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803672"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-python"></a>Démarrage rapide : Synthétise vocale avec le SDK Speech pour Python

Des guides de démarrage rapide sont également disponibles pour la [reconnaissance vocale](quickstart-python.md).

Cet article explique comment utiliser les services Speech via le kit SDK Speech pour Python. Il illustre comment synthétiser la voix à partir de texte et la lire avec la sortie audio par défaut.

## <a name="prerequisites"></a>Prérequis

* Clé d’abonnement Azure pour les services Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).
* [Python 3.5 ou ultérieur](https://www.python.org/downloads/).
* Le package Python du kit SDK Speech est disponible pour les systèmes d’exploitation suivants :
    * Windows 10 : x64 et x86.
    * Mac : macOS X version 10.12 ou ultérieure.
    * Linux : Ubuntu 16.04, Ubuntu 18.04, Debian 9 sur x64.
* Sur Linux, exécutez les commandes suivantes pour installer les packages requis :

  * Sur Ubuntu :

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  * Sur Debian 9 :

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

* Sur Windows, vous avez besoin de [Microsoft Redistributable Visual C++ pour Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pour votre plateforme.

## <a name="install-the-speech-sdk"></a>Installer le Kit de développement logiciel (SDK) Speech

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Notez que ce tutoriel ne fonctionnera pas avec une version du SDK antérieure à la version 1.7.0.

Cette commande installe le package Python à partir de [PyPI](https://pypi.org/) pour le kit SDK Speech :

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Mises à jour et support technique

Les mises à jour du package Python du kit SDK Speech sont distribuées via PyPI et annoncées dans les [notes de publication](./releasenotes.md).
Si une nouvelle version est disponible, vous pouvez effectuer la mise à jour avec la commande `pip install --upgrade azure-cognitiveservices-speech`.
Vérifiez la version actuellement installée en inspectant la variable `azure.cognitiveservices.speech.__version__`.

Si vous avez un problème ou si une fonctionnalité est manquante, consultez [Options d’aide et de support](./support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Créer une application Python qui utilise le kit SDK Speech

### <a name="run-the-sample"></a>Exécution de l'exemple

Vous pouvez copier l’[exemple de code](#sample-code) de ce guide de démarrage rapide dans un fichier source `quickstart.py` et l’exécuter dans votre environnement de développement intégré ou dans la console :

```sh
python quickstart.py
```

Vous pouvez également télécharger ce tutoriel de démarrage rapide en tant que notebook [Jupyter](https://jupyter.org) à partir du [référentiel d’exemples de kit SDK Speech](https://aka.ms/csspeech/samples) et l’exécuter en tant que notebook.

### <a name="sample-code"></a>Exemple de code

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installer et utiliser le SDK Speech avec Visual Studio Code

1. Téléchargez et installez une version 64 bits de [Python](https://www.python.org/downloads/) (version 3.5 ou ultérieure) sur votre ordinateur.
1. Téléchargez et installez [Visual Studio Code](https://code.visualstudio.com/Download).
1. Ouvrez Visual Studio Code et installez l’extension Python. Sélectionnez **Fichier** > **Préférences** > **Extensions** dans le menu. Recherchez **Python**.

   ![Installer l’extension Python](media/sdk/qs-python-vscode-python-extension.png)

1. Créez un dossier pour y stocker le projet. Vous pouvez par exemple utiliser l’Explorateur Windows.
1. Dans Visual Studio Code, sélectionnez l’icône **Fichier**. Ouvrez ensuite le dossier que vous avez créé.

   ![Ouvrir un dossier](media/sdk/qs-python-vscode-python-open-folder.png)

1. Créez un nouveau fichier source Python, `speechsdk.py`, en sélectionnant l’icône de nouveau fichier.

   ![Créer un fichier](media/sdk/qs-python-vscode-python-newfile.png)

1. Copiez, collez et enregistrez le [code Python](#sample-code) sur le fichier qui vient d’être créé.
1. Insérez les informations de votre abonnement aux services Speech.
1. S’il est sélectionné, un interpréteur Python s’affiche sur le côté gauche de la barre d’état, en bas de la fenêtre.
   Sinon, affichez la liste des interpréteurs Python disponibles. Ouvrez la palette de commandes (Ctrl+Maj+P) et entrez **Python : Sélectionner l’interpréteur**. Choisissez un interpréteur approprié.
1. Vous pouvez installer le package Python du kit SDK Speech à partir de Visual Studio Code. Procédez ainsi s’il n'est pas encore installé pour l’interpréteur Python que vous avez sélectionné.
   Pour installer le package du kit SDK Speech, ouvrez un terminal. Faite réapparaître la palette de commandes (Ctrl+Maj+P) et entrez **Terminal : Créer un terminal intégré**.
   Dans le terminal qui s’ouvre, entrez la commande `python -m pip install azure-cognitiveservices-speech` ou la commande appropriée pour votre système.
1. Pour exécuter l’exemple de code, cliquez avec le bouton droit dans l’éditeur. Sélectionnez **Run Python File in Terminal** (Exécuter le fichier Python dans le terminal).
   Tapez du texte lorsque vous y êtes invité. L’audio synthétisé est lu peu de temps après.

   ![Exécuter un exemple](media/sdk/qs-python-vscode-python-run-tts.png)

Si vous rencontrez des problèmes pour suivre ces instructions, reportez-vous au [tutoriel Python plus complet sur Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Python sur GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Voir aussi

- [Personnaliser les polices de la voix](how-to-customize-voice-font.md)
- [Enregistrer des échantillons vocaux](record-custom-voice-samples.md)
