---
title: Activer les paramètres de collaboration B2B externe - Azure AD
description: Découvrez comment activer la collaboration B2B externe dans Active Directory et gérer les utilisateurs autorisés à en inviter d’autres. Utilisez le rôle Inviteur d’invités pour déléguer des invitations.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa2b5f17af466d5ea2b6e3ba942fafc24cc94713
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204257"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Permettre une collaboration B2B externe et gérer les utilisateurs autorisés à en inviter d’autres

Cet article explique comment activer la collaboration B2B externe dans Azure Active Directory (Azure AD) et déterminer qui peut inviter des invités. Par défaut, tous les utilisateurs et invités de votre annuaire peuvent inviter des invités, même s’ils ne sont pas associés à un rôle d’administrateur. Les paramètres de collaboration externe vous permettent d’activer ou de désactiver les invitations d’invités pour différents types d’utilisateurs dans votre organisation. Vous pouvez également déléguer des invitations aux utilisateurs individuels, en leur attribuant des rôles qui leur permettent d’inviter des invités.

## <a name="configure-b2b-external-collaboration-settings"></a>Configurer les paramètres de collaboration B2B externe

Avec Azure AD B2B Collaboration, un administrateur de clients peut définir les stratégies d’invitation suivantes :

- Désactiver les invitations
- Seuls les administrateurs et les utilisateurs membres du rôle Inviteur d’invités peuvent envoyer des invitations
- Les administrateurs, le rôle Inviteur d’invités et les membres peuvent envoyer des invitations
- Tous les utilisateurs, notamment les invités, peuvent inviter

Par défaut, tous les utilisateurs, notamment les invités, peuvent inviter des invités.

### <a name="to-configure-external-collaboration-settings"></a>Pour configurer les paramètres de la collaboration externe, procédez comme suit :

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com) en tant qu’administrateur de locataires.
2. Sélectionnez **Azure Active Directory**.
3. Sélectionnez **Identités externes** > **Paramètres de collaboration externe**.
6. Sur la page **Paramètres de collaboration externe**, choisissez les stratégies que vous souhaitez activer.

   ![Paramètres de collaboration externe](./media/delegate-invitations/control-who-to-invite.png)

  - **Les autorisations de l’utilisateur invité sont limitées** : cette stratégie détermine les autorisations associées aux invités dans votre répertoire. Sélectionnez **Oui** pour empêcher les invités d’effectuer certaines tâches d’annuaire, telles que l’énumération d’utilisateurs, de groupes ou d’autres ressources de répertoire. Sélectionnez **Non** afin d’accorder aux invités le même accès aux données d’annuaire que les utilisateurs standard de votre répertoire.
   - **Les administrateurs et utilisateurs ayant le rôle d’Inviteur d’invités peuvent inviter** : Pour autoriser les administrateurs et utilisateurs ayant le rôle « Inviteur d’invités » à inviter des invités, définissez cette stratégie sur **Oui**.
   - **Les membres peuvent inviter** : pour autoriser des membres autres que des administrateurs de votre annuaire à inviter des invités, définissez cette stratégie sur **Oui**.
   - **Les invités peuvent inviter** : pour autoriser les invités à en inviter d’autres, définissez cette stratégie sur **Oui**.
   - **Activer le mot de passe e-mail à usage unique pour les invités (préversion)** : pour en savoir plus sur la fonctionnalité de code secret à usage unique, voir [Authentification par code secret à usage unique d’e-mail (préversion)](one-time-passcode.md).
   - **Restrictions de collaboration** : pour en savoir plus sur la procédure visant à interdire ou autoriser des invitations à des domaines spécifiques, voir [Autoriser ou bloquer des invitations à des organisations spécifiques](allow-deny-list.md).
   
   > [!NOTE]
   > Si **Les membres peuvent inviter** est défini sur **Non** et **Les administrateurs et utilisateurs ayant le rôle d’Inviteur d’invités peuvent inviter** est défini sur **Oui**, les utilisateurs du rôle **Inviteur d’invité** pourront toujours inviter des utilisateurs.

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Affecter le rôle Inviteur d’invités à un utilisateur

Avec le rôle Inviteur d’invités, vous pouvez donner à des utilisateurs la possibilité d’inviter des invités sans leur attribuer un rôle d’administrateur d’entreprise, ou tout autre rôle d’administration. Affectez le rôle Inviteur d’invités à des utilisateurs. Ensuite, vérifiez que le paramètre **Les administrateurs et utilisateurs ayant le rôle d’inviteur invité peuvent inviter** a la valeur **Oui**.

Voici un exemple qui montre comment utiliser PowerShell pour ajouter un utilisateur au rôle Inviteur d’invités :

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur Azure AD B2B Collaboration :

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
- [Ajouter des utilisateurs invités B2B Collaboration sans invitation](add-user-without-invite.md)
- [Ajout d’un utilisateur B2B Collaboration à un rôle](add-guest-to-role.md)

