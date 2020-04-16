---
title: Présentation de la gestion des droits d’utilisation - Azure AD
description: Obtenez une vue d’ensemble de la gestion des droits d'utilisation Azure Active Directory et découvrez comment l’utiliser pour gérer l’accès aux groupes, applications et sites SharePoint Online pour les utilisateurs internes et externes.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65100e8584eba5c31edc2f9fd8c57ad8bd14c0d1
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582540"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Présentation de la gestion des droits d’utilisation Azure AD

La gestion des droits d’utilisation Azure Active Directory (Azure AD) est une fonctionnalité de [gouvernance des identités](identity-governance-overview.md) qui permet aux organisations de gérer le cycle de vie des identités et des accès à grande échelle, en automatisant les workflows de requête d’accès, les attributions d’accès, les révisions et l’expiration.

Pour travailler, les employés d'une organisation doivent pouvoir accéder à différents groupes, applications et sites. La gestion de cet accès est complexe, car les exigences changent : de nouvelles applications sont ajoutées ou des utilisateurs ont besoin de droits d’accès supplémentaires.  Ce scénario se complexifie davantage quand vous collaborez avec des organisations extérieures : vous pouvez ne pas savoir quelles personnes dans l’autre organisation a besoin d’accéder aux ressources de votre organisation, et ces personnes ne pas savoir quels groupes, applications ou sites sont utilisés par votre organisation.

La gestion des droits d’utilisation Azure AD vous aide à gérer plus efficacement l’accès aux groupes, applications et sites SharePoint Online pour les utilisateurs internes, ainsi que pour les utilisateurs externes à votre organisation devant accéder à ces ressources.

## <a name="why-use-entitlement-management"></a>Pourquoi utiliser la gestion des droits d’utilisation ?

Les entreprises souvent font face à des défis lorsqu’il leur faut gérer l’accès des employés aux ressources, notamment :

- Les utilisateurs peuvent ne pas connaître l’accès dont ils ont besoin, et même s’ils le connaissent, peuvent rencontrer des difficultés pour trouver les personnes habilitées à approuver leur accès
- Après avoir localisé une ressource et obtenu l'accès correspondant, les utilisateurs peuvent y avoir accès plus longtemps que nécessaire à des fins professionnelles

Ces problèmes sont compliqués pour les utilisateurs qui ont besoin d’un accès à partir d’une autre organisation, comme les utilisateurs externes issus d’organisations de la chaîne logistique ou d’autres partenaires commerciaux. Par exemple :

- Aucune personne ne peut connaître tous les utilisateurs spécifiques des autres annuaires d’organisation en mesure de l’inviter
- Et même si elle était capable d’inviter ces utilisateurs, aucune personne dans cette organisation ne peut se souvenir de la façon dont gérer tous les accès utilisateur de manière cohérente

La gestion des droits d'utilisation Azure AD permet de relever ces défis.  Pour en savoir plus sur l’utilisation de la gestion des droits d’utilisation Azure AD par les clients, vous pouvez lire [l’étude de cas Avanade](https://customers.microsoft.com/story/avanade-professional-services-azure-canada) et [l’étude de cas Centrica](https://customers.microsoft.com/story/757467-centrica-energy-azure).  Cette vidéo fournit une vue d’ensemble de la gestion des droits d’utilisation et de son intérêt :

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>À quoi sert la gestion des droits d'utilisation ?

La gestion des droits d'utilisation offre différentes possibilités, notamment :

- Déléguer à des non-administrateurs la possibilité de créer des packages d’accès. Ces packages d’accès contiennent des ressources que les utilisateurs peuvent demander ; les gestionnaires délégués de package d’accès ont la possibilité de définir des stratégies avec des règles pour lesquelles les utilisateurs peuvent demander quelles personnes doivent approuver leur accès et quand l’accès expire.
- Sélectionner les organisations connectées dont les utilisateurs peuvent demander l’accès.  Lorsqu’un utilisateur, qui n’est pas encore dans votre annuaire demande l’accès, et qu’il est approuvé, il est automatiquement invité dans votre annuaire, et l’accès lui est affecté.  Lorsque son accès expire, s’il n’a pas d’autres affectations de package d’accès, son compte B2B dans votre annuaire peut être automatiquement supprimé.

Vous pouvez commencer en suivant notre [tutoriel pour créer votre premier package d’accès](entitlement-management-access-package-first.md). Vous pouvez également lire les [scénarios courants](entitlement-management-scenarios.md) ou regarder des vidéos, par exemple

- [Comment déployer la gestion des droits d’utilisation Azure AD dans votre organisation](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Comment superviser et mettre à l’échelle votre utilisation de la gestion des droits d’utilisation Azure AD](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Comment déléguer dans la gestion des droits d’utilisation](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Que sont les packages d’accès et quelles ressources gérer avec eux ?

La gestion des droits d’utilisation introduit sur Azure AD le concept de *package d’accès*. Un package d’accès regroupe toutes les ressources avec l’accès dont un utilisateur a besoin pour travailler sur un projet ou accomplir sa tâche. Les packages d'accès régissent l'accès de vos employés internes et des utilisateurs extérieurs à votre organisation.

 La gestion des droits d’utilisation vous permet de gérer l’accès utilisateur aux types de ressources ci-dessous :

- Appartenance des groupes de sécurité Azure AD
- Appartenance des groupes et équipes Office 365
- Affectation aux applications d’entreprise Azure AD, y compris aux applications SaaS et aux applications intégrées personnalisées prenant en charge la fédération/l’authentification unique et/ou le provisionnement
- Appartenance des sites SharePoint Online

Vous pouvez également contrôler l’accès à d’autres ressources qui s’appuient sur des groupes de sécurité Azure AD ou des groupes Office 365.  Par exemple :

- Vous pouvez accorder des licences aux utilisateurs pour Microsoft Office 365 en utilisant un groupe de sécurité Azure AD dans un package d’accès et en configurant la [gestion des licences par groupe](../users-groups-roles/licensing-groups-assign.md) pour ce groupe
- Vous pouvez accorder un accès aux utilisateurs pour gérer des ressources Azure en utilisant un groupe de sécurité Azure AD dans un package d'accès et en créant un [attribution de rôle Azure](../../role-based-access-control/role-assignments-portal.md) pour ce groupe

## <a name="how-do-i-control-who-gets-access"></a>Comment contrôler qui a accès ?

Avec un package d’accès, un administrateur ou un gestionnaire délégué de package d’accès liste les ressources (groupes, applications et sites) et les rôles dont les utilisateurs ont besoin pour ces ressources.

Les packages d’accès incluent également une ou plusieurs *stratégies*. Une stratégie définit les règles ou barrières mises en place pour l’affectation d’un package d’accès. Chaque stratégie peut être utilisée pour s’assurer que seuls les utilisateurs appropriés sont en mesure de demander l’accès, qu’il existe des approbateurs pour leur requête, et que leur accès à ces ressources est limité dans le temps et qu’il expirera s’il n’est pas renouvelé.

![Package d’accès et stratégies](./media/entitlement-management-overview/elm-overview-access-package.png)

Dans chaque stratégie, un administrateur ou un gestionnaire de package d’accès définit

- soit les utilisateurs déjà existants (généralement des employés ou des invités déjà conviés), soit les organisations partenaires d’utilisateurs externes, qui sont éligibles à la demande d’accès
- Le processus d’approbation et les utilisateurs autorisés à approuver ou refuser un accès
- La durée de l’affectation de l’accès d’un utilisateur, une fois approuvée, avant l’expiration de celle-ci

Le diagramme suivant montre un exemple des différents éléments en matière de gestion des droits d'utilisation. Il présente un catalogue contenant deux exemples de packages d’accès.

- Le **package d’accès 1** comprend un seul groupe en tant que ressource. L’accès est défini par une stratégie qui autorise un ensemble d’utilisateurs du répertoire à demander un accès.
- Le **package d’accès 2** comprend un groupe, une application et un site SharePoint Online en tant que ressources. L’accès est défini par deux stratégies différentes. La première stratégie autorise un ensemble d’utilisateurs du répertoire à demander un accès. La seconde stratégie permet aux utilisateurs d'un répertoire externe de demander un accès.

![Vue d'ensemble de la gestion des droits d’utilisation](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Quand utiliser des packages d’accès ?

Les packages d’accès ne remplacent pas d’autres mécanismes d’affectation d’accès.  Ils sont le plus appropriés dans des situations telles que les suivantes :

- Les employés ont besoin d’un accès limité dans le temps pour une tâche particulière.  Par exemple, vous pouvez utiliser la gestion de licences par groupe et un groupe dynamique pour vérifier que tous les employés disposent d’une boîte aux lettres Exchange Online, puis utiliser des packages d’accès pour les situations dans lesquelles les employés ont besoin d’un accès supplémentaire, par exemple pour lire les ressources d’un service à partir d’un autre service.
- L’accès doit être approuvé par un responsable de l’employé ou par d’autres personnes désignées.
- Les services souhaitent gérer leurs propres stratégies d’accès à leurs ressources sans implication informatique.  
- Au moins deux organisations travaillent en collaboration sur un projet et, par conséquent, plusieurs utilisateurs d’une organisation seront amenés à passer par Azure AD B2B pour accéder aux ressources d’une autre organisation.

## <a name="how-do-i-delegate-access"></a>Comment déléguer l’accès ?

 Les packages d’accès sont définis dans des conteneurs appelés *catalogues*.  Vous pouvez disposer d’un seul catalogue pour tous vos packages d’accès, ou vous pouvez désigner des personnes pour créer et posséder leurs propres catalogues. Un administrateur peut ajouter des ressources à n’importe quel catalogue, mais un non-administrateur ne peut ajouter à un catalogue que les ressources dont il est propriétaire. Un propriétaire de catalogue peut ajouter d’autres utilisateurs en tant que copropriétaires de catalogue, ou en tant que gestionnaires de package d’accès.  Ces scénarios sont décrits plus en détail dans l’article [Délégation et rôles dans la gestion des droits d’utilisation Azure AD](entitlement-management-delegate.md).

## <a name="summary-of-terminology"></a>Abrégé de terminologie

Pour mieux comprendre la gestion des droits d’utilisation et sa documentation, vous pouvez vous référer à la liste les termes suivants.

| Terme | Description |
| --- | --- |
| package d'accès | Bundle de ressources dont une équipe ou un projet a besoin et qui est régi par des stratégies. Un package d’accès est toujours contenu dans un catalogue. Vous créez un package d’accès pour un scénario dans lequel les utilisateurs doivent demander l’accès.  |
| demande d’accès | Demande d’accès aux ressources dans un package d’accès. Cette demande transite généralement par un flux d’approbation.  Si elle est approuvée, l’utilisateur demandeur reçoit une affectation de package d’accès. |
| affectation | L’affectation d’un package d’accès à un utilisateur garantit que l’utilisateur dispose de tous les rôles de ressources de ce package d’accès.  Les affectations de package d’accès ont généralement une durée limite avant leur expiration. |
| catalogue | Conteneur de ressources connexes et de packages d’accès.  Les catalogues sont utilisés pour la délégation, afin que les non-administrateurs puissent créer leurs propres packages d’accès. Les propriétaires de catalogue peuvent ajouter les ressources qu’ils possèdent à un catalogue. |
| créateur de catalogue | Regroupement d’utilisateurs autorisés à créer des catalogues.  Lorsqu’un utilisateur non-administrateur, autorisé à être créateur de catalogue, crée un catalogue, il devient automatiquement le propriétaire de ce catalogue. |
| organisation connectée | Domaine ou annuaire Azure AD externe avec lequel vous collaborez. Les utilisateurs provenant d’une organisation connectée peuvent être spécifiés dans une stratégie comme étant autorisés à demander l’accès. |
| policy | Ensemble de règles définissant le cycle de vie d’un accès, telles que le mode d’accès des utilisateurs, les approbateurs et la durée d’accès par le biais d’une affectation. Une stratégie est liée à un package d’accès. Par exemple, un package d’accès peut avoir deux stratégies de demande d’accès : l’une pour les employés, l’autre pour les utilisateurs externes. |
| resource | Ressource (un groupe Office, un groupe de sécurité, une application ou un site SharePoint Online, par exemple) dotée d’un rôle pour lequel un utilisateur peut obtenir des autorisations. |
| répertoire de ressources | Répertoire comprenant une ou plusieurs ressources à partager. |
| rôle de ressource | Collection d’autorisations associées à une ressource et définies par elle. Un groupe est doté de deux rôles : membre et propriétaire. Les sites SharePoint compte généralement trois rôles, mais ils peuvent bénéficier de rôles personnalisés supplémentaires. Les applications peuvent avoir plusieurs rôles personnalisés. |


## <a name="license-requirements"></a>Conditions de licence :

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Les clouds spécialisés, tels qu’Azure Allemagne et Azure Chine - 21Vianet, ne sont pas prêts à être utilisés actuellement.

### <a name="how-many-licenses-must-you-have"></a>De combien de licences avez-vous besoin ?

Vérifiez que votre annuaire comporte au moins autant de licences Azure AD Premium P2 que vous avez de :

- Utilisateurs membres qui **peuvent** demander un package d’accès.
- Utilisateurs membres et invités qui demandent un package d’accès.
- Utilisateurs membres et invités qui approuvent les demandes de package d’accès.
- Utilisateurs membres et invités qui ont une affectation directe à un package d’accès.

Les licences Azure AD Premium P2 ne sont **pas** nécessaires pour les tâches suivantes :

- Aucune licence n’est requise pour les utilisateurs ayant le rôle d’administrateur général qui configurent les catalogues initiaux, les packages d’accès et les stratégies et délèguent des tâches d’administration à d’autres utilisateurs.
- Aucune licence n’est requise pour les utilisateurs auxquels ont été délégués des tâches administratives, telles que le créateur du catalogue, le propriétaire du catalogue et le gestionnaire de package d’accès.
- Aucune licence n’est requise pour les invités qui **peuvent** demander des packages d’accès, mais ne demandent **pas** de package d’accès.

Pour chaque licence Azure AD Premium P2 payante que vous achetez pour vos utilisateurs membres (employés), vous pouvez utiliser Azure AD B2B pour inviter jusqu’à 5 utilisateurs invités. Ces utilisateurs invités peuvent également utiliser les fonctionnalités d’Azure AD Premium P2. Pour plus d’informations, consultez les conseils sur l’[affectation de licences Azure AD B2B Collaboration](../b2b/licensing-guidance.md).

Pour plus d’informations sur les licences, consultez [Attribuer ou supprimer des licences à l’aide du portail Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Exemples de scénarios de licence

Voici quelques exemples de scénarios de licence pour vous permettre de déterminer le nombre de licences dont vous devez disposer.

| Scénario | Calcul | Nombre de licences |
| --- | --- | --- |
| Un administrateur général de Woodgrove Bank crée des catalogues initiaux et délègue des tâches administratives à 6 autres utilisateurs. L’une des stratégies spécifie que **tous les employés** (2 000 employés) peuvent demander un ensemble spécifique de packages d’accès. 150 employés demandent les packages d’accès. | 2 000 employés qui **peuvent** demander les packages d’accès | 2 000 |
| Un administrateur général de Woodgrove Bank crée des catalogues initiaux et délègue des tâches administratives à 6 autres utilisateurs. L’une des stratégies spécifie que **tous les employés** (2 000 employés) peuvent demander un ensemble spécifique de packages d’accès. Une autre stratégie spécifie que certains utilisateurs des **Utilisateurs du partenaire Contoso** (invités) peuvent demander les mêmes packages d’accès soumis à approbation. Contoso a 30 000 utilisateurs. 150 employés demandent les packages d’accès et 10 500 utilisateurs de Contoso demandent un accès. | 2 000 employés + 500 utilisateurs invités de Contoso qui dépassent le ratio 1:5 (10 500-(2 000*5)) | 2 500 |

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Créer votre premier package d’accès](entitlement-management-access-package-first.md)
- [Scénarios courants](entitlement-management-scenarios.md)
