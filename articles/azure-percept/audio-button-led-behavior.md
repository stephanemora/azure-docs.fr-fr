---
title: États des boutons et des voyants d’Azure Percept Audio
description: Découvrir les états des boutons et des voyants d’Azure Percept Audio
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 04ee60d6ebe9c84d77ea7e5ead140f7930b8cfe4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224548"
---
# <a name="azure-percept-audio-button-and-led-states"></a>États des boutons et des voyants d’Azure Percept Audio

Consultez ce qui suit pour obtenir des informations sur les états des boutons et des voyants de l’appareil Azure Percept Audio.

## <a name="button-behavior"></a>Comportement des boutons

Utilisez les boutons pour contrôler le comportement de l’appareil.

|État du bouton|Comportement|
|------------|----------|
|Désactiver le son|Appuyez sur ce bouton pour activer/désactiver la matrice de microphones. L’événement de bouton est déclenché quand il est enfoncé.|
|PTT/PTS|Appuyez sur PTT pour ignorer l’état de repérage de mot clé et activer l’état d’écoute de commande. Appuyez de nouveau sur le bouton pour arrêter la boîte de dialogue active de l’agent et pour revenir à l’état de repérage du mot clé. L’événement de bouton est déclenché quand il est enfoncé. PTS ne fonctionne que si le bouton est enfoncé alors que l’agent parle, et non lorsqu’il est à l’écoute ou en pleine réflexion.|

## <a name="led-states"></a>États des voyants

Utilisez les voyants (LED) pour comprendre dans quel l’état se trouve votre appareil.

|LED|État du voyant|État du SoM d’écoute|
|---|------------|----------------|
|L02|1 voyant blanc statique allumé|Mise sous tension |
|L02|1 voyant blanc clignotant (0,5 Hz)|Authentification en cours |
|L01 & L02 & L03|3 voyants bleus statiques allumés|En attente du mot clé|
|L01 & L02 & L03|Matrice de voyants clignotants, 20 fps |Mode écoute ou vocal|
|L01 & L02 & L03|Matrice de voyants à clignotement rapide, 20 fps|Opération en cours|
|L01 & L02 & L03|3 voyants rouges statiques allumés |Désactiver le son|

## <a name="understanding-ear-som-led-indicators"></a>Fonctionnement des indicateurs LED du Ear SoM
Vous pouvez utiliser les voyants (LED) pour comprendre dans quel l’état se trouve votre appareil. Il faut environ 4-5 minutes pour que l’appareil s’allume complètement après la mise sous tension. Au fil des étapes d’initialisation, vous allez voir :

1. LED blanche centrale allumée (statique) : l’appareil est sous tension.
1. LED blanche centrale allumée (clignotante) : l’authentification est en cours.
1. LED blanche centrale allumée (statique) : l’appareil est authentifié mais le mot clé n’est pas configuré.
1. Les trois voyants LED deviennent bleus une fois qu’une démonstration a été déployée et que l’appareil est prêt à être utilisé.


## <a name="troubleshooting-led-issues"></a>Résolution des problèmes liés aux LED
- **Si la LED centrale est blanche**, essayez [d’utiliser un modèle pour créer un Assistant vocal](./tutorial-no-code-speech.md).
- **Si la LED centrale clignote toujours**, cela indique un problème d’authentification. Essayez ces étapes de résolution des problèmes :
    1. Assurez-vous que vos connexions USB-A et micro USB sont sécurisées 
    1. Vérifier si le [module de reconnaissance vocale est en cours d’exécution](./troubleshoot-audio-accessory-speech-module.md#checking-runtime-status-of-the-speech-module)
    1. Redémarrer l’appareil
    1. [Collecter les journaux](./troubleshoot-audio-accessory-speech-module.md#collecting-speech-module-logs) et les joindre à une demande de support
    1. Vérifiez si votre kit de développement exécute les derniers logiciels et appliquez une mise à jour le cas échéant.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des conseils de dépannage pour votre appareil Azure Percept Audio, consultez ce [guide](./troubleshoot-audio-accessory-speech-module.md).