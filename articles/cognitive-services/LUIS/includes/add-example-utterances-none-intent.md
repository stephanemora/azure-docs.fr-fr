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
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c81aaa02af0895e6c3a851fe15273d85de9b55bc
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262744"
---
L’application cliente doit savoir si un énoncé est inutile ou inapproprié pour l’application. L’intention **None** est ajoutée à chaque application dans le cadre du processus de création pour déterminer si un énoncé ne doit pas être traité par l’application cliente.

Si LUIS retourne l’intention **None** pour un énoncé, votre application cliente peut demander si l’utilisateur souhaite mettre fin à la conversation ou donner d’autres instructions pour poursuivre la conversation. 

Si vous ne complétez pas l’intention **Aucune**, un énoncé qui devrait être prédit en dehors du domaine du sujet le sera dans l’une des intentions de domaine du sujet existant. En conséquence, l’application cliente, par exemple un bot conversationnel, effectue des opérations incorrectes en s’appuyant sur une prédiction incorrecte. 

1. Dans le panneau gauche, sélectionnez **Intents** (Intentions).

1. Sélectionnez l’intention **None**. Ajoutez trois énoncés que votre utilisateur est susceptible d’entrer, mais qui ne sont pas pertinents pour votre application de commande de pizzas :

    |Exemples d’énoncés `None`|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Ces exemples ne doivent pas utiliser des mots auxquels vous vous attendez dans le domaine couvert, par exemple `pizza`, `cheese``crust`, `pickup` `deliver`.