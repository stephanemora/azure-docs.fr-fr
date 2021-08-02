---
title: Connexion à Azure Virtual Desktop sur Android – Azure
description: Guide pratique pour se connecter à Azure Virtual Desktop avec le client Android.
author: Heidilohr
ms.topic: how-to
ms.date: 03/25/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: bb88149c3efb6eca528fe38141b3a472b2990107
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111757928"
---
# <a name="connect-to-azure-virtual-desktop-with-the-android-client"></a>Connexion à Azure Virtual Desktop avec le client Android

> S’applique à : Android 4.1 et versions ultérieures, Chromebooks avec ChromeOS 53 et versions ultérieures.

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop avec des objets Azure Virtual Desktop pour Azure Resource Manager. Si vous utilisez Azure Virtual Desktop (classique) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/connect-android-2019.md).

Vous pouvez accéder aux ressources Azure Virtual Desktop sur votre appareil Android avec notre client téléchargeable. Vous pouvez également utiliser le client Android sur des appareils Chromebook qui prennent en charge le Google Play Store. Ce guide explique comment configurer le client Android.

## <a name="install-the-android-client"></a>Installer le client Android

Pour commencer, [téléchargez](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) et installez le client sur votre appareil Android.

## <a name="subscribe-to-a-feed"></a>S’abonner à un flux

Abonnez-vous au flux fourni par votre administrateur pour obtenir la liste des ressources managées auxquelles vous pouvez accéder sur votre appareil Android.

Pour s’abonner à un flux :

1. Dans Connection Center, appuyez sur **+** , puis sur **Remote Resource Feed** (Flux de ressources à distance).
2. Entrez l’URL du flux dans le champ **Feed URL** (URL du flux). Il peut s’agir d’une URL ou d’une adresse e-mail.
   - Si vous utilisez une URL, choisissez celle que votre administrateur vous a donnée, normalement <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>.
   - Pour utiliser l’e-mail, entrez votre adresse e-mail. Le client recherche une URL associée à votre adresse de adresse e-mail si votre administrateur a configuré le serveur de cette façon.
   - Pour vous connecter par le biais du portail US Gov, utilisez <https://rdweb.wvd.azure.us/api/arm/feeddiscovery>.
3. Appuyez sur **SUIVANT**.
4. À l’invite, indiquez vos informations d’identification.
   - Pour **Nom d’utilisateur**, indiquez le nom de l’utilisateur ayant l’autorisation d’accéder aux ressources.
   - Pour **Mot de passe**, indiquez le mot de passe associé au nom d’utilisateur.
   - Vous pouvez également être invité à fournir des facteurs supplémentaires si votre administrateur a configuré l’authentification de cette manière.

Une fois l’abonnement souscrit, le Connection Center doit afficher les ressources distantes.

Une fois que vous êtes abonné à un flux, son contenu est mis à jour automatiquement et régulièrement. Des ressources peuvent être ajoutées, modifiées ou supprimées en fonction des modifications apportées par votre administrateur.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation du client Android, voir [Prise en main du client Android](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).
