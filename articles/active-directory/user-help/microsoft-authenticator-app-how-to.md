---
title: Prise en main de l’application Microsoft Authenticator - Azure Active Directory | Microsoft Docs
description: Découvrez comment effectuer une mise à niveau vers la dernière version de Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: 590f9e2a9cf531a1124f9cb132791f2956227d9c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47046047"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Prise en main de l’application Microsoft Authenticator

L’application Microsoft Authenticator empêche tout accès non autorisé aux comptes et arrête les transactions frauduleuses en vous apportant un niveau de sécurité supplémentaire pour votre compte professionnel ou scolaire (par exemple, alain@contoso.com) ou votre compte Microsoft personnel (par exemple, alain@outlook.com).

Lors de l’utilisation de l’application pour la vérification en deux étapes, elle peut fonctionner de l’une des deux façons suivantes :

- **Notification.** L’application envoie une notification à votre appareil. Assurez-vous que la notification est juste, puis sélectionnez **Vérifier**. Si vous ne reconnaissez pas la notification, sélectionnez **Refuser**.

- **Code de vérification.** Après avoir tapé votre nom d’utilisateur et votre mot de passe, vous pouvez ouvrir l’application et copiez le code de vérification fourni dans l’écran **Comptes** sur l’écran de connexion. Le code de vérification agit comme un deuxième formulaire d’authentification.

## <a name="opt-in-for-two-step-verification"></a>Abonnement à la vérification en deux étapes

Votre organisation décide si vous devez utiliser la vérification en deux étapes avec votre compte professionnel ou scolaire. Votre administrateur vous indiquera quelles méthodes de vérification doivent être configurées et utilisées. Pour plus d’informations, consultez [Présentation concrète de Multi-Factor Authentication Azure](multi-factor-authentication-end-user.md).

Vous pouvez utiliser la vérification en deux étapes pour votre compte Microsoft personnel. Pour plus d’informations détaillées et des instructions, consultez [À propos de la vérification en deux étapes](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Vous pouvez également utiliser l’application Microsoft Authenticator avec des comptes non Microsoft. Ces comptes peuvent ne pas appeler la fonctionnalité « vérification en deux étapes », mais vous devez pouvoir la trouver dans les paramètres de sécurité ou de connexion. Pour plus d’informations sur la configuration de ces comptes non Microsoft, consultez les [vidéos de support technique Microsoft](https://www.youtube.com/playlist?list=PLyhj1WZ29G65QdD9NxTOAm8HwOS-OBUrX).

## <a name="install-the-app"></a>Installer l’application

L’application Microsoft Authenticator est disponible pour [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) et [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071). Pour une expérience optimale, vous devez laisser l’application recevoir des notifications lorsque vous y êtes invité. 

## <a name="add-accounts-to-the-app"></a>Ajouter des comptes à l’application

Vous pouvez ajouter des comptes professionnels ou scolaires ou des comptes personnels à l’application Microsoft Authenticator. 

### <a name="add-a-personal-microsoft-account"></a>Ajouter un compte Microsoft personnel

Pour un compte Microsoft personnel (celui qui vous permet de vous connecter à Outlook.com, Xbox, Skype, etc.), il vous suffit de vous connecter à votre compte dans l’application Microsoft Authenticator.

### <a name="add-a-work-or-school-account"></a>Ajouter un compte professionnel ou scolaire

1. Si possible, accédez à l’écran [Vérification de sécurité supplémentaire](http://aka.ms/mfasetup) sur un autre ordinateur ou appareil. Pour savoir comment accéder à cet écran, consultez [Modification des paramètres de sécurité](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) ou contactez votre administrateur.

    >[!Note]
    >Si votre administrateur a activé les informations de sécurité en préversion, vous pouvez suivre les instructions dans la section [Set up security info to use an authenticator app](security-info-setup-auth-app.md) (Configurer les informations de sécurité pour utiliser une application d’authentification).

2. Cochez la case **Application d’authentification**, puis sélectionnez **Configurer**.

    ![Le bouton Configurer sur l’écran des paramètres de vérification de la sécurité.](./media/microsoft-authenticator-app-how-to/auth-app-configure.png)

    L’écran **Configurer l’application mobile** s’affiche avec un code QR que vous pouvez scanner avec l’application d’authentification.

    ![Écran qui fournit le code QR](./media/microsoft-authenticator-app-how-to/auth-app-barcode.png)

3. Ouvrez l’application Microsoft Authenticator. Sur l’écran **Comptes**, sélectionnez **Ajouter un compte**, puis **Compte professionnel ou scolaire**.

4. Utilisez l’appareil photo de votre appareil pour scanner le code QR, puis sélectionnez **Terminé** pour fermer l’écran de code QR.

    >[!Note]
    >Si votre appareil photo ne fonctionne pas correctement, vous pouvez [entrer manuellement le code QR et l’URL](#add-an-account-to-the-app-manually).

    L’écran **Comptes** de l’application affiche votre nom de compte et un code de vérification à six chiffres. Pour renforcer la sécurité, le code de vérification change toutes les 30 secondes, ce qui vous empêche d’utiliser deux fois le même code.  

    ![Écran de comptes](./media/microsoft-authenticator-app-how-to/auth-app-accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Ajouter manuellement un compte à l’application

1. Accédez à l’écran **Vérification de sécurité supplémentaire**. Pour savoir comment accéder à cet écran, consultez [Problèmes avec Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).

2. Cochez la case **Application d’authentification**, puis sélectionnez **Configurer**.

    L’écran **Configurer l’application mobile** s’affiche.

3. Copiez le code et les informations d’URL depuis l’écran **Configurer l’application mobile** afin de les taper manuellement dans le scanner QR.

4. Ouvrez l’application Microsoft Authenticator. Sur l’écran **Comptes**, sélectionnez **Ajouter un compte**, puis **Compte professionnel ou scolaire**.

5. Dans l’écran du scanner QR, sélectionnez **Entrer le code manuellement**.

    ![Écran pour scanner un code QR](./media/microsoft-authenticator-app-how-to/auth-app-manual-code.png)
   
6. Tapez le code et l’URL depuis l’écran avec le code QR dans l’écran **Ajouter un compte**, puis sélectionnez **Terminer**.

    ![Écran pour saisir le code et de l’URL](./media/microsoft-authenticator-app-how-to/auth-app-code-url.png)

    L’écran **Comptes** de l’application affiche votre nom de compte et un code de vérification à six chiffres. Pour renforcer la sécurité, le code de vérification change toutes les 30 secondes, ce qui vous empêche d’utiliser deux fois le même code.

### <a name="using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Utiliser les fonctionnalités d’identification par empreinte digitale ou reconnaissance faciale de votre appareil

Votre organisation peut nécessiter un code PIN pour effectuer la vérification d’identité. Au lieu de demander un code PIN, vous pouvez configurer l’application Microsoft Authenticator pour qu’elle utilise les fonctionnalités d’identification par empreinte digitale ou reconnaissance faciale de votre appareil. Vous pouvez configurer cela la première fois que vous utilisez l’application d’authentification pour vérifier votre compte, en sélectionnant l’option pour utiliser les fonctionnalités biométriques de votre appareil comme moyen d’identification au lieu de votre code PIN.

## <a name="use-the-app-when-you-sign-in"></a>Utilisation de l’application lors de la connexion

Après avoir ajouté vos comptes à l’application, vous pouvez utiliser l’application pour vous connecter à vos comptes.

Si vous avez choisi d’utiliser des codes de vérification dans l’application, ils s’afficheront sur la page **Comptes**. Le code change toutes les 30 secondes, de sorte que vous ayez toujours un nouveau code lorsque vous en avez besoin. Mais vous n’avez rien à faire tant que vous n’êtes pas connecté et invité à saisir un code de vérification.

## <a name="next-steps"></a>Étapes suivantes

- Si vous avez des questions plus générales concernant l’application, consultez le [Forum aux questions sur l’application Microsoft Authenticator](microsoft-authenticator-app-faq.md)

- Pour plus d’informations sur la vérification en deux étapes, consultez [Configurer mon compte pour la vérification en deux étapes](multi-factor-authentication-end-user-first-time.md).

- Pour plus d’informations sur les informations de sécurité, consultez [Gérer vos informations de sécurité](security-info-manage-settings.md).
