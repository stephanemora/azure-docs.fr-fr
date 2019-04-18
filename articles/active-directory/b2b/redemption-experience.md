---
title: Utilisation d’invitations dans Azure Active Directory B2B Collaboration | Microsoft Docs
description: Cet article décrit l’utilisation des invitations dans Azure AD B2B Collaboration pour les utilisateurs finaux, dont l’acceptation des conditions de confidentialité.
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
ms.openlocfilehash: 5e5a8d46f67553cc10bd9cdb31cf64511858948f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046493"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Utilisation d'invitations Azure Active Directory B2B Collaboration

Pour collaborer avec des utilisateurs d’organisations partenaires via Azure Active Directory (Azure AD) B2B Collaboration, vous pouvez inviter des utilisateurs à accéder à des applications partagées. Une fois ajouté à l’annuaire via l’interface utilisateur ou invité via PowerShell, l’utilisateur doit passer par un processus de consentement initial pour accepter les [conditions de confidentialité](#privacy-policy-agreement). Ce processus se déroule de l’une des manières suivantes :

- L’inviteur envoie un lien direct vers une application partagée. L’invité clique sur le lien pour se connecter, accepte les conditions de confidentialité et accède sans encombre à la ressource partagée. (L’utilisateur invité reçoit toujours un e-mail d’invitation avec une URL d’échange mais, sauf dans des cas particuliers, il n’est plus tenu d’utiliser l’e-mail d’invitation.)  
- L’utilisateur invité reçoit un e-mail d’invitation et clique sur l’URL d’échange. Lors de la première connexion, il est invité à accepter les conditions de confidentialité.

## <a name="redemption-through-a-direct-link"></a>Échange via un lien direct

Un inviteur peut inviter un utilisateur en lui envoyant un [lien direct vers une application partagée](../manage-apps/end-user-experiences.md#direct-sign-on-links). Pour l’utilisateur invité, l’expérience d’échange est aussi simple que de se connecter à l’application partagée avec lui. Ils peut cliquer sur un lien vers l’application, consulter et accepter les conditions de confidentialité, puis accéder sans encombre à l’application. Dans la plupart des cas, l’utilisateur invité n’a plus besoin de cliquer sur une URL d’échange dans un e-mail d’invitation.

Que vous ayez invité un utilisateur via l’interface utilisateur ou choisi d’envoyer un e-mail d’invitation dans le cadre de l’expérience d’invitation de PowerShell, l’utilisateur invité reçoit toujours un e-mail d’invitation. Cet e-mail est utile dans les cas particuliers suivants :

- L’utilisateur n’a pas de compte Azure AD ou Microsoft (MSA). Dans ce cas, l’utilisateur doit créer un MSA avant de cliquer sur le lien, ou utiliser l’URL d’échange figurant dans l’e-mail d’invitation. Le processus d’invitation propose automatiquement à l’utilisateur de créer un MSA.
- Parfois, l’objet utilisateur invité n’a pas d’adresse e-mail en raison d’un conflit avec un objet contact (par exemple, un objet contact Outlook). Dans ce cas, l’utilisateur doit cliquer sur l’URL d’échange dans l’e-mail d’invitation.
- L’utilisateur peut se connecter avec un alias de l’adresse e-mail via laquelle il a été invité (un alias est une adresse e-mail supplémentaire associée à un compte de courrier). Dans ce cas, l’utilisateur doit cliquer sur l’URL d’acceptation de l’e-mail d’invitation.

Si ces cas particuliers sont importants pour votre organisation, nous vous recommandons d’inviter des utilisateurs à l’aide de méthodes qui envoient toujours l’e-mail d’invitation. Par ailleurs, si un utilisateur ne tombe pas dans l’un de ces cas particuliers, il peut toujours cliquer sur l’URL dans un e-mail d’invitation pour obtenir l’accès.

## <a name="redemption-through-the-invitation-email"></a>Acceptation via l’e-mail d’invitation

Si un utilisateur est invité via une méthode consistant à envoyer un e-mail d’invitation, il peut également accepter une invitation via cet e-mail. Un utilisateur invité peut cliquer sur l’URL d’acceptation dans l’e-mail, puis consulter et accepter les conditions de confidentialité. Le processus est décrit plus en détail ici :

1.  L’invité reçoit une invitation par e-mail envoyée dans **Invitations Microsoft**.
2.  L’invité sélectionne **Prise en main** dans l’e-mail.
3.  Si l’invité n’a pas de compte Azure AD ou de MSA, il est invité à créer un MSA.
4.  L’invité est redirigé vers l’écran **Révision des autorisations**, dans lequel il peut consulter la déclaration de confidentialité de l’organisation invitante et en accepter les termes.

## <a name="privacy-policy-agreement"></a>Contrat de politique de confidentialité

Après s’être connecté pour accéder aux ressources d’une organisation partenaire pour la première fois, l’utilisateur invité voit un écran **Révision des autorisations**. Il peut y consulter la déclaration de confidentialité de l’organisation invitante. Pour continuer, l’utilisateur doit accepter l’utilisation de ses informations conformément à la politique de confidentialité de l’organisation invitante.

![Capture d’écran des paramètres utilisateur dans le Panneau d’accès](media/redemption-experience/ConsentScreen.png) 

Pour plus d’informations sur la manière dont vous pouvez, en tant qu’administrateur de locataire, créer un lien vers la déclaration de confidentialité de votre organisation, consultez le [Guide pratique pour ajouter les informations de confidentialité de votre organisation dans Azure Active Directory](https://aka.ms/adprivacystatement).

## <a name="terms-of-use"></a>Conditions d’utilisation

Vous pouvez présenter les conditions d’utilisation à l’utilisateur invité pendant le processus d’acceptation initial à l’aide de la fonctionnalité Conditions d’utilisation Azure AD. Dans Azure Active Directory, accédez à cette fonctionnalité sous **Gérer** > **Relations organisationnelles** > **Conditions d’utilisation** ou sous **Sécurité** > **Accès conditionnel** > **Conditions d’utilisation**. Pour plus d’informations, consultez [Fonctionnalité Conditions d’utilisation Azure AD](../conditional-access/terms-of-use.md).

![Capture d’écran montrant les nouvelles conditions d’utilisation](media/redemption-experience/organizational-relationships-terms-of-use.png) 

## <a name="next-steps"></a>Étapes suivantes

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
- [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le Portail Azure](add-users-administrator.md)
- [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration à Azure Active Directory ?](add-users-information-worker.md)
- [Ajouter des utilisateurs Azure Active Directory B2B Collaboration à l’aide de PowerShell](customize-invitation-api.md#powershell)
- [Quitter une organisation en tant qu’utilisateur invité](leave-the-organization.md)
