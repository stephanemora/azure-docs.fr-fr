---
title: Se connecter à Windows Virtual Desktop à partir d’Android – Azure
description: Comment se connecter à Windows Virtual Desktop à l’aide du client Android.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: helohr
ms.openlocfilehash: c19aa6e0acc936c5b03afdab99ce0b9230838ce2
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71341184"
---
# <a name="connect-with-the-android-client"></a>Se connecter avec le client Android

> S’applique à : Android 4.1 et versions ultérieures, Chromebooks avec ChromeOS 53 et versions ultérieures.

>[!NOTE]
> La possibilité d’accéder aux ressources Windows Virtual Desktop à partir du client Android est actuellement disponible en préversion.

Vous pouvez accéder aux ressources Windows Virtual Desktop à partir de votre appareil Android avec notre client téléchargeable. Vous pouvez également utiliser le client Android sur des appareils Chromebook qui prennent en charge le Google Play Store. Ce guide explique comment configurer le client Android.

## <a name="install-the-android-client"></a>Installer le client Android

Pour commencer, [téléchargez](https://play.google.com/store/apps/details?id=com.microsoft.rdc.android) et installez le client sur votre appareil Android.

## <a name="subscribe-to-a-feed"></a>S’abonner à un flux

Abonnez-vous au flux fourni par votre administrateur pour obtenir la liste des ressources managées auxquelles vous pouvez accéder sur votre appareil Android.

Pour s’abonner à un flux :

1. Dans Connection Center, appuyez sur **+** , puis sur **Remote Resource Feed** (Flux de ressources à distance).
2. Entrez l’URL du flux dans le champ **URL du flux**. L’URL du flux peut être une URL ou une adresse e-mail.
   - Si vous utilisez une URL, choisissez celle que votre administrateur vous a donnée, normalement <https://rdweb.wvd.microsoft.com>.
   - Pour utiliser une adresse e-mail, entrez la vôtre. Le client recherche une URL associée à votre adresse de adresse e-mail si votre administrateur a configuré le serveur de cette façon.
3. Appuyez sur **SUIVANT**.
4. À l’invite, indiquez vos informations d’identification.
   - Pour **Nom d´utilisateur**, entrez le nom de l’utilisateur autorisé à accéder aux ressources.
   - Pour **Mot de passe**, entrez le mot de passe associé au nom d’utilisateur.
   - Il se peut que vous soyez invité à fournir des informations supplémentaires si votre administrateur a configuré l’authentification de cette façon.

Une fois l’abonnement souscrit, le Connection Center doit afficher les ressources distantes.

Le contenu du flux de données auquel vous êtes abonné est régulièrement mis à jour. Des ressources peuvent être ajoutées, modifiées ou supprimées en fonction des modifications apportées par votre administrateur.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation du client Android, voir [Prise en main du client Android](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-android).
