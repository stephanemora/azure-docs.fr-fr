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
ms.date: 01/22/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ecf3a8819518c674a3d8bd7af55d1a3c6393c42
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77483854"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Ajouter une organisation connectée dans la gestion des droits d’utilisation Azure AD

La gestion des droits d’utilisation Azure vous permet de collaborer avec des personnes extérieures à votre organisation. Si vous collaborez fréquemment avec des utilisateurs dans un annuaire ou domaine Azure AD externe, vous pouvez les ajouter en tant qu’organisation connectée. Cet article explique comment ajouter une organisation connectée afin de permettre aux utilisateurs extérieurs à votre organisation de demander des ressources dans votre annuaire.

## <a name="what-is-a-connected-organization"></a>Qu’est-ce qu’une organisation connectée ?

Une organisation connectée est un domaine ou un annuaire Azure AD externe avec lequel vous collaborez.

Par exemple, supposons que vous travaillez chez Woodgrove Bank et que vous souhaitez collaborer avec deux organisations externes. Ces deux organisations ont des configurations différentes :

- Graphic Design Institute utilise Azure AD et ses utilisateurs ont un nom d’utilisateur principal qui se termine par `graphicdesigninstitute.com`
- Contoso n’utilise pas encore Azure AD. Les utilisateurs Contoso ont un nom d’utilisateur principal qui se termine par `contoso.com`.

Dans ce cas, vous pouvez configurer deux organisations connectées. Vous créez une organisation connectée pour Graphic Design Institute et une autre pour Contoso. Si vous ajoutez ensuite ces deux organisations connectées à une stratégie, les utilisateurs de chaque organisation avec un nom d’utilisateur principal qui correspond à la stratégie peuvent demander des packages d’accès. Les utilisateurs qui ont un nom d’utilisateur principal avec le domaine graphicdesigninstitute.com correspondent à l’organisation connectée Graphical Design Institute et sont autorisés à envoyer des demandes, et les utilisateurs qui ont un nom d’utilisateur principal avec le domaine contoso.com correspondent à l’organisation connectée Contoso et sont aussi autorisés à demander des packages. De plus, étant donné que Graphic Design Institute utilise Azure AD, tous les utilisateurs avec un nom principal correspondant à un [domaine vérifié](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) ajouté à leur locataire, par exemple graphicdesigninstitute.exemple, peuvent également demander des packages d’accès à l’aide de la même stratégie.

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

## <a name="update-a-connected-organization"></a>Mettre à jour une organisation connectée 

Si l’organisation connectée change de domaine et que vous avez un nouveau nom pour cette organisation ou souhaitez modifier les sponsors, vous pouvez mettre à jour l’organisation connectée.

**Rôle prérequis :** administrateur général, administrateur d’utilisateurs ou inviteur d’invités

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Organisations connectées**, puis cliquez sur pour ouvrir l’organisation connectée.

1. Dans la page de présentation, cliquez sur **Modifier** pour modifier le nom ou la description de l’organisation.  

1. Dans la page Répertoire + domaine, cliquez sur **mettre à jour le répertoire et le domaine** pour basculer vers un autre répertoire ou domaine.

1. Dans la page Sponsors, cliquez sur **Ajouter des sponsors internes** ou **Ajouter des sponsors externes** pour ajouter un utilisateur en tant que sponsor.  Pour supprimer un sponsor, cliquez dessus, puis, dans le menu de droite, cliquez sur **Supprimer**.


## <a name="delete-a-connected-organization"></a>Supprimer une organisation connectée

Si vous n’avez plus de relation avec un annuaire ou un domaine Azure AD externe, vous pouvez supprimer l’organisation connectée.

**Rôle prérequis :** administrateur général, administrateur d’utilisateurs ou inviteur d’invités

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Organisations connectées**, puis cliquez sur pour ouvrir l’organisation connectée.

1. Dans la page Vue d’ensemble, cliquez sur **Supprimer** pour supprimer l’organisation connectée.

    Actuellement, vous ne pouvez supprimer une organisation connectée que si aucun utilisateur n’est connecté.

    ![Gouvernance des identités - Organisations connectées - Supprimer une organisation connectée](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Étapes suivantes

- [Régir l’accès des utilisateurs externes](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Pour les utilisateurs qui ne sont pas dans votre annuaire](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
