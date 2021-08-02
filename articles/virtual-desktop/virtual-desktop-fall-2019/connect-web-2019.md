---
title: Connecter un client web Azure Virtual Desktop (classique) - Azure
description: Explique comment se connecter à Azure Virtual Desktop (classique) à l’aide du client web.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: e604e5de1a5abd46961c0ec447f7d3fb29aa3b49
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111749954"
---
# <a name="connect-to-azure-virtual-desktop-classic-with-the-web-client"></a>Se connecter à Azure Virtual Desktop (classique) avec le client web

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop pour Azure Resource Manager. Si vous essayez de gérer des objets Azure Virtual Desktop pour Azure Resource Manager, consultez [cet article](../connect-web.md).

Le client web vous permet d’accéder à vos ressources Azure Virtual Desktop à partir d’un navigateur web sans processus d’installation fastidieux.

>[!NOTE]
>Le client web ne prend pas en charge les systèmes d’exploitation mobiles.

## <a name="supported-operating-systems-and-browsers"></a>Systèmes d’exploitation et navigateurs pris en charge

Tous les navigateurs HTML5 doivent fonctionner, et nous prenons officiellement en charge les systèmes d’exploitation et les navigateurs suivants.

| Browser           | Systèmes d’exploitation pris en charge                     | Notes               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Version 55 ou ultérieure |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Accéder au flux de ressources distantes

Dans un navigateur, accédez au client web d’Azure Virtual Desktop sur <https://rdweb.wvd.microsoft.com/webclient> et connectez-vous avec votre compte d’utilisateur.

>[!NOTE]
>Si vous utilisez Azure Virtual Desktop avec l’intégration dans Azure Resource Manager, connectez-vous à vos ressources sur <https://rdweb.wvd.microsoft.com/arm/webclient> à la place.

>[!NOTE]
>Si vous vous êtes déjà connecté avec un compte Azure Active Directory différent de celui que vous voulez utiliser pour Azure Virtual Desktop, vous devez vous déconnecter ou utiliser une fenêtre de navigateur privée.

Une fois que vous êtes connecté, une liste de ressources apparaît. Vous pouvez lancer des ressources en les sélectionnant comme vous le feriez pour une application normale dans l’onglet **Toutes les ressources**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation du client web, voir [Prise en main du client web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
