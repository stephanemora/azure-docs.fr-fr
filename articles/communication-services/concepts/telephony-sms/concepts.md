---
title: Concepts relatifs aux SMS dans Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Découvrez les concepts relatifs aux SMS dans Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e5cfc1e27bae10a1c67e4506afe9db825664785f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944240"
---
# <a name="sms-concepts"></a>Concepts relatifs aux SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure Communication Services vous permet d’envoyer et de recevoir des messages texte SMS à l’aide des bibliothèques clientes SMS Communication Services. Ces bibliothèques clientes peuvent être utilisées pour prendre en charge les scénarios de service client, les rappels de rendez-vous, l’authentification à 2 facteurs et d’autres besoins de communication en temps réel. Le service SMS de Communication Services vous permet d’envoyer des messages de manière fiable tout en affichant leur remise et des insights sur le taux de réponse dans le cadre de vos campagnes.

Les principales fonctionnalités des bibliothèques clientes SMS d’Azure Communication Services incluent :

-  Une expérience de configuration **simple** pour l’ajout de la fonctionnalité SMS dans vos applications.
- Une prise en charge des messages à **haute vitesse** via des numéros de téléphone gratuits pour les cas d’utilisation A2P (application à personne) aux États-Unis.
- Des conversations **bidirectionnelles** pour prendre en charge des scénarios tels que le support client, les alertes et les rappels de rendez-vous.
- Une **remise fiable** avec des rapports de remise en temps réel pour les messages envoyés à partir de votre application.
- Des **analyses** pour effectuer le suivi de vos modèles d’utilisation et de l’engagement client.
- Une prise en charge de la gestion des **désactivations** pour détecter et respecter automatiquement les désactivations pour les numéros gratuits. Communication Services détecte les messages STOP et START, et envoie les réponses par défaut suivantes aux utilisateurs finaux : 
  - STOP - *« Vous avez été correctement désabonné des messages de ce numéro. Répondez START pour vous réabonner. »*
  - START - *« Vous avez été correctement réabonné aux messages de ce numéro. Répondez STOP pour vous désabonner. »*
  - Les messages STOP et START sont relayés jusqu’à vous. Azure Communication Services vous encourage à surveiller et à implémenter ces désactivations pour garantir qu’aucun autre message n’est envoyé aux destinataires qui ont refusé vos communications.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Prise en main de l’envoi de SMS](../../quickstarts/telephony-sms/send.md)

Les documents suivants peuvent vous intéresser :

- Se familiariser avec la [bibliothèque cliente SMS](../telephony-sms/sdk-features.md)
- Obtenir un [numéro de téléphone](../../quickstarts/telephony-sms/get-phone-number.md) compatible SMS
- [Planifier votre solution SMS](../telephony-sms/plan-solution.md)
