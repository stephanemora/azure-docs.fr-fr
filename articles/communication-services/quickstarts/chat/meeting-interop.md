---
title: Bien démarrer avec l’interopérabilité de Teams sur Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: Dans ce guide de démarrage rapide, vous allez apprendre à rejoindre une réunion Teams avec la bibliothèque de client Azure Communication Chat.
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 75580931b2df33b02b9759b6faaa5bec548daaae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496170"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Démarrage rapide : Joindre votre application de conversation à une réunion Teams

> [!IMPORTANT]
> Pour activer/désactiver l’[interopérabilité des locataires Teams](../../concepts/teams-interop.md), remplissez [ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Démarrez avec Azure Communication Services en connectant votre solution de conversation à Microsoft Teams à l’aide de la bibliothèque de client JavaScript. 

## <a name="prerequisites"></a>Prérequis 

1.  [Déploiement Teams](/deployoffice/teams-install). 
2. [Application de conversation](./get-started.md) opérationnelle. 

## <a name="enable-teams-interoperability"></a>Activer l’interopérabilité de Teams 

Un utilisateur Communication Services qui rejoint une réunion Teams en tant qu’utilisateur invité ne peut accéder à la conversation de la réunion qu’après avoir rejoint l’appel de réunion Teams. Consultez la documentation [Interopérabilité de Teams](../voice-video-calling/get-started-teams-interop.md) pour savoir comment ajouter un utilisateur Communication Services à un appel de réunion Teams.

Vous devez être membre de l’organisation propriétaire des deux entités pour pouvoir utiliser cette fonctionnalité.

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez nettoyer et supprimer un abonnement Communication Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées. Apprenez-en davantage sur le [nettoyage des ressources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- Consultez notre [exemple de bannière de conversation](../../samples/chat-hero-sample.md).
- Apprenez-en davantage sur le [fonctionnement de la conversation](../../concepts/chat/concepts.md).