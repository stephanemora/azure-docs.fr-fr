---
title: Gérer les paramètres utilisateur pour Azure Multi-Factor Authentication - Azure Active Directory
description: Découvrir comment configurer les paramètres utilisateur Azure Active Directory pour Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9861e8e8be39781ad1b256b9939df3ab03e74be6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027527"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Gérer les paramètres utilisateur pour Azure Multi-Factor Authentication

Pour faciliter la gestion des utilisateurs d’Azure Multi-Factor Authentication, vous pouvez imposer à ces derniers de réinitialiser leur mot de passe, de se réinscrire auprès de MFA ou de révoquer les sessions MFA existantes. Pour les utilisateurs qui ont défini des mots de passe d’application, vous pouvez également choisir de supprimer ces mots de passe, ce qui va entraîner l’échec de l’authentification héritée dans ces applications. Ces actions peuvent être nécessaires si vous devez fournir une assistance à un utilisateur, ou si vous souhaitez réinitialiser son état de sécurité.

## <a name="manage-user-authentication-options"></a>Gérer les options d’authentification des utilisateurs

Si le rôle *Administrateur d’authentification* vous est attribué, vous pouvez imposer aux utilisateurs de réinitialiser leur mot de passe, de se réinscrire auprès de MFA ou de révoquer les sessions MFA existantes de leur objet utilisateur. Pour gérer les paramètres utilisateur, effectuez les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sur la gauche, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
1. Choisissez l’utilisateur sur lequel vous souhaitez effectuer une action et sélectionnez **Méthodes d’authentification**. En haut de la fenêtre, choisissez l’une des options suivantes pour l’utilisateur :
   - **Réinitialiser le mot de passe** permet de réinitialiser le mot de passe de l’utilisateur et d’affecter un mot de passe temporaire qui doit être changé à la prochaine connexion.
   - **Exiger le réenregistrement de la MFA** permet d’imposer à l’utilisateur, à sa prochaine connexion, de configurer une nouvelle méthode d’authentification MFA.
   
      > [!NOTE]
      > Les méthodes d’authentification actuellement inscrites de l’utilisateur ne sont pas supprimées lorsqu’un administrateur requiert une nouvelle inscription pour MFA. Une fois qu’un utilisateur a effectué une nouvelle inscription pour MFA, nous lui recommandons de consulter ses informations de sécurité et de supprimer toutes les méthodes d’authentification précédemment inscrites qui ne sont plus utilisables.
   
   - **Révoquer les sessions MFA** permet d’effacer les sessions MFA mémorisées de l’utilisateur et d’obliger ce dernier à s’authentifier via MFA la prochaine fois que cette méthode est imposée par la stratégie de l’appareil.

   ![Gérer les méthodes d’authentification à partir du portail Azure](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Supprimer les mots de passe d’application existants des utilisateurs

Si nécessaire, vous pouvez supprimer tous les mots de passe d’application créés par un utilisateur. Les applications sans navigateur qui ont été associées à ces mots de passe d’application cessent de fonctionner jusqu’à la création d’un nouveau mot de passe d’application. Des autorisations d’*Administrateur général* sont nécessaires pour effectuer cette action.

Pour supprimer les mots de passe d’application d’un utilisateur, effectuez les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sur la gauche, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
1. Sélectionnez **Multi-Factor Authentication**. Il se peut que vous deviez faire défiler vers la droite pour afficher cette option de menu. Sélectionnez l’exemple de capture d’écran ci-dessous pour afficher entièrement la fenêtre du portail Azure et l’emplacement du menu : [![](media/howto-mfa-userstates/selectmfa-cropped.png "Sélectionnez Authentification multifacteur dans la fenêtre Utilisateurs d’Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Cochez la case en regard du ou des utilisateurs que vous souhaitez gérer. Une liste d’options rapides s’affiche à droite.
1. Sélectionnez **Gérer les paramètres utilisateur**, puis cochez la case **Supprimer tous les mots de passe d’application existants qui ont été générés par les utilisateurs sélectionnés**, comme illustré dans l’exemple suivant : ![Supprimer tous les mots de passe d’application existants](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Sélectionnez **Enregistrer**, puis **Fermer**.

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a montré comment configurer des paramètres utilisateur individuels. Pour configurer l’ensemble des paramètres du service Azure Multi-Factor Authentication, consultez [Configurer les paramètres d’Azure Multi-Factor Authentication](howto-mfa-mfasettings.md).

Si vos utilisateurs ont besoin d’aide, consultez le [Guide de l’utilisateur pour Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user-first-time.md).
