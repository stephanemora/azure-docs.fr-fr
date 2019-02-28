---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 355fe134939b26c51d6e03368f782845628a6b96
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738882"
---
L’application cliente doit savoir si un énoncé est inutile ou inapproprié pour l’application. L’intention **None** est ajoutée à chaque application dans le cadre du processus de création pour déterminer si un énoncé ne peut pas être traité par l’application cliente.

Si LUIS retourne l’intention **None** pour un énoncé, votre application cliente peut demander si l’utilisateur souhaite mettre fin à la conversation ou donner d’autres instructions pour poursuivre la conversation. 

> [!CAUTION] 
> Ne laissez pas l’intention **None** vide. 

1. Dans le panneau gauche, sélectionnez **Intents** (Intentions).

2. Sélectionnez l’intention **None**. Ajoutez trois énoncés que votre utilisateur est susceptible d’entrer, mais qui ne sont pas pertinents pour votre application Ressources humaines :

    | Exemples d’énoncés|
    |--|
    |Les chiens qui aboient sont agaçants|
    |Commande une pizza pour moi|
    |Pingouins dans l’océan|
