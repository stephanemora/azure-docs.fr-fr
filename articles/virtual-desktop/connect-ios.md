---
title: Se connecter à Windows Virtual Desktop à partir d’iOS – Azure
description: Guide pratique pour se connecter à Windows Virtual Desktop à l’aide du client iOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: bfc7efa6e8ead3b53704e3c9bd189b18cb787618
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605832"
---
# <a name="connect-with-the-ios-client"></a>Se connecter avec le client iOS

> S’applique à : iOS 8.0 ou version ultérieure. Compatible avec iPhone, iPad et iPod touch.

>[!NOTE]
> Le client iOS est en préversion.

Vous pouvez accéder aux ressources Windows Virtual Desktop à partir de votre appareil iOS avec notre client téléchargeable. Ce guide vous explique comment configurer le client iOS.

## <a name="install-the-ios-beta-client"></a>Installer la version bêta du client iOS
Pour installer la version bêta du client iOS :

1. Installez l’application [Apple TestFlight](https://apps.apple.com/us/app/testflight/id899247664) sur votre appareil iOS.
2. Sur votre appareil iOS, ouvrez un navigateur et accédez à [aka.ms/rdiosbeta](https://aka.ms/rdiosbeta).
3. Sous l’étiquette **Step 2: Join the Beta** (Étape 2 - Participer à la version bêta), sélectionnez **Start Testing** (Commencer à tester).
4. Une fois que vous êtes redirigé vers l’application TestFlight, sélectionnez **Accept** (Accepter), puis **Install** (Installer).

## <a name="subscribe-to-a-feed"></a>S’abonner à un flux

Abonnez-vous au flux fourni par votre administrateur pour obtenir la liste des ressources managées auxquelles vous pouvez accéder sur votre appareil iOS.

Pour s’abonner à un flux :

1. Dans le Centre de connexion, appuyez sur **+** , puis sur **Add Workspace** (Ajouter un espace de travail).
2. Entrez l’URL du flux dans le champ **Feed URL** (URL du flux). Il peut s’agir d’une URL ou d’une adresse e-mail.
   - Si vous utilisez une URL, utilisez celle que votre administrateur vous a donnée. Normalement, l’URL est <https://rdweb.wvd.microsoft.com>.
   - Pour utiliser l’e-mail, entrez votre adresse e-mail. Cela indique au client de rechercher une URL associée à votre adresse e-mail si votre administrateur a configuré le serveur de cette manière.
3. Appuyez sur **Suivant**.
4. À l’invite, indiquez vos informations d’identification.
   - Pour **Nom d’utilisateur**, indiquez le nom de l’utilisateur ayant l’autorisation d’accéder aux ressources.
   - Pour **Mot de passe**, indiquez le mot de passe associé au nom d’utilisateur.
   - Vous pouvez également être invité à fournir des facteurs supplémentaires si votre administrateur a configuré l’authentification de cette manière.
5. Appuyez sur **Save** (Enregistrer).

Le Centre de connexion doit ensuite afficher les ressources distantes.

Une fois que vous êtes abonné à un flux, son contenu est mis à jour automatiquement et régulièrement. Des ressources peuvent être ajoutées, modifiées ou supprimées en fonction des modifications apportées par votre administrateur.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation de la version bêta du client iOS, consultez [Bien démarrer avec le client iOS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios).
