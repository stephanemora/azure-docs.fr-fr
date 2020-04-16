---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 10a11168b8046dbcc877f45141571fccdca879f0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400332"
---
## <a name="prerequisites"></a>Prérequis

* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](~/articles/cognitive-services/Speech-Service/get-started.md).
* [Python 3.5 à 3.8](https://www.python.org/downloads/).
* Le package Python du kit SDK Speech est disponible pour les systèmes d’exploitation suivants :
    * Windows : x64 et x86.
    * Mac : macOS X version 10.12 ou ultérieure.
    * Linux : Ubuntu 16.04/18.04, Debian 9, RHEL 7/8, CentOS 7/8 sur x64.
* Sur Linux, exécutez les commandes suivantes pour installer les packages requis :

# <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel--centos-8"></a>[RHEL / CentOS 8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl python3
```

> [!NOTE]
> Sur RHEL/CentOS 7/8, suivez les instructions du [Guide pratique pour configurer OpenSSL pour Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

* Sur Windows, vous avez besoin de [Microsoft Redistributable Visual C++ pour Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pour votre plateforme.

## <a name="install-the-speech-sdk"></a>Installer le Kit de développement logiciel (SDK) Speech

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Cette commande installe le package Python à partir de [PyPI](https://pypi.org/) pour le kit SDK Speech :

```Bash
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Mises à jour et support technique

Les mises à jour du package Python du kit SDK Speech sont distribuées via PyPI et annoncées dans les [notes de publication](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Si une nouvelle version est disponible, vous pouvez effectuer la mise à jour avec la commande `pip install --upgrade azure-cognitiveservices-speech`.
Vérifiez la version actuellement installée en inspectant la variable `azure.cognitiveservices.speech.__version__`.

Si vous avez un problème ou si une fonctionnalité est manquante, consultez [Options d’aide et de support](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Créer une application Python qui utilise le kit SDK Speech

### <a name="run-the-sample"></a>Exécution de l'exemple

Vous pouvez copier l’[exemple de code](#sample-code) de ce guide de démarrage rapide dans un fichier source `quickstart.py` et l’exécuter dans votre environnement de développement intégré ou dans la console :

```Bash
python quickstart.py
```

Vous pouvez également télécharger ce tutoriel de démarrage rapide en tant que notebook [Jupyter](https://jupyter.org) à partir du [référentiel d’exemples de kit SDK Speech](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) et l’exécuter en tant que notebook.

### <a name="sample-code"></a>Exemple de code

````python
import azure.cognitiveservices.speech as speechsdk

# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "helloworld.wav"
audio_output = speechsdk.audio.AudioOutputConfig(filename=audio_filename)

# Creates a synthesizer with the given settings
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_output)

# Synthesizes the text to speech.
# Replace with your own text.
text = "Hello world!"
result = speech_synthesizer.speak_text_async(text).get()

# Checks result.
if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    print("Speech synthesized to [{}] for text [{}]".format(audio_filename, text))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech synthesis canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        if cancellation_details.error_details:
            print("Error details: {}".format(cancellation_details.error_details))
    print("Did you update the subscription info?")
````

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
   Sinon, affichez la liste des interpréteurs Python disponibles. Ouvrez la palette de commandes (<kbd>Ctrl+Maj+P</kbd>) et entrez **Python : Sélectionner l’interpréteur**. Choisissez un interpréteur approprié.
1. Vous pouvez installer le package Python du kit SDK Speech à partir de Visual Studio Code. Procédez ainsi s’il n'est pas encore installé pour l’interpréteur Python que vous avez sélectionné.
   Pour installer le package du kit SDK Speech, ouvrez un terminal. Ouvrez de nouveau la palette de commandes (<kbd>Ctrl+Maj+P</kbd>) et entrez **Terminal : Créer un terminal intégré**.
   Dans le terminal qui s’ouvre, entrez la commande `python -m pip install azure-cognitiveservices-speech` ou la commande appropriée pour votre système.
1. Pour exécuter l’exemple de code, cliquez avec le bouton droit dans l’éditeur. Sélectionnez **Run Python File in Terminal** (Exécuter le fichier Python dans le terminal).
   Votre texte est converti en synthèse vocale et enregistré dans les données audio spécifiées.

   ```console
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

Si vous rencontrez des problèmes pour suivre ces instructions, reportez-vous au [tutoriel Python plus complet sur Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Voir aussi

- [Créer une voix personnalisée Custom Voice](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Enregistrer des échantillons vocaux personnalisés](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
