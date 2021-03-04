---
title: Vue d’ensemble de la bibliothèque de client Chat dans Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Découvrez la bibliothèque de client Azure Communication Services Chat.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ef013d97be8a5decbee46d25752ced7bd622a646
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657772"
---
# <a name="chat-client-library-overview"></a>Vue d’ensemble de la bibliothèque de client Chat

Les bibliothèques de client Azure Communication Services Chat peuvent être utilisées pour ajouter un système de conversation en temps réel complet à vos applications.

## <a name="chat-client-library-capabilities"></a>Fonctionnalités de la bibliothèque de client Chat

La liste suivante présente l’ensemble des fonctionnalités actuellement disponibles dans les bibliothèques de client Communication Services Chat.

| Groupe de fonctionnalités | Fonctionnalité                                                                                                          | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | --- | ----- | ---- | -----  |
| Fonctionnalités principales | Créer un fil de conversation entre plusieurs utilisateurs (jusqu’à 250 utilisateurs)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Mettre à jour le sujet d’un fil de conversation                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |
|                   | Ajouter ou supprimer des membres d’un fil de conversation                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Indiquez si vous souhaitez partager l’historique des messages de conversation avec les membres récemment ajoutés : *Tous/Aucun/Jusqu’à une certaine période* | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Obtenir la liste de tous les membres d’un fil de conversation                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Supprimer un fil de conversation                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Obtenir la liste des appartenances aux fils de conversation d’un utilisateur                                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Obtenir des informations sur un fil de conversation                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Envoyer et recevoir des messages dans un fil de conversation                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Modifier le contenu d’un message une fois qu’il a été envoyé                                                                   | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Supprimer un message                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Étiqueter un message dont la priorité est normale ou élevée au moment de l’envoi                                               | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Envoyer et recevoir des confirmations de lecture pour les messages lus par les membres <br/> *Non disponible lorsqu’il y a plus de 20 membres dans un fil de conversation*    | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Envoyer et recevoir des notifications de saisie lorsqu’un membre est en train de taper un message dans un fil de conversation <br/> *Non disponible lorsqu’il y a plus de 20 membres dans un fil de conversation*      | ✔️   | ✔️   | ✔️    | ✔️    |
|                   | Obtenir tous les messages d’un fil de conversation <br/> *Emojis Unicode pris en charge*                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Envoyer des emojis dans le contenu d’un message                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|Signalisation en temps réel (activé pour chaque package de signalisation propriétaire**)| Recevoir une notification lorsqu’un utilisateur reçoit un nouveau message dans un fil de conversation dont il est membre                                     | ✔️   | ❌    | ❌  | ❌  |
|                    | Recevoir une notification quand un message a été modifié par un autre membre dans un fil de conversation dont il est membre                | ✔️   | ❌    | ❌    | ❌  |
|                    | Recevoir une notification quand un message a été supprimé par un autre membre dans un fil de conversation dont il est membre                | ✔️   | ❌    | ❌    | ❌  |
|                    | Recevoir une notification lorsqu’un autre membre du fil de conversation est en train de taper                                                             | ✔️   | ❌    | ❌    | ❌  |
|                    | Recevoir une notification lorsqu’un autre membre a lu un message (confirmation de lecture) dans le fil de conversation                               | ✔️   | ❌    | ❌    | ❌  |
| Événements             | Utiliser Event Grid pour vous abonner à une activité utilisateur se produisant dans des fils de conversation et intégrer des services de notification personnalisés ou une logique métier     | ✔️   | ✔️  | ✔️    | ✔️  |
| Surveillance        | Superviser l’utilisation (nombre de messages envoyés)                                                                               | ✔️   | ✔️  | ✔️    | ✔️  |
|                    | Superviser la qualité et l’état des requêtes d’API envoyées par votre application et configurer des alertes via le portail                                                          | ✔️   | ✔️  | ✔️    | ✔️  |
|Fonctionnalités supplémentaires | Utilisez les [API Cognitive Services](../../../cognitive-services/index.yml) avec la bibliothèque de client Chat pour activer les fonctionnalités intelligentes : *traduction et analyse des sentiments du message entrant sur un client, conversion de la parole en texte pour composer un message pendant que le membre parle, etc.*                                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |

**Le package de signalisation propriétaire est implémenté en utilisant des sockets web. Il passe à l’interrogation longue si les sockets web ne sont pas pris en charge.

## <a name="javascript-chat-client-library-support-by-os-and-browser"></a>Prise en charge de la bibliothèque de client de conversation JavaScript par système d’exploitation et navigateur

Le tableau suivant représente l’ensemble des navigateurs pris en charge et des versions actuellement disponibles.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPadOS|
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ | -------|
| **Bibliothèque de client de conversation** | Firefox *, Chrome*, nouveau Edge | Firefox *, Chrome*, Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |


*Notez que la dernière version est prise en charge en plus des deux versions précédentes.<br/>

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Bien démarrer avec les conversations](../../quickstarts/chat/get-started.md)

Les documents suivants peuvent vous intéresser :

- Familiarisez-vous avec les [concepts relatifs aux conversations](../chat/concepts.md).
