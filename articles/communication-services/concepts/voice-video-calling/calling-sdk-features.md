---
title: Vue d’ensemble de la bibliothèque de client Azure Communication Services Calling
titleSuffix: An Azure Communication Services concept document
description: Fournit une vue d’ensemble de la bibliothèque cliente Calling.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 9cc00cfa9f44c69a5880d53c0b7ac623f60be16b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944602"
---
# <a name="calling-client-library-overview"></a>Vue d’ensemble de la bibliothèque cliente d’appel

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Il existe deux familles distinctes de bibliothèques de client Calling, pour les *clients* et pour les *services*. Les bibliothèques de client actuellement disponibles sont destinées à des expériences de l’utilisateur final : les sites web et les applications natives.

Les bibliothèques de client de service ne sont pas encore disponibles et fournissent l’accès aux plans de données vocales et vidéo brutes, adaptés à une intégration avec des bots et d’autres services.

## <a name="calling-client-library-capabilities"></a>Fonctionnalités des bibliothèques de client Calling

La liste suivante présente l’ensemble des fonctionnalités actuellement disponibles dans les bibliothèques de client Azure Communication Services Calling.

| Groupe de fonctionnalités | Fonctionnalité                                                                                                          | JS  | Java (Android) | Objective-C (iOS) 
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Fonctionnalités principales | Passer un appel un-à-un entre deux utilisateurs                                                                           | ✔️   | ✔️            | ✔️  
|                   | Passer un appel de groupe avec plus de deux utilisateurs (jusqu’à 350 utilisateurs)                                                       | ✔️   | ✔️            | ✔️ 
|                   | Promouvoir un appel un-à-un avec deux utilisateurs en un appel de groupe avec plus de deux utilisateurs                                 | ✔️   | ✔️            | ✔️ 
|                   | Rejoindre un appel de groupe après son démarrage                                                                              | ✔️   | ✔️            | ✔️ 
|                   | Inviter un autre participant VoIP à rejoindre un appel de groupe en cours                                                       | ✔️   | ✔️            | ✔️ 
|                   | Désactiver/réactiver le micro                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | Basculer entre les caméras                                                                                              | ✔️   | ✔️            | ✔️           
|                   | Mettre en attente/reprendre                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | Intervenant actif                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Choisir un intervenant pour les appels                                                                                            | ✔️   | ✔️            | ✔️           
|                   | Choisir un microphone pour les appels                                                                                         | ✔️   | ✔️            | ✔️           
|                   | Afficher l’état d’un participant<br/>*Inactif, Médias préliminaires, Connexion, Connecté, En attente, Dans la salle d’attente, Déconnecté*         | ✔️   | ✔️            | ✔️           
|                   | Afficher l’état d’un appel<br/>*Médias préliminaires, Entrant, Connexion, Sonnerie, Connecté, Attente, Déconnexion, Déconnecté* | ✔️   | ✔️            | ✔️           
|                   | Montrer si le micro d’un participant est désactivé                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Afficher la raison pour laquelle un participant a quitté un appel                                                                       | ✔️   | ✔️            | ✔️     
| Partage d’écran    | Partager la totalité de l’écran dans l’application                                                                 | ✔️   | ❌            | ❌           
|                   | Partager une application spécifique (à partir de la liste des applications en cours d’exécution)                                                | ✔️   | ❌            | ❌           
|                   | Partager un onglet de navigateur web à partir de la liste des onglets ouverts                                                                  | ✔️   | ❌            | ❌           
|                   | Le participant peut visionner le partage d’écran à distance                                                                            | ✔️   | ✔️            | ✔️         
| Liste            | Lister les participants                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | Supprimer un participant                                                                                                | ✔️   | ✔️            | ✔️         
| RTPC              | Passer un appel un-à-un avec un participant RTPC                                                                     | ✔️   | ✔️            | ✔️   
|                   | Passer un appel de groupe avec des participants RTPC                                                                           | ✔️   | ✔️            | ✔️
|                   | Promouvoir un appel un-à-un avec un participant RTPC en appel de groupe                                                 | ✔️   | ✔️            | ✔️
|                   | Composer un numéro à partir d’un appel de groupe en tant que participant RTPC                                                                    | ✔️   | ✔️            | ✔️   
| Général           | Participer à un appel de groupe contenant jusqu’à 350 participants                                                       |  ✔️  | ✔️            | ✔️    
|                   | Tester votre micro, votre haut-parleur et votre caméra avec un service de test audio (disponible en appelant 8:echo123)                   |  ✔️  | ✔️            | ✔️   

## <a name="calling-client-library-browser-support"></a>Prise en charge des navigateurs de bibliothèques clientes d’appel

Le tableau suivant représente l’ensemble des navigateurs pris en charge et des versions actuellement disponibles.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    |
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ |
| **Bibliothèque de client Calling** | Chrome*, nouveau Edge | Chrome *, Safari** | Chrome*  | Chrome* | Chrome* | Safari** |


*Notez que la dernière version de Chrome est prise en charge en plus des deux versions précédentes.<br/>

**Notez que les versions 13.1+ de Safari sont prises en charge. La vidéo sortante pour Safari macOS n’est pas encore prise en charge, mais elle est prise en charge sur iOS. Le partage d’écran sortant est pris en charge uniquement sur iOS pour ordinateur de bureau.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Bien démarrer avec les appels](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Pour plus d’informations, consultez les articles suivants :
- Se familiariser avec les [flux d’appels](../call-flows.md) généraux
- Découvrir les [types d’appels](../voice-video-calling/about-call-types.md)
- [Planifier votre solution RTPC](../telephony-sms/plan-solution.md)
