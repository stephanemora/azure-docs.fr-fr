---
title: Gérer l’accès externe avec l’accès conditionnel Azure Active Directory
description: Comment utiliser des stratégies d’accès conditionnel Azure Active Directory afin de sécuriser l’accès externe aux ressources.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64209a4d9ca200c69783a4ae317b38beef8ded89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222307"
---
# <a name="manage-external-access-with-conditional-access-policies"></a>Gérer l’accès externe avec les stratégies d’accès conditionnel 

L’[accès conditionnel](../conditional-access/overview.md) est l’outil qu’Azure AD utilise pour rassembler des signaux, appliquer des stratégies et déterminer si un utilisateur doit être autorisé à accéder à des ressources. Pour obtenir des informations détaillées sur la façon de créer et d’utiliser des stratégies d’accès conditionnel, consultez [Planifier un déploiement d’accès conditionnel](../conditional-access/plan-conditional-access.md). 

![Diagramme des signaux et des décisions d’accès conditionnel](media/secure-external-access//7-conditional-access-signals.png)



Cet article explique comment appliquer des stratégies d’accès conditionnel à des utilisateurs externes et il suppose que vous n’avez pas accès aux fonctionnalités de [gestion des droits d’utilisation](../governance/entitlement-management-overview.md). Des stratégies d’accès conditionnel peuvent être utilisées avec la gestion des droits d’utilisation.

Précédemment, dans cette série de documents, vous avez [créé un plan de sécurité](3-secure-access-plan.md) qui indiquait :

* Les applications et les ressources ont les mêmes exigences de sécurité et elles peuvent être regroupées pour l’accès.

* Les exigences de connexion pour les utilisateurs externes.

Vous allez utiliser ce plan pour créer vos stratégies d’accès conditionnel pour l’accès externe. 

> [!IMPORTANT]
> Créez quelques comptes de test d’utilisateur externe afin de pouvoir tester les stratégies que vous créez avant de les appliquer à tous les utilisateurs externes.

## <a name="conditional-access-policies-for-external-access"></a>Stratégies d’accès conditionnel pour l’accès externe

Les meilleures pratiques liées à l’administration de l’accès externe avec des stratégies d’accès conditionnel sont présentées ci-dessous.

* Si vous ne pouvez pas utiliser les organisations connectées dans la gestion des droits d’utilisation, créez un groupe de sécurité Azure AD ou un groupe Microsoft 365 pour chaque organisation partenaire avec laquelle vous travaillez. Affectez tous les utilisateurs de ce partenaire à ce groupe. Vous pouvez alors utiliser ces groupes dans des stratégies d’accès conditionnel.

* Créez autant de stratégies d’accès conditionnel que possible. Pour les applications qui ont les mêmes besoins d’accès, ajoutez-les toutes à la même stratégie.  
‎ 
   > [!NOTE]
   > Une stratégie d’accès conditionnel peut s’appliquer à un maximum de 250 applications. Si plus de 250 applications ont les mêmes besoins d’accès, créez des stratégies dupliquées. La stratégie A s’appliquera aux applications 1-250, la stratégie B aux applications 251-500, etc.

* Nommez clairement les stratégies spécifiques à l’accès externe à l’aide d’une convention d’affectation de noms. Une convention d’affectation de noms est ‎*ExternalAccess_actiontaken_AppGroup*. Par exemple, ExternalAccess_Block_FinanceApps.

## <a name="block-all-external-users-from-resources"></a>Bloquer l’accès aux ressources à tous les utilisateurs externes

Vous pouvez empêcher les utilisateurs externes d’accéder à des ensembles spécifiques de ressources grâce à des stratégies d’accès conditionnel. Une fois que vous avez déterminé l’ensemble des ressources dont vous souhaitez bloquer l’accès, créez une stratégie.

Pour créer une stratégie qui bloque l’accès des utilisateurs externes à un ensemble d’applications :

1. Accédez au **portail Azure** et sélectionnez **Azure Active Directory**, **Sécurité** et **Accès conditionnel**.

2. Sélectionnez **Nouvelle stratégie** et entrez un **nom**, par exemple ExternalAccess_Block_FinanceApps.

3. Sélectionnez **Utilisateurs et groupes**. Sous l’onglet Inclure, choisissez **Sélectionner des utilisateurs et des groupes**, puis sélectionnez **Tous les utilisateurs invités et externes**. 

4. Sélectionnez **Exclure** et entrez votre ou vos groupes d’administrateurs et tous les comptes d’accès d’urgence (de secours).

5. Sélectionnez **Applications ou actions cloud**, choisissez **Sélectionner les applications**, sélectionnez toutes les applications dont vous souhaitez bloquer l’accès externe, puis choisissez **Sélectionner**.

6. Sélectionnez **Conditions**, sélectionnez **Emplacements**, sous Configurer, sélectionnez **Oui**, puis sélectionnez **N’importe quel emplacement**.

7. Sous Contrôles d’accès, sélectionnez **Octroyer**, basculez le bouton sur **Bloquer** et choisissez **Sélectionner**.

8. Assurez-vous que le paramètre Activer la stratégie a la valeur **Rapport uniquement**, puis sélectionnez **Créer**.

## <a name="block-external-access-to-all-except-specific-external-users"></a>Bloquer l’accès externe à tous, à l’exception d’utilisateurs externes spécifiques

Il peut arriver que vous souhaitiez bloquer les utilisateurs externes, à l’exception d’un groupe spécifique. Par exemple, vous pouvez bloquer l’accès aux applications financières à tous les utilisateurs externes, à l’exception de ceux qui travaillent pour l’équipe financière. Pour ce faire :

1. Créez un groupe de sécurité destiné à accueillir les utilisateurs externes qui doivent accéder au groupe Finance.

2. Suivez les étapes 1-3 de la section Bloquer l’accès aux ressources à tous les utilisateurs externes ci-dessus.

3. À l’étape 4, ajoutez le groupe de sécurité que vous souhaitez exclure du blocage de l’accès aux applications financières.

4. Effectuez le reste des étapes.

## <a name="implement-conditional-access"></a>Implémenter un accès conditionnel

De nombreuses stratégies d’accès conditionnel courantes font l’objet d’une documentation. Consultez les documents suivants que vous pouvez adapter pour des utilisateurs externes.

* [Exiger l’authentification multifacteur pour tous les utilisateurs](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

* [Accès conditionnel basé sur les risques d’utilisateur](../conditional-access/howto-conditional-access-policy-risk-user.md)

* [Exiger une authentification multifacteur (MFA) pour l’accès à partir de réseaux non approuvés](../conditional-access/untrusted-networks.md) 

* [Exiger des conditions d’utilisation](../conditional-access/terms-of-use.md)

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur la sécurisation de l’accès externe aux ressources. Nous vous recommandons d’effectuer les actions dans l’ordre indiqué.

1. [Déterminer votre posture de sécurité souhaitée pour l’accès externe](1-secure-access-posture.md)

2. [Découvrir votre état actuel](2-secure-access-current-state.md)

3. [Créer un plan de gouvernance](3-secure-access-plan.md)

4. [Utiliser des groupes pour la sécurité](4-secure-access-groups.md)

5. [Transition vers Azure AD B2B](5-secure-access-b2b.md)

6. [Sécuriser l’accès avec la gestion des droits d’utilisation](6-secure-access-entitlement-managment.md)

7. [Sécuriser l’accès avec les stratégies d’accès conditionnel](7-secure-access-conditional-access.md) (Vous êtes ici.)

8. [Sécuriser l’accès avec des étiquettes de confidentialité](8-secure-access-sensitivity-labels.md)

9. [Sécuriser l’accès à Microsoft Teams, OneDrive et SharePoint](9-secure-access-teams-sharepoint.md)
