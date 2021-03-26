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
ms.openlocfilehash: 705bd926c2ac6f414464254969b5c511c88891f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656105"
---
# <a name="chat-client-library-overview"></a>Vue d’ensemble de la bibliothèque de client Chat  

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Les bibliothèques de client Azure Communication Services Chat peuvent être utilisées pour ajouter un système de conversation en temps réel complet à vos applications.
    
## <a name="chat-client-library-capabilities"></a>Fonctionnalités de la bibliothèque de client Chat 

La liste suivante présente l’ensemble des fonctionnalités actuellement disponibles dans les bibliothèques de client Communication Services Chat.  

| Groupe de fonctionnalités | Fonctionnalité | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Fonctionnalités principales | Créer un fil de conversation entre plusieurs utilisateurs (jusqu’à 250 utilisateurs)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Mettre à jour le sujet d’un fil de conversation                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Ajouter ou supprimer des participants dans un fil de conversation                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Choisir si vous souhaitez partager l’historique des messages de conversation avec le participant en cours d’ajout                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Obtenir la liste des participants d’un fil de conversation                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Supprimer un fil de conversation                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Pour un utilisateur de communication, obtenir la liste des fils de conversation auxquels il participe                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Obtenir des informations sur un fil de conversation                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Envoyer et recevoir des messages dans un fil de conversation                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Modifier le contenu d’un message envoyé                                                                                | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Supprimer un message                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Confirmation de lecture des messages qui ont été lus par d’autres participants dans une conversation <br/> *Non disponible lorsqu’il y a plus de 20 participants dans un fil de conversation*    | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Recevoir une notification lorsque des participants tapent activement un message dans un fil de conversation <br/> *Non disponible lorsqu’il y a plus de 20 membres dans un fil de conversation*      | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Obtenir tous les messages d’un fil de conversation <br/>                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Envoyer des emojis Unicode dans le contenu d’un message                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Signalisation en temps réel (activé pour chaque package de signalisation propriétaire**)|  Abonnez-vous pour obtenir des mises à jour en temps réel des messages entrants et d’autres opérations dans votre application de conversation. Pour voir la liste des mises à jour prises en charge pour la signalisation en temps réel, consultez [Concepts de conversation](concepts.md#real-time-signaling)                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  |    
| Prise en charge d’Event Grid             | Utiliser l’intégration avec Azure Event Grid et configurer votre service de communication pour exécuter une logique métier basée sur l’activité de conversation ou pour brancher un service de notifications push personnalisé   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
| Surveillance        | Utiliser les métriques de demande d’API émises dans le portail Azure pour créer des tableaux de bord, superviser l’intégrité de votre application de conversation et définir des alertes pour détecter les anomalies      | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Configurer vos ressources Communication Services pour recevoir des journaux opérationnels de conversation à des fins de supervision et de diagnostic          | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  


**Le package de signalisation propriétaire est implémenté en utilisant des sockets web. Il passe à l’interrogation longue si les sockets web ne sont pas pris en charge.  

## <a name="javascript-chat-client-library-support-by-os-and-browser"></a>Prise en charge de la bibliothèque de client de conversation JavaScript par système d’exploitation et navigateur 

Le tableau suivant représente l’ensemble des navigateurs pris en charge et des versions actuellement disponibles.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPadOS|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Bibliothèque de client de conversation** | Firefox *, Chrome*, nouveau Edge | Firefox *, Chrome*, Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

*Notez que la dernière version est prise en charge en plus des deux versions précédentes.<br/>   

## <a name="next-steps"></a>Étapes suivantes   

> [!div class="nextstepaction"] 
> [Bien démarrer avec les conversations](../../quickstarts/chat/get-started.md)    

Les documents suivants peuvent vous intéresser :  
- Familiarisez-vous avec les [concepts relatifs aux conversations](../chat/concepts.md).