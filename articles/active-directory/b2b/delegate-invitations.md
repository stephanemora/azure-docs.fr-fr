---
title: Déléguer des invitations pour Azure Active Directory B2B Collaboration | Microsoft Docs
description: Les propriétés de l’utilisateur Azure Active Directory B2B Collaboration sont configurables
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: cd0ee37643e92ba37a9b14408b85a3437fb40d38
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269307"
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

![externalusers](https://user-images.githubusercontent.com/13383753/45905128-2c47f680-bda4-11e8-955d-6219c67935e0.PNG)

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


