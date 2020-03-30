---
title: Gérer les utilisateurs et les appareils avec Azure MFA - Azure Active Directory
description: Comment les administrateurs peuvent modifier les paramètres utilisateur tels que le fait de forcer les utilisateurs à réexécuter le processus de vérification.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07845bb5b742b1bcfbb22d260457e9a8e16edab6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79230925"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gestion des paramètres utilisateur avec Azure Multi-Factor Authentication dans le cloud

En tant qu’administrateur, vous pouvez gérer les paramètres des utilisateurs et des appareils :

* Demander aux utilisateurs de fournir à nouveau des méthodes de contact
* Supprimer des mots de passe d’application
* Exiger l’authentification MFA sur tous les appareils de confiance

## <a name="manage-authentication-methods"></a>Gérer les méthodes d’authentification

En tant qu’administrateur doté du rôle d’administrateur d’authentification, vous pouvez exiger des utilisateurs qu’ils réinitialisent leur mot de passe, se réinscrivent à MFA ou révoquent les sessions MFA existantes de leur objet utilisateur.

![Gérer les méthodes d’authentification à partir du portail Azure](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sur la gauche, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
1. Choisissez l’utilisateur sur lequel vous souhaitez effectuer une action et sélectionnez **Méthodes d’authentification**.
   - **Réinitialiser le mot de passe** permet de réinitialiser le mot de passe de l’utilisateur et d’attribuer un mot de passe temporaire qui doit être modifié lors de la prochaine connexion.
   - **Exiger le réenregistrement de la MFA** fera en sorte que, lors de la prochaine connexion de l’utilisateur, il sera invité à configurer une nouvelle méthode d’authentification MFA.
   - **Révoquer les sessions MFA** efface les sessions d’authentification multifacteur mémorisées de l’utilisateur et lui demande d’effectuer l’authentification multifacteur la prochaine fois qu’elle est requise par la stratégie sur l’appareil.

## <a name="delete-users-existing-app-passwords"></a>Supprimer les mots de passe d’application existants des utilisateurs

Ce paramètre supprime tous les mots de passe d’application créés par un utilisateur. Les applications sans navigateur qui ont été associées à ces mots de passe d’application cessent de fonctionner jusqu’à la création d’un nouveau mot de passe d’application. Des autorisations d’administrateur général sont nécessaires pour effectuer cette action.

### <a name="how-to-delete-users-existing-app-passwords"></a>Supprimer les mots de passe d’application existants des utilisateurs

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sur la gauche, sélectionnez **Azure Active Directory** > **Utilisateurs** > **Tous les utilisateurs**.
3. Sur la droite, sélectionnez **Authentification multifacteur** dans la barre d’outils. La page de l’authentification multifacteur s’affiche.
4. Cochez la case en regard du ou des utilisateurs que vous souhaitez gérer. Une liste d’options rapides s’affiche à droite.
5. Sélectionnez **Gérer les paramètres utilisateur**.
6. Cochez la case **Supprimer tous les mots de passe d’application existants, générés par les utilisateurs sélectionnés**.
   ![Supprimer tous les mots de passe d’application existants](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Cliquez sur **save**.
8. Cliquez sur **Fermer**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, consultez [Configurer les paramètres d’Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
- Si vos utilisateurs ont besoin d’aide, dirigez-les vers le [guide de l’utilisateur pour la vérification en deux étapes](../user-help/multi-factor-authentication-end-user.md)
