---
title: Se connecter à la version d’évaluation bureau virtuel Windows à partir de Windows 10 ou Windows 7 - Azure
description: Comment se connecter à la préversion de bureau virtuel Windows à partir de Windows 10 ou Windows 7.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/24/2019
ms.author: helohr
ms.openlocfilehash: b7d7b25d0355f2379b90313f17e2b595234df827
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145989"
---
# <a name="connect-from-windows-10-or-windows-7"></a>Se connecter à partir de Windows 10 ou Windows 7

> S’applique à : Windows 7 et Windows 10.

Un client téléchargeable est disponible qui fournit l’accès aux ressources de la version préliminaire de bureau virtuel Windows à partir d’appareils exécutant Windows 7 et Windows 10.

> [!IMPORTANT]
> N’utilisez pas **aux programmes RemoteApp et bureau connexions (RADC)** ou **connexion Bureau à distance (MSTSC)** pour accéder aux ressources de bureau virtuel Windows car bureau virtuel Windows ne prend pas en charge soit client.

## <a name="install-the-client"></a>Installation du client

[Télécharger](https://go.microsoft.com/fwlink/?linkid=2068602) et installer le client sur votre ordinateur local. L’installation nécessite des droits d’administrateur.

## <a name="subscribe-to-a-feed"></a>S’abonner à un flux

Obtenir la liste des ressources managées disponibles pour vous en vous abonnant au flux fourni par votre administrateur. Abonnement rend les ressources disponibles sur votre ordinateur local.

Pour vous abonner à un flux :

1. Démarrez le client à partir de la liste de toutes les applications, recherchez pour **Bureau à distance**.
1. Sélectionnez **s’abonner** sur la page principale pour vous connecter au service et récupérer vos ressources.
1. **Connectez-vous** avec votre compte d’utilisateur lorsque vous y êtes invité.

Après avoir authentifié, vous devez maintenant voir une liste de ressources disponibles pour vous.

Vous pouvez lancer des ressources par une des deux méthodes.

- À partir de la page principale du client, double-cliquez sur une ressource pour le lancer.
- Lancez une ressource comme vous le feriez normalement autres applications à partir du Menu Démarrer.
  - Vous pouvez également rechercher les applications dans la barre de recherche.

Une fois abonné à un flux, le contenu du flux de données est automatiquement mis à jour régulièrement. Ressources peuvent être ajoutées, modifiées ou supprimées en fonction des modifications apportées par votre administrateur.

## <a name="view-the-details-of-a-feed"></a>Afficher les détails d’un flux de données

Une fois abonné, vous pouvez afficher des informations supplémentaires sur le flux en accédant au panneau de détails.

1. Dans la page principale du client, sélectionnez les points de suspension (**...** ) à droite du nom du flux.
1. Dans le menu déroulant, sélectionnez **détails**.
1. Le panneau de détails s’affiche sur le côté droit du client.

Le volet des détails contient des informations utiles sur le flux :

- L’URL et le nom d’utilisateur utilisé pour l’abonnement
- Le nombre d’applications et des postes de travail
- La date/heure de la dernière mise à jour
- L’état de la dernière mise à jour

Si nécessaire, vous pouvez démarrer une mise à jour manuelle en sélectionnant sur **mettre à jour maintenant**.

## <a name="unsubscribe-from-a-feed"></a>Se désabonner d’un flux

Cette section va vous apprendre à vous désabonner à partir d’un flux. Vous pouvez vous désabonner pour vous inscrire à nouveau avec un autre compte ou supprimer vos ressources à partir du système.

1. Dans la page principale du client, sélectionnez les points de suspension (**...** ) à droite du nom du flux.
1. Dans le menu déroulant, sélectionnez **Unsubscribe**.
1. Passez en revue et sélectionnez **continuer** à partir de la boîte de dialogue.

## <a name="update-the-client"></a>Mettre à jour le client

Lorsqu’une nouvelle version du client est disponible, vous serez averti par le client et le centre de maintenance de Windows. Sélectionnez la notification pour démarrer le processus de mise à jour.
