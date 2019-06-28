---
title: Se connecter à Windows Virtual Desktop (préversion) à partir de Windows 10 ou Windows 7 - Azure
description: Comment se connecter à la préversion de Windows Virtual Desktop à partir de Windows 10 ou Windows 7.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/24/2019
ms.author: helohr
ms.openlocfilehash: b7d7b25d0355f2379b90313f17e2b595234df827
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65145989"
---
# <a name="connect-from-windows-10-or-windows-7"></a>Se connecter à partir de Windows 10 ou Windows 7

> S’applique à : Windows 7 et Windows 10.

Un client téléchargeable, qui permet d’accéder aux ressources de la préversion de Windows Virtual Desktop à partir d’appareils exécutant Windows 7 et Windows 10, est disponible.

> [!IMPORTANT]
> N’utilisez pas **Connexions aux programmes RemoteApp et aux services Bureau à distance (RADC)** ou **Connexion Bureau à distance (MSTSC)** pour accéder aux ressources Windows Virtual Desktop car Windows Virtual Desktop ne prend pas en charge ces clients.

## <a name="install-the-client"></a>Installation du client

[Téléchargez](https://go.microsoft.com/fwlink/?linkid=2068602) et installez le client sur votre ordinateur local. L’installation nécessite des droits d’administrateur.

## <a name="subscribe-to-a-feed"></a>S’abonner à un flux

Obtenez la liste des ressources managées disponibles pour vous en vous abonnant au flux fourni par votre administrateur. Grâce à un abonnement, les ressources sont disponibles sur votre ordinateur local.

Pour s’abonner à un flux :

1. Démarrez le client à partir de la liste de toutes les applications, puis recherchez **Bureau à distance**.
1. Sélectionnez **S’abonner** dans la page principale pour vous connecter au service et récupérer vos ressources.
1. **Connectez-vous** avec votre compte d’utilisateur lorsque vous y êtes invité.

Lorsque vous êtes authentifié, vous devez voir une liste de ressources disponibles pour vous.

Vous pouvez lancer des ressources selon l’une de deux méthodes suivantes.

- Dans la page principale du client, double-cliquez sur une ressource pour la lancer.
- Lancez une ressource comme vous le feriez normalement pour d’autres applications à partir du menu Démarrer.
  - Vous pouvez également rechercher les applications dans la barre de recherche.

Une fois abonné à un flux, le contenu du flux de données est automatiquement mis à jour régulièrement. Des ressources peuvent être ajoutées, modifiées ou supprimées en fonction des modifications apportées par votre administrateur.

## <a name="view-the-details-of-a-feed"></a>Afficher les détails d’un flux

Une fois abonné, vous pouvez afficher des informations supplémentaires sur le flux en accédant au panneau de détails.

1. Dans la page principale du client, sélectionnez les points de suspension ( **...** ) à droite du nom du flux.
1. Dans le menu déroulant, sélectionnez **Détails**.
1. Le panneau de détails s’affiche sur le côté droit du client.

Le panneau de détails contient des informations utiles sur le flux :

- L’URL et le nom d’utilisateur utilisé pour l’abonnement
- Le nombre d’applications et de bureaux
- La date/heure de la dernière mise à jour
- L’état de la dernière mise à jour

Si nécessaire, vous pouvez démarrer une mise à jour manuelle en sélectionnant **Mettre à jour maintenant**.

## <a name="unsubscribe-from-a-feed"></a>Se désabonner d’un flux

Cette section va vous apprendre à vous désabonner d’un flux. Vous pouvez vous désabonner pour vous inscrire à nouveau avec un autre compte ou supprimer vos ressources du système.

1. Dans la page principale du client, sélectionnez les points de suspension ( **...** ) à droite du nom du flux.
1. Dans le menu déroulant, sélectionnez **Se désabonner**.
1. Passez en revue et sélectionnez **Continuer** dans la boîte de dialogue.

## <a name="update-the-client"></a>Mettre à jour le client

Lorsqu’une nouvelle version du client est disponible, vous êtes informé par le client et le Centre de notifications Windows. Sélectionnez la notification pour démarrer le processus de mise à jour.
