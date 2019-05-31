---
title: Limitations de la collaboration B2B - Azure Active Directory | Microsoft Docs
description: Limitations actuelles d’Azure Active Directory B2B Collaboration
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1466614dfeb8fa4d3d095bae070d01c6503c5bb
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357238"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitations d’Azure AD B2B Collaboration
Azure Active Directory (Azure AD) B2B Collaboration subit actuellement les limitations décrites dans le présent article.

## <a name="possible-double-multi-factor-authentication"></a>Risque de redondance de l’authentification multifacteur
Avec Azure AD B2B, vous pouvez appliquer l’authentification multifacteur au niveau de l’organisation de la ressource (l’organisation à l’origine de l’invitation). Les raisons de cette approche sont détaillées dans l’article [Conditional access for B2B collaboration users](conditional-access.md) (Accès conditionnel pour les utilisateurs de B2B Collaboration). Si un partenaire a déjà configuré et appliqué l’authentification multifacteur, ses utilisateurs devront peut-être effectuer l’authentification une fois dans leur organisation d’origine, puis de nouveau dans les vôtres.

## <a name="instant-on"></a>Activation instantanée
Dans les flux B2B Collaboration, nous ajoutons des utilisateurs au répertoire et les mettons à jour de manière dynamique pendant l’échange d’invitation, l’affectation d’application, etc. Les mises à jour et les écritures se produisent d’ordinaire dans une instance de répertoire et doivent être répliquées entre toutes les instances. La réplication est terminée une fois toutes les instances mises à jour. Parfois, lorsque l’objet est écrit ou mis à jour dans une instance et quand l’appel pour récupérer cet objet se fait vers une autre instance, des latences de réplication peuvent se produire. Si cela se produit, actualisez ou recommencez. Si vous écrivez une application à l’aide de notre API, effectuer de nouvelles tentatives avec des temporisations peut être une pratique judicieuse et préventive pour atténuer ce problème.

## <a name="azure-ad-directories"></a>Répertoires Azure AD
Azure AD B2B est soumis aux limites de répertoire du service Azure AD. Pour plus d’informations sur le nombre de répertoires qu’un utilisateur peut créer et sur le nombre de répertoires auxquels un utilisateur ou un utilisateur invité peuvent appartenir, consultez l’article [Restrictions et limites de service Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="national-clouds"></a>Clouds nationaux
[Clouds nationaux](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) sont physiquement isolées les instances d’Azure. B2B collaboration n’est pas prise en charge au-delà des limites de cloud national. Par exemple, si votre client Azure se trouve dans le cloud public en général, vous ne pouvez pas inviter un utilisateur dont le compte est dans un cloud national. Pour collaborer avec l’utilisateur, demandez-lui pour une autre adresse e-mail ou créer un compte d’utilisateur membre pour eux dans votre répertoire.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur Azure AD B2B Collaboration :

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
- [Déléguer des invitations B2B Collaboration](delegate-invitations.md)

