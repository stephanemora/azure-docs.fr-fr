---
title: Utilisateurs et groupes dans une stratégie d’accès conditionnel – Azure Active Directory
description: Qui sont des utilisateurs et des groupes dans une stratégie d’accès conditionnel Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36898e75680771a9cb084fa142bb635ddbf51c70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192046"
---
# <a name="conditional-access-users-and-groups"></a>Accès conditionnel : Utilisateurs et groupes

Une stratégie d’accès conditionnel doit inclure une attribution d’utilisateur comme l’un des signaux dans le processus de décision. Les utilisateurs peuvent être inclus ou exclus des stratégies d’accès conditionnel. 

![Utilisateur comme signal dans les décisions prises par l’accès conditionnel](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Inclure des utilisateurs

Cette liste d’utilisateurs comprend généralement tous les utilisateurs ciblés par une organisation dans une stratégie d’accès conditionnel. 

Les options suivantes sont disponibles pour l’inclusion lors de la création d’une stratégie d’accès conditionnel.

- None
   - Aucun utilisateur sélectionné
- tous les utilisateurs
   - Tous les utilisateurs qui existent dans le répertoire, y compris les invités B2B.
- Sélection de l’option Utilisateurs et groupes
   - Tous les utilisateurs invités et externes (préversion)
      - Cette sélection inclut tous les invités B2B et les utilisateurs externes, y compris tout utilisateur dont l’attribut `user type` est défini sur `guest`. Cette sélection s’applique également à tout utilisateur externe connecté à partir d’une autre organisation, telle qu’un fournisseur de solutions Cloud (CSP). 
   - Rôles d’annuaire (préversion)
      - Permet aux administrateurs de sélectionner des rôles d’annuaire Azure AD spécifiques utilisés pour déterminer l’attribution. Par exemple, les organisations peuvent créer une stratégie plus restrictive sur les utilisateurs qui ont le rôle d’administrateur général.
   - Utilisateurs et groupes
      - Permet le ciblage d’ensembles spécifiques d’utilisateurs. Par exemple, les organisations peuvent sélectionner un groupe qui contient tous les membres du service RH lorsqu’une application RH est sélectionnée en tant qu’application cloud. Un groupe peut être n’importe quel type de groupe dans Azure AD, y compris les groupes de sécurité et de distribution dynamiques ou affectés.

## <a name="exclude-users"></a>Exclure des utilisateurs

Les exclusions sont couramment utilisées pour les comptes d’accès d’urgence ou les comptes de secours. Pour plus d’informations sur les comptes d’accès d’urgence et la raison pour laquelle ils sont importants, consultez les articles suivants : 

* [Gérer des comptes d’accès d’urgence dans Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Créer une stratégie de gestion du contrôle d'accès résiliente avec Azure Active Directory](../authentication/concept-resilient-controls.md)

Les options suivantes sont disponibles pour l’exclusion lors de la création d’une stratégie d’accès conditionnel.

- Tous les utilisateurs invités et externes (préversion)
   - Cette sélection inclut tous les invités B2B et les utilisateurs externes, y compris tout utilisateur dont l’attribut `user type` est défini sur `guest`. Cette sélection s’applique également à tout utilisateur externe connecté à partir d’une autre organisation, telle qu’un fournisseur de solutions Cloud (CSP). 
- Rôles d’annuaire (préversion)
   - Permet aux administrateurs de sélectionner des rôles d’annuaire Azure AD spécifiques utilisés pour déterminer l’attribution. Par exemple, les organisations peuvent créer une stratégie plus restrictive sur les utilisateurs qui ont le rôle d’administrateur général.
- Utilisateurs et groupes
   - Permet le ciblage d’ensembles spécifiques d’utilisateurs. Par exemple, les organisations peuvent sélectionner un groupe qui contient tous les membres du service RH lorsqu’une application RH est sélectionnée en tant qu’application cloud. Un groupe peut être n’importe quel type de groupe dans Azure AD, y compris les groupes de sécurité et de distribution dynamiques ou affectés.

## <a name="next-steps"></a>Étapes suivantes

- [Accès conditionnel : applications ou actions cloud](concept-conditional-access-cloud-apps.md)

- [Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)
