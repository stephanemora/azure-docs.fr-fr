---
title: Les administrateurs de gérer les utilisateurs et périphériques - Azure MFA - Azure Active Directory
description: Comment les administrateurs peuvent modifier les paramètres utilisateur tels qu’obliger les utilisateurs à refaire le processus de vérification.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d4848a00fd645bcf23342f27fe820ccf034a8b
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298837"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gestion des paramètres utilisateur avec Azure Multi-Factor Authentication dans le cloud

En tant qu’administrateur, vous pouvez gérer les paramètres des utilisateurs et des appareils :

* Demander aux utilisateurs de fournir à nouveau des méthodes de contact
* Supprimer des mots de passe d’application
* Exiger l’authentification MFA sur tous les appareils de confiance

## <a name="require-users-to-provide-contact-methods-again"></a>Demander aux utilisateurs de fournir à nouveau des méthodes de contact

Ce paramètre force l’utilisateur à réexécuter le processus d’inscription. Les applications sans navigateur continuent de fonctionner si l’utilisateur dispose pour elles de mots de passe d’application.  Vous pouvez supprimer les mots de passe d’application des utilisateurs en choisissant **Supprimer tous les mots d'application existants, générés par les utilisateurs sélectionnés**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
3. Sur la droite, sélectionnez **Authentification multifacteur** dans la barre d’outils. La page de l’authentification multifacteur s’affiche.
4. Cochez la case en regard du ou des utilisateurs que vous souhaitez gérer. Une liste d’options rapides s’affiche sur la droite.
5. Sélectionnez **Gérer les paramètres utilisateur**.
6. Cochez la case **Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact**.
   ![Demander aux utilisateurs de fournir à nouveau des méthodes de contact](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Cliquez sur **save**.
8. Cliquez sur **Fermer**.

Les organisations peuvent réaliser ces étapes avec PowerShell à l’aide de ce qui suit comme guide pour effacer le `StrongAuthenticationMethods` attribut :

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
4. Cochez la case en regard du ou des utilisateurs que vous souhaitez gérer. Une liste d’options rapides s’affiche sur la droite.
5. Sélectionnez **Gérer les paramètres utilisateur**.
6. Cochez la case **Supprimer tous les mots de passe d’application existants, générés par les utilisateurs sélectionnés**.
   ![Supprimer tous les mots de passe d’application existant](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Cliquez sur **save**.
8. Cliquez sur **Fermer**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restaurer MFA sur tous les appareils mémorisés d’un utilisateur

L’une des fonctionnalités configurables d’Azure Multi-Factor Authentication permet à vos utilisateurs de marquer les appareils comme approuvés. Pour plus d’informations, consultez [Configurer les paramètres d’Azure Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

Les utilisateurs peuvent refuser la vérification en deux étapes pendant un nombre configurable de jours sur les appareils qu’ils utilisent régulièrement. Si un compte est compromis ou si un appareil de confiance est perdu, vous devez être en mesure de supprimer l’état approuvé et d’exiger à nouveau la vérification en deux étapes.

Lorsqu’elle est activée, **restaurer de l’authentification multifacteur sur tous les appareils mémorisés** les utilisateurs sont nécessaires pour effectuer la vérification en deux étapes la prochaine fois qu’ils se connectent, même s’ils marqués son appareil comme approuvé.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Restaurer MFA sur tous les appareils interrompus d’un utilisateur

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
3. Sur la droite, sélectionnez **Authentification multifacteur** dans la barre d’outils. La page de l’authentification multifacteur s’affiche.
4. Cochez la case en regard du ou des utilisateurs que vous souhaitez gérer. Une liste d’options rapides s’affiche sur la droite.
5. Sélectionnez **Gérer les paramètres utilisateur**.
6. Cochez la case **restaurer de l’authentification multifacteur sur tous les appareils mémorisés**
   ![restaurer de l’authentification multifacteur sur tous les appareils mémorisés](./media/howto-mfa-userdevicesettings/rememberdevices.png)
7. Cliquez sur **save**.
8. Cliquez sur **Fermer**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, consultez [Configurer les paramètres d’Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
- Si vos utilisateurs ont besoin d’aide, dirigez-les vers le [guide de l’utilisateur pour la vérification en deux étapes](../user-help/multi-factor-authentication-end-user.md)
