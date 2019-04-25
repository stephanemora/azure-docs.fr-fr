---
title: Comment gérer les mots de passe - Azure Active Directory | Microsoft Docs
description: En savoir plus sur les mots de passe d’application et ce qu’ils sont utilisés pour en ce qui concerne la vérification en deux étapes.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6790fa1cbb10999a751b31bcb27db2edcb67b4a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60334953"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Gérer les mots de passe d’application pour la vérification en deux étapes

Certaines applications sans navigateur, telles qu’Outlook 2010, ne prennent pas en charge la vérification en deux étapes. Cela signifie que si vous utilisez la vérification en deux étapes, l’application ne fonctionnera pas. Pour contourner ce problème, vous pouvez créer un mot de passe généré automatiquement, distinct de votre mot de passe normal, que vous devrez utiliser avec chaque application sans navigateur.

Lorsque vous utilisez des mots de passe d’application, gardez à l’esprit les points suivants :

- Les mots de passe d’application sont générés automatiquement et ne sont entrés qu’une seule fois par application.

- Un utilisateur peut posséder jusqu’à 40 mots de passe. Si vous essayez de créer un mot de passe supplémentaire au-delà de cette limite, vous serez invité à supprimer un mot de passe existant avant d’être autorisé à en créer un autre.

- Utilisez un mot de passe d’application par appareil, et non par application. Par exemple, créez un mot de passe unique pour toutes les applications de votre ordinateur portable, et un autre mot de passe unique pour toutes les applications de votre Bureau.

    >[!Note]
    >Les clients Office 2013 (y compris Outlook) prennent en charge de nouveaux protocoles d’authentification et peuvent être utilisés dans le cadre de la vérification en deux étapes. Cela signifie qu’après l’activation de la vérification en deux étapes, vous n’aurez plus besoin de mots de passe d’application pour les clients Office 2013. Pour plus d’informations, consultez l’article [Fonctionnement de l’authentification moderne pour les applications clientes Office 2013 et Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517).

## <a name="where-to-create-and-delete-your-app-passwords"></a>Emplacements de création et de suppression de vos mots de passe d’application

Vous recevez un mot de passe d’application lors de votre inscription initiale au processus de vérification en deux étapes. Si vous avez besoin de mots de passe supplémentaires, vous pouvez en créer d’autres en fonction de la manière dont vous utilisez la vérification en deux étapes :

- **Vous utilisez la vérification en deux étapes avec votre compte professionnel ou scolaire et le portail MyApps.** Créer et supprimer vos mots de passe en suivant les instructions dans la créer et supprimer des mots de passe à l’aide de la section du portail MyApps de cet article. Pour plus d’informations sur le portail MyApps et la manière de l’utiliser, consultez [Qu’est-ce que le portail MyApps dans Azure Active Directory ?](active-directory-saas-access-panel-introduction.md).

- **Vous utilisez la vérification en deux étapes avec votre compte professionnel ou scolaire et le portail Office 365.** Créez et supprimez vos mots de passe d’application en suivant les instructions de la section [Créer et supprimer des mots de passe d’application à l’aide du Portail Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) de cet article.

- **Vous utilisez la vérification en deux étapes avec votre compte Microsoft personnel.** Créez et supprimez vos mots de passe d’application à partir de la page [Notion de base sur la sécurité](https://account.microsoft.com/account/) avec votre compte Microsoft personnel. Pour plus d’informations, consultez l’article [Mots de passe d’application et vérification en deux étapes](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification).

## <a name="create-and-delete-app-passwords-using-the-myapps-portal"></a>Créer et supprimer des mots de passe d’application à l’aide du portail MyApps
Vous pouvez créer et supprimer des mots de passe d’application via le portail MyApps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Pour créer un mot de passe d’application à l’aide du portail MyApps

1. Connectez-vous à [https://myapps.microsoft.com](https://myapps.microsoft.com).

2. Sélectionnez votre nom en haut à droite, puis choisissez **Profil**.

3. Sélectionnez **Vérification de sécurité supplémentaire**.

   ![Capture d’écran : Vérification de sécurité supplémentaire](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Sélectionnez **Mots de passe d’application**.

   ![Capture d’écran : sélection de Mots de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Cliquez sur **Créer**.

6. Tapez un nom pour le mot de passe d’application, puis sélectionnez **Suivant**.

7. Copiez le mot de passe d’application dans le Presse-papiers et collez-le dans votre application.
   
    ![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Pour supprimer un mot de passe d’application à l’aide du portail MyApps

1. Accédez à votre profil, puis sélectionnez **Vérification de sécurité supplémentaire**.

2. Sélectionnez **Mots de passe d’application**, puis **Supprimer** en regard du mot de passe d’application à supprimer.

   ![Supprimer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

3. Sélectionnez **Oui** pour confirmer la suppression du mot de passe, puis sélectionnez **Fermer**.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Créer et supprimer des mots de passe d’application à l’aide du Portail Office 365

Si vous utilisez la vérification en deux étapes avec votre compte professionnel ou scolaire et vos applications Office 365, vous pouvez créer et supprimer des mots de passe d’application à l’aide du Portail Office 365. Vous pouvez posséder jusqu’à 40 mots de passe à tout moment. Si vous avez besoin d’un autre mot de passe d’application au-delà de cette limite, vous devrez supprimer l’un de vos mots de passe d’application existants.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Pour créer des mots de passe d’application à l’aide du Portail Office 365

1. Connectez-vous à votre compte professionnel ou scolaire.

2. Accédez à [ https://portal.office.com ](https://portal.office.com), sélectionnez le **paramètres** icône dans le coin supérieur droit de la **portail Office 365** page, puis développez **renforcer la sécurité vérification**.

    ![Portail Office sur lequel la zone Vérification de sécurité supplémentaire est développée](media/security-info/security-info-o365password.png)

3. Sélectionnez le texte **Create and manage app passwords** (Créer et gérer des mots de passe d’application) pour ouvrir la page **Mots de passe d’application**.

4. Sélectionnez **Créer**, tapez un nom convivial pour l’application nécessitant un mot de passe, puis sélectionnez **Suivant**.

5. Sélectionnez **Copy password to clipboard** (Copier le mot de passe dans le Presse-papiers), puis sélectionnez **Fermer**.

6. Utilisez le mot de passe d’application copié pour vous connecter à votre application sans navigateur. Vous n’avez besoin d’entrer ce mot de passe qu’une seule fois, car l’application le mémorise.

### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Pour supprimer des mots de passe d’application avec le portail Office 365

1. Connectez-vous à votre compte professionnel ou scolaire.

2. Accédez à [ https://portal.office.com ](https://portal.office.com), sélectionnez le **paramètres** icône dans le coin supérieur droit de la **portail Office 365** page, puis sélectionnez **renforcer la sécurité vérification**.

3. Sélectionnez le texte **Create and manage app passwords** (Créer et gérer des mots de passe d’application) pour ouvrir la page **Mots de passe d’application**.

4. Sélectionnez **Supprimer** en regard du mot de passe d’application à supprimer, sélectionnez **Oui** dans la zone de confirmation, puis sélectionnez **Fermer**.

    Le mot de passe d’application est supprimé.

5. Créez un autre mot de passe d’application en exécutant la procédure correspondante.

## <a name="if-your-app-passwords-arent-working-properly"></a>Si vos mots de passe d’application ne fonctionnent pas correctement

Vérifiez que vous avez correctement tapé le mot de passe. Si vous êtes sûr d’avoir entré le mot de passe correct, vous pouvez essayer de vous reconnecter et de créer un autre mot de passe d’application. Si aucune de ces options ne résout votre problème, contactez le support technique de votre entreprise pour demander la suppression de vos mots de passe d’application existants, ce qui vous permettra d’en créer de nouveaux. 

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les paramètres de la vérification en deux étapes](multi-factor-authentication-end-user-manage-settings.md)

- Essayez l’[application Microsoft Authenticator](user-help-auth-app-download-install.md) pour vérifier vos connexions grâce aux notifications d’application, plutôt que de recevoir des SMS ou des appels.
