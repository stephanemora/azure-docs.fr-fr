---
title: 'Démarrage rapide : Kit de développement logiciel (SDK) Speech pour la configuration de la plateforme Python – Service de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour configurer votre plateforme pour l’utilisation de Python avec le kit SDK du service Speech.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/09/2019
ms.author: erhopf
ms.openlocfilehash: 218d0dca43d126c1318c273603a4980697c465af
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751602"
---
Ce guide explique comment installer le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour Python.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

- Le package Python du kit SDK Speech est disponible pour les systèmes d’exploitation suivants :
  - Windows : x64 et x86
  - Mac : macOS X version 10.12 ou ultérieure
  - Linux : Ubuntu 16.04, Ubuntu 18.04, Debian 9 sur x64

## <a name="prerequisites"></a>Conditions préalables requises

- Les plateformes Linux prises en charge nécessitent l’installation de certaines bibliothèques (`libssl` pour la prise en charge du protocole SSL et `libasound2` pour la prise en charge du son). Reportez-vous à votre distribution ci-dessous pour connaître les commandes nécessaires à l’installation des versions appropriées de ces bibliothèques.

  - Sur Ubuntu, exécutez les commandes suivantes pour installer les packages requis :

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - Sur Debian 9, exécutez les commandes suivantes pour installer les packages requis :

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- Sur Windows, vous avez besoin de [Microsoft Redistributable Visual C++ pour Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pour votre plateforme. Notez que la première installation peut nécessiter le redémarrage de Windows avant de continuer à suivre ce guide.
- Enfin, vous aurez besoin de [Python 3.5, 3.6 ou 3.7](https://www.python.org/downloads/). Pour vérifier votre installation, ouvrez une invite de commandes, tapez la commande `python --version` et vérifiez le résultat. Si elle est correctement installée, vous obtenez une réponse « Python 3.5.1 » ou similaire.

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Installer le Kit de développement logiciel (SDK) Speech avec Visual Studio Code

1. Téléchargez et installez la dernière version prise en charge de [Python](https://www.python.org/downloads/) pour votre plateforme 3.5 ou version ultérieure.
   - Les utilisateurs de Windows veillent à sélectionner « Ajouter Python à votre chemin d’accès » au cours du processus d’installation.
1. Téléchargez et installez [Visual Studio Code](https://code.visualstudio.com/Download).
1. Ouvrez Visual Studio Code et installez l’extension Python. Sélectionnez **Fichier** > **Préférences** > **Extensions** dans le menu. Recherchez **Python**, puis cliquez sur **Installer**.

   ![Installer l’extension Python](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. À partir de Visual Studio Code, installez le package Python du Kit de développement logiciel (SDK) Speech à partir de la ligne de commande intégrée :
   1. Ouvrez un terminal (dans les menus déroulants, **Terminal** > **Nouveau terminal**)
   1. Dans le terminal qui s’ouvre, entrez la commande `python -m pip install azure-cognitiveservices-speech`

Voilà. Vous êtes prêt à commencer à coder à l’aide du Kit de développement logiciel (SDK) Speech dans Python. Et vous pouvez passer aux [Étapes suivantes](#next-steps) ci-dessous. Si vous débutez avec Visual Studio Code, reportez-vous à la [Documentation Visual Studio Code](https://code.visualstudio.com/docs) plus complète. Pour en savoir plus sur Visual Studio Code et Python, voir le [Didacticiel Python pour Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="install-the-speech-sdk-using-the-command-line"></a>Installer le Kit de développement logiciel (SDK) Speech à l’aide de la ligne de commande

Si vous n’utilisez pas Visual Studio Code, la commande suivante installe le package Python à partir de [PyPI](https://pypi.org/) pour le Kit de développement logiciel (SDK) Speech. Pour les utilisateurs de Visual Studio Code, passez à la sous-section suivante.

```sh
pip install azure-cognitiveservices-speech
```

Si vous utilisez macOS, il se peut que vous deviez exécuter la commande suivante pour faire fonctionner la commande `pip` ci-dessus :

```sh
python3 -m pip install --upgrade pip
```

Une fois que vous avez correctement utilisé `pip` pour installer `azure-cognitiveservices-speech`, vous pouvez utiliser le Kit de développement logiciel (SDK) Speech en important l’espace de noms dans vos projets Python. Par exemple :

```py
import azure.cognitiveservices.speech as speechsdk
```

Cela est décrit plus en détail dans les exemples de code répertoriés dans [Étapes suivantes](#next-steps) ci-dessous.

## <a name="support-and-updates"></a>Mises à jour et support technique

Les mises à jour du package Python du kit SDK Speech sont distribuées via PyPI et annoncées dans les [notes de publication](~/articles/cognitive-services/speech-service/releasenotes.md).
Si une nouvelle version est disponible, vous pouvez effectuer la mise à jour avec la commande `pip install --upgrade azure-cognitiveservices-speech`.
Vérifiez la version actuellement installée en inspectant la variable `azure.cognitiveservices.speech.__version__`.

Si vous avez un problème ou si une fonctionnalité est manquante, consultez [Options d’aide et de support](~/articles/cognitive-services/speech-service/support.md).

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [windows](../quickstart-list.md)]
