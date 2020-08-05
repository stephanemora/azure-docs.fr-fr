---
title: Se connecter à Windows Virtual Desktop à partir d’iOS – Azure
description: Guide pratique pour se connecter à Windows Virtual Desktop à l’aide du client iOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bbf169a4be639d8745670a33b47a7392d13b6b07
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291467"
---
# <a name="connect-to-windows-virtual-desktop-with-the-ios-client"></a>Connexion à Windows Virtual Desktop avec le client iOS

> S’applique à : iOS 13.0 ou ultérieur. Compatible avec iPhone, iPad et iPod touch.

>[!IMPORTANT]
>Ce contenu s’applique à Windows Virtual Desktop avec des objets Windows Virtual Desktop Azure Resource Manager. Si vous utilisez Windows Virtual Desktop (classique) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/connect-ios-2019.md).

Vous pouvez accéder aux ressources Windows Virtual Desktop à partir de votre appareil iOS avec notre client téléchargeable. Ce guide vous explique comment configurer le client iOS.

## <a name="install-the-ios-client"></a>Installer le client iOS

Pour commencer, [téléchargez](https://aka.ms/rdios) et installez le client sur votre appareil iOS.

## <a name="subscribe-to-a-feed"></a>S’abonner à un flux

Abonnez-vous au flux fourni par votre administrateur pour obtenir la liste des ressources managées auxquelles vous pouvez accéder sur votre appareil iOS.

Pour s’abonner à un flux :

1. Dans le Centre de connexion, appuyez sur **+** , puis sur **Add Workspace** (Ajouter un espace de travail).
2. Entrez l’URL du flux dans le champ **Feed URL** (URL du flux). Il peut s’agir d’une URL ou d’une adresse e-mail.
   - Si vous utilisez une URL, utilisez celle que votre administrateur vous a donnée. En règle générale, l’URL est <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>.
   - Pour utiliser l’e-mail, entrez votre adresse e-mail. Cela indique au client de rechercher une URL associée à votre adresse e-mail si votre administrateur a configuré le serveur de cette manière.
3. Appuyez sur **Suivant**.
4. À l’invite, indiquez vos informations d’identification.
   - Pour **Nom d’utilisateur**, indiquez le nom de l’utilisateur ayant l’autorisation d’accéder aux ressources.
   - Pour **Mot de passe**, indiquez le mot de passe associé au nom d’utilisateur.
   - Vous pouvez également être invité à fournir des facteurs supplémentaires si votre administrateur a configuré l’authentification de cette manière.
5. Appuyez sur **Save** (Enregistrer).

Une fois cette opération terminée, le Centre de connexion doit afficher les ressources distantes.

Une fois que vous êtes abonné à un flux, son contenu est automatiquement mis à jour de façon régulière. Des ressources peuvent être ajoutées, modifiées ou supprimées en fonction des modifications apportées par votre administrateur.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation du client iOS, consultez [Bien démarrer avec le client iOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/).
