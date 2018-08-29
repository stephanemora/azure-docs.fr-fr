---
title: Inscrire votre appareil personnel sur le réseau de votre organisation - Azure Active Directory | Microsoft Docs
description: Découvrez comment inscrire votre appareil personnel sur le réseau de votre organisation afin de pouvoir accéder à des ressources protégées de votre organisation.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 7126a47bd90168c7d86fe9fcc05fab0a60955063
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180584"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Inscrire votre appareil personnel sur le réseau de votre organisation
Inscrivez votre appareil personnel, généralement un téléphone ou une tablette, sur le réseau de votre organisation. Une fois votre appareil inscrit, il peut accéder aux ressources restreintes de votre organisation.

>[!Note]
>Cet article utilise un appareil Windows à des fins de démonstration, mais vous pouvez également inscrire des appareils exécutant iOS, Android ou macOS.

## <a name="what-happens-when-you-register-your-device"></a>Ce qui se passe quand vous inscrivez votre appareil
Quand vous inscrivez votre appareil sur le réseau de votre organisation, les actions suivantes se produisent :

- Windows inscrit votre appareil sur le réseau de votre organisation.

- Selon les choix de votre organisation, vous devrez peut-être configurer une vérification en deux étapes à travers [l’authentification multifacteur](multi-factor-authentication-end-user-first-time.md) ou des [informations de sécurité](user-help-security-info-overview.md).

- Selon les choix de votre organisation, vous pouvez être automatiquement inscrit dans une solution de gestion des appareils mobiles, comme Microsoft Intune. Pour plus d’informations sur l’inscription dans Microsoft Intune, consultez [Inscrire votre appareil sur Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Vous suivrez le processus de connexion en utilisant le nom d’utilisateur et le mot de passe de votre compte Microsoft personnel.

## <a name="to-register-your-windows-device"></a>Pour inscrire votre appareil Windows

Suivez ces étapes pour inscrire votre appareil personnel sur votre réseau.

1. Ouvrez **Paramètres**, puis sélectionnez **Comptes**.

    ![Comptes dans l’écran Paramètres](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Sélectionnez **E-mail et comptes**, puis sélectionnez **Ajouter un compte Microsoft**.

    ![Liens E-mail et comptes et Ajouter un compte Microsoft](./media/user-help-register-device-on-network/register-device-email-and-accounts.png)

3. Dans l’écran **Ajoutez votre compte Microsoft**, tapez l’adresse e-mail de votre compte Microsoft personnel.

    ![Écran Ajoutez votre compte Microsoft, avec adresse e-mail](./media/user-help-register-device-on-network/register-device-add-accounts.png)

4. Dans l’écran **Saisie du mot de passe**, tapez le mot de passe de votre compte Microsoft personnel, puis sélectionnez **Se connecter**.

    ![Écran Saisie du mot de passe](./media/user-help-register-device-on-network/register-device-enter-password.png)

5. Effectuez le reste du processus d’inscription, y compris l’approbation de votre demande de vérification d’identité (si vous utilisez la vérification en deux étapes) et la configuration de Windows Hello (si nécessaire).

## <a name="to-make-sure-youre-registered"></a>Pour vérifier que vous êtes inscrit
Vous pouvez vérifier que vous êtes inscrit en examinant vos paramètres.

1. Ouvrez **Paramètres**, puis sélectionnez **Comptes**.

    ![Comptes dans l’écran Paramètres](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Sélectionnez **E-mail et comptes** et vérifiez que vous voyez votre compte Microsoft personnel.

    ![Écran Accès Professionnel ou Scolaire avec le compte contoso connecté](./media/user-help-register-device-on-network/register-device-verify-account.png)

## <a name="next-steps"></a>Étapes suivantes
Après avoir inscrit votre appareil personnel sur le réseau de votre organisation, vous devriez pouvoir accéder à la plupart de vos ressources.

- Si votre organisation vous demande de joindre votre appareil professionnel, consultez [Joindre votre appareil professionnel au réseau de votre organisation](user-help-join-device-on-network.md).



