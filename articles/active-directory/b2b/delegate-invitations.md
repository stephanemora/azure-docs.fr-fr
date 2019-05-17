---
title: Activer les paramètres de collaboration externe B2B - Azure Active Directory | Microsoft Docs
description: Découvrez comment bénéficier d’une collaboration Active Directory B2B externe et gérer qui peut inviter des utilisateurs invités. Utilisez le rôle Inviteur d’invités pour déléguer des invitations.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11dda7fc3760f468c094fb4cf4484a27895f83b9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812668"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Permettre une collaboration B2B externe et gérer qui peut inviter des invités

Cet article décrit comment activer Azure Active Directory (Azure AD) B2B collaboration et de déterminer qui peut inviter des invités. Par défaut, tous les utilisateurs et invités dans votre annuaire peuvent inviter des invités même s’ils ne sont pas attribués à un rôle d’administrateur. Paramètres de collaboration externes vous permettent d’activer les invitations d’invités ou désactiver pour différents types d’utilisateurs dans votre organisation. Vous pouvez également déléguer des invitations aux utilisateurs individuels en attribuant des rôles qui leur permettent d’inviter des invités.

## <a name="configure-b2b-external-collaboration-settings"></a>Configurer les paramètres de collaboration externe B2B

Avec Azure AD B2B Collaboration, un administrateur de clients peut définir les stratégies d’invitation suivantes :

- Désactiver les invitations
- Seuls les administrateurs et les utilisateurs membres du rôle Inviteur d’invités peuvent envoyer des invitations
- Les administrateurs, le rôle Inviteur d’invités et les membres peuvent envoyer des invitations
- Tous les utilisateurs, notamment les invités, peuvent inviter

Par défaut, tous les utilisateurs, y compris les invités, peuvent inviter des utilisateurs invités.

### <a name="to-configure-external-collaboration-settings"></a>Pour configurer les paramètres de collaboration externe :

1. Se connecter à la [portail](https://portal.azure.com) en tant qu’un administrateur de locataire.
2. Sélectionnez **Azure Active Directory** > **Utilisateurs** > **Paramètres d’utilisateur**.
3. Sous **Utilisateurs externes**, sélectionnez **Gérer les paramètres de collaboration externe**.
   > [!NOTE]
   > Les **Paramètres de collaboration externe** sont également disponibles dans la page **Relations organisationnelles**. Dans Azure Active Directory, sous **Gérer**, accédez à **Relations organisationnelles** > **Paramètres**.
4. Sur le **les paramètres de collaboration externe** page, choisissez les stratégies que vous souhaitez activer.

   ![Paramètres de collaboration externes](./media/delegate-invitations/control-who-to-invite.png)

  - **Autorisations des utilisateurs invités sont limitées**: Cette stratégie détermine les autorisations pour les invités dans votre répertoire. Sélectionnez **Oui** aux invités de bloc à partir de certaines tâches d’annuaire, telles que l’énumération des utilisateurs, groupes ou autres ressources de répertoire. Sélectionnez **non** afin de donner des invités du même accès aux données d’annuaire en tant qu’utilisateurs standards dans votre répertoire.
   - **Les administrateurs et utilisateurs dans le rôle inviteur d’invités peuvent inviter**: Pour autoriser les utilisateurs et les administrateurs dans le rôle « Inviteur » pour inviter des invités, définissez cette stratégie sur **Oui**.
   - **Les membres peuvent inviter**: Pour autoriser des membres non-administrateurs de votre annuaire à inviter des invités, définissez cette stratégie sur **Oui**.
   - **Invités peuvent inviter**: Pour autoriser les invités d’inviter d’autres invités, définissez cette stratégie sur **Oui**.
   - **Activer le code secret à usage unique de messagerie pour les invités (version préliminaire)**: Pour plus d’informations sur la fonctionnalité de code secret à usage unique, consultez [l’authentification d’E-mail du code secret à usage unique (version préliminaire)](one-time-passcode.md).
   - **Restrictions de collaboration**: Pour plus d’informations sur Autoriser ou de bloquer des invitations à des domaines spécifiques, consultez [autoriser ou bloquer des invitations aux utilisateurs de B2B à partir d’organisations spécifiques](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Affecter le rôle Inviteur d’invités à un utilisateur

Avec le rôle Inviteur d’invités, vous pouvez donner à des utilisateurs la possibilité d’inviter des invités sans leur attribuer un administrateur général ou l’autre rôle d’administrateur. Affecter le rôle inviteur d’invités à des individus. Assurez-vous de définir **administrateurs et utilisateurs dans le rôle inviteur d’invités peuvent inviter** à **Oui**.

Voici un exemple qui montre comment utiliser PowerShell pour ajouter un utilisateur au rôle Inviteur d’invités :

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur Azure AD B2B Collaboration :

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
- [Ajouter des utilisateurs invités B2B Collaboration sans invitation](add-user-without-invite.md)
- [Ajout d’un utilisateur B2B Collaboration à un rôle](add-guest-to-role.md)


