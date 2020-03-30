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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67177371"
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
