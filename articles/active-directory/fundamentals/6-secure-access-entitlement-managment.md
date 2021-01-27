---
title: Gérer l’accès externe avec Gestion des droits d'utilisation Azure Active Directory
description: Comment utiliser Gestion des droits d'utilisation Azure Active Directory dans le cadre de votre plan de sécurité d’accès externe global.
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
ms.openlocfilehash: f06a54f59405d9833194b2e7d4488bc93d2437ae
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725176"
---
# <a name="manage-external-access-with-entitlement-management"></a>Gérer l’accès externe avec Gestion des droits d’utilisation 


[Gestion des droits d'utilisation](../governance/entitlement-management-overview.md) est une fonctionnalité de gouvernance des identités qui permet aux organisations de gérer le cycle de vie des identités et des accès à grande échelle, en automatisant les workflows de requête d’accès, les attributions d’accès, les évaluations et l’expiration. Gestion des droits d'utilisation permet à des non-administrateurs délégués de créer des [packages d’accès](../governance/entitlement-management-overview.md) auxquels les utilisateurs externes d’autres organisations peuvent demander l’accès. Les workflows d’approbation à un et plusieurs index peuvent être configurés pour évaluer les demandes et [approvisionner](../governance/what-is-provisioning.md) les utilisateurs pour un accès limité dans le temps avec des révisions récurrentes. Gestion des droits d'utilisation permet l’approvisionnement et la suppression des privilèges d’accès des comptes externes basés sur des stratégies.

## <a name="key-concepts-for-enabling-entitlement-management"></a>Concepts clés pour l’activation de la Gestion des droits d'utilisation

Les concepts clés suivants sont importants à comprendre pour la Gestion des droits d'utilisation.

### <a name="access-packages"></a>Packages d’accès

Un [package d’accès](../governance/entitlement-management-overview.md) est la base de la Gestion des droits d'utilisation. Les packages d’accès sont des regroupements de ressources régies par la stratégie dont un utilisateur a besoin pour collaborer sur un projet ou effectuer d’autres tâches. Par exemple, un package d’accès peut inclure les éléments suivants :

* accès à des sites SharePoint spécifiques

* applications d’entreprise, y compris vos applications SaaS et internes personnalisées telles que Salesforce.

* Canaux Microsoft Teams.

* Groupes Microsoft 365. 

### <a name="catalogs"></a>Catalogues

Les packages d’accès résident dans des [catalogues](../governance/entitlement-management-catalog-create.md). Vous créez un catalogue lorsque vous souhaitez regrouper des ressources et packages d’accès liés et déléguer la capacité pour les gérer. Tout d’abord, vous ajoutez des ressources à un catalogue, puis vous pouvez ajouter ces ressources pour accéder aux packages. Par exemple, vous souhaiterez peut-être créer un catalogue « Finance » et [déléguer sa gestion](../governance/entitlement-management-delegate.md) à un membre de l’équipe finance. Cette personne peut ensuite [ajouter des ressources](../governance/entitlement-management-catalog-create.md), créer des packages d’accès et gérer l’approbation de l’accès à ces packages.

Le diagramme suivant montre un cycle de vie de gouvernance typique pour un utilisateur externe qui accède à un package d’accès qui dispose d’une expiration.

![Diagramme du cycle de gouvernance des utilisateurs externes.](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>Accès externe libre-service

Vous pouvez accéder aux packages d’accès par le biais du [Portail Mon accès Azure AD](../governance/entitlement-management-request-access.md) pour permettre aux utilisateurs externes de demander l’accès. Les stratégies déterminent qui est autorisé à demander un package d’accès. Vous spécifiez les personnes autorisées à demander le package d’accès :

* [Organisations connectées](../governance/entitlement-management-organization.md) spécifiques

* Toutes les organisations connectées configurées

* Tous les utilisateurs de n’importe quelle organisation

* Utilisateurs membres ou invités déjà présents dans votre abonné

### <a name="approvals"></a>Approbations   
Les packages d’accès peuvent inclure une approbation obligatoire pour l’accès. **Implémentez toujours les processus d’approbation pour les utilisateurs externes**. Les approbations peuvent être à index unique ou multi-index. Les approbations sont déterminées en fonction des stratégies. Si des utilisateurs internes et externes doivent accéder au même package, vous devrez probablement définir des stratégies d’accès différentes pour différentes catégories d’organisations connectées et pour les utilisateurs internes.

### <a name="expiration"></a>Expiration  
Les packages d’accès peuvent inclure une date d’expiration. L’expiration peut être définie sur un jour spécifique ou attribuer à l’utilisateur un nombre de jours d’accès spécifique. Lorsque le package d’accès expire et que l’utilisateur n’a pas d’autre accès, l’objet utilisateur invité B2B représentant l’utilisateur peut être supprimé ou bloqué à la connexion. Nous vous recommandons d’appliquer l’expiration des packages d’accès pour les utilisateurs externes. Tous les packages d’accès n’ont pas d’expiration. Pour ceux qui n’en ont pas, veillez à effectuer des révisions d’accès.

### <a name="access-reviews"></a>Révisions d’accès

Les packages d’accès peuvent exiger des [révisions d’accès](../governance/manage-guest-access-with-access-reviews.md) périodiques qui requièrent du propriétaire du package ou du responsable une certification du besoin d’accès continu des utilisateurs. 

Avant de configurer votre évaluation, déterminez ce qui suit.

* Qui

   * Quels sont les critères d’un accès continu ?

   * Qui sont les réviseurs spécifiés ?

* À quelle fréquence les révisions planifiées se produisent-elles ?

   * Les options intégrées sont mensuelles, trimestrielles, bi-annuelles ou annuelles. 

   * Nous vous recommandons d’utiliser des packages qui prennent en charge l’accès externe chaque trimestre ou plus fréquemment. 

 

> [!IMPORTANT]
> Les révisions d’accès des packages d’accès évaluent uniquement l’accès accordé via la Gestion des droits d'utilisation. Par conséquent, vous devez configurer d’autres processus pour évaluer tout accès fourni aux utilisateurs externes en dehors de la Gestion des droits d'utilisation.

Pour plus d’informations sur les révisions d’accès, consultez [Planification d’un déploiement de révisions d’accès Azure AD](../governance/deploy-access-reviews.md).

## <a name="using-automation-in-entitlement-management"></a>Utilisation de l’automatisation dans Gestion des droits d'utilisation

Vous pouvez exécuter les [fonctions de Gestion des droits d'utilisation à l’aide de Microsoft Graph](/graph/tutorial-access-package-api), notamment

* [Gérer les packages d'accès](/graph/api/resources/accesspackage?view=graph-rest-beta)

* [Gérer les révisions d’accès](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta)

* [Gérer les organisations connectées](/graph/api/resources/connectedorganization?view=graph-rest-beta)

* [Gérer les paramètres de Gestion des droits d'utilisation](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta)

## <a name="recommendations"></a>Recommandations 

Nous vous recommandons d’utiliser les pratiques pour régir l’accès externe avec la Gestion des droits d'utilisation.

**Pour les projets avec un ou plusieurs partenaires commerciaux, [Créez et utilisez des packages d’accès](../governance/entitlement-management-access-package-create.md) pour intégrer et configurer les accès utilisateur de ces partenaires aux ressources**. 

* Si vous avez déjà des utilisateurs B2B dans votre répertoire, vous pouvez également les attribuer directement aux packages d’accès appropriés.

* Vous pouvez attribuer l’accès dans le [Portail Azure](../governance/entitlement-management-access-package-assignments.md) ou via [Microsoft Graph](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta).

**Utilisez vos paramètres de gouvernance des identités pour supprimer les utilisateurs de votre répertoire lorsque leurs packages d’accès expirent**.

![Capture d’écran de la configuration de la gestion du cycle de vie des utilisateurs externes.](media/secure-external-access/6-manage-external-lifecycle.png)

Ces paramètres s’appliquent uniquement aux utilisateurs qui ont été intégrés via la Gestion des droits d'utilisation.

**[Déléguez la gestion des catalogues et des packages d’accès](../governance/entitlement-management-delegate.md) aux propriétaires d’entreprise disposant d’informations supplémentaires sur les utilisateurs à qui l’accès est autorisé**.

![Capture d’écran de la configuration d’un catalogue.](media/secure-external-access/6-catalog-management.png)

**‎[Appliquer l’expiration des packages d’accès](../governance/entitlement-management-access-package-lifecycle-policy.md) auxquels les utilisateurs externes ont accès.**


![Capture d’écran de la configuration de l’expiration du package d’accès.](media/secure-external-access/6-access-package-expiration.png)

* Si vous connaissez la date de fin d’un package d’accès basé sur un projet, utilisez la Date pour définir la date spécifique. 

* Dans le cas contraire, nous recommandons que l’expiration ne soit plus de 365 jours, sauf s’il s’agit d’un engagement sur plusieurs années.

* Autoriser les utilisateurs à étendre l'accès.

* Exiger une approbation pour accorder l'extension.

**[Appliquer les révisions d’accès des packages](../governance/manage-guest-access-with-access-reviews.md) pour éviter un accès inapproprié aux invités.**

![Capture d’écran de la création d’un nouveau package d'accès.](media/secure-external-access/6-new-access-package.png)

* Appliquez des révisions trimestrielles.

* Pour les projets sensibles à la conformité, définissez les réviseurs comme réviseurs spécifiques, plutôt que l’auto-évaluation pour des utilisateurs externes. Les utilisateurs gestionnaires de package d’accès constituent un bon point de départ pour les réviseurs. 

* Pour les projets moins sensibles, l’auto-évaluation attribuée aux utilisateurs permet d’éviter à l’organisation la charge de suppression de l’accès des utilisateurs n’appartenant plus à leur organisation d’origine.

Pour plus d’informations, consultez [Régir l’accès des utilisateurs externes dans la Gestion des droits d’utilisation Azure AD](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur la sécurisation de l’accès externe aux ressources. Nous vous recommandons d’effectuer les actions dans l’ordre indiqué.

1. [Déterminer votre posture de sécurité pour l’accès externe](1-secure-access-posture.md)

2. [Découvrir votre état actuel](2-secure-access-current-state.md)

3. [Créer un plan de gouvernance](3-secure-access-plan.md)

4. [Utiliser des groupes pour la sécurité](4-secure-access-groups.md)

5. [Transition vers Azure AD B2B](5-secure-access-b2b.md)

6. [Sécuriser l’accès avec la Gestion des droits d’utilisation](6-secure-access-entitlement-managment.md) (Vous êtes ici.)

7. [Sécuriser l’accès avec des stratégies d’Accès conditionnel](7-secure-access-conditional-access.md)

8. [Sécuriser l’accès avec des Étiquettes de confidentialité](8-secure-access-sensitivity-labels.md)

9. [Sécuriser l’accès à Microsoft Teams, OneDrive et SharePoint](9-secure-access-teams-sharepoint.md)

 

