---
title: Forum Aux Questions (FAQ) - Personality Chat
titlesuffix: Azure Cognitive Services
description: FAQ sur Personality Chat
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ROBOTS: NOINDEX
ms.openlocfilehash: e763e3c3731858e20226efbd354927f38c3d5d70
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704222"
---
# <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)

## <a name="why-doesnt-this-answer-every-question-i-ask-it-like-other-chat-bots"></a>Pourquoi cet agent conversationnel ne répond-il pas à toutes les questions que je pose, comme le font d’autres bots ?

Project Personality Chat vise à améliorer un bot avec un mode de conversation courante reflétant une personnalité afin de créer une expérience utilisateur plus complète. Il n’est pas conçu pour tenir de longues conversations sur des sujets sans relation avec la fonction principale du robot. Bien qu’il puisse prendre part à toutes les conversations, il est limité (dans sa version bêta) aux domaines de conversation courants.

## <a name="how-can-i-customize-the-personality-to-suit-my-brand"></a>Comment puis-je adapter la personnalité à mon image de marque ?

Sélectionnez le personnage le plus proche parmi les personnages par défaut disponibles. Actuellement, vous pouvez modifier les réponses dans la bibliothèque éditoriale pour l’adapter à votre image de marque. À l’avenir, vous pourrez télécharger un jeu échantillon d’énoncés correspondant à la personnalité de votre choix, et trouver l’identité de personnage la plus proche. Il existe également des méthodes pour reformer et personnaliser le modèle.

## <a name="is-this-service-powering-existing-intelligent-agents-such-aszo"></a>Ce service alimente-t-il des agents intelligents existants tels que Zo ?

Les services alimentant Zo, Cortana et Project Personality Chat partagent des techniques similaires, mais sont des piles différentes. Ce service intègre les enseignements tirés des expériences avec Zo et Cortana.

## <a name="can-this-service-lead-to-bad-customer-experiences"></a>Ce service peut-il conduire à de mauvaises expériences utilisateur ?

Pour offrir une expérience plus riche, Personality Chat peut générer des réponses qui vont au-delà de celles du jeu de données éditorial, et tente d’interpréter toutes les entrées de l’utilisateur. Donc, il est possible qu’une réponse ne semble pas correcte dans son contexte. Un certain nombre de contrôles ont été mis en place pour aider à prévenir des réponses défavorables, en s’appuyant sur les connaissances d’agents intelligents tels que Zo. Par défaut, Project Personality Chat est configuré pour réagir uniquement aux intentions d’utilisateurs reconnus. Vous pouvez tester Project Personality Chat pour voir s’il est adapté à votre situation. Vos commentaires sont les bienvenus si vous constatez que quelque chose nécessite une formation supplémentaire. Si vous utilisez ce service avec vos clients à l’avenir, nous vous recommandons d’envisager une journalisation rendue anonyme pour vous aider à identifier les problèmes liés aux interactions avec les utilisateurs.
