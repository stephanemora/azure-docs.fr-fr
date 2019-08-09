---
title: Présentation de la gestion des droits d’utilisation Azure AD (Préversion) - Azure Active Directory
description: Obtenez une vue d’ensemble de la gestion des droits d'utilisation Azure Active Directory et découvrez comment l’utiliser pour gérer l’accès aux groupes, applications et sites SharePoint Online pour les utilisateurs internes et externes.
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
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12452b4e9cc6caa64d4c81a310fbccb5d1717817
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678152"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Présentation de la gestion des droits d’utilisation Azure AD (Préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pour travailler, les employés d'une organisation doivent pouvoir accéder à différents groupes, applications et sites. Gérer cet accès n'est pas facile. La plupart du temps, aucune liste organisée ne répertorie toutes les ressources dont un utilisateur a besoin pour un projet. Le chef de projet a une bonne compréhension des ressources requises, des personnes impliquées et de la durée du projet. Pour autant, le chef de projet ne dispose généralement pas des autorisations nécessaires pour approuver ou accorder un accès à d'autres personnes. Ce scénario se complique lorsqu'il vous faut collaborer avec des personnes ou entreprises externes.

La gestion des droits d'utilisation Azure Active Directory (Azure AD) vous aide à gérer l'accès aux groupes, applications et sites SharePoint Online pour les utilisateurs internes et externes à votre organisation.

## <a name="why-use-entitlement-management"></a>Pourquoi utiliser la gestion des droits d’utilisation ?

Les entreprises font souvent face à des défis lorsqu'il leur faut gérer l'accès aux ressources, notamment :

- Les utilisateurs peuvent ne pas connaître l'accès dont ils ont besoin
- Les utilisateurs peuvent avoir du mal à localiser les bonnes personnes ou les bonnes ressources
- Après avoir localisé une ressource et obtenu l'accès correspondant, les utilisateurs peuvent y avoir accès plus longtemps que nécessaire à des fins professionnelles

Ces problèmes sont complexes pour les utilisateurs qui ont besoin d'un accès à partir d'un autre répertoire, tels que les utilisateurs externes issus d'organisations de la chaîne logistique ou d'autres partenaires commerciaux. Par exemple :

- Les organisations peuvent ne pas connaître toutes les personnes spécifiques d'autres répertoires pour les inviter
- Et même si les organisations étaient en mesure d'inviter ces utilisateurs, il n'est pas certain qu'elles penseraient à gérer tous les accès des utilisateurs de manière cohérente

La gestion des droits d'utilisation Azure AD permet de relever ces défis.

## <a name="what-can-i-do-with-entitlement-management"></a>À quoi sert la gestion des droits d'utilisation ?

La gestion des droits d'utilisation offre différentes possibilités, notamment :

- Créer des packages de ressources associées que les utilisateurs peuvent demander
- Définir des règles sur la manière de demander des ressources et l'expiration de l'accès correspondant
- Gérer le cycle de vie de l'accès pour les utilisateurs internes et externes
- Déléguer la gestion des ressources
- Désigner des personnes chargées d'approuver les demandes
- Créer des rapports pour effectuer le suivi de l’historique

Pour une vue d’ensemble de la gouvernance des identités et de la gestion des droits d'utilisation, regardez la vidéo suivante de la conférence Ignite 2018 :

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Quelles ressources puis-je gérer ?

La gestion des droits d'utilisation vous permet de gérer l'accès aux types de ressources ci-dessous :

- Groupes de sécurité Azure AD
- Groupes Office 365
- Applications d’entreprise Azure AD, y compris les applications SaaS et les applications intégrées personnalisées prenant en charge la fédération ou l’approvisionnement
- Sites et collections de sites SharePoint Online

Vous pouvez également contrôler l’accès à d’autres ressources s'appuyant sur des groupes de sécurité Azure AD ou Office 365.  Par exemple :

- Vous pouvez accorder des licences aux utilisateurs pour Microsoft Office 365 en utilisant un groupe de sécurité Azure AD dans un package d’accès et en configurant la [gestion des licences par groupe](../users-groups-roles/licensing-groups-assign.md) pour ce groupe
- Vous pouvez accorder un accès aux utilisateurs pour gérer des ressources Azure en utilisant un groupe de sécurité Azure AD dans un package d'accès et en créant un [attribution de rôle Azure](../../role-based-access-control/role-assignments-portal.md) pour ce groupe

## <a name="what-are-access-packages-and-policies"></a>Quels sont les packages d'accès et les stratégies ?

La gestion des droits d'utilisation introduit le concept de *package d'accès*. Un package d'accès regroupe toutes les ressources dont un utilisateur a besoin pour travailler sur un projet ou exercer ses fonctions. Ces ressources incluent un accès à des groupes, applications ou sites. Les packages d'accès régissent l'accès de vos employés internes et des utilisateurs extérieurs à votre organisation. Les packages d’accès sont définis dans des conteneurs appelés *catalogues*.

Les packages d’accès incluent également une ou plusieurs *stratégies*. Une stratégie définit les règles ou barrières mises en place pour accéder à un package d’accès. Lorsqu'une stratégie est activée, seuls les utilisateurs appropriés se voient accorder l'accès aux bonnes ressources pendant le bon laps de temps.

![Package d’accès et stratégies](./media/entitlement-management-overview/elm-overview-access-package.png)

Moyennant un package d’accès et les stratégies correspondantes, le gestionnaire de package d'accès définit ce qui suit :

- Ressources
- Rôles dont les utilisateurs ont besoin pour les ressources
- Utilisateurs internes et organisations partenaires d'utilisateurs externes autorisés à demander un accès
- Processus d’approbation et utilisateurs autorisés à approuver ou refuser un accès
- Durée d'accès d'un utilisateur

Le diagramme suivant montre un exemple des différents éléments en matière de gestion des droits d'utilisation. Il porte sur deux exemples de packages d'accès.

- Le **package d’accès 1** comprend un seul groupe en tant que ressource. L’accès est défini par une stratégie qui autorise un ensemble d’utilisateurs du répertoire à demander un accès.
- Le **package d’accès 2** comprend un groupe, une application et un site SharePoint Online en tant que ressources. L’accès est défini par deux stratégies différentes. La première stratégie autorise un ensemble d’utilisateurs du répertoire à demander un accès. La seconde stratégie permet aux utilisateurs d'un répertoire externe de demander un accès.

![Vue d'ensemble de la gestion des droits d’utilisation](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>Utilisateurs externes

Lorsque vous utilisez l'expérience d'invitation [Azure AD entreprise à entreprise (B2B)](../b2b/what-is-b2b.md), vous devez déjà connaître les adresses e-mail des utilisateurs invités externes que vous souhaitez importer dans votre répertoire de ressources et avec lesquels collaborer. Cela fonctionne très bien lorsque vous travaillez sur un projet plus petit ou à court terme et que vous connaissez déjà tous les participants, mais s'avère plus complexe si vous souhaitez collaborer avec un grand nombre d'utilisateurs ou si les participants changent au fil du temps.  Par exemple, vous pouvez travailler avec une autre organisation et avoir un point de contact avec cette organisation, mais au fil du temps, d'autres utilisateurs de cette organisation auront également besoin d'un accès.

Avec la gestion des droits d'utilisation, vous pouvez définir une stratégie autorisant les utilisateurs des organisations que vous spécifiez, et qui utilisent également Azure AD, à demander un package d'accès. Vous pouvez spécifier si une approbation est requise, ainsi qu'une date d’expiration de l’accès. Si une approbation est requise, vous pouvez également désigner un ou plusieurs utilisateurs de l'organisation externe que vous avez précédemment invités en tant qu'approbateurs, ceux-ci étant susceptibles de savoir quels utilisateurs externes à leur organisation ont besoin d'un accès. Une fois le package d'accès configuré, vous pouvez envoyer le lien correspondant à la personne à contacter au sein de l'organisation externe. Cette personne peut le partager avec d'autres utilisateurs de l'organisation externe et ceux-ci peuvent à leur tour utiliser ce lien pour demander le package d'accès.  Les utilisateurs de cette organisation déjà invités dans votre répertoire peuvent également utiliser ce lien.

Lorsqu'une demande est approuvée, la gestion des droits d'utilisation accorde à l'utilisateur l'accès nécessaire, notamment une invitation si cet utilisateur n'est pas encore présent dans votre répertoire. Azure AD crée automatiquement un compte B2B pour cet utilisateur.  Notez qu’un administrateur peut avoir précédemment restreint les organisations autorisées à collaborer, en définissant une [liste d'autorisations ou de refus B2B](../b2b/allow-deny-list.md) pour autoriser ou refuser les invitations liées à d’autres organisations.  Si l’utilisateur ne figure pas sur la liste d'autorisations ou de refus, il ne sera pas invité.

Pour limiter dans le temps l'accès de l'utilisateur externe, vous spécifiez une date d'expiration dans la stratégie, comme 180 jours. Une fois ces 180 jours écoulés et en l'absence de renouvellement, la gestion des droits d'utilisateur supprime tous les accès associés à ce package d'accès.  Si l'utilisateur invité par le biais de la gestion des droits d'utilisation ne dispose d'aucune autre attribution de package d'accès, lorsqu'il perd sa dernière attribution, son compte B2B est bloqué pendant 30 jours, puis supprimé.  Et ce, afin d'éviter la prolifération de comptes inutiles.  

## <a name="terminology"></a>Terminologie

Pour mieux comprendre la gestion des droits d'utilisation et sa documentation, vous devez connaître les termes suivants.

| Terme ou concept | Description |
| --- | --- |
| gestion des droits d’utilisation | Service permettant d'attribuer, de révoquer et d'administrer les packages d'accès. |
| package d'accès | Ensemble d'autorisations et de stratégies liées à des ressources que les utilisateurs peuvent demander. Un package d’accès est toujours contenu dans un catalogue. |
| demande d’accès | Demande d'accès à un package d’accès. Cette demande transite généralement par un flux de travail. |
| policy | Ensemble de règles définissant le cycle de vie d'un accès, telles que le mode d'accès des utilisateurs, les approbateurs et la durée des accès. À titre d'exemple, il peut s'agir de l'accès des employés et de l’accès externe. |
| catalog | Conteneur de ressources connexes et de packages d’accès. |
| catalogue Général | Catalogue intégré toujours disponible. L'ajout de ressources au catalogue Général implique certaines autorisations. |
| resource | Ressource ou service (par exemple, un groupe Office, un groupe de sécurité, une application ou un site SharePoint Online) auquel un utilisateur peut accorder des autorisations. |
| type de ressource | Type de ressource incluant des groupes, applications et sites SharePoint Online. |
| rôle de ressource | Ensemble d’autorisations associées à une ressource. |
| répertoire de ressources | Répertoire comprenant une ou plusieurs ressources à partager. |
| utilisateurs attribués | Attribution d’un package d’accès à un utilisateur pour lui permettre de disposer de tous les rôles de ressources de ce package d’accès. |
| enable | Processus permettant aux utilisateurs de demander un package d'accès. |

## <a name="license-requirements"></a>Conditions de licence :

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Les clouds spécialisés, tels que Azure Government, Azure Allemagne et Azure China 21Vianet, ne peuvent pas être utilisés dans cette préversion.

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Créer votre premier package d’accès](entitlement-management-access-package-first.md)
- [Scénarios courants](entitlement-management-scenarios.md)
