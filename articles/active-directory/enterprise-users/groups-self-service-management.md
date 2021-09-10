---
title: Configurer la gestion de groupes en libre-service - Azure Active Directory | Microsoft Docs
description: Créer et gérer des groupes de sécurité ou Microsoft 365 dans Azure Active Directory et demander des appartenances aux groupes de sécurité ou Microsoft 365
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 07/27/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bff97bd767bb9045c5fa018bbcbffc2cb7445ca1
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778052"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Configurer la gestion de groupes en libre-service dans Azure Active Directory 

Vous pouvez autoriser les utilisateurs à créer et gérer leurs propres groupes de sécurité ou Microsoft 365 dans Azure Active Directory (Azure AD). Le propriétaire du groupe peut approuver ou refuser des demandes d’appartenance et peut déléguer le contrôle de l’appartenance au groupe. Les fonctionnalités de gestion de groupes en libre-service ne sont pas disponibles pour les groupes de sécurité activés pour la messagerie électronique ou les listes de distribution.

## <a name="self-service-group-membership-defaults"></a>Valeurs par défaut de l’appartenance de groupes en libre-service

Lorsque des groupes de sécurité sont créés dans le Portail Azure ou en utilisant Azure AD PowerShell, seuls les propriétaires du groupe peuvent mettre à jour leur appartenance. Les groupes de sécurité créés en libre-service dans le [volet Accès](https://account.activedirectory.windowsazure.com/r#/joinGroups) et tous les groupes Microsoft 365 sont disponibles pour tous les utilisateurs, qu’ils soient approuvés par le propriétaire ou approuvés automatiquement. Dans le volet Accès, vous pouvez modifier les options d’appartenance lorsque vous créez le groupe.

Groupes créés dans | Comportement par défaut des groupes de sécurité | Comportement par défaut des groupes Microsoft 365
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](../enterprise-users/groups-settings-cmdlets.md) | Seuls les propriétaires peuvent ajouter des membres<br>Visible mais non disponible pour se joindre au panneau Accès | Ouvert pour tous les utilisateurs
[Azure portal](https://portal.azure.com) | Seuls les propriétaires peuvent ajouter des membres<br>Visible mais non disponible pour se joindre au panneau Accès<br>Propriétaire n’est pas affecté automatiquement lors de la création de groupe | Ouvert pour tous les utilisateurs
[Volet d’accès](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Ouvert pour tous les utilisateurs<br>Les options d’appartenance peuvent être modifiées lorsque le groupe est créé. | Ouvert pour tous les utilisateurs<br>Les options d’appartenance peuvent être modifiées lorsque le groupe est créé.

## <a name="self-service-group-management-scenarios"></a>Scénarios de gestion de groupes en libre-service

* **Gestion de groupes déléguée** : prenons l’exemple d’un administrateur qui gère l’accès à une application SaaS utilisée dans son entreprise. La gestion de ces droits d’accès devenant fastidieuse, cet administrateur demande au propriétaire de l’entreprise d’ajouter un nouveau groupe. L’administrateur accorde l’accès à l’application au nouveau groupe et ajoute au groupe toutes les personnes accédant déjà à l’application. Le propriétaire de l’entreprise peut ainsi ajouter d’autres utilisateurs, qui sont automatiquement approvisionnés dans l’application. Il n’a pas besoin d’attendre que l’administrateur gère l’accès des utilisateurs. Si l’administrateur accorde la même autorisation à un responsable d’un autre groupe de l’entreprise, cette personne peut également gérer l’accès de ses propres membres de groupe. Le propriétaire de l’entreprise ne peut ni visualiser ni gérer les appartenances de groupe, et inversement. L’administrateur a toujours la possibilité de voir l’ensemble des utilisateurs ayant accès à l’application et, si nécessaire, de bloquer les droits d’accès.
* **Gestion de groupes en libre-service** Prenons l’exemple de deux utilisateurs disposant tous deux d’un site SharePoint Online. Ils les gèrent indépendamment. Cependant, ils veulent les rendre accessibles à chacune des deux équipes. Pour cela, ils peuvent créer un groupe dans Azure AD, puis, dans SharePoint Online, chacun d’eux choisit ce même groupe pour lui donner accès aux deux sites. Ainsi, quand une personne souhaite obtenir un accès, elle en effectue la demande dans le volet d’accès, et dès que sa demande a été approuvée, elle obtient automatiquement un accès aux deux sites SharePoint Online. Par la suite, l’un des deux utilisateurs décide que toutes les personnes ayant accès au site doivent également pouvoir accéder à une application SaaS. L’administrateur de l’application SaaS peut ajouter des droits d’accès à l’application pour le site SharePoint Online. Les demandes qu’il approuvera par la suite donneront ainsi accès non seulement aux deux sites SharePoint Online, mais également à cette application SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Mettre un groupe à disposition en libre-service pour un utilisateur

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com) à l’aide d’un compte auquel a été attribué le rôle d’administrateur général ou d’administrateur de rôle privilégié pour l’annuaire.

1. Sélectionnez **Groupes**, puis les paramètres **Généraux**.

    ![Paramètres généraux des groupes Azure Active Directory.](./media/groups-self-service-management/groups-settings-general.png)

1. Définissez **Les propriétaires peuvent gérer les demandes d'appartenance au groupe dans le panneau d'accès** sur **Oui**.

1. Définissez l’option **Restreindre la capacité des utilisateurs à accéder aux groupes de fonctionnalités dans le volet d’accès** sur **Non**.

1. Définissez **Les utilisateurs peuvent créer des groupes de sécurité dans les portails Azure, l’API ou PowerShell** sur **Oui** ou **Non**.

    Pour plus d’informations sur ce paramètre, consultez la section suivante [Paramètres de groupe](#group-settings).

1. Définissez **Les utilisateurs peuvent créer des groupes Microsoft 365 dans les portails Azure, l’API ou PowerShell** sur **Oui** ou **Non**.

    Pour plus d’informations sur ce paramètre, consultez la section suivante [Paramètres de groupe](#group-settings).

Vous pouvez également utiliser **Propriétaires qui peuvent attribuer des membres en tant que propriétaires de groupes dans le portail Azure** pour un contrôle d’accès plus granulaire en termes de gestion de groupes en libre-service pour vos utilisateurs.

Lorsque tous les utilisateurs peuvent créer des groupes, tous les utilisateurs de votre organisation sont autorisés à créer des groupes, puis à ajouter, comme le propriétaire par défaut, des membres à ces groupes. Vous ne pouvez pas spécifier les personnes qui peuvent créer leurs propres groupes. Vous ne pouvez spécifier une personne du groupe que si vous la promouvez propriétaire celui-ci.

> [!NOTE]
> Une licence Azure Active Directory Premium (P1 ou P2) est requise pour que les utilisateurs puissent demander à rejoindre un groupe de sécurité ou un groupe Microsoft 365 et pour permettre aux propriétaires d’approuver ou de refuser les demandes d’adhésion. Sans licence Azure Active Directory Premium, les utilisateurs peuvent toujours gérer leurs groupes dans le volet d’accès, mais ils ne peuvent pas créer de groupe qui nécessite l’approbation du propriétaire dans le volet d’accès ni demander à rejoindre un groupe.

## <a name="group-settings"></a>Paramètres de groupe

Les paramètres de groupe permettent de contrôler qui peut créer des groupes de sécurité et des groupes Microsoft 365.

![Modification du paramètre des Groupes de sécurité Azure Active Directory.](./media/groups-self-service-management/security-groups-setting.png)

> [!NOTE]
> Le comportement de ces paramètres a été modifié récemment. Vérifiez que ces paramètres ont été configurés pour votre organisation. Pour plus d’informations, consultez [Pourquoi les paramètres de groupe ont-ils changé ?](#why-were-the-group-settings-changed).

 Le tableau suivant vous aide à déterminer les valeurs à choisir.

| Paramètre | Valeur | Effet sur votre locataire |
| --- | :---: | --- |
| Les utilisateurs peuvent créer des groupes de sécurité dans les portails Azure, l’API ou PowerShell | Oui | Tous les utilisateurs de votre organisation Azure AD sont autorisés à créer des groupes de sécurité et à ajouter des membres à ces groupes dans les portails Azure, l’API ou PowerShell. Ces nouveaux groupes apparaissent également dans le volet d’accès de tous les autres utilisateurs. Si le paramètre de stratégie du groupe l’autorise, d’autres utilisateurs peuvent créer des demandes d’adhésion à ces groupes. |
|  | Non | Les utilisateurs ne peuvent pas créer de groupes de sécurité, ni modifier les groupes existants dont ils sont propriétaires. Cependant, ils peuvent toujours gérer les appartenances de ces groupes et approuver les demandes d’adhésion d’autres utilisateurs à leurs groupes. |
| Les utilisateurs peuvent créer des groupes Microsoft 365 dans les portails Azure, l’API ou PowerShell | Oui | Tous les utilisateurs de votre organisation Azure AD sont autorisés à créer des groupes Microsoft 365 et à ajouter des membres à ces groupes dans les portails Azure, l’API ou PowerShell. Ces nouveaux groupes apparaissent également dans le volet d’accès de tous les autres utilisateurs. Si le paramètre de stratégie du groupe l’autorise, d’autres utilisateurs peuvent créer des demandes d’adhésion à ces groupes. |
|  | Non | Les utilisateurs ne peuvent pas créer de groupes Microsoft 365, ni modifier les groupes existants dont ils sont propriétaires. Cependant, ils peuvent toujours gérer les appartenances de ces groupes et approuver les demandes d’adhésion d’autres utilisateurs à leurs groupes. |

Voici quelques détails supplémentaires sur ces paramètres de groupe.

- Ces paramètres peuvent mettre jusqu’à 15 minutes pour être appliqués.
- Si vous souhaitez permettre à certains de vos utilisateurs de créer des groupes, vous pouvez attribuer à ces utilisateurs un rôle comme [Administrateur de groupes](../roles/permissions-reference.md#groups-administrator).
- Ces paramètres sont destinés aux utilisateurs et ne concernent pas les principaux de service. Par exemple, si vous avez un principal de service disposant des autorisations pour créer des groupes, même si vous définissez ces paramètres sur **Non**, le principal de service sera toujours en mesure de créer des groupes. 

### <a name="why-were-the-group-settings-changed"></a>Pourquoi les paramètres de groupe ont-ils changé ?

Dans l’implémentation précédente, les paramètres de groupe s’appelaient **Les utilisateurs peuvent créer des groupes de sécurité dans les portails Azure** et **Les utilisateurs peuvent créer des groupes Microsoft 365 dans les portails Azure**. Les paramètres précédents ne contrôlaient que la création des groupes dans le portail Azure et ne s’appliquaient pas aux API ni à PowerShell. Les nouveaux paramètres contrôlent la création de groupes dans les portails Azure, ainsi que dans l’API et dans PowerShell. Les nouveaux paramètres sont plus sécurisés.

Les valeurs par défaut des nouveaux paramètres ont été affectées aux valeurs précédentes de l’API ou de PowerShell. Il est possible que les valeurs par défaut des nouveaux paramètres soient différentes de celles des valeurs précédentes qui contrôlaient uniquement le comportement du portail Azure. À partir de mai 2021, il y a eu une période de transition de quelques semaines durant laquelle vous pouviez sélectionner la valeur par défaut de votre choix pour que les nouveaux paramètres soient pris en compte. Maintenant que les nouveaux paramètres ont été officiellement ajoutés, vous devez vérifier qu’ils ont bien été configurés pour votre organisation.

## <a name="next-steps"></a>Étapes suivantes

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gérer l’accès aux ressources avec les groupes Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](../enterprise-users/groups-settings-cmdlets.md)
* [Gestion des applications dans Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Qu’est-ce qu’Azure Active Directory ?](../fundamentals/active-directory-whatis.md)
* [Intégrer des répertoires locaux à Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
