---
title: Comportement des boutons et des voyants d’Azure Percept Audio
description: Découvrir les états des boutons et des voyants d’Azure Percept Audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 1d956e33a84b5509c16400c8f5f8e813d116411a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095746"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Comportement des boutons et des voyants d’Azure Percept Audio

Consultez ce qui suit pour obtenir des informations sur les états des boutons et des voyants d’Azure Percept Audio.

## <a name="button-behavior"></a>Comportement des boutons

Vous pouvez utiliser les boutons pour contrôler le comportement de l’appareil.

|État du bouton|  Comportement|
|------------|----------|
|Désactiver le son|  Appuyez sur ce bouton pour activer/désactiver la matrice de microphones. L’événement de bouton est déclenché quand il est enfoncé.|
|PTT/PTS|   Appuyez sur PTT pour ignorer l’état de repérage de mot clé et activer l’état d’écoute de commande. Appuyez de nouveau sur le bouton pour arrêter la boîte de dialogue active de l’agent et revenir à l’état de repérage de mot clé. L’événement de bouton est déclenché quand il est enfoncé. PTS ne fonctionne que si le bouton est enfoncé alors que l’agent parle, et non lorsqu’il est à l’écoute ou en pleine réflexion.|

## <a name="led-behavior"></a>Comportement des voyants

Vous pouvez utiliser les voyants (LED) pour comprendre dans quel l’état se trouve votre appareil.

|LED|   État du voyant|  État du SoM d’écoute|
|---|------------|----------------| 
|L02|   1 voyant blanc statique allumé |Mise sous tension |
|L02|   1 voyant blanc clignotant (0,5 Hz)|  Authentification en cours |
|L01 & L02 & L03|   3 voyants bleus statiques allumés|     En attente du mot clé|
|L01 & L02 & L03|   Matrice de voyants clignotants (20 fps) | Mode écoute ou vocal|
|L01 & L02 & L03|   Matrice de voyants à clignotement rapide (20 fps)|    Opération en cours|
|L01 & L02 & L03|   3 voyants rouges statiques allumés | Désactiver le son|

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des conseils de dépannage pour votre appareil Azure Percept Audio, consultez ce [guide](./troubleshoot-audio-accessory-speech-module.md).