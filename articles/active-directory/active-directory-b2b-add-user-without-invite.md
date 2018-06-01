---
title: Ajouter des utilisateurs B2B Collaboration à Azure Active Directory sans invitation | Microsoft Docs
description: Vous pouvez permettre à un utilisateur invité d’ajouter d’autres utilisateurs invités à votre Azure AD sans échanger d’invitation dans Azure Active Directory B2B Collaboration.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 5a37a5a14dcb07db7e078558072f7edad7432d9b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075621"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Ajouter des utilisateurs invités B2B Collaboration sans invitation

> [!NOTE]
> Désormais, les utilisateurs invités n’ont plus besoin de l’e-mail d’invitation, sauf dans certains cas spéciaux. Pour plus d’informations, consultez [Utilisation d'invitations B2B Collaboration](active-directory-b2b-redemption-experience.md).  

Vous pouvez autoriser un utilisateur, tel qu’un représentant de partenaire, à ajouter des utilisateurs du partenaire vers votre organisation sans avoir à échanger des invitations. Il vous suffit d’accorder à cet utilisateur des privilèges d’énumération dans le répertoire que vous utilisez pour l’organisation partenaire. 

Accordez ces privilèges lorsque :

1. Un utilisateur de l’organisation hôte (par exemple, WoodGrove) invite un utilisateur d’une organisation partenaire (par exemple, Sam@litware.com) en tant qu’invité.
2. L’administrateur de l’organisation hôte [définit les stratégies](active-directory-b2b-delegate-invitations.md) qui permettent à Sam d’identifier et d’ajouter d’autres utilisateurs de l’organisation partenaire (Litware).
3. Maintenant, Sam peut ajouter d’autres utilisateurs Litware au répertoire, à des groupes et à des applications WoodGrove sans avoir besoin d’utiliser des invitations. Si Sam possède les privilèges d’énumération appropriés de Litware, cela se produit automatiquement.

### <a name="next-steps"></a>Étapes suivantes

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
- [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
- [Déléguer des invitations pour Azure Active Directory B2B Collaboration](active-directory-b2b-delegate-invitations.md)

