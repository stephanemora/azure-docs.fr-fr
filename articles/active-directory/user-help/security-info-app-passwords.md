---
title: Définir les mots de passe d’application à l’aide des informations de sécurité - Azure Active Directory | Microsoft Docs
description: Définissez des mots de passe générés automatiquement (mots de passe d’application) à utiliser avec chaque application sans navigateur, distincts d’un mot de passe normal, à l’aide des informations de sécurité.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 211e282dc4334753b90d050dc82c8bf35ad145cd
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54826205"
---
# <a name="manage-app-passwords-using-security-info-preview"></a>Gérer les mots de passe d’application à l’aide des informations de sécurité (préversion)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Certaines applications sans navigateur, telles qu’Outlook 2010, ne prennent pas en charge la vérification en deux étapes. Cela signifie que si vous utilisez la vérification en deux étapes, l’application ne fonctionnera pas. Pour contourner ce problème, vous pouvez créer un mot de passe généré automatiquement, distinct de votre mot de passe normal, que vous devrez utiliser avec chaque application sans navigateur.

Lorsque vous utilisez des mots de passe d’application, gardez à l’esprit les points suivants :

- Les mots de passe d’application sont générés automatiquement et ne sont entrés qu’une seule fois par application.

- Un utilisateur peut posséder jusqu’à 40 mots de passe. Si vous essayez de créer un mot de passe supplémentaire au-delà de cette limite, vous serez invité à supprimer un mot de passe existant avant d’être autorisé à en créer un autre.

- Utilisez un mot de passe d’application par appareil, et non par application. Par exemple, créez un mot de passe unique pour toutes les applications de votre ordinateur portable, et un autre mot de passe unique pour toutes les applications de votre Bureau.

    >[!Note]
    >Les clients Office 2013 (y compris Outlook) prennent en charge de nouveaux protocoles d’authentification et peuvent être utilisés dans le cadre de la vérification en deux étapes. Cela signifie qu’après l’activation de la vérification en deux étapes, vous n’aurez plus besoin de mots de passe d’application pour les clients Office 2013. Pour plus d’informations, consultez l’article [Fonctionnement de l’authentification moderne pour les applications clientes Office 2013 et Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517).

## <a name="create-and-delete-app-passwords-using-security-info"></a>Créer et supprimer des mots de passe d’application à l’aide des informations de sécurité

Si vous utilisez la vérification en deux étapes avec votre compte professionnel ou scolaire et que votre administrateur a activé la fonctionnalité d’informations de sécurité, vous pouvez créer et supprimer des mots de passe d’application à l’aide du Portail My Apps.

Si votre administrateur n’a pas activé la fonctionnalité d’informations de sécurité, vous devez suivre les instructions et les informations contenues dans la section [Gérer les mots de passe pour la vérification en deux étapes](multi-factor-authentication-end-user-app-passwords.md).

### <a name="to-create-app-passwords-using-the-my-apps-portal"></a>Pour créer des mots de passe d’application à l’aide du portail MyApps

1. Connectez-vous à votre compte professionnel ou scolaire.

2. Accédez à myapps.microsoft.com, sélectionnez votre nom en haut à droite de la page, puis sélectionnez **Profil**.

3. Dans la zone **Gérer le compte**, sélectionnez **Modifier les informations de sécurité**.

    ![Écran du profil, avec le lien Modifier les informations de sécurité mis en évidence](media/security-info/security-info-profile.png)

4. Dans l’écran **Sécuriser votre compte**, sélectionnez **Ajouter des informations de sécurité**.

    ![Écran des informations de sécurité, avec les informations existantes modifiables](media/security-info/security-info-edit-add-info.png)

5. Dans l’écran **Ajouter des informations de sécurité**, sélectionnez **Mot de passe de l’application**.

6. Dans l’écran **Créer votre mot de passe d’application**, saisissez un nom pour votre mot de passe d’application, puis sélectionnez **Suivant**.

    ![Écran où vous nommez votre mot de passe d’application](media/security-info/security-info-name-app-password.png)

7. Sélectionnez **Copier** pour copier le mot de passe dans le Presse-papiers, puis sélectionnez **Suivant**.

    ![Écran avec mot de passe d’application à copier](media/security-info/security-info-create-app-password.png)
    
8. Vérifiez que le mot de passe d’application apparaît sur l’écran **Sécuriser votre compte**.

    ![Écran sécurisé, avec mot de passe de l’application](media/security-info/security-info-keep-secure-app-password.png)

### <a name="to-delete-app-passwords-using-the-my-apps-portal"></a>Pour supprimer des mots de passe d’application à l’aide du portail My Apps

1. Sur l’écran **Sécuriser votre compte**, sélectionnez la **X** en regard du mot de passe d’application à supprimer.

    ![Écran sécurisé, supprimer le mot de passe de l’application](media/security-info/security-info-keep-secure-delete-app-password.png)

2. Dans l’écran **Supprimer le mot de passe de l’application**, sélectionnez **Supprimer**.

    ![Écran Supprimer le mot de passe d’application](media/security-info/security-info-keep-secure-delete-app-password2.png)

## <a name="next-steps"></a>Étapes suivantes

- Si vous devez mettre à jour vos informations de sécurité, suivez les instructions dans l’article [Manage your security info](security-info-manage-settings.md) (Gérer vos informations de sécurité).

- Pour obtenir des informations plus générales sur les informations de sécurité et ce que vous pouvez faire, consultez [Vue d’ensemble des informations de sécurité ](user-help-security-info-overview.md) 