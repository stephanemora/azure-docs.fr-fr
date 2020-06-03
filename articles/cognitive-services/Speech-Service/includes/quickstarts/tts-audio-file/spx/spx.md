---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: bd48618a520f547c72bfba7ff04ae6a249d0a673
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806398"
---
## <a name="run-the-speech-cli"></a>Exécuter l’interface CLI Speech

Vous êtes maintenant prêt à exécuter l’interface CLI Speech pour synthétiser la voix à partir du texte dans un nouveau fichier audio.

À partir de la ligne de commande, accédez au répertoire qui contient le fichier binaire de l’interface CLI Speech, puis tapez :

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

L’interface CLI Speech génère un langage naturel en anglais dans le fichier audio `greetings.wav`.
Dans Windows, vous pouvez lire le fichier audio en entrant `start greetings.wav`.
