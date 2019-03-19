---
title: Déléguer des invitations pour la collaboration B2B - Azure Active Directory | Microsoft Docs
description: Les propriétés de l’utilisateur Azure Active Directory B2B Collaboration sont configurables
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18d40397f30b471699f42878a38c88efebcc6305
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674508"
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Déléguer des invitations pour Azure Active Directory B2B Collaboration

Avec Azure Active Directory (Azure AD) B2B Collaboration, il n’est pas nécessaire d’être un administrateur global pour envoyer des invitations. Vous pouvez utiliser des stratégies et déléguer des invitations aux utilisateurs dont les rôles les autorisent à envoyer des invitations. Le rôle Inviteur d’invités est une nouvelle façon importante de déléguer les invitations d’utilisateurs invités.

## <a name="guest-inviter-role"></a>Rôle Inviteur d’invités
Il est possible d’affecter l’utilisateur au rôle Inviteur d’invités pour envoyer des invitations. Vous n’êtes pas tenu d’être membre du rôle Administrateur général pour envoyer des invitations. Par défaut, les utilisateurs standard peuvent également appeler l’API d’invitation, sauf si un administrateur général a désactivé les invitations à leur intention. Un utilisateur peut également appeler l’API à l’aide du portail Azure ou de PowerShell.

Voici un exemple qui montre comment utiliser PowerShell pour ajouter un utilisateur au rôle Inviteur d’invités :

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>Contrôler qui peut inviter

Dans Azure Active Directory, sélectionnez **Paramètres utilisateur**. Sous **Utilisateurs externes**, sélectionnez **Gérer les paramètres de collaboration externe**.

> [!NOTE]
> Les **Paramètres de collaboration externe** sont également disponibles dans la page **Relations organisationnelles**. Dans Azure Active Directory, sous **Gérer**, accédez à **Relations organisationnelles** > **Paramètres**.

![Paramètres de collaboration externe](./media/delegate-invitations/control-who-to-invite.png)

Avec Azure AD B2B Collaboration, un administrateur de clients peut définir les stratégies d’invitation suivantes :

- Désactiver les invitations
- Seuls les administrateurs et les utilisateurs membres du rôle Inviteur d’invités peuvent envoyer des invitations
- Les administrateurs, le rôle Inviteur d’invités et les membres peuvent envoyer des invitations
- Tous les utilisateurs, notamment les invités, peuvent inviter

Par défaut, les clients sont définis sur 4. Tous les utilisateurs, notamment les invités, peuvent inviter des utilisateurs B2B.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur Azure AD B2B Collaboration :

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
- [Ajouter des utilisateurs invités B2B Collaboration sans invitation](add-user-without-invite.md)
- [Ajout d’un utilisateur B2B Collaboration à un rôle](add-guest-to-role.md)


