---
title: Planifier un déploiement de Privileged Identity Management ? – Azure | Microsoft Docs
description: Découvrez comment déployer Privileged Identity Management (PIM) dans votre organisation Azure AD.
services: active-directory
documentationcenter: ''
author: BarbaraSelden
manager: martinco
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: baselden
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99bdfeff59f26f59c9d64bcca9226d9b1f70ec1d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525208"
---
# <a name="plan-a-privileged-identity-management-deployment"></a>Planifier un déploiement de Privileged Identity Management

**Privileged Identity Management (PIM)** assure une activation de rôle basée sur l’heure et l’approbation pour atténuer les risques d’autorisations d’accès excessives, injustifiées ou malveillantes à des ressources importantes. Ces ressources incluent des ressources dans Azure Active Directory (Azure AD), Azure et d’autres services en ligne Microsoft tels que Microsoft 365 ou Microsoft Intune. Vous pouvez également utiliser PIM avec des applications de service (SaaS).

PIM vous permet d’autoriser un ensemble spécifique d’actions au niveau d’une étendue particulière. Ses caractéristiques principales sont les suivantes :

* Fournir un accès privilégié **juste-à-temps** aux ressources

* Attribuer les **conditions d’appartenance ou de propriété** des groupes d’accès privilégié.

* Affecter un accès aux ressources **limité dans le temps** à l’aide de dates de début et de fin

* Exiger une **approbation** pour activer les rôles privilégiés

* Appliquer l’**authentification multifacteur** pour l’activation des rôles

* Utiliser la **justification** pour comprendre le motif d’activation des utilisateurs

* Recevoir des **notifications** lors de l’activation de rôles privilégiés

* Effectuer des **révisions d’accès** pour vérifier que les utilisateurs ont toujours besoin de leurs rôles

* Télécharger l’**historique des audits** (internes ou externes)

Pour tirer le meilleur parti de ce plan de déploiement, il est important que vous ayez une vue d’ensemble complète de [ce qu’est Privileged Identity Management](pim-configure.md).

## <a name="understand-pim"></a>Comprendre PIM

Les concepts de PIM présentés dans cette section vous aideront à comprendre les besoins de votre organisation en matière d’identité privilégiée.

### <a name="what-can-you-manage-in-pim"></a>Ce que vous pouvez gérer dans PIM

Aujourd’hui, vous pouvez utiliser PIM avec :

* **Rôles Azure AD** : parfois appelés rôles d’annuaire, les rôles Azure AD incluent des rôles intégrés et personnalisés permettant de gérer Azure AD et d’autres services en ligne Microsoft 365.

* **Rôles Azure** : rôles de contrôle d’accès en fonction du rôle (RBAC) dans Azure qui accordent l’accès à des groupes d’administration, des abonnements, des groupes de ressources et des ressources.

* **Groupes d’accès privilégié** : pour configurer l’accès juste-à-temps au rôle de membre et de propriétaire d’un groupe de sécurité Azure AD. Les groupes d’accès privilégié vous offrent non seulement un autre moyen de configurer PIM pour les rôles Azure AD et les rôles Azure, mais également de configurer PIM pour d’autres autorisations sur des services en ligne de Microsoft comme Intune, Azure Key Vault et Azure Information Protection. 

Vous pouvez assigner les éléments suivants à ces rôles ou groupes : 

* **Utilisateurs** : pour bénéficier d’un accès juste-à-temps aux rôles Azure AD, aux rôles Azure et aux groupes d’accès privilégié. 

* **Groupes** : toute personne d’un groupe pour obtenir un accès juste-à-temps aux rôles Azure AD et aux rôles Azure. Pour les rôles Azure AD, le groupe doit être un groupe de clouds nouvellement créé et marqué comme pouvant être affecté à un rôle, tandis que pour les rôles Azure, le groupe peut être n’importe quel groupe de sécurité Azure AD. Il est déconseillé d’assigner ou d’incorporer un groupe à des groupes d’accès privilégié. 

> [!NOTE] 
>Vous ne pouvez pas désigner des principaux de service comme admissibles aux rôles Azure AD, aux rôles Azure et aux groupes d’accès privilégié, mais vous pouvez accorder une affectation active limitée dans le temps aux trois.

### <a name="principle-of-least-privilege"></a>Principe du privilège minimum

Vous attribuez aux utilisateurs le rôle comportant [le moins de privilèges nécessaires à l’exécution de leurs tâches](../roles/delegate-by-task.md). Cette pratique réduit le nombre d’administrateurs généraux et utilise à la place des rôles d’administrateur spécifiques pour certains scénarios.

> [!NOTE] 
> Microsoft compte très peu d’administrateurs généraux. Apprenez-en davantage sur [la façon dont Microsoft utilise Privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access).

### <a name="type-of-assignments"></a>Type d’affectations 

Il existe deux types d’affectations : **admissible** et **actif**. Lorsqu’un utilisateur devient éligible pour un rôle, il peut l’activer pour réaliser des tâches privilégiées. 

Vous pouvez également définir une heure de début et de fin pour chaque type d’affectation. Cet ajout vous donne quatre types d’affectations possibles :

* Admissible en permanence

* Active en permanence

* Admissible limitée dans le temps, avec les dates de début et de fin spécifiées pour l’affectation

* Active limitée dans le temps, avec les dates de début et de fin spécifiées pour l’attribution

En cas d’expiration du rôle, vous pouvez **prolonger** ou **renouveler** ces affectations. 

**Nous vous recommandons** de ne conserver aucune affectation active en permanence pour les rôles autres que les [deux comptes d’accès d’urgence de secours](../roles/security-emergency-access.md) recommandés, qui doivent avoir le rôle permanent Administrateur général. 

## <a name="plan-the-project"></a>Planifier le projet

Les échecs de projets informatiques, lorsqu’ils se produisent, proviennent généralement d’une disparité entre les attentes et l’impact, les responsabilités et les résultats. Pour éviter ces écueils, [assurez-vous de faire appel aux bonnes personnes](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders), et que les rôles des parties prenantes soient bien compris.

### <a name="plan-a-pilot"></a>Prévoir un pilote

À chaque étape de votre déploiement, assurez-vous que les évaluations effectuées donnent les résultats attendus. Consultez [Meilleures pratiques pour un pilote](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot).

* Commencez par un petit ensemble d’utilisateurs (groupe pilote) et vérifiez que PIM se comporte comme prévu.

* Vérifiez si toutes les configurations que vous avez mises en place pour les rôles ou les groupes d’accès privilégié fonctionnent correctement. 

* Déployez-la en production uniquement après avoir effectué des tests approfondis. 

### <a name="plan-communications"></a>Planifier les communications

La communication est essentielle à la réussite de tout nouveau service. Communiquez de manière proactive avec vos utilisateurs sur ce qui va changer, à quel moment les changements seront appliqués et comment ils peuvent obtenir de l’aide en cas de problème.

Prenez rendez-vous avec votre support informatique interne pour le guider dans le flux de travail de PIM. Fournissez-lui la documentation appropriée et vos coordonnées.

## <a name="plan-testing-and-rollback"></a>Planifier les tests et la restauration

> [!NOTE] 
> Pour les rôles Azure AD, les organisations testent et déploient souvent les administrateurs généraux en premier, tandis que pour les ressources Azure, elles testent généralement PIM un abonnement Azure à la fois. 

### <a name="plan-testing"></a>Planifier les tests

Créez des utilisateurs de test pour vérifier que les paramètres PIM fonctionnent comme prévu avant d’avoir un impact sur les utilisateurs réels et de risquer d’interrompre leur accès aux applications et aux ressources. Construisez un plan de test pour comparer les résultats attendus et les résultats réels. 

Le tableau suivant présente un exemple de test : 

| Role| Comportement attendu lors de l’activation| Résultats réels |
| --- | --- | --- |
|Administrateur général| <li> Exiger une authentification multifacteur <br><li>  Exiger une approbation <br><li>  L’approbateur reçoit une notification et peut approuver <br><li>  Le rôle expire au bout d’un délai prédéfini|

Pour les rôles Azure AD et les rôles de ressource Azure, assurez-vous que des utilisateurs sont représentés et qu’ils assumeront ces rôles. En outre, tenez compte des rôles suivants lorsque vous testez PIM dans votre environnement intermédiaire :

| Rôles| Rôles Azure AD| Rôles Ressource Azure| Groupes d’accès privilégié |
| --- | --- | --- |--- |
| Membre d’un groupe| | | x |
| Membres d’un rôle| x| x|  |
| Propriétaire du service informatique| x| | x |
| Propriétaire de l’abonnement ou de la ressource| | x| x |
| Propriétaire du groupe d’accès privilégié| | | x |

### <a name="plan-rollback"></a>Planifier la restauration

Si PIM ne fonctionne pas comme vous le souhaitez dans l’environnement de production, vous pouvez modifier l’attribution de rôle d’admissible à active une nouvelle fois. Pour chaque rôle que vous avez configuré, sélectionnez le bouton de sélection (…) pour tous les utilisateurs dont le type d’affectation est **admissible**. Vous pouvez ensuite sélectionner l’option **Rendre active** pour revenir en arrière et rendre l’attribution de rôle **active**.

## <a name="plan-and-implement-pim-for-azure-ad-roles"></a>Planifier et implémenter PIM pour les rôles Azure AD

Suivez ces tâches pour préparer PIM à gérer des rôles Azure AD. 

### <a name="discover-and-mitigate-privileged-roles"></a>Découvrir et atténuer les rôles privilégiés

Listez les utilisateurs avec des rôles privilégiés dans votre organisation. Passez en revue les utilisateurs affectés, identifiez les administrateurs qui n’ont plus besoin du rôle et supprimez-les de leurs affectations. 

Vous pouvez utiliser des [révisions d’accès des rôles Azure AD](pim-how-to-start-security-review.md) pour automatiser la détection, l’examen et l’approbation ou la suppression des attributions.

### <a name="determine-roles-to-be-managed-by-pim"></a>Déterminer les rôles à gérer par PIM

Protégez en priorité les rôles Azure AD qui comportent le plus d’autorisations. Il est également important de prendre en compte quelles données et autorisations sont les plus sensibles pour votre organisation. 

Tout d’abord, assurez-vous que tous les rôles d’administrateur général et de sécurité sont gérés par PIM, car ce sont les utilisateurs qui causer le plus de dommages lorsqu’ils sont compromis. Envisagez ensuite d’autres rôles à gérer qui pourraient être vulnérables aux attaques.

### <a name="configure-pim-settings-for-azure-ad-roles"></a>Configurer les paramètres PIM pour les rôles Azure AD

[Rédigez et configurez vos paramètres PIM](pim-how-to-change-default-settings.md) pour chaque rôle Azure AD privilégié que votre organisation utilise. 

Le tableau suivant présente des exemples de paramètres :

| Role| Exiger une authentification multifacteur| Notification| Ticket d’incident| Exiger une approbation| Approbateur| Durée d’activation| Administrateur permanent |
| --- | --- | --- |--- |--- |--- |--- |--- |
| Administrateur général| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| Autre administrateur général| 1 heure| Comptes d’accès d’urgence |
| Administrateur Exchange| :heavy_check_mark:| :heavy_check_mark:| :x:| :x:| None| 2 heures| None |
| Administrateur du support technique| :x:| :x:| :heavy_check_mark:| :x:| None| 8 heures| None |


### <a name="assign-and-activate-azure-ad-roles"></a>Attribuer et activer des rôles Azure AD 

Pour les rôles Azure AD dans PIM, seul un utilisateur qui détient le rôle Administrateur de rôle privilégié ou Administrateur général peut gérer les attributions des autres administrateurs. Les administrateurs généraux, les administrateurs de la sécurité, les lecteurs généraux et les lecteurs de la sécurité peuvent aussi consulter les attributions de rôles Azure AD dans PIM. 

Suivez les instructions des liens ci-dessous :

1. [Donnez des affectations admissibles](pim-how-to-add-role-to-user.md).

2. [Autorisez les utilisateurs admissibles à activer leur rôle Azure AD juste-à-temps](pim-how-to-activate-role.md).

Lorsque le rôle approche de son expiration, [utilisez PIM pour prolonger ou renouveler les rôles](pim-resource-roles-renew-extend.md). Ces deux actions utilisateur exigent l’approbation d’un Administrateur général ou d’un Administrateur de rôle privilégié.  Ces deux actions utilisateur exigent l’approbation d’un Administrateur général ou d’un Administrateur de rôle privilégié. 

Lorsque ces événements importants se produisent dans des rôles Azure AD, PIM [envoie des notifications par e-mail et des e-mails de synthèse hebdomadaire](pim-email-notifications.md) aux administrateurs de privilèges en fonction du rôle, de l’événement et des paramètres de notification. Ces e-mails peuvent également inclure des liens vers des tâches appropriées, comme l’activation ou le renouvellement d’un rôle. 

> [!NOTE] 
>Vous pouvez également effectuer ces tâches PIM [à l’aide des API Microsoft Graph pour les rôles Azure AD](pim-apis.md). 

### <a name="approve-or-deny-pim-activation-requests"></a>Approuver ou refuser des demandes d’activation PIM 

Un approbateur délégué reçoit une notification par e-mail lorsqu’une demande est en attente d’approbation. Procédez comme suit pour [approuver ou refuser les demandes d’activation d’un rôle de ressource Azure](pim-resource-roles-approval-workflow.md).

### <a name="view-audit-history-for-azure-ad-roles"></a>Voir l’historique d'audit pour les rôles Azure AD

[Affichez l’historique d’audit de toutes les attributions et activations de rôles](pim-how-to-use-audit-log.md) au cours des 30 derniers jours pour les rôles Azure AD. Vous pouvez accéder aux journaux d’audit si vous êtes un administrateur général ou un administrateur de rôle privilégié. 

**Nous vous recommandons** d’avoir au moins un administrateur pour lire tous les événements d’audit une fois par semaine et exporter vos événements d’audit tous les mois.

### <a name="security-alerts-for-azure-ad-roles"></a>Alertes de sécurité pour les rôles Azure AD

[Configurez des alertes de sécurité pour les rôles Azure AD](pim-how-to-configure-security-alerts.md) qui déclencheront une alerte en cas d’activité suspecte et non sécurisée.

## <a name="plan-and-implement-pim-for-azure-resource-roles"></a>Planifier et implémenter PIM pour les rôles de ressources Azure

Suivez ces tâches pour préparer PIM à gérer des rôles de ressource Azure.

### <a name="discover-and-mitigate-privileged-roles"></a>Découvrir et atténuer les rôles privilégiés

Réduisez au minimum les attributions de rôles Propriétaire et Administrateur de l’accès utilisateur attachées à chaque abonnement ou ressource et supprimez les attributions inutiles.

En tant qu’administrateur général, vous pouvez [élever l’accès pour gérer tous les abonnements Azure](../../role-based-access-control/elevate-access-global-admin.md). Vous pouvez ensuite rechercher le propriétaire de chaque abonnement et travailler avec lui pour supprimer les attributions inutiles au sein de ses abonnements.

Utilisez les [révisions d’accès pour les ressources Azure](pim-resource-roles-start-access-review.md) afin de vérifier et de supprimer les attributions de rôles inutiles. 

### <a name="determine-roles-to-be-managed-by-pim"></a>Déterminer les rôles à gérer par PIM

Lorsque vous décidez quelles attributions de rôles doivent être gérées à l’aide de PIM pour les ressources Azure, vous devez tout d’abord identifier les [groupes d’administration](../../governance/management-groups/overview.md), les abonnements, les groupes de ressources et les ressources qui sont essentiels pour votre organisation. Envisagez d’utiliser des groupes d’administration pour organiser toutes les ressources au sein de votre organisation.

**Nous vous recommandons** de gérer tous les rôles Propriétaire d’abonnement et Administrateur de l’accès utilisateur à l’aide de PIM. 

Collaborez avec les propriétaires d’abonnements pour documenter les ressources gérées par chaque abonnement et classer le niveau de risque de chaque ressource en cas de compromission. Donnez la priorité à la gestion des ressources avec PIM en fonction du niveau de risque. Cela inclut également les ressources personnalisées attachées à l’abonnement.

**Nous vous recommandons également** de collaborer avec les propriétaires des abonnements ou des ressources des services critiques afin de configurer un flux de travail PIM pour tous les rôles au sein des abonnements ou des ressources sensibles.

Pour les abonnements ou ressources qui ne sont pas aussi critiques, vous n’avez pas besoin de configurer PIM pour tous les rôles. Toutefois, vous devez toujours protéger les rôles Propriétaire et Administrateur de l’accès utilisateur avec PIM.

### <a name="configure-pim-settings-for-azure-resource-roles"></a>Configurer les paramètres PIM pour les rôles de ressources Azure

[Rédigez et configurez les paramètres](pim-resource-roles-configure-role-settings.md) pour les rôles de ressource Azure que vous avez prévu de protéger avec PIM. 

Le tableau suivant présente des exemples de paramètres :

| Role| Exiger une authentification multifacteur| Notification| Exiger une approbation| Approbateur| Durée d’activation| Administrateur actif| Expiration active| Expiration éligible|
| --- | --- | --- |--- |--- |--- |--- |---|---|
| Propriétaire d’abonnements critiques| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| Autres propriétaires de l’abonnement| 1 heure| None| n/a| 3 mois |
| Administrateur de l’accès utilisateur d’abonnements moins critiques| :heavy_check_mark:| :heavy_check_mark:| :x:| None| 1 heure| None| n/a| 3 mois |

### <a name="assign-and-activate-azure-resource-role"></a>Attribuer et activer le rôle de ressource Azure

Pour les rôles de ressource Azure dans PIM, seul un propriétaire ou un administrateur de l’accès utilisateur peut gérer les affectations d’autres administrateurs. Par défaut, les utilisateurs qui possèdent un rôle d’administrateur de rôle privilégié, d’administrateur de la sécurité ou de lecteur de la sécurité ne peuvent pas consulter les affectations aux rôles de ressource Azure.

Suivez les instructions des liens ci-dessous :

1. [Donnez des affectations admissibles](pim-resource-roles-assign-roles.md).

2. [Autorisez les utilisateurs admissibles à activer leurs rôles Azure juste-à-temps](pim-resource-roles-activate-your-roles.md).

Lorsque l’attribution de rôle privilégié approche de son expiration, [utilisez PIM pour prolonger ou renouveler les rôles](pim-resource-roles-renew-extend.md). Ces deux actions initiées par l’utilisateur nécessitent une approbation du propriétaire de la ressource ou de l’administrateur de l’accès utilisateur. 

Lorsque ces événements importants se produisent dans les rôles de ressource Azure, PIM envoie des [notifications par e-mail](pim-email-notifications.md) aux propriétaires et aux administrateurs de l’accès utilisateur. Ces e-mails peuvent également inclure des liens vers des tâches appropriées, comme l’activation ou le renouvellement d’un rôle.

>[!NOTE]
>Vous pouvez également effectuer ces tâches PIM [à l’aide des API Microsoft Azure Resource Manager pour les rôles de ressource Azure](pim-apis.md). 

### <a name="approve-or-deny-pim-activation-requests"></a>Approuver ou refuser des demandes d’activation PIM

[Approuver ou refuser les demandes d’activation pour les rôles Azure AD](azure-ad-pim-approval-workflow.md) : Un approbateur délégué reçoit une notification par e-mail lorsqu’une demande est en attente d’approbation.

### <a name="view-audit-history-for-azure-resource-roles"></a>Afficher l’historique d’audit pour les rôles de ressource Azure

[Affichez l’historique d’audit de toutes les attributions et activations](azure-pim-resource-rbac.md) des rôles de ressource Azure au cours des 30 derniers jours.

### <a name="security-alerts-for-azure-resource-roles"></a>Alertes de sécurité pour les rôles de ressource Azure

[Configurez des alertes de sécurité pour les rôles de ressource Azure](pim-resource-roles-configure-alerts.md) qui déclencheront une alerte en cas d’activité suspecte et non sécurisée.

## <a name="plan-and-implement-pim-for-privileged-access-groups"></a>Planifier et implémenter PIM pour les groupes d’accès privilégié

Suivez ces tâches pour préparer PIM à gérer des groupes d’accès privilégié.

### <a name="discover-privileged-access-groups"></a>Découvrir les groupes d’accès privilégié

Il peut arriver qu’une personne dispose de cinq ou six affectations admissibles à des rôles Azure AD par le biais de PIM. Elle devra activer chaque rôle individuellement, ce qui peut réduire sa productivité. Pire encore, elle peut également avoir des dizaines ou des centaines de ressources Azure qui lui sont attribuées, ce qui aggrave le problème.

Dans ce cas, vous devez utiliser des groupes d’accès privilégié. Créez un groupe d’accès privilégié et accordez-lui un accès actif permanent à plusieurs rôles. Consultez les [capacités de gestion des groupes d’accès privilégié](groups-features.md).

Pour gérer un groupe assignable à un rôle Azure AD en tant que groupe d’accès privilégié, vous devez [l’amener sous la gestion de PIM](groups-discover-groups.md).

### <a name="configure-pim-settings-for-privileged-access-groups"></a>Configurer les paramètres PIM pour les groupes d’accès privilégié

[Rédigez et configurez les paramètres](groups-role-settings.md) des groupes d’accès privilégié que vous avez prévu de protéger avec PIM.

Le tableau suivant présente des exemples de paramètres :

| Role| Exiger une authentification multifacteur| Notification| Exiger une approbation| Approbateur| Durée d’activation| Administrateur actif| Expiration active| Expiration éligible |
| --- | --- | --- |--- |--- |--- |--- |---|---|
| Propriétaire| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| Autres propriétaires de la ressource| 1 heure| None| n/a| 3 mois |
| Membre| :heavy_check_mark:| :heavy_check_mark:| :x:| None| 5 heures| None| n/a| 3 mois |

### <a name="assign-eligibility-for-privileged-access-groups"></a>Attribuer l’admissibilité aux groupes d’accès privilégié

Vous pouvez [attribuer l’admissibilité à des membres ou propriétaires des groupes d’accès privilégié](groups-assign-member-owner.md). En une seule activation, il aura accès à toutes les ressources liées. 

>[!NOTE] 
>Vous pouvez affecter le groupe privilégié à un ou plusieurs rôles Azure AD et rôles de ressource Azure de la même manière que vous attribuez des rôles aux utilisateurs. Un maximum de 250 groupes assignables à un rôle peuvent être créés dans une seule organisation (locataire) Azure AD.

![Attribuer l’admissibilité aux groupes d’accès privilégié](media/pim-deployment-plan/privileged-access-groups.png)


Lorsque l’attribution d’un groupe privilégié approche de son expiration, [utilisez PIM pour prolonger ou renouveler l’affectation du groupe](groups-renew-extend.md). Vous aurez besoin de l’approbation du propriétaire du groupe.

### <a name="approve-or-deny-pim-activation-request"></a>Approuver ou refuser une demande d’activation PIM

Configurez les membres et propriétaires des groupes d’accès privilégié pour qu’ils aient besoin d’une approbation pour l’activation et désignez des utilisateurs ou des groupes de votre organisation Azure AD comme approbateurs délégués. Nous vous recommandons de sélectionner plusieurs approbateurs pour chaque groupe afin de réduire la charge de travail pour l’administrateur de rôle privilégié. 

[Approuvez ou refusez les demandes d’activation de rôle pour les groupes d’accès privilégié](groups-approval-workflow.md). En tant qu’approbateur délégué, vous recevrez une notification par e-mail lorsqu’une demande est en attente de votre approbation.

### <a name="view-audit-history-for-privileged-access-groups"></a>Afficher l’historique d’audit pour les groupes d’accès privilégié

[Affichez l’historique d’audit de toutes les attributions et activations](groups-audit.md) des groupes d’accès privilégié au cours des 30 derniers jours.

## <a name="next-steps"></a>Étapes suivantes

* En cas de problèmes liés à PIM, consultez [Résoudre un problème avec PIM](pim-troubleshoot.md).

* [Déployer d’autres fonctionnalités d’identité](../fundamentals/active-directory-deployment-plans.md)

 

