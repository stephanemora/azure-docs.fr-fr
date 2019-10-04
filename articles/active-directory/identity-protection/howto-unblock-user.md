---
title: Comment débloquer des utilisateurs avec Azure Active Directory Identity Protection | Microsoft Docs
description: Découvrez comment débloquer les utilisateurs bloqués par une stratégie Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e3756435703c4e8c887a4e7b9d4f75a6701d840
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126246"
---
# <a name="how-to-unblock-users"></a>Procédure : Débloquer des utilisateurs

Avec Azure Active Directory Identity Protection, vous pouvez configurer des stratégies pour bloquer les utilisateurs si les conditions configurées sont remplies. En règle générale, un utilisateur bloqué contacte le support technique pour pouvoir être débloqué. Cet article explique les étapes à suivre pour débloquer un utilisateur bloqué.

## <a name="determine-the-reason-for-blocking"></a>Déterminer la raison du blocage

Dans un premier temps, pour débloquer un utilisateur, vous devez déterminer le type de stratégie qui l’a bloqué, car les étapes suivantes en dépendent.
Avec Azure Active Directory Identity Protection, un utilisateur peut être bloqué par une stratégie en matière de risque à la connexion ou par une stratégie de risque d’utilisateur.

Vous pouvez déterminer le type de stratégie qui a bloqué l’utilisateur à partir de l’en-tête dans la boîte de dialogue présentée à l’utilisateur lors d’une tentative de connexion :

| Stratégie | Boîte de dialogue utilisateur |
| --- | --- |
| Risque à la connexion |![Connexion bloquée](./media/howto-unblock-user/02.png) |
| Risque de l’utilisateur |![Compte bloqué](./media/howto-unblock-user/104.png) |

Un utilisateur qui est bloqué par :

* Une stratégie en matière de risque à la connexion, également appelée connexion suspecte
* Une stratégie de risque d’utilisateur, également appelé compte à risque

## <a name="unblocking-suspicious-sign-ins"></a>Déblocage des connexions suspectes

Pour débloquer une connexion suspecte, vous disposez des options suivantes :

1. **Connexion à partir d’un emplacement ou d’un appareil connu** : les connexions suspectes bloquées sont généralement des tentatives de connexion effectuées à partir d’un emplacement ou d’un appareil inconnu. Vos utilisateurs peuvent déterminer rapidement s’il s’agit bien de la raison du blocage en essayant de se connecter depuis un appareil ou un emplacement connu.
2. **Exclure de la stratégie** : si vous pensez que la configuration actuelle de votre stratégie d’authentification est à l’origine de problèmes pour certains utilisateurs, vous pouvez les exclure de cette dernière. Pour plus d’informations, consultez [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
3. **Désactiver la stratégie** : si vous pensez que votre configuration de la stratégie est à l’origine des problèmes pour tous vos utilisateurs, vous pouvez désactiver la stratégie. Pour plus d’informations, consultez [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>Déblocage des comptes à risque

Pour débloquer un compte à risque, vous disposez des options suivantes :

1. **Réinitialiser le mot de passe réinitialisé** : vous pouvez réinitialiser le mot de passe de l’utilisateur. 
2. **Ignorer toutes les détections de risques** : la stratégie de risque de l’utilisateur bloque un utilisateur si le niveau de risque d’un utilisateur configuré a été atteint. Vous pouvez réduire le niveau de risque d’un utilisateur en fermant manuellement les détections de risques signalées. 
3. **Exclure de la stratégie** : si vous pensez que la configuration actuelle de votre stratégie d’authentification est à l’origine de problèmes pour certains utilisateurs, vous pouvez les exclure de cette dernière. Pour plus d’informations, consultez [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
4. **Désactiver la stratégie** : si vous pensez que votre configuration de la stratégie est à l’origine des problèmes pour tous vos utilisateurs, vous pouvez désactiver la stratégie. Pour plus d’informations, consultez [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="next-steps"></a>Étapes suivantes
 
Vous souhaitez en savoir plus sur Azure AD Identity Protection ? Consultez la rubrique [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
