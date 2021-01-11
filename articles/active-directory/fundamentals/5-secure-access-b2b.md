---
title: Transition vers une collaboration contrôlée avec Azure Active Directory B2B Collaboration
description: Passez à une collaboration contrôlée avec Azure AD B2B Collaboration.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96a671d44133a6f508cc5026e06fc6a56b9e6f20
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743694"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>Transition vers une collaboration contrôlée avec Azure Active Directory B2B Collaboration 

Le contrôle de votre collaboration est la clé de la sécurisation de l’accès externe à vos ressources. Avant de poursuivre cet article, vérifiez que vous avez accompli les étapes suivantes :

* [déterminé votre posture de sécurité](1-secure-access-posture.md) ;

* [découvert votre état actuel](2-secure-access-current-state.md) ;

* [créé un plan de sécurité](3-secure-access-plan.md) ;

* [compris la manière dont les groupes et la sécurité fonctionnent ensemble](4-secure-access-groups.md).

Cela fait, vous êtes prêt à passer à une collaboration contrôlée. Cet article vous guide dans la migration de votre collaboration externe vers [Azure Active Directory B2B Collaboration](../external-identities/what-is-b2b.md) (Azure AD B2B). Azure AD B2B est une fonctionnalité d’[Azure AD External Identities](../external-identities/compare-with-b2c.md).

## <a name="control-who-your-organization-collaborates-with"></a>Contrôler les personnes avec lesquelles votre organisation collabore

Vous devez décider s’il est nécessaire de limiter les organisations avec lesquelles vos utilisateurs peuvent collaborer et les personnes au sein de votre organisation qui peuvent initier une collaboration. La plupart des organisations adoptent l’approche consistant à permettre aux unités commerciales de décider avec qui elles collaborent, et à déléguer l’approbation et la supervision en fonction des besoins. Par exemple, certaines organisations du secteur public, d’enseignement et de services financiers n’autorisent pas de collaboration ouverte. Vous pouvez utiliser les fonctionnalités d’Azure AD pour étendre la collaboration, comme indiqué dans le reste de cette section.

### <a name="determine-collaboration-partners"></a>Déterminer les partenaires de collaboration

Commencez par répertorier les organisations avec lesquelles vous collaborez actuellement, ainsi que les domaines pour les utilisateurs au sein de ces organisations. Un partenaire de collaboration peut avoir plusieurs domaines. Par exemple, un partenaire peut avoir plusieurs unités commerciales avec des domaines distincts.

Ensuite, déterminez si vous souhaitez permettre une collaboration future avec 

* n’importe quel domaine (option la plus inclusive)

* tous les domaines sauf ceux explicitement exclus 

* uniquement des domaines spécifiques (option la plus restrictive)

> [!NOTE]
> Plus vos paramètres de collaboration sont restrictifs, plus il y a de probabilité que vos utilisateurs sortent de votre infrastructure de collaboration approuvée. Nous vous recommandons de permettre la collaboration la plus large que vos besoins en matière de sécurité autorisent, et de suivre de près cette collaboration plutôt que d’être trop restrictif. 

Notez également que la limitation à un seul domaine peut avoir pour effet involontaire d’empêcher une collaboration autorisée avec des organisations qui ont d’autres domaines non liés pour leurs utilisateurs. Par exemple, si vous faites affaire avec une organisation Contoso, le point de contact initial avec celle-ci peut être l’un de ses employés basé aux État-Unis possédant un e-mail du domaine « .com ». Toutefois, si vous n’autorisez que le domaine « .com », vous risquez d’omettre par inadvertance les employés canadiens de Contoso dont le domaine est « .ca ». 

Dans certains cas, pouvez être amené à autoriser uniquement des partenaires de collaboration spécifiques. Par exemple, un système universitaire peut vouloir n’autoriser que son propre personnel enseignant à accéder à un locataire de ressource. Ou un conglomérat peut vouloir n’autoriser que des filiales spécifiques à collaborer entre elles pour assurer la conformité avec un cadre requis.

#### <a name="using-allow-and-deny-lists"></a>Utiliser des listes vertes et des listes d’exclusion

Vous pouvez utiliser une liste verte ou une liste d’exclusion pour [restreindre les invitations aux utilisateurs B2B](../external-identities/allow-deny-list.md) d’organisations spécifiques. Vous ne pouvez utiliser qu’une liste d’autorisation ou une liste d’exclusion, pas les deux.

* Une [liste verte](../external-identities/allow-deny-list.md) limite la collaboration aux seuls domaines répertoriés, tous les autres domaines figurant effectivement dans la liste d’exclusion.

* Une [liste de d’exclusion](../external-identities/allow-deny-list.md) autorise la collaboration avec tout domaine qui n’y est pas repris.

> [!IMPORTANT]
> Ces listes ne s’appliquent pas aux utilisateurs figurant déjà dans votre annuaire. Elles ne s’appliquent pas non plus à OneDrive Entreprise et à SharePoint qui ont des listes vertes et d’exclusion séparées.

Certaines organisations utilisent une liste de domaines d’« acteurs malveillants » connus épinglés par leur fournisseur de sécurité gérée pour leur liste d’exclusion. Par exemple, si l’organisation fait légitimement affaire avec Contoso et utilise un domaine. com, il peut arriver qu’une organisation non liée utilisant le domaine .org Contoso tente une attaque par hameçonnage en empruntant l’identité d’employés de Contoso. 

## <a name="control-how-external-users-gain-access"></a>Contrôler la façon dont les utilisateurs externes obtiennent l’accès

Il existe de nombreuses façons de collaborer avec des partenaires externes à l’aide d’Azure AD B2B. Pour commencer à collaborer, vous pouvez inviter ou autoriser votre partenaire à accéder à vos ressources. Les utilisateurs peuvent obtenir l’accès en procédant comme suit :

* en acceptant [une invitation envoyée par e-mail](../external-identities/redemption-experience.md) ou [un lien direct pour partager](../external-identities/redemption-experience.md) une ressource ;

* en demandant l’accès [via une application](../external-identities/self-service-sign-up-overview.md) que vous créez ;

* en demandant l’accès via le portail [Mon Accès](../governance/entitlement-management-request-access.md).

Lorsque vous activez Azure AD B2B, vous avez la possibilité d’inviter des utilisateurs via des liens directs et des invitations par e-mail par défaut. Les invitations envoyées via un mot de passe à usage unique et un portail en libre-service sont actuellement en préversion, et doivent être activées sous Identités externes, Paramètres de collaboration externe dans le portail Azure AD.

### <a name="control-who-can-invite-guest-users"></a>Contrôler qui peut inviter des utilisateurs

Déterminez qui peut inviter des utilisateurs à accéder aux ressources.

* Avec le paramètre le plus restrictif, seuls les administrateurs et les utilisateurs auxquels est attribué le [rôle Inviteur invité](../external-identities/delegate-invitations.md) sont autorisés à inviter des utilisateurs.

* Si vos exigences en matière de sécurité le permettent, nous vous recommandons d’autoriser tous les utilisateurs dont le userType est Member à inviter des utilisateurs.

* Déterminez si vous souhaitez que les utilisateurs dont le userType est Guest, soit le type de compte par défaut pour les utilisateurs B2B Azure AD, puissent inviter d’autres utilisateurs. 

![Capture d’écran des paramètres d’invitation d’invité.](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>Collecter des informations supplémentaires sur les utilisateurs externes

Si vous utilisez la gestion des droits d'utilisation Azure AD, vous pouvez configurer des questions auxquelles les utilisateurs externes doivent répondre. Les questions seront ensuite présentées aux approbateurs pour les aider à prendre une décision. Vous pouvez configurer différents ensembles de questions pour chaque [stratégie de package d’accès](../governance/entitlement-management-access-package-approval-policy.md) afin que les approbateurs puissent disposer d’informations pertinentes pour l’accès qu’ils approuvent. Par exemple, si un package d’accès est destiné à l’accès fournisseur, le demandeur peut être invité à entrer son numéro de contrat fournisseur. Un autre package d’accès destiné aux fournisseurs peut leur demander leur pays d’origine.

Si vous disposez d’un portail en libre-service, vous pouvez utiliser des [connecteurs d’API](../external-identities/api-connectors-overview.md) pour recueillir des attributs supplémentaires sur les utilisateurs lors de leur inscription. Vous pouvez ensuite utiliser ces attributs pour attribuer l’accès. Par exemple, si, pendant le processus d’inscription, vous collectez l’ID de fournisseur, vous pouvez utiliser cet attribut pour les affecter de manière dynamique à un groupe ou à un package d’accès pour ce fournisseur. Vous pouvez créer des attributs personnalisés dans le portail Azure et les utiliser dans vos flux utilisateur d’inscription en libre-service. Vous pouvez également lire et écrire ces attributs à l’aide de [l’API Microsoft Graph](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api). 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>Résoudre les problèmes d’acceptation d'invitation d’utilisateurs Azure AD

Il existe trois cas où les utilisateurs invités d’un partenaire de collaboration utilisant Azure AD éprouveront des difficultés à accepter une invitation.

* Si vous utilisez une liste verte alors que le domaine de l’utilisateur n’est pas inclus dans une liste verte.

* Si le locataire de base du partenaire de collaboration a des restrictions qui lui sont liées, qui empêchent la collaboration avec des utilisateurs externes.

* Si l’utilisateur ne fait pas partie du locataire Azure AD du partenaire. Par exemple, il existe dans contoso.com des utilisateurs se trouvant uniquement dans Active Directory (ou un autre fournisseur d’identité local), qui ne peuvent accepter des invitations que via le processus de mot de passe à usage unique envoyé par e-mail. Pour plus d’informations, consultez le [flux d’acceptation d'invitation](../external-identities/redemption-experience.md).

## <a name="control-what-external-users-can-access"></a>Contrôler ce à quoi les utilisateurs externes peuvent accéder

La plupart des organisations ne sont pas monolithiques. Autrement dit, il existe des ressources qui peuvent être partagées avec des utilisateurs externes, et d’autres auxquelles vous ne voulez pas que des utilisateurs externes puissent accéder. Par conséquent, vous devez contrôler ce à quoi les utilisateurs externes accèdent. Envisagez d’utiliser [des packages d’accès et de gestion des droits d'utilisation pour contrôler l’accès](6-secure-access-entitlement-managment.md) à des ressources spécifiques.

Par défaut, les utilisateurs invités peuvent voir des informations et des attributs sur des membres de client et d’autres partenaires, y compris des appartenances aux groupes. Déterminez si vos exigences de sécurité appellent à limiter l’accès des utilisateurs externes à ces informations.

![Capture d’écran de la configuration des paramètres de collaboration externe](media/secure-external-access/5-external-collaboration-settings.png)

Nous vous recommandons d’utiliser les restrictions suivantes pour les utilisateurs invités.

* **Limitez l’accès invité à des groupes de navigation et à d’autres propriétés dans l’annuaire**.

   * Utilisez les paramètres de collaboration externe pour limiter la capacité des invités à lire des groupes dont ils ne sont pas membres.

* **Bloquez l’accès à des applications réservées aux employés**.

   * Créez une stratégie d’accès conditionnel pour bloquer l’accès aux applications intégrées à Azure AD qui ne sont appropriées que pour des utilisateurs non invités. 

* **Bloquez l’accès au portail Azure. Vous pouvez raréfier les exceptions nécessaires**. 

   * Créez une stratégie d’accès conditionnel qui comprend tous les utilisateurs invités et externes, puis [implémentez une stratégie pour bloquer l’accès](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management).

 

## <a name="remove-users-who-no-longer-need-access"></a>Supprimer les utilisateurs qui n’ont plus besoin d’un accès

Évaluez l’accès actuel afin de [déterminer et supprimer les utilisateurs qui n’ont plus besoin d’accéder](../governance/access-reviews-external-users.md). Incluez des utilisateurs externes dans votre locataire en tant qu’invités, et ceux avec des comptes de membres. 

Certaines organisations ont ajouté des utilisateurs externes tels que des vendeurs (fournisseurs), des partenaires et des contractuels (sous-traitants) en tant que membres. Ces membres peuvent avoir un attribut spécifique, ou des noms d’utilisateur commençant, par exemple, par :

* v pour les fournisseurs (vendeurs) ;

* p pour les partenaires ;

* c pour les contractuels.

Évaluez tous les utilisateurs externes disposant de comptes de membres pour déterminer s’ils ont toujours besoin d’un accès. Si c’est le cas, migrez ces utilisateurs vers Azure AD B2B en procédant de la manière décrite dans la section suivante.

Vous pouvez également avoir des utilisateurs invités qui n’ont pas été invités via la gestion des droits d'utilisation ou Azure AD B2B.

Pour trouver ces utilisateurs, vous pouvez :

* [Rechercher des invités non autorisés via la gestion des droits d’utilisation](../governance/access-reviews-external-users.md).

   * Nous fournissons un [exemple de script PowerShell](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse).

Migrez ces utilisateurs vers Azure AD B2B en procédant de la manière décrite dans la section suivante.

## <a name="transition-your-current-external-users-to-b2b"></a>Migrer vos utilisateurs externes actuels vers B2B

Si vous n’avez pas utilisé Azure AD B2B, vous avez probablement des utilisateurs non-employés dans votre locataire. Nous vous recommandons de migrer ces comptes vers des comptes d’utilisateurs externes Azure AD B2B, puis de modifier leur UserType en Guest. Cela vous permet de tirer parti des nombreuses façons dont Azure AD et Microsoft 365 vous permettent de traiter les utilisateurs externes différemment. En voici quelques manières de procéder :

* en incluant ou excluant facilement des utilisateurs invités dans des stratégies d’accès conditionnel ;

* en incluant ou excluant facilement des utilisateurs invités dans des packages d’accès et des révisions d’accès ;

* en incluant ou excluant facilement un accès externe à Teams, à SharePoint et à d’autres ressources.

Pour migrer ces utilisateurs internes tout en maintenant leur accès, leur nom d'utilisateur principal et leurs appartenances aux groupes actuels, consultez [Inviter des utilisateurs internes sur B2B Collaboration](../external-identities/invite-internal-users.md).

## <a name="decommission-undesired-collaboration-methods"></a>Désactiver les méthodes de collaboration indésirables

Pour achever votre transition vers la collaboration contrôlée, vous devez désactiver les méthodes de collaboration indésirables. Le choix des méthodes de collaboration à désactiver dépend du degré de contrôle que vous souhaitez que l’équipe informatique exerce sur la collaboration et sur votre posture de sécurité. Pour plus d’informations sur le contrôle par l’équipe informatique ou par l’utilisateur final, consultez [Déterminer votre posture de sécurité pour l’accès externe](1-secure-access-posture.md).

Vous trouverez ci-dessous les véhicules de collaboration que vous pouvez évaluer.

### <a name="direct-invitation-through-microsoft-teams"></a>Invitation directe via Microsoft teams

Par défaut, Teams autorise l’accès externe, ce qui signifie que l’organisation peut communiquer avec tous les domaines externes. Si vous souhaitez restreindre ou autoriser des domaines spécifiques uniquement pour Teams, vous pouvez le faire dans le [portail d’administration Teams](https://admin.teams.microsoft.com/company-wide-settings/external-communications). 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>Partage direct via SharePoint et OneDrive

Un partage direct via SharePoint et OneDrive peut ajouter des utilisateurs en dehors du processus de gestion des droits d'utilisation. Pour une présentation détaillée de ces configurations, consultez [Gérer l’accès avec Microsoft Teams, SharePoint et OneDrive Entreprise](9-secure-access-teams-sharepoint.md). Vous pouvez également [Bloquer l’utilisation du OneDrive personnel de l’utilisateur](https://docs.microsoft.com/office365/troubleshoot/group-policy/block-onedrive-use-from-office) si vous le souhaitez.

### <a name="sending-documents-through-email"></a>Envoi de documents par e-mail

Vos utilisateurs envoient des documents par e-mail à des utilisateurs externes. Réfléchissez à la façon dont vous souhaitez restreindre et chiffrer l’accès à ces documents à l’aide d’étiquettes de sensibilité. Pour plus d’informations, consultez Gérer l’accès avec des étiquettes de sensibilité.

### <a name="unsanctioned-collaboration-tools"></a>Outils de collaboration non approuvés

La panoplie des outils de collaboration est vaste. Vos utilisateurs en utilisent probablement un grand nombre en dehors de leurs tâches officielles, dont des plateformes telles que Google Docs, DropBox, Slack ou Zoom. Il est possible de bloquer l’utilisation de ces outils à partir d’un réseau d’entreprise au niveau du pare-feu et avec une gestion des applications mobiles pour les appareils gérés par l’organisation. Toutefois, cela bloque également toute instance approuvée de ces plateformes et ne bloque pas l’accès à partir d’appareils non gérés. Bloquez les plateformes qui ne doivent pas être utilisées si nécessaire, et créez des stratégies métier pour une utilisation non approuvée pour les plateformes que vous devez utiliser. 

Pour plus d’informations sur la gestion des applications non approuvées, consultez les rubriques suivantes :

* [Gouvernance des applications connectées](https://docs.microsoft.com/cloud-app-security/governance-actions)

* [Approbation/non-approbation d’une application](https://docs.microsoft.com/cloud-app-security/governance-discovery)

 
### <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur la sécurisation de l’accès externe aux ressources. Nous vous recommandons d’effectuer les actions dans l’ordre indiqué.

1. [Déterminer votre posture de sécurité pour l’accès externe](1-secure-access-posture.md)

2. [Découvrir votre état actuel](2-secure-access-current-state.md)

3. [Créer un plan de gouvernance](3-secure-access-plan.md)

4. [Utiliser des groupes pour la sécurité](4-secure-access-groups.md)

5. [Passer à Azure AD B2B](5-secure-access-b2b.md) (vous êtes ici)

6. [Sécuriser l’accès avec la gestion des droits d’utilisation](6-secure-access-entitlement-managment.md)

7. [Sécuriser l’accès avec des stratégies d’accès conditionnel](7-secure-access-conditional-access.md)

8. [Sécuriser l’accès avec des étiquettes de confidentialité](8-secure-access-sensitivity-labels.md)

9. [Sécuriser l’accès à Microsoft Teams, OneDrive et SharePoint](9-secure-access-teams-sharepoint.md)