---
title: Scénarios de tarifs pour les appels (vocaux/vidéo) et la conversation
titleSuffix: An Azure Communication Services concept document
description: Découvrez le modèle tarifaire de Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: bf255837a08a4810321f2f03cb97838e31a7cddc
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657755"
---
# <a name="pricing-scenarios"></a>Scénarios de tarifs

Les tarifs pour Azure Communication Services sont généralement basés sur un modèle de paiement à l’utilisation. Les prix dans les exemples suivants sont fournis à titre indicatif et peuvent ne pas refléter les tarifs Azure les plus récents.

## <a name="voicevideo-calling-and-screen-sharing"></a>Appels vocaux/vidéo et partage d’écran

Azure Communication Services permet d’ajouter les appels vocaux/vidéo et le partage d’écran dans vos applications. Vous pouvez incorporer cette expérience dans vos applications à l’aide des bibliothèques de client JavaScript, Objective-C (Apple), Java (Android) et .NET. Reportez-vous à notre [liste complète des bibliothèques de client disponibles](./sdk-options.md).

### <a name="pricing"></a>Tarifs

Les services d’appel et de partage d’écran sont facturés à la minute pour chaque participant à un tarif de 0,004 USD par participant par minute pour les appels de groupe. Pour comprendre les différents flux d’appels possibles, reportez-vous à [cette page](./call-flows.md).

Chaque participant d’un appel est comptabilisé dans la facturation pour chaque minute où il est connecté à l’appel. Cela vaut que l’utilisateur effectue un appel vidéo, un appel vocal ou un partage d’écran.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Exemple de tarif : Appel audio/vidéo de groupe à l’aide des bibliothèques de client JS et iOS

Alice a effectué un appel de groupe avec ses collègues, Bob et Charlie. Alice et Bob ont utilisé les bibliothèques de client JS, et Charlie les bibliothèques de client iOS.

- L’appel a duré un total de 60 minutes.
- Alice et Bob ont participé à la totalité de l’appel. Alice a activé la vidéo pendant cinq minutes et a partagé son écran pendant 23 minutes. Bob avait la vidéo activée pendant l’appel entier (60 minutes) et a partagé son écran pendant 12 minutes.
- Charlie a quitté l’appel après 43 minutes. Charlie a utilisé l’audio et la vidéo pendant toute la période où il a participé à l’appel (43 minutes).

**Calculs des coûts**

- 2 participants x 60 minutes x 0,004 USD par participant par minute = 0,48 USD [les flux vidéo et audio sont facturés au même tarif]
- 1 participant x 43 minutes x 0,004 USD par participant par minute = 0,172 USD [les flux vidéo et audio sont facturés au même tarif]

**Coût total de l’appel de groupe** : 0,48 USD + 0,172 USD = 0,652 USD

### <a name="pricing-example-a-user-of-the-communication-services-js-client-library-joins-a-scheduled-microsoft-teams-meeting"></a>Exemple de tarif : Un utilisateur de la bibliothèque de client Communication Services JS rejoint une réunion Microsoft Teams planifiée

Alice est un médecin en réunion avec son patient, Bob. Alice va se joindre à la visite à partir de l’application Teams Desktop. Bob va recevoir un lien pour s’y joindre en utilisant le site web médical du fournisseur, qui se connecte à la réunion en utilisant la bibliothèque de client Communication Services JS. Bob va utiliser son téléphone mobile pour participer à la réunion en utilisant un navigateur web (iPhone avec Safari). Une conversation sera disponible pendant la visite virtuelle.

- L’appel dure 30 minutes au total.
- Alice et Bob participent à la totalité de l’appel. Alice active sa vidéo 5 minutes après le démarrage de l’appel et partage son écran pendant 13 minutes. Bob a sa vidéo activée pendant toute la durée de l’appel.
- Alice envoie 5 messages et Bob répond avec 3 messages.


**Calculs des coûts**

- 1 participant (Bob) x 30 minutes x 0,004 USD par participant par minute = 0,12 USD [les flux vidéo et audio sont facturés au même tarif]
- 1 participant (Alice) x 30 minutes x 0,000 USD par participant par minute = 0,0 USD*.
- 1 participant (Bob) x 3 messages de conversation x 0,0008 USD = 0,0024 USD.
- 1 participant (Alice) x 5 messages de conversation x 0,000 USD = 0,0 USD.

*La participation d’Alice est couverte par sa licence Teams. Votre facture Azure indique pour information les minutes et les messages de conversation que les utilisateurs Teams ont eus avec des utilisateurs Communication Services, mais ces minutes et ces messages provenant du client Teams, ils ne sont pas facturés.

**Coût total de la visite** :
- Utilisateur se joignant en utilisant la bibliothèque de client Communication Services JS : 0,12 USD + 0,0024 USD = 0,1224 USD
- Utilisateur se joignant à l’application Teams Desktop : 0 USD (couvert par une licence Teams)


## <a name="chat"></a>Conversation

Avec Communication Services, vous pouvez améliorer votre application en ajoutant la possibilité d’envoyer et de recevoir des messages de conversation entre deux utilisateurs ou plus. Les bibliothèques de client Chat sont disponibles pour JavaScript, .NET, Python et Java. Reportez-vous à [cette page pour en savoir plus sur les bibliothèques de client](./sdk-options.md).

### <a name="price"></a>Price

Il vous est facturé 0,0008 USD pour chaque message de conversation envoyé.

### <a name="pricing-example-chat-between-two-users"></a>Exemple de tarif : Conversation entre deux utilisateurs

Geeta démarre un fil de conversation avec Emily pour partager une mise à jour et envoie 5 messages. La conversation dure 10 minutes. Geeta et Emily s’envoient chacune 15 messages.

**Calculs des coûts**
- Nombre de messages envoyés (5 + 15 + 15) x 0,0008 USD = 0,028 USD

### <a name="pricing-example-group-chat-with-multiple-users"></a>Exemple de tarif : Conversation de groupe avec plusieurs utilisateurs

Charlie démarre un fil de conversation avec ses amis Casey et Jasmine pour planifier des vacances. Ils discutent pendant un certain temps, au cours duquel Charlie, Casey et Jasmine envoient 20, 30 et 18 messages respectivement. Ils se rendent compte que leur amie Rose pourrait avoir envie de partir avec eux, de sorte qu’ils l’ajoutent au fil de conversation et partagent tout l’historique des messages avec elle.

Rose consulte les messages et s’engage dans la conversation. Entre-temps, Casey reçoit un appel et décide de reprendre la conversation ultérieurement. Charlie, Jasmine et Rose décident des dates du voyage et envoient encore 30, 25 et 35 messages respectivement.

**Calculs des coûts**

- Nombre de messages envoyés (20 + 30 + 18 + 30 + 25 + 35) x 0,0008 USD = 0,1264 USD


## <a name="telephony-and-sms"></a>Téléphonie et SMS

## <a name="price"></a>Price

Les services de téléphonie sont facturés à la minute, tandis que les SMS sont facturés à l’unité. Les tarifs sont déterminés par le type et le lieu du numéro que vous utilisez ainsi que par la destination de vos appels et SMS.

### <a name="telephone-number-leasing"></a>Location de numéros de téléphone

Les frais de location de numéros de téléphone sont facturés au départ, puis tous les mois :

|Type de nombre   |Frais mensuels   |
|--------------|-----------|
|Local (États-Unis)     |1 USD/mois        |
|Numéro gratuit (États-Unis) |2 USD/mois |


### <a name="telephone-calling"></a>Appels téléphoniques

Les appels téléphoniques traditionnels (appels pris en charge par le réseau téléphonique commuté public) sont disponibles au tarif de paiement à l’utilisation pour les numéros de téléphone basés aux États-Unis. Le prix est facturé par minute en fonction du type de numéro utilisé et de la destination de l’appel. Le tableau ci-dessous indique les tarifs pour les principales destinations des appels. Pour obtenir la liste complète des destinations, reportez-vous à la [liste détaillée des tarifs](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv).


#### <a name="united-states-calling-prices"></a>Prix d’appel aux États-Unis

Les tarifs suivants incluent les taxes et frais de communication obligatoires jusqu’au 30 juin 2021 :

|Type de nombre   |Passer des appels   |Recevoir des appels|
|--------------|-----------|------------|
|Local     |À partir de 0,013 USD/min       |0,0085 USD/min        |
|Numéro gratuit |0,013 USD/min   |0,0220 USD/min |

#### <a name="other-calling-destinations"></a>Autres destinations d’appels

Les tarifs suivants comprennent les taxes et frais de communication obligatoires jusqu’au 30 juin 2021 :

|Destinations des appels   |Prix par minute|
|-----------|------------|
|Canada     |À partir de 0,013 USD/min   |
|Royaume-Uni     |À partir de 0,015 USD/min   |
|Allemagne     |À partir de 0,015 USD/min   |
|France     |À partir de 0,016 USD/min   |


### <a name="sms"></a>sms

Les SMS sont facturés à l’utilisation. Le prix est fixé à l’unité, en fonction de la destination du message. Les messages peuvent être envoyés depuis des numéros gratuits vers des numéros de téléphone situés aux États-Unis. Notez que les numéros de téléphone locaux (géographiques) ne peuvent pas être utilisés pour envoyer des SMS.

Les tarifs suivants comprennent les taxes et frais de communication obligatoires jusqu’au 30 juin 2021 :

|Pays ou région   |Envoyer des messages|Recevoir des messages|
|-----------|------------|------------|
|États-Unis (numéro gratuit)    |0,0075 USD/SMS   | 0,0075 USD/SMS |
