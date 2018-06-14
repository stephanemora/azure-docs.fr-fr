---
title: Limitations d’Azure Active Directory B2B Collaboration | Microsoft Docs
description: Limitations actuelles d’Azure Active Directory B2B Collaboration
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 562076e9529ffeac4cb0f99c1ffd4d4866d0bd1a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34266995"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitations d’Azure AD B2B Collaboration
Azure Active Directory (Azure AD) B2B Collaboration subit actuellement les limitations décrites dans le présent article.

## <a name="possible-double-multi-factor-authentication"></a>Risque de redondance de l’authentification multifacteur
Avec Azure AD B2B, vous pouvez appliquer l’authentification multifacteur au niveau de l’organisation de la ressource (l’organisation à l’origine de l’invitation). Les raisons de cette approche sont détaillées dans l’article [Conditional access for B2B collaboration users](conditional-access.md) (Accès conditionnel pour les utilisateurs de B2B Collaboration). Si un partenaire a déjà configuré et appliqué l’authentification multifacteur, ses utilisateurs devront peut-être effectuer l’authentification une fois dans leur organisation d’origine, puis de nouveau dans les vôtres.

## <a name="instant-on"></a>Activation instantanée
Dans les flux B2B Collaboration, nous ajoutons des utilisateurs au répertoire et les mettons à jour de manière dynamique pendant l’échange d’invitation, l’affectation d’application, etc. Les mises à jour et les écritures se produisent d’ordinaire dans une instance de répertoire et doivent être répliquées entre toutes les instances. La réplication est terminée une fois toutes les instances mises à jour. Parfois, lorsque l’objet est écrit ou mis à jour dans une instance et quand l’appel pour récupérer cet objet se fait vers une autre instance, des latences de réplication peuvent se produire. Si cela se produit, actualisez ou recommencez. Si vous écrivez une application à l’aide de notre API, effectuer de nouvelles tentatives avec des temporisations peut être une pratique judicieuse et préventive pour atténuer ce problème.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur Azure AD B2B Collaboration :

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
- [Déléguer des invitations B2B Collaboration](delegate-invitations.md)

