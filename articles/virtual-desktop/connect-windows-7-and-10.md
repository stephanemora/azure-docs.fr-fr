---
title: Se connecter à la préversion de bureau virtuel Windows avec Windows 7 et Windows 10 - Azure
description: Comment se connecter au service Windows Virtual Desktop Preview avec Windows 7 ou Windows 10.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/27/2019
ms.author: helohr
ms.openlocfilehash: 8b5a649d767dae9ae9c0fb077f550451a7f83601
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630377"
---
# <a name="connect-with-windows-7-and-windows-10"></a>Se connecter avec Windows 7 et Windows 10

> S’applique à Windows 7 et Windows 10.

Un client téléchargeable est disponible qui fournit l’accès aux ressources de la version préliminaire de bureau virtuel Windows à partir d’appareils exécutant Windows 7 et Windows 10.

> [!IMPORTANT]
> N’utilisez pas **aux programmes RemoteApp et bureau connexions (RADC)** ou **connexion Bureau à distance (MSTSC)** pour accéder aux ressources de bureau virtuel Windows car bureau virtuel Windows ne prend pas en charge soit client.

## <a name="install-the-client"></a>Installation du client

[Télécharger](https://go.microsoft.com/fwlink/?linkid=2068602) et installer le client sur votre ordinateur local. Cela nécessite des droits d’administrateur.

## <a name="subscribe-to-a-feed"></a>S’abonner à un flux

Obtenir la liste des ressources disponibles pour vous à partir de votre ordinateur local en vous abonnant au flux fourni par votre administrateur.

Pour vous abonner à un flux :

1. Démarrez le client à partir de la liste de toutes les applications, recherchez pour **Bureau à distance**.
1. Sélectionnez **s’abonner** sur la page principale pour vous connecter au service et récupérer vos ressources.
1. **Connectez-vous** avec votre compte d’utilisateur lorsque vous y êtes invité.

Après avoir authentifié, vous devez maintenant voir une liste de ressources disponibles pour vous.

Vous pouvez lancer des ressources par une des deux méthodes.

- À partir de la page principale du client, double-cliquez sur une ressource pour le lancer.
- Lancez une ressource comme vous le feriez normalement autres applications à partir du Menu Démarrer.
  - Vous pouvez également rechercher les applications dans la barre de recherche.

## <a name="update-the-client"></a>Mettre à jour le client

Lorsqu’une nouvelle version du client est disponible, vous serez averti par le client et le centre de maintenance de Windows. Sélectionnez la notification pour démarrer le processus de mise à jour.
