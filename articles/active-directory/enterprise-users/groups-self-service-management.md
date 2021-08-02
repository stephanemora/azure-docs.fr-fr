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
ms.date: 05/18/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47e3ea0a8ea5dc8dbb01d532a52436ed581311e7
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110089891"
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

    ![Paramètres généraux des groupes Azure Active Directory](./media/groups-self-service-management/groups-settings-general.png)

1. Définissez **Les propriétaires peuvent gérer les demandes d'appartenance au groupe dans le panneau d'accès** sur **Oui**.

1. Définissez l’option **Restreindre la capacité des utilisateurs à accéder aux groupes de fonctionnalités dans le volet d’accès** sur **Non**.

1. Si vous définissez **Les utilisateurs peuvent créer des groupes de sécurité dans les portails Azure, l’API ou PowerShell** ou **Les utilisateurs peuvent créer des groupes Microsoft 365 dans les portails Azure, l’API ou PowerShell** sur

    - **Oui** : Tous les utilisateurs de votre organisation Azure AD sont autorisés à créer des groupes de sécurité et à ajouter des membres à ces groupes dans les portails Azure, API ou PowerShell. Ces nouveaux groupes apparaissent également dans le volet d’accès de tous les autres utilisateurs. Si le paramètre de stratégie du groupe l’autorise, d’autres utilisateurs peuvent créer des demandes d’adhésion à ces groupes.
    - **Non** : Les utilisateurs ne peuvent pas créer de groupes, ni modifier les groupes existants dont ils sont propriétaires. Cependant, ils peuvent toujours gérer les appartenances de ces groupes et approuver les demandes d’adhésion d’autres utilisateurs à leurs groupes.

    Ces paramètres ont été récemment modifiés pour ajouter la prise en charge de l’API et de PowerShell. Pour plus d’informations sur cette modification, consultez la section suivante [Modification des paramètres des groupes](#groups-setting-change).

Vous pouvez également utiliser **Propriétaires qui peuvent attribuer des membres en tant que propriétaires de groupes dans le portail Azure** pour un contrôle d’accès plus granulaire en termes de gestion de groupes en libre-service pour vos utilisateurs.

Lorsque tous les utilisateurs peuvent créer des groupes, tous les utilisateurs de votre organisation sont autorisés à créer des groupes, puis à ajouter, comme le propriétaire par défaut, des membres à ces groupes. Vous ne pouvez pas spécifier les personnes qui peuvent créer leurs propres groupes. Vous ne pouvez spécifier une personne du groupe que si vous la promouvez propriétaire celui-ci.

> [!NOTE]
> Une licence Azure Active Directory Premium (P1 ou P2) est requise pour que les utilisateurs puissent demander à rejoindre un groupe de sécurité ou un groupe Microsoft 365 et pour permettre aux propriétaires d’approuver ou de refuser les demandes d’adhésion. Sans licence Azure Active Directory Premium, les utilisateurs peuvent toujours gérer leurs groupes dans le volet d’accès, mais ils ne peuvent pas créer de groupe qui nécessite l’approbation du propriétaire dans le volet d’accès ni demander à rejoindre un groupe.

## <a name="groups-setting-change"></a>Modification des paramètres des groupes

Les paramètres actuels des groupes de sécurité et des groupes Microsoft 365 sont obsolètes et ont été remplacés. Les paramètres actuels sont remplacés, car ils contrôlent uniquement la création de groupes dans les portails Azure et ne s’appliquent pas à l’API ou à PowerShell. Les nouveaux paramètres contrôlent la création de groupes dans les portails Azure, ainsi que dans l’API et PowerShell.

| Paramètre déconseillé | Nouveau paramètre |
| --- | --- |
| Les utilisateurs peuvent créer des groupes de sécurité dans les portails Azure | Les utilisateurs peuvent créer des groupes de sécurité dans les portails Azure, l’API ou PowerShell |
| Les utilisateurs peuvent créer des groupes Microsoft 365 dans les portails Azure | Les utilisateurs peuvent créer des groupes Microsoft 365 dans les portails Azure, l’API ou PowerShell |

Jusqu'à ce que le paramètre actuel soit complètement obsolète, les deux paramètres apparaîtront dans les portails Azure. Vous devez configurer ce nouveau paramètre avant la fin **mai 2021**. Pour configurer les paramètres des groupes de sécurité, le rôle d'administrateur global ou d'administrateur de rôle privilégié doit vous être attribué. 

![Modification du paramètre des Groupes de sécurité Azure Active Directory](./media/groups-self-service-management/security-groups-setting.png)

Le tableau suivant vous aide à déterminer les valeurs à choisir.

| Si vous souhaitez... | Choisissez ces valeurs |
| --- | --- |
| Les utilisateurs peuvent créer des groupes à l’aide des portails Azure, de l’API ou de PowerShell | Définissez les deux paramètres sur **Oui**. La prise en compte des modifications peut prendre jusqu’à 15 minutes. |
| Les utilisateurs **ne peuvent pas** créer des groupes à l’aide des portails Azure, de l’API ou de PowerShell | Définissez les deux paramètres sur **Non**. La prise en compte des modifications peut prendre jusqu’à 15 minutes. |
| Les utilisateurs peuvent créer des groupes à l’aide des portails Azure, mais pas en utilisant l’API ou PowerShell | Non prise en charge |
| Les utilisateurs peuvent créer des groupes à l’aide de l’API ou de PowerShell, mais pas en utilisant des portails Azure | Non prise en charge |

Le tableau suivant répertorie ce qui se passe pour les différentes valeurs de ces paramètres. Il n’est pas recommandé de définir le paramètre obsolète et le nouveau paramètre sur des valeurs différentes.

| Les utilisateurs peuvent créer des groupes à l’aide des portails Azure | Les utilisateurs peuvent créer des groupes à l’aide des portails Azure, de l’API ou de PowerShell | Effet sur votre locataire |
| :---: | :---: | --- |
| Oui | Oui | Les utilisateurs peuvent créer des groupes à l’aide des portails Azure, de l’API ou de PowerShell. La prise en compte des modifications peut prendre jusqu’à 15 minutes.|
| Non | Non | Les utilisateurs **ne peuvent pas** créer des groupes à l’aide des portails Azure, de l’API ou de PowerShell. La prise en compte des modifications peut prendre jusqu’à 15 minutes. |
| Oui | Non | Les utilisateurs **ne peuvent pas** créer des groupes à l’aide des portails Azure, de l’API ou de PowerShell. Il n’est pas recommandé de définir des valeurs différentes pour ces paramètres. La prise en compte des modifications peut prendre jusqu’à 15 minutes. |
| Non | Oui | Jusqu’à ce que le paramètre **Les utilisateurs peuvent créer des groupes à l’aide des portails Azure** soit entièrement obsolète en **juin 2021**, les utilisateurs peuvent créer des groupes à l’aide de l’API ou de PowerShell, mais pas en utilisant les portails Azure. À partir de **juin 2021**, le paramètre **Les utilisateurs peuvent créer des groupes à l’aide des portails Azure, de l’API ou de PowerShell** prendra effet et les utilisateurs pourront créer des groupes à l’aide des portails Azure, de l’API ou de PowerShell. |

## <a name="next-steps"></a>Étapes suivantes

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gérer l’accès aux ressources avec les groupes Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](../enterprise-users/groups-settings-cmdlets.md)
* [Gestion des applications dans Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Qu’est-ce qu’Azure Active Directory ?](../fundamentals/active-directory-whatis.md)
* [Intégrer des répertoires locaux à Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
