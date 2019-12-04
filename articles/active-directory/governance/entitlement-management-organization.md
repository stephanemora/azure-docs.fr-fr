---
title: Ajouter une organisation connectée dans la gestion des droits d’utilisation Azure AD – Azure Active Directory
description: Découvrez comment autoriser les personnes extérieures à votre organisation à demander des packages d’accès afin de pouvoir collaborer sur des projets.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dec950f5475a8a64cfecfac1fb25246d6a7aa29
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561924"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Ajouter une organisation connectée dans la gestion des droits d’utilisation Azure AD

La gestion des droits d’utilisation Azure vous permet de collaborer avec des personnes extérieures à votre organisation. Si vous collaborez fréquemment avec des utilisateurs dans un annuaire ou domaine Azure AD externe, vous pouvez les ajouter en tant qu’organisation connectée. Cet article explique comment ajouter une organisation connectée afin de permettre aux utilisateurs extérieurs à votre organisation de demander des ressources dans votre annuaire.

## <a name="what-is-a-connected-organization"></a>Qu’est-ce qu’une organisation connectée ?

Une organisation connectée est un domaine ou un annuaire Azure AD externe avec lequel vous collaborez.

Par exemple, supposons que vous travaillez à la Woodgrove Bank et que vous souhaitez collaborer avec deux organisations externes : Graphic Design Institute et Contoso. Votre contact chez Graphic Design Institute vous a indiqué qu’il utilisait Azure AD et que les utilisateurs de Graphics Design Institute possédaient un nom d’utilisateur principal qui se termine par `graphicdesigninstitute.com`. Vous avez été informé par votre contact chez Contoso qu’il n’utilise pas encore Azure AD, mais que les utilisateurs de Contoso ont un nom d’utilisateur principal qui se termine par `contoso.com`.

Vous pouvez configurer deux organisations connectées, une pour Graphic Design Institute avec le domaine `graphicdesigninstitute.com`, et une pour Contoso avec le domaine `contoso.com`. Si vous ajoutez ensuite ces deux organisations connectées à une stratégie, les utilisateurs de chaque organisation qui ont un nom d’utilisateur principal correspondant à la stratégie peuvent demander des packages d’accès. En outre, comme Graphic Design Institute a été identifié comme utilisant Azure AD, si Graphic Design Institute a en plus des sous-domaines, comme `graphicdesigninstitute.example`, les utilisateurs avec ce nom d’utilisateur principal pourront également demander des packages d’accès à l’aide de la même stratégie.

![Exemple d’organisation connectée](./media/entitlement-management-organization/connected-organization-example.png)

La manière dont les utilisateurs de l’annuaire ou du domaine Azure AD s’authentifient dépend du type d’authentification. Les types d’authentification pour les organisations connectées sont les suivants :

- Azure AD
- [Fédération directe](../b2b/direct-federation.md)
- [Code secret à usage unique](../b2b/one-time-passcode.md) (domaine)

Pour une démonstration de l’ajout d’une organisation connectée, regardez la vidéo suivante :

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Ajouter une organisation connectée

Procédez comme suit pour ajouter un annuaire ou un domaine Azure AD externe en tant qu’organisation connectée.

**Rôle prérequis :** administrateur général, administrateur d’utilisateurs ou inviteur d’invités

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Organisations connectées**, puis cliquez sur **Ajouter une organisation connectée**.

    ![Gouvernance des identités - Organisations connectées - Ajouter une organisation connectée](./media/entitlement-management-organization/connected-organization.png)

1. Sous l’onglet **De base**, entrez un nom d’affichage et une description pour l’organisation.

    ![Ajouter une organisation connectée - onglet De base](./media/entitlement-management-organization/organization-basics.png)

1. Sous l’onglet **Annuaire + domaine**, cliquez sur **Ajouter un annuaire + domaine** pour ouvrir le volet Sélectionner des annuaires et des domaines.

1. Entrez un nom de domaine pour rechercher le répertoire ou le domaine Azure AD. Vous devez entrer le nom de domaine complet.

1. Vérifiez qu’il s’agit de l’organisation correcte pour le nom et le type d’authentification fournis. La manière dont les utilisateurs se connectent dépend du type d’authentification.

    ![Ajouter une organisation connectée - Sélectionner des annuaires et des domaines](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Cliquez sur **Ajouter** pour ajouter l’annuaire ou domaine Azure AD. Actuellement, vous ne pouvez ajouter qu’un seul annuaire ou domaine Azure AD par organisation connectée.

    > [!NOTE]
    > Tous les utilisateurs de l’annuaire ou du domaine Azure AD seront en mesure de demander ce package d’accès. Cela comprend les utilisateurs Azure AD de tous les sous-domaines associés à l’annuaire, à moins que ces domaines soient bloqués par la liste d’autorisation ou de refus d’Azure B2B. Pour plus d’informations, consultez [Autoriser ou bloquer des invitations aux utilisateurs B2B à partir d’organisations spécifiques](../b2b/allow-deny-list.md).

1. Une fois que vous avez ajouté l’annuaire ou le domaine Azure AD, cliquez sur **Sélectionner**.

    L’organisation apparaît dans la liste.

    ![Ajouter une organisation connectée - Onglet Annuaires](./media/entitlement-management-organization/organization-directory-domain.png)

1. Sous l’onglet **Commanditaires**, ajoutez des commanditaires facultatifs pour cette organisation connectée.

    Les commanditaires sont des utilisateurs internes ou externes qui se trouvent déjà dans votre annuaire et qui constituent le point de contact pour la relation avec cette organisation connectée. Les commanditaires internes sont des utilisateurs membres de votre annuaire. Les commanditaires externes sont des utilisateurs invités de l’organisation connectée qui ont été invités précédemment et qui se trouvent déjà dans votre annuaire. Les commanditaires peuvent être utilisés en tant qu’approbateurs lorsque des utilisateurs de cette organisation connectée demandent l’accès à ce package d’accès. Pour plus d’informations sur l’invitation d’un utilisateur invité à votre annuaire, consultez [Ajout d'utilisateurs de collaboration Azure Active Directory B2B dans le portail Azure](../b2b/add-users-administrator.md).

    Lorsque vous cliquez sur **Ajouter/Supprimer**, un volet s’affiche pour sélectionner les commanditaires internes ou externes. Le volet affiche une liste non filtrée d’utilisateurs et de groupes dans votre annuaire.

    ![Package d’accès - Stratégie - Ajouter une organisation connectée - Onglet Commanditaires](./media/entitlement-management-organization/organization-sponsors.png)

1. Sous l’onglet **Vérifier + créer**, vérifiez les paramètres de votre organisation, puis cliquez sur **Créer**.

    ![Package d’accès - Stratégie - Ajouter une organisation connectée - Onglet Vérifier + créer](./media/entitlement-management-organization/organization-review-create.png)

## <a name="delete-a-connected-organization"></a>Supprimer une organisation connectée

Si vous n’avez plus de relation avec un annuaire ou un domaine Azure AD externe, vous pouvez supprimer l’organisation connectée.

**Rôle prérequis :** administrateur général, administrateur d’utilisateurs ou inviteur d’invités

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Organisations connectées**, puis cliquez sur pour ouvrir l’organisation connectée.

1. Dans la page Vue d’ensemble, cliquez sur **Supprimer** pour supprimer l’organisation connectée.

    Actuellement, vous ne pouvez supprimer une organisation connectée que si aucun utilisateur n’est connecté.

    ![Gouvernance des identités - Organisations connectées - Supprimer une organisation connectée](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Étapes suivantes

- [Régir l’accès des utilisateurs externes](entitlement-management-organization.md)
- [Pour les utilisateurs qui ne sont pas dans votre annuaire](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
