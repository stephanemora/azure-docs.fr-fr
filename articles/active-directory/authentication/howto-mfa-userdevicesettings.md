---
title: Gestion des utilisateurs et des appareils par les administrateurs - Azure MFA | Microsoft Docs
description: Cette section vous explique comment modifier les paramètres utilisateur tels que le fait de forcer les utilisateurs à réexécuter le processus de vérification.
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
ms.openlocfilehash: 941d11e9768d60c106c576e3f93bc7ad2067b73c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174858"
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
4. Cochez la case en regard du ou des utilisateurs que vous souhaitez gérer. Une liste d’options rapides s’affiche à droite. 
5. Sélectionnez **Gérer les paramètres utilisateur**.
6. Cochez la case **Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact**.
   ![Fournir des méthodes de contact](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Cliquez sur **save**.
8. Cliquez sur **Fermer**.

## <a name="delete-users-existing-app-passwords"></a>Supprimer les mots de passe d’application existants des utilisateurs
Ce paramètre supprime tous les mots de passe d’application créés par un utilisateur. Les applications sans navigateur qui ont été associées à ces mots de passe d’application cessent de fonctionner jusqu’à la création d’un nouveau mot de passe d’application.

### <a name="how-to-delete-users-existing-app-passwords"></a>Supprimer les mots de passe d’application existants des utilisateurs
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
3. Sur la droite, sélectionnez **Authentification multifacteur** dans la barre d’outils. La page de l’authentification multifacteur s’affiche. 
6. Cochez la case en regard du ou des utilisateurs que vous souhaitez gérer. Une liste d’options rapides s’affiche à droite. 
7. Sélectionnez **Gérer les paramètres utilisateur**.
8. Cochez la case **Supprimer tous les mots de passe d’application existants, générés par les utilisateurs sélectionnés**.
   ![Supprimer des mots de passe d'application](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
9. Cliquez sur **save**.
10. Cliquez sur **Fermer**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restaurer MFA sur tous les appareils mémorisés d’un utilisateur
L’une des fonctionnalités configurables d’Azure Multi-Factor Authentication permet à vos utilisateurs de marquer les appareils comme approuvés. Pour plus d’informations, consultez [Configurer les paramètres d’Azure Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

Les utilisateurs peuvent refuser la vérification en deux étapes pendant un nombre configurable de jours sur les appareils qu’ils utilisent régulièrement. Si un compte est compromis ou si un appareil de confiance est perdu, vous devez être en mesure de supprimer l’état approuvé et d’exiger à nouveau la vérification en deux étapes.

Le paramètre **Restaurer l’authentification multifacteur pour tous les appareils mémorisés** signifie que l’utilisateur sera invité à procéder à la vérification en deux étapes lors de sa prochaine connexion, qu’il ait choisi ou non de marquer son appareil comme approuvé. 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Restaurer MFA sur tous les appareils interrompus d’un utilisateur
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
3. Sur la droite, sélectionnez **Authentification multifacteur** dans la barre d’outils. La page de l’authentification multifacteur s’affiche. 
6. Cochez la case en regard du ou des utilisateurs que vous souhaitez gérer. Une liste d’options rapides s’affiche à droite. 
7. Sélectionnez **Gérer les paramètres utilisateur**.
8. Cochez la case **Restaurer Multi-Factor Authentication sur tous les appareils mémorisés**
   ![Supprimer les mots de passe d’application](./media/howto-mfa-userdevicesettings/rememberdevices.png)
9. Cliquez sur **save**.
10. Cliquez sur **Fermer**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, consultez [Configurer les paramètres d’Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)

- Si vos utilisateurs ont besoin d’aide, dirigez-les vers le [guide de l’utilisateur pour la vérification en deux étapes](../user-help/multi-factor-authentication-end-user.md)
