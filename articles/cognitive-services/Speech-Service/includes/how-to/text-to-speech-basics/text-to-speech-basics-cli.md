---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: bee28c946242ecf227287fb36b5b03aa6defb1c2
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88170142"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>Synthétiser la voix vers un haut-parleur

Vous êtes maintenant prêt à exécuter l’interface CLI Speech pour synthétiser la voix à partir du texte. À partir de la ligne de commande, accédez au répertoire qui contient le fichier binaire de l'interface CLI Speech. Exécutez ensuite la commande suivante.

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

L’interface CLI Speech génère un langage naturel en anglais par le biais du haut-parleur de l’ordinateur.

## <a name="synthesize-speech-to-a-file"></a>Synthétiser la voix dans un fichier

Exécutez la commande suivante pour convertir la sortie de votre haut-parleur en fichier `.wav`.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

L’interface CLI Speech génère un langage naturel en anglais dans le fichier audio `greetings.wav`.
Dans Windows, vous pouvez lire le fichier audio en entrant `start greetings.wav`.