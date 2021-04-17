---
title: Meilleures pratiques pour les rôles Azure AD – Azure Active Directory
description: Meilleures pratiques pour l’utilisation des rôles Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 03/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78f4642b8f9f1ede65766a0026940c0af0f01ec2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110936"
---
# <a name="best-practices-for-azure-ad-roles"></a>Meilleures pratiques pour les rôles Azure AD

Cet article décrit quelques-unes des meilleures pratiques d’utilisation du contrôle d’accès en fonction du rôle Azure Active Directory (Azure AD RBAC). Ces meilleures pratiques sont issues de notre expérience d’Azure AD RBAC, mais également de celle des clients, comme vous. Nous vous encourageons également à lire notre aide détaillée en matière de sécurité à la page [Sécurisation de l’accès privilégié pour les déploiements hybrides et cloud dans Azure AD](security-planning.md).

## <a name="1-manage-to-least-privilege"></a>1. Gérer au moindre privilège

Lorsque vous planifiez votre stratégie de contrôle d’accès, la meilleure pratique consiste à gérer au moindre privilège. Le moindre privilège signifie que vous accordez à vos administrateurs exactement les autorisations dont ils ont besoin pour effectuer leur travail. Il existe trois aspects à prendre en compte lorsque vous attribuez un rôle à vos administrateurs : un ensemble spécifique d’autorisations, sur une étendue spécifique, pour une période spécifique. Évitez d’attribuer des rôles plus larges à des étendues plus importantes, même s’ils semblent plus pratiques dans un premier temps. En limitant les rôles et les étendues, vous limitez les ressources menacées en cas de compromission du principal de sécurité. Azure AD RBAC prend en charge plus de 65 [rôles intégrés](permissions-reference.md). Il existe des rôles Azure AD qui permettent de gérer les objets annuaire comme les utilisateurs, les groupes et les applications, ainsi que les services Microsoft 365 comme Exchange, SharePoint et Intune. Pour mieux comprendre les rôles intégrés Azure AD, consultez [Présentation des rôles dans Azure Active Directory](concept-understand-roles.md). Si aucun rôle intégré n’est adapté à vos besoins, vous pouvez créer vos propres [rôles personnalisés](custom-create.md).  
 
### <a name="finding-the-right-roles"></a>Recherche des rôles appropriés

Suivez ces étapes pour vous aider à trouver le bon rôle.

1. Dans le portail Azure, ouvrez [Rôles et administrateurs](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) pour afficher la liste des rôles Azure AD.

1. Utilisez le filtre **Service** pour réduire la liste des rôles.

    ![Page Rôles et administrateurs dans Azure AD avec le filtre Service ouvert](./media/best-practices/roles-administrators.png)

1. Reportez-vous à la documentation sur les [rôles intégrés Azure AD](permissions-reference.md). Les autorisations associées à chaque rôle sont répertoriées ensemble pour une meilleure lisibilité. Pour comprendre la structure et la signification des autorisations de rôle, consultez [Comment comprendre les autorisations de rôle](permissions-reference.md#how-to-understand-role-permissions).

1. Reportez-vous à la documentation [Rôle de moindre privilège par tâche](delegate-by-task.md).

## <a name="2-use-privileged-identity-management-to-grant-just-in-time-access"></a>2. Utiliser Privileged Identity Management pour accorder un accès juste-à-temps

L’un des principes du moindre privilège est que l’accès doit être accordé uniquement pour une période spécifique. [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) vous permet d’accorder un accès juste-à-temps à vos administrateurs. Microsoft vous recommande d’activer PIM dans Azure AD. Grâce à PIM, un utilisateur peut devenir éligible à un rôle Azure AD. Il peut ensuite activer son rôle pour une durée limitée chaque fois qu’il a besoin de l’utiliser. L’accès privilégié est automatiquement supprimé lorsque le délai expire. Vous pouvez également [configurer les paramètres de PIM](../privileged-identity-management/pim-how-to-change-default-settings.md) pour exiger une approbation ou recevoir des notifications par e-mail lorsqu’un utilisateur active son attribution de rôle. Ces notifications contiennent une alerte lorsque des utilisateurs sont ajoutés à des rôles hautement privilégiés. 

## <a name="3-turn-on-multi-factor-authentication-for-all-your-administrator-accounts"></a>3. Activer l’authentification multifacteur pour tous vos comptes d’administrateur

[Nos études](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984) révèlent que votre compte court 99,9 % moins de risque d’être compromis si vous utilisez l’authentification multifacteur. 
 
Vous pouvez activer l’authentification multifacteur sur les rôles Azure AD en utilisant deux méthodes :
- [Paramètres des rôles](../privileged-identity-management/pim-how-to-change-default-settings.md) dans Privileged Identity Management
- [Accès conditionnel](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

## <a name="4-configure-recurring-access-reviews-to-revoke-unneeded-permissions-over-time"></a>4. Configurer des révisions d’accès périodiques pour révoquer les autorisations inutiles au fil du temps

Les révisions d’accès permettent aux organisations d’examiner régulièrement l’accès des administrateurs pour s’assurer que seules les bonnes personnes disposent d’un accès continu. L’audit régulier de vos administrateurs est essentiel pour les raisons suivantes :
- Un acteur malveillant peut compromettre un compte.
- Les personnes changent d’équipe au sein d’une entreprise. En l’absence d’audit, elles peuvent accumuler des accès inutiles au fil du temps.
 
Pour plus d’informations sur les révisions d’accès pour les rôles, consultez [Créer une révision d’accès des rôles Azure AD dans PIM](../privileged-identity-management/pim-how-to-start-security-review.md). Pour plus d’informations sur les révisions d’accès des groupes auxquels des rôles sont attribués, consultez [Créer une révision d’accès des groupes et applications dans les révisions d’accès Azure AD](../governance/create-access-review.md).

## <a name="5-limit-the-number-of-global-administrators-to-less-than-5"></a>5. Limiter le nombre d’administrateurs généraux à moins de cinq

À titre de meilleure pratique, Microsoft recommande d’attribuer le rôle Administrateur général à **moins de cinq** personnes dans votre organisation. Les administrateurs généraux détiennent les clés du royaume, et il est dans votre intérêt d’avoir une surface d’attaque la plus petite possible. Comme indiqué précédemment, tous ces comptes doivent être protégés par une authentification multifacteur.

Par défaut, quand un utilisateur s’inscrit à un service cloud Microsoft, un locataire Azure AD est créé, et l’utilisateur est promu membre du rôle Administrateur général. Les utilisateurs qui sont affectés au rôle Administrateur général peuvent lire et modifier chaque paramètre d’administration de votre organisation Azure AD. À quelques exceptions près, les administrateurs généraux peuvent également lire et modifier tous les paramètres de configuration de votre organisation Microsoft 365. Les administrateurs généraux ont également la possibilité d’élever leur accès pour lire les données.

Microsoft recommande de conserver deux comptes de secours qui sont attribués de façon permanente au rôle Administrateur général. Assurez-vous que ces comptes ne nécessitent pas le même mécanisme d’authentification multifacteur que vos comptes d’administration normaux pour se connecter, comme décrit dans la section [ Gérer les comptes d’accès d’urgence dans Azure AD](../roles/security-emergency-access.md). 

## <a name="6-use-groups-for-azure-ad-role-assignments-and-delegate-the-role-assignment"></a>6. Utiliser des groupes pour l’attribution de rôles Azure AD et déléguer l’attribution de rôle

Si vous disposez d’un système de gouvernance externe qui tire parti des groupes, vous devez envisager d’attribuer des rôles à des groupes Azure AD plutôt qu’à des utilisateurs individuels. Vous pouvez également gérer des groupes assignables à un rôle dans PIM pour vous assurer qu’il n’y a pas de propriétaires ni de membres permanents dans ces groupes privilégiés. Pour plus d’informations, consultez [Fonctionnalités de gestion pour les groupes Azure AD d’accès privilégié](../privileged-identity-management/groups-features.md).

Vous pouvez affecter un propriétaire à des groupes assignables à un rôle. Ce propriétaire décide qui est ajouté ou supprimé dans le groupe et donc, indirectement, décide qui obtient l’attribution de rôle. De cette façon, un administrateur général ou un administrateur de rôle privilégié peut déléguer la gestion des rôles par rôle en utilisant des groupes. Pour plus d’informations, consultez [Utiliser des groupes cloud pour gérer les attributions de rôles dans Azure Active Directory](groups-concept.md).

## <a name="7-activate-multiple-roles-at-once-using-privileged-access-groups"></a>7. Activer plusieurs rôles à la fois à l’aide de groupes d’accès privilégié

Il peut arriver qu’une personne dispose de cinq ou six affectations admissibles à des rôles Azure AD par le biais de PIM. Elle devra activer chaque rôle individuellement, ce qui peut réduire sa productivité. Pire encore, elle peut également avoir des dizaines ou des centaines de ressources Azure qui lui sont attribuées, ce qui aggrave le problème.
 
Dans ce cas, vous devez utiliser des [groupes d’accès privilégié](../privileged-identity-management/groups-features.md). Créez un groupe d’accès privilégié et accordez-lui un accès permanent à plusieurs rôles (Azure AD et/ou Azure). Faites de cet utilisateur un membre éligible ou un propriétaire de ce groupe. En une seule activation, il aura accès à toutes les ressources liées.

![Diagramme du groupe accès privilégié montrant l’activation de plusieurs rôles à la fois](./media/best-practices/privileged-access-group.png)

## <a name="8-use-cloud-native-accounts-for-azure-ad-roles"></a>8. Utiliser des comptes natifs Cloud pour les rôles Azure AD

Évitez d’utiliser des comptes synchronisés locaux pour l’attribution de rôles Azure AD. Si votre compte local est compromis, vos ressources Azure AD peuvent l’être également.

## <a name="next-steps"></a>Étapes suivantes

- [Sécurisation de l’accès privilégié pour les déploiements hybrides et cloud dans Azure AD](security-planning.md)