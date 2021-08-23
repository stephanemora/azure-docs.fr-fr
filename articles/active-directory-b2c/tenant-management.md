---
title: Gérer votre locataire Azure Active Directory B2C
titleSuffix: Azure Active Directory B2C
description: Découvrez comment gérer votre locataire Azure Active Directory B2C. Découvrez les fonctionnalités Azure AD qui sont prises en charge dans Azure AD B2C, ainsi que l’utilisation des rôles administrateur pour gérer des ressources, et la procédure d’ajout de comptes professionnels et d’utilisateurs invités à votre locataire Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8aeb818104e1424d96374bf535d4c02a870f4705
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112453877"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Gérer votre locataire Azure Active Directory B2C

Dans Azure Active Directory B2C (Azure AD B2C), un locataire représente votre répertoire d’utilisateurs consommateurs. Chaque locataire Azure AD B2C est distinct et séparé des autres locataires Azure AD B2C. Un locataire Azure AD B2C n’est pas un locataire Azure Active Directory ; d’ailleurs vous disposez peut-être déjà de ce dernier. Dans cet article, vous allez apprendre à gérer votre locataire Azure AD B2C.

## <a name="supported-azure-ad-features"></a>Fonctionnalités Azure AD prises en charge

Azure AD B2C repose sur la plateforme Azure AD. Les fonctionnalités Azure AD suivantes peuvent être utilisées dans votre locataire Azure AD B2C.

|Fonctionnalité  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [Groupes](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | Les groupes peuvent permettre de gérer les comptes d’utilisateur et d’administration.| Les groupes peuvent permettre de gérer les comptes d’administration. Des [comptes consommateur](user-overview.md#consumer-user) ne peuvent pas être membres d’un groupe. |
| [Invitation d’invités External Identities](../active-directory//external-identities/add-users-administrator.md)| Vous pouvez inviter des utilisateurs invités et configurer des fonctionnalités External Identities, telles que la fédération et la connexion avec des comptes Facebook et Google. | Vous ne pouvez inviter qu’un compte Microsoft ou un utilisateur Azure AD en tant qu’invité à votre locataire Azure AD pour accéder à des applications ou gérer des locataires. Pour les [comptes consommateur](user-overview.md#consumer-user), vous utilisez des flux d’utilisateurs et des stratégies personnalisées Azure AD B2C pour gérer des utilisateurs et vous inscrire ou vous connecter avec des fournisseurs d’identités externes, tels que Google ou Facebook. |
| [Rôles et administrateurs](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| Entièrement pris en charge pour les comptes d’utilisateur et d’administration. | Les rôles ne sont pas pris en charge avec les [comptes consommateur](user-overview.md#consumer-user). Les comptes consommateur n’ont accès à aucune ressource Azure.|
| [Noms de domaine personnalisés](../active-directory/fundamentals/add-custom-domain.md) |  Vous pouvez utiliser des domaines personnalisés Azure AD pour les comptes d’administration uniquement. | Les [comptes consommateur](user-overview.md#consumer-user) peuvent se connecter à l’aide d’un nom d’utilisateur, d’un numéro de téléphone ou d’une adresse e-mail. Vous pouvez utiliser des [domaines personnalisés](custom-domain.md) dans vos URL de redirection.|
| [Accès conditionnel](../active-directory/conditional-access/overview.md) | Entièrement pris en charge pour les comptes d’utilisateur et d’administration. | Un sous-ensemble des fonctionnalités d’accès conditionnel Azure AD est pris en charge avec les [comptes consommateur](user-overview.md#consumer-user) Découvrez la procédure de configuration de l’[accès conditionnel](conditional-access-user-flow.md) Azure AD B2C.|
| [Premium P1](https://azure.microsoft.com/pricing/details/active-directory) | Entièrement pris en charge pour les fonctionnalités d’Azure AD Premium P1. Par exemple, [Protection par mot de passe](../active-directory/authentication/concept-password-ban-bad.md), [Identités hybrides](../active-directory/hybrid/whatis-hybrid-identity.md), [Accès conditionnel](../active-directory/roles/permissions-reference.md#), [Groupes dynamiques](../active-directory/enterprise-users/groups-create-rule.md), etc. | Un sous-ensemble de fonctionnalités d’accès conditionnel d’Azure AD sont prises en charge avec des [comptes consommateur](user-overview.md#consumer-user). Découvrez comment configurer un [accès conditionnel](conditional-access-user-flow.md) Azure AD B2C.|
| [Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) | Entièrement pris en charge pour les fonctionnalités d’Azure AD Premium P2. Par exemple, [Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) et [Identity Governance](../active-directory/governance/identity-governance-overview.md).  | Un sous-ensemble de fonctionnalités d’Azure AD Identity Protection sont prises en charge avec des [comptes de consommateurs](user-overview.md#consumer-user). Découvrez comment [Investiguer les risques avec Identity Protection](identity-protection-investigate-risk.md) et configurer un [accès conditionnel](conditional-access-user-flow.md) Azure AD B2C. |

## <a name="other-azure-resources-in-your-tenant"></a>Autres ressources Azure dans votre locataire

Dans un locataire Azure AD B2C, vous ne pouvez pas approvisionner d’autres ressources Azure, telles que des machines virtuelles, des applications web Azure ou des fonctions Azure. Vous devez créer ces ressources dans votre locataire Azure AD.

## <a name="azure-ad-b2c-accounts-overview"></a>Vue d’ensemble des comptes Azure AD B2C

Les types de comptes suivants peuvent être créés dans un locataire Azure AD B2C :

Dans un locataire Azure AD B2C, il existe plusieurs types de comptes qui peuvent être créés comme décrit dans [Vue d’ensemble des comptes d’utilisateur dans Azure Active Directory B2C](user-overview.md).

- **Compte professionnel** – Un compte professionnel peut accéder aux ressources dans un locataire et, avec un rôle d’administrateur, peut gérer des locataires.
- **Compte invité** – Un compte invité peut uniquement être un compte Microsoft ou un utilisateur Azure Active Directory qui peut être utilisé pour accéder à des applications ou gérer des locataires.
- **Compte de consommateur** – Un compte de consommateur est utilisé par un utilisateur des applications inscrites auprès d’Azure AD B2C.

Pour plus de détails sur ces types de comptes, consultez [Vue d’ensemble des comptes d’utilisateur dans Azure Active Directory B2C](user-overview.md). L’utilisateur qui sera affecté à la gestion de votre locataire Azure AD B2C doit disposer d’un compte d’utilisateur Azure AD pour pouvoir accéder aux services Azure. Vous pouvez ajouter un tel utilisateur en [créant un compte](#add-an-administrator-work-account) (compte professionnel) dans votre locataire Azure AD B2C ou en l’[invitant](#invite-an-administrator-guest-account) dans votre locataire Azure AD B2C en tant qu’utilisateur invité.

## <a name="use-roles-to-control-resource-access"></a>Utiliser des rôles pour contrôler l’accès aux ressources

Lors de la planification de votre stratégie de contrôle d’accès, il est préférable d’attribuer le rôle le moins privilégié requis pour accéder aux ressources. Le tableau suivant décrit les ressources principales de votre locataire Azure AD B2C et les rôles administratifs les plus appropriés pour les utilisateurs qui les gèrent.

|Ressource  |Description  |Role  |
|---------|---------|---------|
|[Inscriptions d’applications](tutorial-register-applications.md) | Créez et gérez tous les aspects de vos inscriptions d’applications web, mobiles et natives dans Azure AD B2C.|[Administrateur d’application](../active-directory/roles/permissions-reference.md#application-administrator)|
|[Fournisseurs d’identité](add-identity-provider.md)| Configurez le [fournisseur d’identité local](identity-provider-local.md) et les fournisseurs d’identité sociale ou d’entreprise externes. | [Administrateur de fournisseurs d’identité externes](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[Connecteurs d’API](add-api-connector.md)| Intégrez vos flux d’utilisateurs aux API web pour personnaliser l’expérience utilisateur et pour intégrer des systèmes externes.|[Administrateur de flux d’utilisateurs ID externe](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Marque de société](customize-ui.md#configure-company-branding)| Personnalisez vos pages de flux d’utilisateurs.| [Administrateur général](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Attributs utilisateur](user-flow-custom-attributes.md)| Ajoutez ou supprimez des attributs personnalisés disponibles pour tous les flux d’utilisateurs.| [Administrateur d’attribut de flux d’utilisateurs ID externe](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|Gestion des utilisateurs| Gérez les [comptes consommateur](manage-users-portal.md) et les comptes d’administration comme décrit dans cet article.| [Administrateur d’utilisateurs](../active-directory/roles/permissions-reference.md#user-administrator)|
|Rôles et administrateurs| Gérer les attributions de rôle dans le répertoire Azure AD B2C. Créez et gérez des groupes qui peuvent être affectés à des rôles Azure AD B2C. |[Administrateur général](../active-directory/roles/permissions-reference.md#global-administrator), [Administrateur de rôle privilégié](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[Flux d’utilisateurs](user-flow-overview.md)|Pour une configuration rapide et l’activation de tâches d’identité courantes, telles que l’inscription, la connexion et la modification de profil.| [Administrateur de flux d’utilisateurs ID externe](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Stratégies personnalisées](user-flow-overview.md)| Créez, lisez, mettez à jour et supprimez toutes les stratégies personnalisées dans Azure AD B2C.| [Administrateur de stratégies B2C IEF](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[Clés de stratégies](policy-keys-overview.md)|Ajoutez et gérez des clés de chiffrement pour la signature et la validation de jetons, de clés secrètes client, de certificats et de mots de passe utilisés dans des stratégies personnalisées.|[Administrateur de jeux de clés B2C IEF](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|


## <a name="add-an-administrator-work-account"></a>Ajouter un administrateur (compte professionnel)

Pour créer un compte d’administration, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec des autorisations d’administrateur général ou d’administrateur de rôle privilégié.
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Utilisateurs**.
1. Sélectionnez **Nouvel utilisateur**.
1. Sur la page **Utilisateur**, entrez les informations pour cet utilisateur :

   - **Nom**. Obligatoire. Prénom et nom du nouvel utilisateur. Par exemple, *Mary Parker*.
   - **Nom d’utilisateur**. Obligatoire. Nom d’utilisateur du nouvel utilisateur. Par exemple : `mary@contoso.com`.
     Le domaine qui fait partie du nom d’utilisateur doit correspondre au nom de domaine par défaut initial, *\<yourdomainname>.onmicrosoft.com*.
   - **Groupes**. Si vous le souhaitez, vous pouvez ajouter l’utilisateur à un ou plusieurs groupes existants. Vous pouvez également ajouter l’utilisateur à des groupes ultérieurement. 
   - **Rôle d’annuaire** : si vous devez attribuer des autorisations d’administration Azure AD à l’utilisateur, vous pouvez les ajouter à un rôle Azure AD. Vous pouvez attribuer à l'utilisateur le rôle d'Administrateur général, ou lui confier un ou plusieurs des rôles d'administrateur limités dans Azure AD. Pour plus d’informations sur l’attribution de rôles, consultez [Utiliser des rôles pour contrôler l’accès aux ressources](#use-roles-to-control-resource-access).
   - **Informations sur l’emploi** : Vous pouvez ajouter ici des informations supplémentaires sur l’utilisateur, ou le faire ultérieurement. 

1. Copiez le mot de passe généré automatiquement fourni dans le champ **Mot de passe**. Vous devrez fournir ce mot de passe à l’utilisateur pour qu’il se connecte la première fois.
1. Sélectionnez **Create** (Créer).

L’utilisateur est créé et ajouté à votre locataire Azure AD B2C. Il est préférable d’avoir au moins un compte professionnel natif pour votre locataire Azure AD B2C affecté au rôle d’administrateur général. Ce compte peut être considéré comme un compte de secours.

## <a name="invite-an-administrator-guest-account"></a>Inviter un administrateur (compte invité)

Vous pouvez également inviter un nouvel utilisateur invité à gérer votre locataire. Le compte invité est l’option privilégiée lorsque votre organisation possède également Azure AD, car le cycle de vie de cette identité peut être géré en externe. 

Pour inviter un utilisateur, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec des autorisations d’administrateur général ou d’administrateur de rôle privilégié.
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Utilisateurs**.
1. Sélectionnez **Nouveau compte Invité**.
1. Sur la page **Utilisateur**, entrez les informations pour cet utilisateur :

   - **Nom**. Obligatoire. Prénom et nom du nouvel utilisateur. Par exemple, *Mary Parker*.
   - **Adresse e-mail**. Obligatoire. Adresse e-mail de l’utilisateur que vous souhaitez inviter. Par exemple : `mary@contoso.com`.   
   - **Message personnel** : vous ajoutez un message personnel qui sera inclus dans l’e-mail d’invitation.
   - **Groupes**. Si vous le souhaitez, vous pouvez ajouter l’utilisateur à un ou plusieurs groupes existants. Vous pouvez également ajouter l’utilisateur à des groupes ultérieurement.
   - **Rôle d’annuaire** : si vous devez attribuer des autorisations d’administration Azure AD à l’utilisateur, vous pouvez les ajouter à un rôle Azure AD. Vous pouvez attribuer à l'utilisateur le rôle d'Administrateur général, ou lui confier un ou plusieurs des rôles d'administrateur limités dans Azure AD. Pour plus d’informations sur l’attribution de rôles, consultez [Utiliser des rôles pour contrôler l’accès aux ressources](#use-roles-to-control-resource-access).
   - **Informations sur l’emploi** : Vous pouvez ajouter ici des informations supplémentaires sur l’utilisateur, ou le faire ultérieurement.

1. Sélectionnez **Créer**.

Un e-mail d’invitation est envoyé à l’utilisateur. L’utilisateur doit accepter l’invitation pour pouvoir se connecter.

### <a name="resend-the-invitation-email"></a>Renvoyer l’e-mail d’invitation

Si l’invité n’a pas reçu l’e-mail d’invitation ou si l’invitation a expiré, vous pouvez renvoyer l’invitation. Comme alternative à l’e-mail d’invitation, vous pouvez donner un lien direct à un invité pour qu’il accepte l’invitation. Pour renvoyer l’invitation et recevoir le lien direct :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Utilisateurs**.
1. Recherchez et sélectionnez l’utilisateur auquel vous souhaitez renvoyer l’invitation.
1. Sur la page **Utilisateur | Profil**, sous **Identité**, sélectionnez **(Gérer)** .
    
    ![Capture d’écran montrant comment renvoyer un e-mail d’invitation à un compte Invité.](./media/tenant-management/guest-account-resend-invite.png)

1. Pour **Renvoyer une invitation ?** , sélectionnez **Oui**. Lorsque **Voulez-vous vraiment renvoyer une invitation ?** s’affiche, sélectionnez **Oui**.
1. Azure AD B2C envoie l’invitation. Vous pouvez également copier l’URL de l’invitation et la fournir directement à l’invité.
    
    ![Capture d’écran montrant comment recevoir l’URL de l’invitation.](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>Ajouter une attribution de rôle

Vous pouvez attribuer un rôle lorsque vous [créez un utilisateur](#add-an-administrator-work-account) ou que vous [invitez un utilisateur invité](#invite-an-administrator-guest-account). Vous pouvez ajouter, modifier ou supprimer un rôle pour un utilisateur :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec des autorisations d’administrateur général ou d’administrateur de rôle privilégié.
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Utilisateurs**.
1. Sélectionnez l’utilisateur dont vous souhaitez modifier les rôles. Ensuite, sélectionnez **Rôles affectés**.
1. Sélectionnez **Ajouter des affectations**, le rôle à attribuer (par exemple, *Administrateur d’application*), puis choisissez **Ajouter**.

## <a name="remove-a-role-assignment"></a>Supprimer une attribution de rôle

Si vous devez supprimer une attribution de rôle d’un utilisateur, procédez comme suit :

1. Sélectionnez **Azure AD B2C**, sélectionnez **Utilisateurs**, puis recherchez et sélectionnez l’utilisateur.
1. Sélectionnez **Rôles attribués**. Sélectionnez le rôle que vous souhaitez supprimer, tel qu’*Administrateur d’application*, puis sélectionnez **Supprimer l’attribution**.

## <a name="review-administrator-account-role-assignments"></a>Vérifier les attributions de rôle de compte administrateur

Dans le cadre d’un processus d’audit, vous examinez généralement les utilisateurs qui sont attribués à des rôles spécifiques dans le répertoire Azure AD B2C. Procédez comme suit pour vérifier quels utilisateurs sont actuellement affectés à des rôles privilégiés.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec des autorisations d’administrateur général ou d’administrateur de rôle privilégié.
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Rôles et administrateurs**.
1. Sélectionnez un rôle, comme **Administrateur général**. La page de **Rôle | Affectations** répertorie les utilisateurs disposant de ce rôle.

## <a name="delete-an-administrator-account"></a>Supprimer un compte administrateur

Pour supprimer un utilisateur existant, vous devez disposer d’une attribution de rôle *Administrateur général*. Les administrateurs généraux peuvent supprimer n’importe quel utilisateur, notamment les autres administrateurs. Les *administrateurs d’utilisateurs* peuvent supprimer n’importe quel utilisateur non-administrateur.

1. Dans votre répertoire Azure AD B2C, sélectionnez **Utilisateurs**, puis sélectionnez l'utilisateur que vous souhaitez supprimer.
1. Sélectionnez **Supprimer**, puis **Oui** pour confirmer la suppression.

L’utilisateur est supprimé et n’apparaît plus sur la page **Utilisateurs : Tous les utilisateurs**. L’utilisateur est affiché sur la page **Utilisateurs supprimés** pendant 30 jours et peut être restauré durant cette période. Pour plus d’informations sur la restauration d’un utilisateur, consultez [Restaurer ou supprimer un utilisateur supprimé à l’aide d’Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="protect-administrative-accounts"></a>Protéger des comptes d’administration

Il est recommandé de protéger tous les comptes administrateur avec une authentification multifacteur (MFA) pour plus de sécurité. MFA est un processus de vérification d’identité lors de la connexion qui invite l’utilisateur à fournir une plus grande forme d’identification, telle qu’un code de vérification sur son appareil mobile ou une requête dans son application Microsoft Authenticator.

![Méthodes d’authentification utilisées dans la capture d’écran de connexion](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

Vous pouvez activer des [paramètres de sécurité par défaut Azure AD](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) pour forcer tous les comptes d’administration à utiliser MFA.

## <a name="get-your-tenant-name"></a>Récupérer votre nom de locataire

Pour récupérer votre nom de locataire Azure AD B2C, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Dans la **Vue d’ensemble**, copiez le **Nom de domaine**.

![Capture d’écran montrant comment récupérer le nom de locataire Azure AD B2C.](./media/tenant-management/get-azure-ad-b2c-tenant-name.png)  

## <a name="get-your-tenant-id"></a>Récupérer votre ID de locataire

Pour récupérer votre ID de locataire Azure AD B2C, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le Portail Azure, recherchez et sélectionnez **Azure Active Directory**.
1. Dans la **vue d’ensemble**, copiez l’**ID de locataire**.

![Capture d’écran montrant comment récupérer l’ID de locataire Azure AD B2C.](./media/tenant-management/get-azure-ad-b2c-tenant-id.png)  

## <a name="next-steps"></a>Étapes suivantes

- [Créer un locataire Azure Active Directory B2C dans le portail Azure](tutorial-create-tenant.md)
