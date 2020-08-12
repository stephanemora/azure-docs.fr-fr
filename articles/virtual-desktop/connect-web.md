---
title: Se connecter à Windows Virtual Desktop avec le client web - Azure
description: Guide pratique pour se connecter à Windows Virtual Desktop à l’aide du client web.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fdbd0ddb33785655cada2600acdecdced4aeb2f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283433"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Se connecter à Windows Virtual Desktop avec le client web

>[!IMPORTANT]
>Ce contenu s’applique à Windows Virtual Desktop avec des objets Windows Virtual Desktop Azure Resource Manager. Si vous utilisez la version Windows Virtual Desktop (classique) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/connect-web-2019.md).

Le client web vous permet d’accéder à vos ressources Windows Virtual Desktop à partir d’un navigateur web sans processus d’installation fastidieux.

>[!NOTE]
>Le client web ne prend pas en charge les systèmes d’exploitation mobiles.

## <a name="supported-operating-systems-and-browsers"></a>Systèmes d’exploitation et navigateurs pris en charge

Tous les navigateurs HTML5 doivent fonctionner, et nous prenons officiellement en charge les systèmes d’exploitation et les navigateurs suivants.

| Browser           | Systèmes d’exploitation pris en charge                     | Notes               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Version 11 ou ultérieure |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Version 55 ou ultérieure |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Accéder au flux de ressources distantes

Dans un navigateur, accédez à la version du client web de Windows Virtual Desktop intégrée dans Azure Resource Manager, à l’adresse <https://rdweb.wvd.microsoft.com/arm/webclient>, et connectez-vous avec votre compte d’utilisateur.

>[!NOTE]
>Si vous utilisez Windows Virtual Desktop (classique) sans l’intégration dans Azure Resource Manager, connectez-vous à vos ressources à l’adresse <https://rdweb.wvd.microsoft.com/webclient> à la place.

>[!NOTE]
>Si vous vous êtes déjà connecté avec un compte Azure Active Directory différent de celui que vous voulez utiliser pour Windows Virtual Desktop, vous devez vous déconnecter ou utiliser une fenêtre de navigateur privée.

Une fois que vous êtes connecté, une liste de ressources apparaît. Vous pouvez lancer des ressources en les sélectionnant comme vous le feriez pour une application normale dans l’onglet **Toutes les ressources**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation du client web, voir [Prise en main du client web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
