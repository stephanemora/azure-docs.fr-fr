---
title: Ajouter un compte professionnel ou scolaire à l’application Microsoft Authenticator - Azure AD
description: Ajoutez votre compte professionnel ou scolaire à l’application Microsoft Authenticator pour vérifier votre identité tout en utilisant la vérification à deux facteurs.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 05/11/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 329ddb32282d91ec5e990753057aea606622a266
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110535182"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Ajouter votre compte professionnel ou scolaire dans l’application Microsoft Authenticator

Si votre organisation utilise la vérification en deux étapes, vous pouvez configurer votre compte professionnel ou scolaire pour utiliser l’application Microsoft Authenticator comme l’une des méthodes de vérification.

>[!Important]
>Avant de pouvoir ajouter votre compte, vous devez télécharger et installer l’application Microsoft Authenticator. Si ce n’est pas déjà fait, suivez la procédure décrite dans l’article [Télécharger et installer l’application](user-help-auth-app-download-install.md).

## <a name="add-your-work-or-school-account"></a>Ajout de votre compte professionnel ou scolaire

Vous pouvez ajouter vos comptes professionnels ou scolaires à l’application Microsoft Authenticator en procédant de l’une des manières suivantes :

- Connectez-vous avec votre compte professionnel ou scolaire (préversion)
- Scanner un code QR

### <a name="sign-in-with-your-credentials"></a>Se connecter avec les informations d’identification

>[!Note]
>Vous pouvez maintenant vous connecter à l'application Microsoft Authenticator pour ajouter votre compte professionnel ou scolaire.

Pour ajouter un compte en vous connectant à votre compte professionnel ou scolaire à l’aide de vos informations d’identification :

1. Ouvrez l’application Microsoft Authenticator, sélectionnez le bouton **+** , puis appuyez sur **Ajouter un compte professionnel ou scolaire**. Sélectionnez **Connexion**.

1. Entrez les informations d'identification de votre compte professionnel ou scolaire. Le cas échéant, vous pouvez vous connecter à l’aide d’un mot de passe d'accès temporaire (TAP). À ce stade, vous risquez de ne pas pouvoir continuer en raison de l’une des conditions suivantes :

   - Si vous ne disposez pas de suffisamment de méthodes d'authentification sur votre compte pour obtenir un jeton d'authentification renforcée, vous ne pouvez pas procéder à l'ajout d'un compte.

   - Si vous recevez le message `You might be signing in from a location that is restricted by your admin`, cela signifie que votre administrateur n'a pas activé cette fonctionnalité pour vous. Vous pouvez essayer de configurer votre compte en scannant un code QR sur la page **Vérification de sécurité supplémentaire** ou dans [Informations de sécurité](https://mysignins.microsoft.com/security-info).

1. Si votre administrateur vous autorise à utiliser la connexion par téléphone à l'aide de l'application Authenticator, vous pourrez procéder à l'inscription de l'appareil afin de configurer la connexion par téléphone sans mot de passe et l'authentification multifacteur Azure (MFA). Mais vous pourrez toujours configurer l'authentification multifacteur, que vous ayez activé ou non la connexion par téléphone.

1. À ce stade, vous pouvez être invité à scanner un code QR fourni par votre organisation pour configurer un compte local avec authentification multifacteur dans l’application. Vous ne devez effectuer cette opération que si votre organisation utilise un serveur MFA local.

1. Sur votre appareil, appuyez sur le compte et vérifiez dans l'affichage plein écran que votre compte est correct. Pour des raisons de sécurité, le code de vérification change toutes les 30 secondes, ce qui empêche quiconque d’utiliser plusieurs fois un même code.

## <a name="sign-in-with-a-qr-code"></a>Se connecter avec un code QR

Pour ajouter un compte en scannant un code QR, procédez comme suit :

1. Sur votre ordinateur, accédez à la page **Vérification de sécurité supplémentaire**.

   >[!Note]
   >Si la page **Vérification de sécurité supplémentaire** n’est pas affichée, il se peut que votre administrateur ait activé l’expérience des informations de sécurité (préversion). Si tel est le cas, vous devez suivre les instructions de la section [Configurer les informations de sécurité pour utiliser une application d’authentification](security-info-setup-auth-app.md). Si ce n’est pas le cas, vous devez contacter le support technique de votre organisation pour obtenir de l’aide. Pour plus d’informations sur les informations de sécurité, consultez [Configurer vos informations de sécurité à partir d’une invite de connexion](security-info-setup-signin.md).

1. Cochez la case en regarde l’application Authenticator, puis sélectionnez **Configurer**. La page **Configurer l’application mobile** s’affiche.

   ![Écran qui fournit un code QR](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. Ouvrez l’application Microsoft Authenticator, sélectionnez ![l’icône plus sur les appareils iOS ou Android](media/user-help-auth-app-add-work-school-account/plus-icon.png) et sélectionnez **Ajouter un compte**, choisissez **Compte professionnel ou scolaire**, puis **Scanner un code QR**.
   Si vous n’avez pas de compte configuré dans l’application d’authentification, vous verrez un grand bouton bleu intitulé **Ajouter un compte**.

Si vous n’êtes pas invité à utiliser votre appareil photo pour scanner un code QR, dans les paramètres de votre téléphone, assurez-vous que l’application d’authentification a accès à l’appareil photo du téléphone.

## <a name="sign-in-on-a-remote-computer"></a>Se connecter sur un ordinateur distant

De nombreuses applications vous permettent de vous authentifier en saisissant un code sur un autre appareil tel qu'un PC. Si vous souhaitez vous connecter sur un ordinateur distant pour installer l'application Microsoft Authenticator :

1. Ouvrez l'application Microsoft Authenticator, sélectionnez le bouton **+** &gt; **Ajouter un compte professionnel ou scolaire** &gt; **Se connecter**.
1. Sélectionnez **Connexion à partir d’un autre appareil**.
1. Sur l'écran distant, ouvrez la page [**Connectez-vous à votre compte** ](https://microsoft.com/devicelogin) et entrez le code fourni dans votre application Authenticator.
1. Sur votre écran distant, connectez-vous à l'aide des informations d'identification de votre compte professionnel ou scolaire. Le cas échéant, vous pouvez vous connecter à l’aide d’un mot de passe d'accès temporaire (TAP).
1. Une fois authentifié sur l'écran distant, revenez à l'application Authenticator pour finaliser l'installation.

 ## <a name="next-steps"></a>Étapes suivantes

- Après avoir ajouté vos comptes à l’application, vous pouvez vous connecter à l’aide de l’application Authenticator sur votre appareil. Pour plus d’informations, consultez l’article [Se connecter à l’aide de l’application Microsoft Authenticator](user-help-auth-app-sign-in.md).

- Pour les appareils exécutant iOS, vous pouvez également sauvegarder les informations d’identification de votre compte et les paramètres de l’application associée, comme l’ordre de vos comptes, dans le cloud. Pour plus d’informations, consultez l’article [Sauvegarder et récupérer des informations d’authentification de compte avec l’application Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
