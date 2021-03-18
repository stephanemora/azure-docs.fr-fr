---
title: Utilisateurs et groupes dans une stratégie d’accès conditionnel – Azure Active Directory
description: Qui sont des utilisateurs et des groupes dans une stratégie d’accès conditionnel Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d5671626e05145f79c3ad5fc0ecdb5628ac9421
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102427128"
---
# <a name="conditional-access-users-and-groups"></a>Accès conditionnel : Utilisateurs et groupes

Une stratégie d’accès conditionnel doit inclure une attribution d’utilisateur comme l’un des signaux dans le processus de décision. Les utilisateurs peuvent être inclus ou exclus des stratégies d’accès conditionnel. Azure Active Directory évalue toutes les stratégies et vérifie que toutes les conditions requises sont remplies avant d’accorder l’accès à l’utilisateur. En plus de cet article, nous avons une vidéo expliquant comment [inclure ou exclure des utilisateurs des stratégies d’accès conditionnel](https://www.youtube.com/watch?v=5DsW1hB3Jqs), et qui vous guide tout au long de la procédure décrite ci-dessous. 

![Utilisateur comme signal dans les décisions prises par l’accès conditionnel](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Inclure des utilisateurs

Cette liste d’utilisateurs comprend généralement tous les utilisateurs ciblés par une organisation dans une stratégie d’accès conditionnel. 

Les options suivantes sont disponibles pour l’inclusion lors de la création d’une stratégie d’accès conditionnel.

- None
   - Aucun utilisateur sélectionné
- tous les utilisateurs
   - Tous les utilisateurs qui existent dans le répertoire, y compris les invités B2B.
- Sélection de l’option Utilisateurs et groupes
   - Tous les utilisateurs invités et externes
      - Cette sélection inclut tous les invités B2B et les utilisateurs externes, y compris tout utilisateur dont l’attribut `user type` est défini sur `guest`. Cette sélection s’applique également à tout utilisateur externe connecté à partir d’une autre organisation, telle qu’un fournisseur de solutions Cloud (CSP). 
   - Rôles d’annuaire
      - Permet aux administrateurs de sélectionner des rôles d’annuaire Azure AD spécifiques utilisés pour déterminer l’attribution. Par exemple, les organisations peuvent créer une stratégie plus restrictive sur les utilisateurs qui ont le rôle d’administrateur général.
   - Utilisateurs et groupes
      - Permet le ciblage d’ensembles spécifiques d’utilisateurs. Par exemple, les organisations peuvent sélectionner un groupe qui contient tous les membres du service RH lorsqu’une application RH est sélectionnée en tant qu’application cloud. Un groupe peut être n’importe quel type de groupe dans Azure AD, y compris les groupes de sécurité et de distribution dynamiques ou affectés. La stratégie sera appliquée aux utilisateurs et groupes imbriqués.

> [!IMPORTANT]
> Lorsque vous sélectionnez les utilisateurs et les groupes inclus dans une stratégie d’accès conditionnel, il existe une limite au nombre d’utilisateurs individuels qui peuvent être ajoutés directement à une stratégie d’accès conditionnel. Si le nombre d’utilisateurs individuels qui doivent être ajoutés directement à une stratégie d’accès conditionnel est important, nous vous recommandons de placer les utilisateurs dans un groupe et d’attribuer le groupe à la stratégie d’accès conditionnel à la place.

> [!WARNING]
> Si des utilisateurs ou des groupes sont membres de plus de 2 048 groupes, leur accès peut être bloqué. Cette limite s’applique à l’appartenance de groupe directe et imbriquée.

> [!WARNING]
> Les stratégies d’accès conditionnel ne prennent pas en charge les utilisateurs affectés à un rôle d’annuaire [étendue à une unité administrative](../roles/admin-units-assign-roles.md) ou à des rôles d’annuaire étendus directement à un objet, par exemple via [des rôles personnalisés](../roles/custom-create.md).

## <a name="exclude-users"></a>Exclure des utilisateurs

Lorsque les organisations incluent et excluent à la fois un utilisateur ou un groupe, l’utilisateur ou le groupe est exclu de la stratégie, car une action d’exclusion l’emporte sur une action d’inclusion dans la stratégie. Les exclusions sont couramment utilisées pour les comptes d’accès d’urgence ou les comptes de secours. Pour plus d’informations sur les comptes d’accès d’urgence et la raison pour laquelle ils sont importants, consultez les articles suivants : 

* [Gérer des comptes d’accès d’urgence dans Azure AD](../roles/security-emergency-access.md)
* [Créer une stratégie de gestion du contrôle d'accès résiliente avec Azure Active Directory](../authentication/concept-resilient-controls.md)

Les options suivantes sont disponibles pour l’exclusion lors de la création d’une stratégie d’accès conditionnel.

- Tous les utilisateurs invités et externes
   - Cette sélection inclut tous les invités B2B et les utilisateurs externes, y compris tout utilisateur dont l’attribut `user type` est défini sur `guest`. Cette sélection s’applique également à tout utilisateur externe connecté à partir d’une autre organisation, telle qu’un fournisseur de solutions Cloud (CSP). 
- Rôles d’annuaire
   - Permet aux administrateurs de sélectionner des rôles d’annuaire Azure AD spécifiques utilisés pour déterminer l’attribution. Par exemple, les organisations peuvent créer une stratégie plus restrictive sur les utilisateurs qui ont le rôle d’administrateur général.
- Utilisateurs et groupes
   - Permet le ciblage d’ensembles spécifiques d’utilisateurs. Par exemple, les organisations peuvent sélectionner un groupe qui contient tous les membres du service RH lorsqu’une application RH est sélectionnée en tant qu’application cloud. Un groupe peut être n’importe quel type de groupe dans Azure AD, y compris les groupes de sécurité et de distribution dynamiques ou affectés.

### <a name="preventing-administrator-lockout"></a>Empêcher le verrouillage de l’administrateur

Pour empêcher qu’un administrateur ne se verrouille hors de son répertoire lors de la création d’une stratégie appliquée à **tous les utilisateurs** et **toutes les applications**, l’avertissement suivant s’affiche.

> Ne vous enfermez pas dehors ! Nous vous recommandons d’appliquer d’abord une stratégie à un petit ensemble d’utilisateurs pour vérifier qu’elle fonctionne comme prévu. Nous vous recommandons également d’exclure au moins un administrateur de cette stratégie. Cette opération garantit que vous disposez toujours d’un accès et que vous pouvez mettre à jour une stratégie si une modification est nécessaire. Vérifiez les utilisateurs et les applications concernés.

Par défaut, la stratégie fournit une option permettant d’exclure l’utilisateur actuel de la stratégie, mais cette valeur par défaut peut être remplacée par l’administrateur, comme indiqué dans l’image suivante. 

![Attention, ne vous enfermez pas dehors !](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

[Que faire si votre accès au portail Azure est verrouillé ?](troubleshoot-conditional-access.md#what-to-do-if-you-are-locked-out-of-the-azure-portal)

## <a name="next-steps"></a>Étapes suivantes

- [Accès conditionnel : applications ou actions cloud](concept-conditional-access-cloud-apps.md)

- [Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)
