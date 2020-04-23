---
title: Délégation de la gestion des utilisateurs avec Mon personnel (préversion) – Azure AD | Microsoft Docs
description: Déléguez la gestion des utilisateurs avec Mon personnel et les unités administratives.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7c12612dbe37de6b08cb05a64af460296ade93
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393903"
---
# <a name="manage-your-users-with-my-staff-preview"></a>Gestion des utilisateurs avec Mon personnel (préversion)

Mon personnel permet de déléguer à une figure d’autorité (par exemple, un responsable de magasin ou un chef d’équipe) les autorisations nécessaires pour que les membres de son personnel puissent accéder à leur compte Azure AD. Au lieu de s’appuyer sur un support technique central, les organisations peuvent déléguer à un chef d’équipe des tâches courantes comme la réinitialisation de mot de passe ou la modification des numéros de téléphone. Avec Mon personnel, l’utilisateur qui ne parvient pas à accéder à son compte peut récupérer l’accès en quelques clics seulement, sans intervention du support technique ou du personnel informatique.

Avant de configurer Mon personnel pour votre organisation, nous vous recommandons de consulter cette documentation ainsi que la [documentation utilisateur](../user-help/my-staff-team-manager.md) pour bien comprendre ses fonctionnalités et son impact sur vos utilisateurs. Vous pouvez utiliser la documentation utilisateur pour former vos utilisateurs et les préparer à la nouvelle expérience, afin de faciliter le déploiement.

## <a name="how-my-staff-works"></a>Fonctionnement de Mon personnel

Mon personnel se base sur des unités administratives, c’est-à-dire des conteneurs de ressources permettant de limiter l’étendue du contrôle administratif d’une attribution de rôle. Dans Mon personnel, les unités administratives sont utilisées pour définir un sous-ensemble des utilisateurs d’une organisation, par exemple un magasin ou un service. Un rôle étendu à une ou plusieurs unités administratives pourrait alors être attribué à un chef d’équipe. Dans l’exemple ci-dessous, l’utilisateur s’est vu accorder le rôle d’administrateur d’authentification, dont l’étendue couvre les trois unités administratives. Pour plus d’informations sur les unités administratives, consultez [Gestion des unités administratives dans Azure Active Directory](directory-administrative-units.md).

## <a name="how-to-enable-my-staff"></a>Activation de Mon personnel

Une fois que vous avez configuré les unités administratives, vous pouvez appliquer cette étendue aux utilisateurs qui accèdent à Mon personnel. Seuls les utilisateurs disposant d’un rôle d’administrateur ont accès à Mon personnel. Pour activer Mon personnel, procédez de la façon suivante :

1. Connectez-vous au Portail Azure en tant qu’Administrateur d’utilisateurs.
2. Accédez à **Azure Active Directory** > **Paramètres utilisateur** > **Préversion des fonctionnalités utilisateur** > **Gérer les paramètres de préversion des fonctionnalités utilisateur**.
3. Sous **Les administrateurs peuvent accéder à Mon personnel**, vous pouvez choisir d’activer l’accès pour tous les utilisateurs, les utilisateurs sélectionnés ou aucun utilisateur.

> [!Note]
> Seuls les utilisateurs disposant d’un rôle d’administrateur ont accès à Mon personnel. Si vous activez Mon personnel pour un utilisateur à qui aucun rôle d’administrateur n’est attribué, il ne pourra pas accéder à Mon personnel.

## <a name="using-my-staff"></a>Utilisation de Mon personnel

Quand un utilisateur accède à Mon personnel, il voit apparaître le nom des [unités administratives](directory-administrative-units.md) sur lesquelles il dispose d’autorisations d’administration. Dans la [documentation utilisateur de Mon personnel](../user-help/my-staff-team-manager.md), nous utilisons le terme « emplacement » pour faire référence aux unités administratives. Les autorisations d’un administrateur qui ne comportent pas d’étendue couvrant des unités administratives s’appliquent à l’ensemble de l’organisation. Après l’activation de Mon personnel, les utilisateurs activés et disposant d’un rôle d’administrateur peuvent y accéder à l’adresse [https://mystaff.microsoft.com](https://mystaff.microsoft.com). Ils ont la possibilité de sélectionner une unité administrative pour afficher les utilisateurs qu’elle comporte, et de sélectionner un utilisateur pour ouvrir son profil.

## <a name="licenses"></a>Licences

Même s’il n’utilise pas le portail Mon personnel, chaque utilisateur activé dans Mon personnel doit disposer d’une des licences Azure AD ou Microsoft 365 suivantes :

- Azure AD Premium P1 ou P2
- Microsoft 365 F1 ou F3

## <a name="reset-a-users-password"></a>Réinitialiser le mot de passe de l’utilisateur

Les rôles suivants sont autorisés à réinitialiser le mot de passe d’un utilisateur :

- [Administrateur d’authentification](directory-assign-admin-roles.md#authentication-administrator)
- [Administrateur d’authentification privilégié](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Administrateur général](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [Administrateur du support technique](directory-assign-admin-roles.md#helpdesk-administrator)
- [Administrateur d’utilisateurs](directory-assign-admin-roles.md#user-administrator)
- [Administrateur de mots de passe](directory-assign-admin-roles.md#password-administrator)

Dans **Mon personnel**, ouvrez le profil d’un utilisateur. Sélectionnez **Réinitialiser le mot de passe**.

- Si l’utilisateur se trouve uniquement sur le cloud, un mot de passe temporaire s’affiche. Vous pouvez le donner à l’utilisateur.
- Si l’utilisateur est synchronisé à partir d’Active Directory en local, vous pouvez entrer un mot de passe qui respecte vos stratégies AD locales. Vous pouvez ensuite le donner à l’utilisateur.

    ![Indicateur de progression de la réinitialisation de mot de passe et notification de réussite](media/my-staff-configure/reset-password.png)

L’utilisateur devra modifier son mot de passe à la prochaine connexion.

## <a name="manage-a-phone-number"></a>Gestion d’un numéro de téléphone

Dans **Mon personnel**, ouvrez le profil d’un utilisateur.

- Sélectionnez la section **Ajouter un numéro de téléphone** pour ajouter un numéro de téléphone à l’utilisateur.
- Sélectionnez **Modifier le numéro de téléphone** pour modifier le numéro de téléphone.
- Sélectionnez **Supprimer le numéro de téléphone** pour supprimer le numéro de téléphone de l’utilisateur.

Selon vos paramètres, l’utilisateur peut ensuite utiliser le numéro de téléphone que vous avez configuré pour se connecter par SMS, effectuer une authentification multifacteur et opérer une réinitialisation du mot de passe en libre-service.

Pour gérer le numéro de téléphone d’un utilisateur, vous devez disposer de l’un des rôles suivants :

- [Administrateur d’authentification](directory-assign-admin-roles.md#authentication-administrator)
- [Administrateur d’authentification privilégié](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Administrateur général](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>Recherche

Pour rechercher des unités administratives et des utilisateurs de votre organisation, vous pouvez utiliser la barre de recherche de Mon personnel. La recherche peut porter sur toutes les unités administratives et tous les utilisateurs de votre organisation, mais vous ne pouvez apporter des modifications qu’aux utilisateurs se trouvant dans une unité administrative sur laquelle des autorisations d’administrateur vous ont été accordées.

Il est également possible de rechercher un utilisateur au sein d’une unité administrative. Pour cela, utilisez la barre de recherche qui se trouve en haut de la liste d’utilisateurs.

## <a name="audit-logs"></a>Journaux d’audit

Vous pouvez afficher les journaux d’audit des actions effectuées dans Mon personnel sur le portail Azure Active Directory. Si un journal d’audit a été généré par une action effectuée dans Mon personnel, cette information est indiquée sous DÉTAILS SUPPLÉMENTAIRES dans l’événement d’audit.

## <a name="next-steps"></a>Étapes suivantes

[Documentation utilisateur de Mon personnel](../user-help/my-staff-team-manager.md)
[Documentation sur les unités administratives](directory-administrative-units.md)
