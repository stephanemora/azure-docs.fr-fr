---
title: Se connecter à Azure Virtual Desktop avec le client web - Azure
description: Guide pratique pour se connecter à Azure Virtual Desktop à l’aide du client web.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: ea72705b7f5adf51dbc793cf73d88154f202b437
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754490"
---
# <a name="connect-to-azure-virtual-desktop-with-the-web-client"></a>Se connecter à Azure Virtual Desktop avec le client web

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop avec des objets Azure Virtual Desktop Azure Resource Manager. Si vous utilisez Azure Virtual Desktop (classique) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/connect-web-2019.md).

Le client web vous permet d’accéder à vos ressources Azure Virtual Desktop à partir d’un navigateur web sans processus d’installation fastidieux.

>[!NOTE]
>Le client web ne prend pas en charge les systèmes d’exploitation mobiles.

## <a name="supported-operating-systems-and-browsers"></a>Systèmes d’exploitation et navigateurs pris en charge
>[!IMPORTANT]
>Le 30 septembre 2021, le client web Azure Virtual Desktop ne prendra plus en charge Internet Explorer 11. Nous vous recommandons de passer à l’utilisation du navigateur Microsoft Edge au lieu d’Internet Explorer 11. Pour plus d’informations, consultez notre [billet de blog](https://aka.ms/WVDSupportIE11) de la communauté technique.

Tous les navigateurs HTML5 doivent fonctionner, et nous prenons officiellement en charge les systèmes d’exploitation et les navigateurs suivants.

| Browser           | Systèmes d’exploitation pris en charge                     | Notes               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Version 11 ou ultérieure |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Version 55 ou ultérieure |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Accéder au flux de ressources distantes

Dans un navigateur, accédez à la version du client web d’Azure Virtual Desktop intégrée dans Azure Resource Manager, à l’adresse <https://rdweb.wvd.microsoft.com/arm/webclient>, et connectez-vous avec votre compte d’utilisateur.

>[!NOTE]
>Si vous utilisez Azure Virtual Desktop (classique) sans l’intégration dans Azure Resource Manager, connectez-vous à vos ressources à l’adresse <https://rdweb.wvd.microsoft.com/webclient> à la place.
>
> Si vous utilisez le portail US Gov, utilisez <https://rdweb.wvd.azure.us/arm/webclient/index.html>.

>[!NOTE]
>Si vous vous êtes déjà connecté avec un compte Azure Active Directory différent de celui que vous voulez utiliser pour Azure Virtual Desktop, vous devez vous déconnecter ou utiliser une fenêtre de navigateur privée.

Une fois que vous êtes connecté, une liste de ressources apparaît. Vous pouvez lancer des ressources en les sélectionnant comme vous le feriez pour une application normale dans l’onglet **Toutes les ressources**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation du client web, voir [Prise en main du client web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
