---
title: Se connecter à Azure Virtual Desktop avec le client web - Azure
description: Guide pratique pour se connecter à Azure Virtual Desktop à l’aide du client web.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 3804714a1a21a482166fbf7d592f5ee094ce6a06
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359719"
---
# <a name="connect-to-azure-virtual-desktop-with-the-web-client"></a>Se connecter à Azure Virtual Desktop avec le client web

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop avec des objets Azure Virtual Desktop Azure Resource Manager. Si vous utilisez Azure Virtual Desktop (classique) sans objets Azure Resource Manager, consultez [cet article](../virtual-desktop-fall-2019/connect-web-2019.md).

Le client web vous permet d’accéder à vos ressources Azure Virtual Desktop à partir d’un navigateur web sans processus d’installation fastidieux.

>[!NOTE]
>Le client web ne prend pas en charge les systèmes d’exploitation mobiles.

## <a name="supported-operating-systems-and-browsers"></a>Systèmes d’exploitation et navigateurs pris en charge

>[!IMPORTANT]
>Depuis le 30 septembre 2021, le client web d’Azure Virtual Desktop ne prend plus en charge Internet Explorer. Nous vous recommandons d’utiliser Microsoft Edge pour vous connecter au client web. Pour plus d’informations, consultez notre [billet de blog](https://aka.ms/WVDSupportIE11).

Tous les navigateurs HTML5 doivent fonctionner, et nous prenons officiellement en charge les systèmes d’exploitation et les navigateurs suivants.

| Browser           | Systèmes d’exploitation pris en charge                     | Notes               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Version 55 ou ultérieure |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Accéder au flux de ressources distantes

Dans un navigateur, accédez à la version du client web d’Azure Virtual Desktop intégrée dans Azure Resource Manager, à l’adresse <https://rdweb.wvd.microsoft.com/arm/webclient>, et connectez-vous avec votre compte d’utilisateur.

>[!NOTE]
>Si vous utilisez Azure Virtual Desktop (classique) sans l’intégration dans Azure Resource Manager, connectez-vous à vos ressources à l’adresse <https://rdweb.wvd.microsoft.com/webclient> à la place.
>
> Si vous utilisez le portail US Gov, utilisez <https://rdweb.wvd.azure.us/arm/webclient/index.html>.
> 
> Pour vous connecter au portail Azure Chine, utilisez <https://rdweb.wvd.azure.cn/arm/webclient/index.html>.

>[!NOTE]
>Si vous vous êtes déjà connecté avec un compte Azure Active Directory différent de celui que vous voulez utiliser pour Azure Virtual Desktop, vous devez vous déconnecter ou utiliser une fenêtre de navigateur privée.

Une fois que vous êtes connecté, une liste de ressources apparaît. Vous pouvez lancer des ressources en les sélectionnant comme vous le feriez pour une application normale dans l’onglet **Toutes les ressources**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation du client web, voir [Prise en main du client web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
