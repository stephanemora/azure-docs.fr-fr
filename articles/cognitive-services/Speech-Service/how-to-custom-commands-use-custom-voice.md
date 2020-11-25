---
title: 'Procédure : Utiliser des commandes personnalisées avec Custom Voice – Service Speech'
titleSuffix: Azure Cognitive Services
description: Cet article montre comment spécifier la voix de sortie d’une application Commandes personnalisées.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: sausin
ms.openlocfilehash: 4a5c14909606dcb862fcf53d99bc5bc00fba63bd
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025682"
---
# <a name="use-custom-commands-with-custom-voice"></a>Utiliser des commandes personnalisées avec Custom Voice

Cet article décrit comment sélectionner une voix de sortie personnalisée pour une application de commandes personnalisées.

## <a name="select-a-custom-voice"></a>Sélectionner une voix personnalisée Custom Voice

1. Dans votre application Commandes personnalisées, sélectionnez **Paramètres** dans le volet gauche.
1. Sélectionnez **Custom Voice** dans le volet central.
1. Sélectionnez la voix personnalisée ou publique souhaitée dans le tableau.
1. Sélectionnez **Enregistrer**.

> [!div class="mx-imgBorder"]
> ![Exemples de phrases avec paramètres](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Concernant les **Voix publiques**, les **Types neuronaux** ne sont disponibles que pour certaines régions. Pour connaître la disponibilité, consultez [Voix standard et neuronales par région/point de terminaison](./regions.md#standard-and-neural-voices).
> - Les **voix personnalisées** peuvent être créées sur la page du projet Custom Voice. Consultez [Bien démarrer avec Custom Voice](./how-to-custom-voice.md).

L’application répond à présent avec la voix sélectionnée, et non plus avec la voix par défaut.