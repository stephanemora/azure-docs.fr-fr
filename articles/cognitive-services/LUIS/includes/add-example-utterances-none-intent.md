---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: bbe40083159d433c0b5746834e1c530b23b03851
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648088"
---
L’application cliente doit savoir si un énoncé est inutile ou inapproprié pour l’application. L’intention **None** est ajoutée à chaque application dans le cadre du processus de création pour déterminer si un énoncé ne peut pas être traité par l’application cliente.

Si LUIS retourne l’intention **None** pour un énoncé, votre application cliente peut demander si l’utilisateur souhaite mettre fin à la conversation ou donner d’autres instructions pour poursuivre la conversation. 

Si vous ne complétez pas l’intention **Aucune**, un énoncé qui devrait être prédit en dehors du domaine du sujet le sera dans l’une des intentions de domaine du sujet existant. En conséquence, l’application cliente, par exemple un bot conversationnel, effectue des opérations incorrectes en s’appuyant sur une prédiction incorrecte. 

1. Dans le panneau gauche, sélectionnez **Intents** (Intentions).

1. Sélectionnez l’intention **None**. Ajoutez trois énoncés que votre utilisateur est susceptible d’entrer, mais qui ne sont pas pertinents pour votre application :

    |Exemples d’énoncés `None`|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|
