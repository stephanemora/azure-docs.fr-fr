---
title: 'Démarrage rapide : Utiliser des commandes personnalisées avec Custom Voice (préversion) – Service Speech'
titleSuffix: Azure Cognitive Services
description: Cet article montre comment spécifier la voix de sortie d’une application Commandes personnalisées.
services: cognitive-services
author: anhoang
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: anhoang
ms.openlocfilehash: c2b9b4d51e89975d988ed94bf85695bd8a1cc770
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872473"
---
# <a name="quickstart-use-custom-commands-with-custom-voice-preview"></a>Démarrage rapide : Utiliser des commandes personnalisées avec Custom Voice (préversion)

Dans [l’article précédent](./quickstart-custom-speech-commands-create-parameters.md), nous avons créé un projet Commandes personnalisées pour répondre aux commandes comportant des paramètres.

Dans cet article, nous allons sélectionner une voix de sortie personnalisée pour l’application que nous avons créée.

## <a name="select-a-custom-voice"></a>Sélectionner une voix personnalisée Custom Voice

1. Ouvrez le projet [que nous avons créé précédemment](./quickstart-custom-speech-commands-create-parameters.md).
1. Sélectionnez **Paramètres** dans le volet gauche.
1. Sélectionnez **Custom Voice** dans le volet central.
1. Sélectionnez la voix personnalisée ou publique souhaitée dans le tableau.
1. Sélectionnez **Enregistrer**.

> [!div class="mx-imgBorder"]
> ![Exemples de phrases avec paramètres](media/custom-speech-commands/select-custom-voice.png)

> [!NOTE]
> - Concernant les **Voix publiques**, les **Types neuronaux** ne sont disponibles que pour certaines régions. Pour connaître la disponibilité, consultez [Voix standard et neuronales par région/point de terminaison](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - Les **voix personnalisées** peuvent être créées sur la page du projet Custom Voice. Consultez [Bien démarrer avec Custom Voice](./how-to-custom-voice.md).

L’application répond à présent avec la voix sélectionnée, et non plus avec la voix par défaut.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Démarrage rapide : Se connecter à une application Commandes personnalisées avec le kit de développement logiciel (SDK) Speech (préversion)](./quickstart-custom-speech-commands-speech-sdk.md)

