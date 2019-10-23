---
title: Gestion des utilisateurs et des appareils par les administrateurs - Azure MFA - Azure Active Directory
description: Comment les administrateurs peuvent modifier les paramètres utilisateur tels que le fait de forcer les utilisateurs à réexécuter le processus de vérification.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 190d697dca56fa51d92987f32db0146aa79881aa
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70162398"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gestion des paramètres utilisateur avec Azure Multi-Factor Authentication dans le cloud

En tant qu’administrateur, vous pouvez gérer les paramètres des utilisateurs et des appareils :

* Demander aux utilisateurs de fournir à nouveau des méthodes de contact
* Supprimer des mots de passe d’application
* Exiger l’authentification MFA sur tous les appareils de confiance

## <a name="manage-authentication-methods"></a>Gérer les méthodes d’authentification

En tant qu’administrateur doté du rôle d’administrateur d’authentification, vous pouvez exiger des utilisateurs qu’ils réinitialisent leur mot de passe, se réinscrivent à MFA ou révoquent les sessions MFA existantes de leur objet utilisateur.

![Gérer les méthodes d’authentification à partir du portail Azure](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Sur la gauche, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
1. Choisissez l’utilisateur sur lequel vous souhaitez effectuer une action et sélectionnez **Méthodes d’authentification**.
   - **Réinitialiser le mot de passe** permet de réinitialiser le mot de passe de l’utilisateur et d’attribuer un mot de passe temporaire qui doit être modifié lors de la prochaine connexion.
   - **Exiger le réenregistrement de la MFA** fera en sorte que, lors de la prochaine connexion de l’utilisateur, il sera invité à configurer une nouvelle méthode d’authentification MFA.
   - **Révoquer les sessions MFA** efface les sessions d’authentification multifacteur mémorisées de l’utilisateur et lui demande d’effectuer l’authentification multifacteur la prochaine fois qu’elle est requise par la stratégie sur l’appareil.

## <a name="require-users-to-provide-contact-methods-again"></a>Demander aux utilisateurs de fournir à nouveau des méthodes de contact

Ce paramètre force l’utilisateur à réexécuter le processus d’inscription. Les applications sans navigateur continuent de fonctionner si l’utilisateur dispose pour elles de mots de passe d’application.  Vous pouvez supprimer les mots de passe d’application des utilisateurs en choisissant **Supprimer tous les mots d'application existants, générés par les utilisateurs sélectionnés**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
3. Sur la droite, sélectionnez **Authentification multifacteur** dans la barre d’outils. La page de l’authentification multifacteur s’affiche.
4. Cochez la case en regard du ou des utilisateurs que vous souhaitez gérer. Une liste d’options rapides s’affiche à droite.
5. Sélectionnez **Gérer les paramètres utilisateur**.
6. Cochez la case **Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact**.
   ![Demander aux utilisateurs de fournir à nouveau des méthodes de contact](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Cliquez sur **save**.
8. Cliquez sur **Fermer**.

Les organisations peuvent mener à bien cette procédure avec PowerShell en suivant nos instructions pour effacer l’attribut `StrongAuthenticationMethods` :

```PowerShell
$Upn = "theuser@domain.com"
$noMfaConfig = @()
Set-MsolUser -UserPrincipalName $Upn -StrongAuthenticationMethods $noMfaConfig
```

## <a name="delete-users-existing-app-passwords"></a>Supprimer les mots de passe d’application existants des utilisateurs

Ce paramètre supprime tous les mots de passe d’application créés par un utilisateur. Les applications sans navigateur qui ont été associées à ces mots de passe d’application cessent de fonctionner jusqu’à la création d’un nouveau mot de passe d’application.

### <a name="how-to-delete-users-existing-app-passwords"></a>Supprimer les mots de passe d’application existants des utilisateurs

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
3. Sur la droite, sélectionnez **Authentification multifacteur** dans la barre d’outils. La page de l’authentification multifacteur s’affiche.
4. Cochez la case en regard du ou des utilisateurs que vous souhaitez gérer. Une liste d’options rapides s’affiche à droite.
5. Sélectionnez **Gérer les paramètres utilisateur**.
6. Cochez la case **Supprimer tous les mots de passe d’application existants, générés par les utilisateurs sélectionnés**.
   ![Supprimer tous les mots de passe d’application existants](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Cliquez sur **save**.
8. Cliquez sur **Fermer**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restaurer MFA sur tous les appareils mémorisés d’un utilisateur

L’une des fonctionnalités configurables d’Azure Multi-Factor Authentication permet à vos utilisateurs de marquer les appareils comme approuvés. Pour plus d’informations, consultez [Configurer les paramètres d’Azure Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

Les utilisateurs peuvent refuser la vérification en deux étapes pendant un nombre configurable de jours sur les appareils qu’ils utilisent régulièrement. Si un compte est compromis ou si un appareil de confiance est perdu, vous devez être en mesure de supprimer l’état approuvé et d’exiger à nouveau la vérification en deux étapes.

Lorsque l’option **Restaurer l’authentification multifacteur pour tous les appareils mémorisés** est activée, les utilisateurs doivent effectuer une vérification en deux étapes lors de leur prochaine connexion, même s’ils ont marqué leur appareil comme fiable.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Restaurer MFA sur tous les appareils interrompus d’un utilisateur

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
3. Sur la droite, sélectionnez **Authentification multifacteur** dans la barre d’outils. La page de l’authentification multifacteur s’affiche.
4. Cochez la case en regard du ou des utilisateurs que vous souhaitez gérer. Une liste d’options rapides s’affiche à droite.
5. Sélectionnez **Gérer les paramètres utilisateur**.
6. Cochez la case **Restaurer l’authentification multifacteur pour tous les appareils mémorisés**
   ![Restaurer l’authentification multifacteur pour tous les appareils mémorisés](./media/howto-mfa-userdevicesettings/rememberdevices.png)
7. Cliquez sur **save**.
8. Cliquez sur **Fermer**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, consultez [Configurer les paramètres d’Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
- Si vos utilisateurs ont besoin d’aide, dirigez-les vers le [guide de l’utilisateur pour la vérification en deux étapes](../user-help/multi-factor-authentication-end-user.md)
