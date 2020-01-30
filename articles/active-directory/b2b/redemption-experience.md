---
title: Utilisation d’une invitation dans B2B Collaboration – Azure AD
description: Cet article décrit l’utilisation des invitations dans Azure AD B2B Collaboration pour les utilisateurs finaux, dont l’acceptation des conditions de confidentialité.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0a71da025b8b2bb571dc9b00e23bc691ecdd44c
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758278"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Utilisation d'invitations Azure Active Directory B2B Collaboration

Cet article décrit les différentes façons dont les utilisateurs invités peuvent accéder à vos ressources, et le processus de consentement qu’ils vont rencontrer. Si vous envoyez un e-mail d’invitation à l’invité, l’invitation comprend un lien que l’invité peut utiliser pour obtenir l’accès à votre application ou portail. L’e-mail d’invitation n’est qu’une des possibilités permettant aux invités d’obtenir l’accès à vos ressources. Comme alternative, vous pouvez ajouter des invités à votre annuaire et leur donner un lien direct vers le portail ou l’application que vous souhaitez partager. Quelle que soit la méthode qu’ils utilisent, les invités sont guidés dans un processus de consentement initial. Ce processus garantit que vos invités sont d’accord avec les conditions de confidentialité, et qu’ils acceptent les [conditions d’utilisation](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) que vous avez définies.

Lorsque vous ajoutez un utilisateur invité à votre annuaire, le compte d’utilisateur invité présente un état de consentement (affichable dans PowerShell) qui est initialement défini sur **PendingAcceptance**. Ce paramètre est maintenu jusqu’à ce que l’invité accepte votre invitation et approuve votre politique de confidentialité ainsi que vos conditions d’utilisation. Après cela, l’état de consentement passe à **Accepté**, et les pages de consentement ne sont plus présentées à l’invité.

## <a name="redemption-through-the-invitation-email"></a>Acceptation via l’e-mail d’invitation

Lorsque vous ajoutez un utilisateur invité à votre annuaire en [utilisant le portail Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), un e-mail d’invitation est envoyé à l’invité dans le processus. Vous pouvez également choisir d’envoyer des e-mails d’invitation lorsque vous [utilisez PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) pour ajouter des utilisateurs invités à votre annuaire. Voici une description de l’expérience de l’invité lorsqu’il accepte le lien dans l’e-mail.

1. L’invité reçoit un [e-mail d’invitation](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) qui est envoyé depuis **Invitations Microsoft**.
2. L’invité sélectionne **Démarrer** dans l’e-mail.
3. Si l’invité n’a pas de compte Azure AD, ni de compte Microsoft (MSA) ou de compte e-mail dans une organisation fédérée, il est invité à créer un compte de service administré (MSA), sauf si la fonctionnalité de [code secret à usage unique](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) est activée, ce qui ne nécessite pas de MSA.
4. L’invité est guidé tout au long de l’[expérience de consentement](#consent-experience-for-the-guest) décrite ci-dessous.

## <a name="redemption-through-a-direct-link"></a>Échange via un lien direct

Comme alternative à l’e-mail d’invitation, vous pouvez donner à un invité un lien direct à votre application ou à votre portail. Vous devez d’abord ajouter l’utilisateur invité à votre annuaire par le biais du [portail Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) ou de [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Ensuite, vous pouvez utiliser un des [moyens personnalisables de votre choix pour déployer des applications vers les utilisateurs](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), notamment les liens d’authentification directe. Lorsqu’un invité utilise un lien direct au lieu de l’e-mail d’invitation, il est toujours guidé dans l’expérience de consentement initial.

> [!IMPORTANT]
> Le lien direct doit être spécifique au locataire. Autrement dit, il doit inclure un ID de locataire ou un domaine vérifié, afin que l’invité puisse être authentifié dans votre locataire, là où se trouve l’application partagée. Une URL ordinaire, telle que https://myapps.microsoft.com ne fonctionne pas pour un invité, car elle redirige vers son locataire de base pour l’authentification. Voici quelques exemples de liens directs avec contexte de locataire :
 > - Panneau d’accès des applications : https://myapps.microsoft.com/?tenantid=&lt;id de locataire&gt; 
 > - Panneau d’accès des applications pour un domaine vérifié : https://myapps.microsoft.com/&lt; domaine vérifié&gt;
 > - Portail Azure : https://portal.azure.com/&lt; id de locataire&gt;
 > - Application individuelle : voir comment utiliser un [lien d’authentification directe](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Il existe certains cas où l’e-mail d’invitation est recommandé par rapport à un lien direct. Si ces cas particuliers sont importants pour votre organisation, nous vous conseillons d’inviter les utilisateurs par l’intermédiaire de méthodes qui envoient toujours l’e-mail d’invitation :
 - L’utilisateur n’a pas de compte Azure AD, de MSA ni de compte d’e-mail dans une organisation fédérée. À moins d’utiliser la fonctionnalité de code secret à usage unique, l’invité doit accepter l’e-mail d’invitation pour être guidé à travers les étapes de la création d’un MSA.
 - Parfois, l’objet utilisateur invité n’a pas d’adresse e-mail en raison d’un conflit avec un objet contact (par exemple, un objet contact Outlook). Dans ce cas, l’utilisateur doit cliquer sur l’URL d’acceptation de l’e-mail d’invitation.
 - L’utilisateur peut se connecter avec un alias de l’adresse e-mail via laquelle il a été invité (un alias est une adresse e-mail supplémentaire associée à un compte de courrier). Dans ce cas, l’utilisateur doit cliquer sur l’URL d’acceptation de l’e-mail d’invitation.

## <a name="consent-experience-for-the-guest"></a>Expérience de consentement de l’invité

Lorsqu’un invité se connecte la première fois pour accéder aux ressources d’une organisation partenaire, il est guidé dans les pages suivantes. 

1. L’invité passe en revue la page **Révision des autorisations** décrivant la déclaration de confidentialité de l’organisation qui invite. Pour continuer, l’utilisateur doit **Accepter** l’utilisation de ses informations conformément à la politique de confidentialité de l’organisation invitante.

   ![Capture d’écran montrant la page Passer en revue les autorisations](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Pour plus d’informations sur la manière dont vous pouvez, en tant qu’administrateur de locataire, créer un lien vers la déclaration de confidentialité de votre organisation, consultez le [Guide pratique pour ajouter les informations de confidentialité de votre organisation dans Azure Active Directory](https://aka.ms/adprivacystatement).

2. Si les conditions d’utilisation sont configurées, l’invité ouvre et passe en revue les conditions d’utilisation, puis il sélectionne **Accepter**. 

   ![Capture d’écran montrant les nouvelles conditions d’utilisation](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > Vous pouvez configurer les [conditions d’utilisation](../governance/active-directory-tou.md) dans **Gérer** > **Relations organisationnelles** > **Conditions d’utilisation**.

3. Sauf indication contraire, l’invité est redirigé vers le panneau d’accès des applications qui liste les applications auxquelles l’invité peut accéder.

   ![Capture d’écran montrant le panneau d’accès des applications](media/redemption-experience/myapps.png) 

Dans votre annuaire, la valeur de **Invitation acceptée** de l’invité passe à **Oui**. Si un MSA a été créé, la **Source** de l’invité affiche **Compte Microsoft**. Pour plus d’informations sur les propriétés du compte d’utilisateur invité, consultez [Propriétés d’un utilisateur B2B Collaboration Azure AD](user-properties.md). 

## <a name="next-steps"></a>Étapes suivantes

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
- [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le Portail Azure](add-users-administrator.md)
- [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration à Azure Active Directory ?](add-users-information-worker.md)
- [Ajouter des utilisateurs Azure Active Directory B2B Collaboration à l’aide de PowerShell](customize-invitation-api.md#powershell)
- [Quitter une organisation en tant qu’utilisateur invité](leave-the-organization.md)
