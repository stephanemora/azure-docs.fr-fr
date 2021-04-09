---
title: Utiliser Mon personnel pour déléguer la gestion des utilisateurs - Azure AD | Microsoft Docs
description: Déléguez la gestion des utilisateurs avec Mon personnel et les unités administratives.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 03/11/2021
ms.author: rolyon
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 1a380c8a3d766c3c11d8cba1148383d924f65a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103224994"
---
# <a name="manage-your-users-with-my-staff"></a>Gérez vos utilisateurs à l'aide de Mon personnel

Mon personnel vous permet de déléguer des autorisations à une figure d'autorité, telle qu'un responsable de magasin ou un chef d'équipe, afin que les membres de son personnel aient accès à leur compte Azure AD. Au lieu de s'appuyer sur un support technique central, les organisations peuvent déléguer à un chef d'équipe local des tâches courantes comme la réinitialisation des mots de passe ou la modification des numéros de téléphone. Avec Mon personnel, l’utilisateur qui ne parvient pas à accéder à son compte peut récupérer l’accès en quelques clics seulement, sans intervention du support technique ou du personnel informatique.

Avant de configurer Mon personnel pour votre organisation, nous vous recommandons de consulter cette documentation ainsi que la [documentation utilisateur](../user-help/my-staff-team-manager.md) afin de bien comprendre son fonctionnement et son impact sur vos utilisateurs. Vous pouvez utiliser la documentation utilisateur pour former vos utilisateurs et les préparer à la nouvelle expérience, afin de faciliter le déploiement.

## <a name="how-my-staff-works"></a>Fonctionnement de Mon personnel

Mon personnel se base sur des unités administratives, c'est-à-dire des conteneurs de ressources permettant de limiter l'étendue du contrôle administratif d'une attribution de rôle. Pour plus d’informations, consultez [Gestion des unités administratives dans Azure Active Directory](administrative-units.md). Dans Mon personnel, les unités administratives peuvent contenir un groupe d'utilisateurs associés à un magasin ou à un service. Un chef d'équipe peut ensuite se voir attribuer un rôle administratif dont l'étendue couvre une ou plusieurs unités.

## <a name="before-you-begin"></a>Avant de commencer

Pour faire ce qui est décrit dans cet article, vous avez besoin des ressources et des privilèges suivants :

* Un abonnement Azure actif.

  * Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un locataire Azure Active Directory associé à votre abonnement.

  * Si nécessaire, [créez un locataire Azure Active Directory](../fundamentals/sign-up-organization.md) ou [associez un abonnement Azure à votre compte](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
* Vous devez disposer de privilèges d’*administrateur d’entreprise* dans votre locataire Azure AD pour activer l’authentification par SMS.
* Chaque utilisateur activé dans la stratégie de méthode d'authentification par SMS doit disposer d'une licence, même s'il ne s'en sert pas. Chaque utilisateur activé doit avoir l’une des licences Azure AD ou Microsoft 365 suivantes :

  * [Azure AD Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [Microsoft 365 (M365) F1 ou F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [Enterprise Mobility + Security (EMS) E3 ou E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) ou [Microsoft 365 (M365) E3 ou E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>Activation de Mon personnel

Une fois que vous avez configuré les unités administratives, vous pouvez appliquer cette étendue aux utilisateurs qui accèdent à Mon personnel. Seuls les utilisateurs disposant d’un rôle d’administrateur ont accès à Mon personnel. Pour activer Mon personnel, procédez de la façon suivante :

1. Connectez-vous au Portail Azure en tant qu’Administrateur d’utilisateurs.
2. Accédez à **Azure Active Directory** > **Paramètres utilisateur** > **Préversion des fonctionnalités utilisateur** > **Gérer les paramètres de préversion des fonctionnalités utilisateur**.
3. Sous **Les administrateurs peuvent accéder à Mon personnel**, vous pouvez choisir d’activer l’accès pour tous les utilisateurs, les utilisateurs sélectionnés ou aucun utilisateur.

> [!Note]
> Seuls les utilisateurs disposant d’un rôle d’administrateur ont accès à Mon personnel. Si vous activez Mon personnel pour un utilisateur à qui aucun rôle d’administrateur n’est attribué, il ne pourra pas accéder à Mon personnel.

## <a name="conditional-access"></a>Accès conditionnel

Vous pouvez protéger le portail Mon personnel à l’aide d’une stratégie d’accès conditionnel Azure AD. Utilisez celle-ci pour des tâches telles que l’authentification multifacteur avant d’accéder au portail Mon personnel.

Nous vous recommandons vivement de protéger le portail Mon personnel à l’aide de [stratégies d’accès conditionnel Azure AD](../conditional-access/index.yml). Pour appliquer une stratégie d'accès conditionnel à Mon personnel, vous devez d'abord vous rendre sur le site Mon personnel pendant quelques minutes afin d'approvisionner automatiquement le principal de service de votre locataire en vue de son utilisation par l'accès conditionnel.

Le principal de service apparaît lorsque vous créez une stratégie d'accès conditionnel qui s'applique à l'application cloud Mon personnel.

![Créer une stratégie d’accès conditionnel pour l’application Mon personnel](./media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Utilisation de Mon personnel

Quand un utilisateur accède à Mon personnel, il voit apparaître le nom des [unités administratives](administrative-units.md) sur lesquelles il dispose d’autorisations d’administration. Dans la [documentation utilisateur de Mon personnel](../user-help/my-staff-team-manager.md), nous utilisons le terme « emplacement » pour faire référence aux unités administratives. Les autorisations d'un administrateur dont l'étendue ne couvre pas d'unités administratives s'appliquent à l'ensemble de l'organisation. Après l’activation de Mon personnel, les utilisateurs activés et disposant d’un rôle d’administrateur peuvent y accéder à l’adresse [https://mystaff.microsoft.com](https://mystaff.microsoft.com). Ils ont la possibilité de sélectionner une unité administrative pour afficher les utilisateurs qu'elle comporte, et de sélectionner un utilisateur pour ouvrir son profil.

## <a name="reset-a-users-password"></a>Réinitialiser le mot de passe de l’utilisateur

Avant de pouvoir réinitialiser les mots de passe des utilisateurs locaux, vous devez remplir les conditions préalables suivantes. Pour obtenir des instructions détaillées, consultez le tutoriel [Activer la réinitialisation de mot de passe en libre-service](../authentication/tutorial-enable-sspr-writeback.md).

* Configurer des autorisations pour la réécriture du mot de passe
* Activer la réécriture du mot de passe dans Azure AD Connect
* Activer la réécriture du mot de passe lors de la réinitialisation de mot de passe en libre-service (SSPR) Azure AD

Les rôles suivants sont autorisés à réinitialiser le mot de passe d’un utilisateur :

* [Administrateur d’authentification](permissions-reference.md#authentication-administrator)
* [Administrateur d’authentification privilégié](permissions-reference.md#privileged-authentication-administrator)
* [Administrateur général](permissions-reference.md#global-administrator)
* [Administrateur du support technique](permissions-reference.md#helpdesk-administrator)
* [Administrateur d’utilisateurs](permissions-reference.md#user-administrator)
* [Administrateur de mots de passe](permissions-reference.md#password-administrator)

Dans **Mon personnel**, ouvrez le profil d’un utilisateur. Sélectionnez **Réinitialiser le mot de passe**.

* Si l’utilisateur se trouve uniquement sur le cloud, un mot de passe temporaire s’affiche. Vous pouvez le donner à l’utilisateur.
* Si l’utilisateur est synchronisé à partir d’Active Directory en local, vous pouvez entrer un mot de passe qui respecte vos stratégies AD locales. Vous pouvez ensuite le donner à l’utilisateur.

    ![Indicateur de progression de la réinitialisation de mot de passe et notification de réussite](./media/my-staff-configure/reset-password.png)

L’utilisateur devra modifier son mot de passe à la prochaine connexion.

## <a name="manage-a-phone-number"></a>Gestion d’un numéro de téléphone

Dans **Mon personnel**, ouvrez le profil d’un utilisateur.

* Sélectionnez la section **Ajouter un numéro de téléphone** pour ajouter un numéro de téléphone à l’utilisateur.
* Sélectionnez **Modifier le numéro de téléphone** pour modifier le numéro de téléphone.
* Sélectionnez **Supprimer le numéro de téléphone** pour supprimer le numéro de téléphone de l’utilisateur.

Selon vos paramètres, l’utilisateur peut ensuite utiliser le numéro de téléphone que vous avez configuré pour se connecter par SMS, effectuer une authentification multifacteur et opérer une réinitialisation du mot de passe en libre-service.

Pour gérer le numéro de téléphone d’un utilisateur, vous devez disposer de l’un des rôles suivants :

* [Administrateur d’authentification](permissions-reference.md#authentication-administrator)
* [Administrateur d’authentification privilégié](permissions-reference.md#privileged-authentication-administrator)
* [Administrateur général](permissions-reference.md#global-administrator)

## <a name="search"></a>Recherche

Pour rechercher des unités administratives et des utilisateurs de votre organisation, vous pouvez utiliser la barre de recherche de Mon personnel. La recherche peut porter sur toutes les unités administratives et tous les utilisateurs de votre organisation, mais vous ne pouvez apporter des modifications qu'aux utilisateurs se trouvant dans une unité administrative sur laquelle des autorisations d'administrateur vous ont été accordées.

Il est également possible de rechercher un utilisateur au sein d'une unité administrative. Pour cela, utilisez la barre de recherche qui se trouve en haut de la liste d’utilisateurs.

## <a name="audit-logs"></a>Journaux d’audit

Vous pouvez afficher les journaux d’audit des actions effectuées dans Mon personnel sur le portail Azure Active Directory. Si un journal d’audit a été généré par une action effectuée dans Mon personnel, cette information est indiquée sous DÉTAILS SUPPLÉMENTAIRES dans l’événement d’audit.

## <a name="next-steps"></a>Étapes suivantes

[Documentation utilisateur de Mon personnel](../user-help/my-staff-team-manager.md)
[Documentation sur les unités administratives](administrative-units.md)
