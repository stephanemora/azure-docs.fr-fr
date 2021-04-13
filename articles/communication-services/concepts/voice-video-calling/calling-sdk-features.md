---
title: Vue d’ensemble du kit SDK Appel Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Fournit une vue d’ensemble du kit SDK Appel.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ac9cef77569dffe461f7711195c5638e831aa218
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110102"
---
# <a name="calling-sdk-overview"></a>Vue d’ensemble du kit SDK Appel

Le kit SDK Appel permet aux appareils des utilisateurs finals de conduire des expériences de communication vocale et vidéo. Cette page offre une description détaillée des fonctionnalités d’appel, y compris les informations de prise en charge des plateformes et des navigateurs. Pour commencer sans attendre, consultez [Démarrages rapides Appel](../../quickstarts/voice-video-calling/getting-started-with-calling.md) ou [Exemple de bannière Appel](../../samples/calling-hero-sample.md). 

Une fois que vous avez commencé le développement, consultez la [page des problèmes connus](../known-issues.md) pour connaître les bogues sur lesquels nous travaillons.

Principales fonctionnalités du kit SDK Appel :

- **Adressage** : Azure Communication Services fournit des [identités](../identity-model.md) génériques utilisées pour traiter les points de terminaison de communication. Les clients utilisent ces identités pour s’authentifier auprès du service et communiquer entre eux. Ces identités sont utilisées dans les API Appel qui fournissent aux clients une visibilité sur les personnes connectées à un appel (la liste).
- **Chiffrement** : le kit SDK Appel chiffre le trafic et empêche la falsification sur le réseau. 
- **Gestion des appareils et contenu multimédia** : le kit SDK Appel fournit des possibilités de liaison avec des appareils audio et vidéo, il encode le contenu pour une transmission efficace sur le plan de données des communications et restitue le contenu sur les appareils et les vues de sortie que vous spécifiez. Les API sont également fournies pour le partage d’écran et d’application.
- **RTC** : le kit SDK Appel peut recevoir et initier des appels vocaux avec le système téléphonique traditionnel à commutation publique, [en utilisant les numéros de téléphone que vous obtenez sur le portail Azure](../../quickstarts/telephony-sms/get-phone-number.md) ou par programme.
- **Réunions Teams** : le kit SDK Appel peut [participer aux réunions Teams](../../quickstarts/voice-video-calling/get-started-teams-interop.md) et interagir avec le plan de données vocal et vidéo de Teams. 
- **Notifications** : le kit SDK Appel fournit des API qui permettent aux clients d’être avertis d’un appel entrant. Dans les situations où votre application ne s’exécute pas au premier plan, des modèles sont disponibles pour [déclencher des notifications contextuelles](../notifications.md) (des « toasts »), afin d’informer les utilisateurs finals d’un appel entrant. 

## <a name="detailed-capabilities"></a>Fonctionnalités détaillées 

La liste suivante présente l’ensemble des fonctionnalités actuellement disponibles dans les kits SDK Appel Azure Communication Services.

| Groupe de fonctionnalités | Fonctionnalité                                                                                                          | JS  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Fonctionnalités principales | Passer un appel un-à-un entre deux utilisateurs                                                                           | ✔️   | ✔️            | ✔️
|                   | Passer un appel de groupe avec plus de deux utilisateurs (jusqu’à 350 utilisateurs)                                                       | ✔️   | ✔️            | ✔️
|                   | Promouvoir un appel un-à-un avec deux utilisateurs en un appel de groupe avec plus de deux utilisateurs                                 | ✔️   | ✔️            | ✔️
|                   | Rejoindre un appel de groupe après son démarrage                                                                              | ✔️   | ✔️            | ✔️
|                   | Inviter un autre participant VoIP à rejoindre un appel de groupe en cours                                                       | ✔️   | ✔️            | ✔️
|  Contrôle durant l’appel | Activer/désactiver la vidéo                                                                                              | ✔️   | ✔️            | ✔️
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
| Général           | Tester votre micro, votre haut-parleur et votre caméra avec un service de test audio (disponible en appelant 8:echo123)                   | ✔️   | ✔️            | ✔️
| Gestion des appareils | Demander l’autorisation d’utiliser l’audio et/ou la vidéo                                                                       | ✔️   | ✔️            | ✔️
|                   | Obtenir la liste des caméras                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Définir la caméra                                                                                                          | ✔️   | ✔️            | ✔️
|                   | Obtenir la caméra sélectionnée                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Obtenir la liste des microphones                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Définir le microphone                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Obtenir le microphone sélectionné                                                                                             | ✔️   | ✔️            | ✔️
|                   | Obtenir la liste des haut-parleurs                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Définir le haut-parleur                                                                                                         | ✔️   | ✔️            | ✔️
|                   | Obtenir le haut-parleur sélectionné                                                                                                | ✔️   | ✔️            | ✔️
| Rendu vidéo   | Afficher une vidéo unique à de nombreux emplacements (caméra locale ou flux distant)                                                  | ✔️   | ✔️            | ✔️
|                   | Définir/mettre à jour le mode de mise à l’échelle                                                                                           | ✔️   | ✔️            | ✔️
|                   | Afficher un flux vidéo distant                                                                                          | ✔️   | ✔️            | ✔️

## <a name="calling-sdk-streaming-support"></a>Prise en charge du streaming du kit SDK Appel
Le kit SDK Appel Communication Services prend en charge les configurations de streaming suivantes :

| Limite          |Web | Android/iOS|
|-----------|----|------------|
|**Nombre de flux sortants qui peuvent être envoyés simultanément** |1 vidéo ou 1 partage d’écran | 1 vidéo + 1 partage d’écran|
|**Nombre de flux entrants qui peuvent être restitués simultanément** |1 vidéo ou 1 partage d’écran| 6 vidéos + 1 partage d’écran |

## <a name="calling-sdk-timeouts"></a>Délais d’attente du kit SDK Appel

Les délais d’attente suivants s’appliquent aux kits SDK Appel Communication Services :

| Action           | Délai en secondes |
| -------------- | ---------- |
| Reconnecter/supprimer un participant | 120 |
| Ajouter ou supprimer une nouvelle modalité à partir d'un appel (démarrage/arrêt d'une vidéo ou partage d'écran) | 40 |
| Délai d’attente de l’opération de transfert d’appel | 60 |
| Délai d’établissement de l’appel en tête-à-tête | 85 % |
| Délai d’établissement de l’appel de groupe | 85 % |
| Délai d’établissement de l’appel PSTN | 115 |
| Délai de la promotion de l’appel en tête-à-tête en appel de groupe | 115 |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>Prise en charge du kit SDK Appel JavaScript par le système d’exploitation et le navigateur

Le tableau suivant représente l’ensemble des navigateurs pris en charge disponibles. Nous prenons en charge les trois versions les plus récentes du navigateur, sauf indication contraire.

| Plateforme                         | Chrome | Safari*  | Edge (Chromium) |
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS***                         |  ✔️    | ✔️**   | ❌             |
| Windows***                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

*Safari (version 13.1 et versions ultérieures) est pris en charge. Les appels 1:1 ne sont pas pris en charge sur Safari.

**Les versions 14+ de Safari et 11+ de macOS sont nécessaires pour la prise en charge des vidéos sortantes.

***Le partage d’écran sortant est pris en charge uniquement sur les plateformes de bureau (Windows, macOS et Linux), quelle que soit la version du navigateur, et n’est pas pris en charge sur les plateformes mobiles (Android, iOS, iPad et tablettes).

****Une application iOS sur Safari ne peut pas énumérer/sélectionner des périphériques de microphone et de haut-parleur (par exemple, Bluetooth) ; il s’agit d’une limitation du système d’exploitation et il n’y a toujours qu’un seul périphérique.


## <a name="calling-client---browser-security-model"></a>Client appelant – Modèle de sécurité de navigateur

### <a name="user-webrtc-over-https"></a>Utiliser WebRTC sur HTTPS

Les API WebRTC comme `getUserMedia` exigent que l’application qui appelle ces API soit traitée sur HTTPS.

Pour un développement local, vous pouvez utiliser `http://localhost`.

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Incorporer le SDK d’appel Communication Services dans un iframe

Une nouvelle [stratégie d’autorisations (aussi appelée « stratégie de fonctionnalité »)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) est adoptée par différents navigateurs. Cette stratégie affecte les scénarios d’appel en contrôlant la façon dont les applications peuvent accéder à la caméra et au microphone d’un appareil via un élément iframe cross-origin.

Si vous souhaitez utiliser un iframe pour héberger une partie de l’application à partir d’un autre domaine, vous devez ajouter l’attribut `allow` à votre iframe avec la valeur correcte.

Par exemple, cet iframe autorise l’accès à la caméra et au microphone :

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Bien démarrer avec les appels](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Pour plus d’informations, consultez les articles suivants :
- Se familiariser avec les [flux d’appels](../call-flows.md) généraux
- Découvrir les [types d’appels](../voice-video-calling/about-call-types.md)
- [Planifier votre solution RTPC](../telephony-sms/plan-solution.md)
