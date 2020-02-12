---
title: Déployer Privileged Identity Management (PIM) - Azure AD | Microsoft Docs
description: Décrit comment planifier le déploiement d’Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/04/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8c77b3454026aa309d979bd938674e7c3ae7b6a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025994"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Déployer Azure AD Privileged Identity Management (PIM)

Ce guide pas à pas décrit comment planifier le déploiement de Privileged Identity Management (PIM) dans votre organisation Azure Active Directory (Azure AD).

> [!TIP]
> Tout au long de cet article, vous verrez des éléments marqués comme :
> 
> :heavy_check_mark: **Microsoft recommande**
> 
> Il s’agit de recommandations générales et vous devez uniquement les implémenter si elles s’appliquent aux besoins spécifiques de votre entreprise.

## <a name="learn-about-privileged-identity-management"></a>En savoir plus sur Privileged Identity Management

Azure AD Privileged Identity Management vous aide à gérer les rôles d’administrateur privilégiés dans Azure AD, les ressources Azure et d’autres services en ligne Microsoft. Dans un monde où les identités privilégiées sont attribuées et oubliées, Privileged Identity Management fournit des solutions telles que l’accès juste-à-temps, des workflows d’approbation de requête et des révisions d’accès entièrement intégrées pour que vous puissiez identifier, découvrir et empêcher les activités malveillantes de rôles privilégiés en temps réel. Le déploiement de Privileged Identity Management pour gérer les rôles privilégiés à l’échelle de votre organisation réduit considérablement les risques tout en exposant des insights pertinents sur les activités de vos rôles privilégiés.

### <a name="business-value-of-privileged-identity-management"></a>Valeur commerciale de Privileged Identity Management

**Gérer les risques** : Protégez votre organisation en appliquant le principe des [privilèges d’accès minimum](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) et l’accès juste-à-temps. En réduisant le nombre d’affectations permanentes d’utilisateurs à des rôles privilégiés ainsi qu’en appliquant des approbations et l’authentification multifacteur pour l’élévation, vous pouvez considérablement réduire les risques de sécurité liés à un accès privilégié dans votre organisation. L’application des privilèges d’accès minimum et de l’accès juste-à-temps vous permet également d’afficher un historique de l’accès aux rôles privilégiés et de détecter les problèmes de sécurité quand ils se produisent.

**Conformité et gouvernance des adresses** : le déploiement de Privileged Identity Management crée un environnement pour la gouvernance des identités en cours. L’élévation juste-à-temps des identités privilégiées offre un moyen à Privileged Identity Management d’effectuer le suivi des activités d’accès privilégié dans votre organisation. Vous serez également en mesure d’afficher et de recevoir des notifications pour toutes les affectations de rôles éligibles et permanents à l’intérieur de votre organisation. La révision d’accès vous permet d’auditer et de supprimer régulièrement des identités privilégiées inutiles et de vous assurer que votre organisation est conforme aux normes les plus rigoureuses en matière d’identité, d’accès et de sécurité.

**Réduire les coûts** : réduisez les coûts en éliminant le manque d’efficacité, les erreurs humaines et les problèmes de sécurité avec un déploiement correct de Privileged Identity Management. Le résultat est une réduction de la cybercriminalité associée à des identités privilégiées, qui est coûteuse et dont il est difficile de se remettre. Privileged Identity Management aide également votre organisation à réduire le coût associé à l’audit des informations d’accès quand il s’agit de se conformer aux normes et réglementations.

Pour plus d’informations, consultez [Qu’est-ce qu’Azure AD Privileged Identity Management ?](pim-configure.md).

### <a name="licensing-requirements"></a>Exigences en termes de licence

Pour utiliser Privileged Identity Management, votre annuaire doit avoir l’une des licences payantes ou d’essai gratuit suivantes :

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Pour plus d’informations, consultez [Exigences relatives aux licences pour l’utilisation de Privileged Identity Management](subscription-requirements.md).

### <a name="key-terminology"></a>Terminologie clé

| Terme ou concept | Description |
| --- | --- |
| Éligible | Attribution de rôle qui oblige l’utilisateur à effectuer une ou plusieurs actions pour utiliser ce rôle. Lorsqu’un utilisateur devient éligible pour un rôle, il peut l’activer pour réaliser des tâches privilégiées. Il n’existe aucune différence entre un accès accordé de façon permanente à un utilisateur et l’affectation d’un rôle éligible. La seule différence réside dans le fait que certaines personnes n’ont pas besoin d’un accès permanent. |
| Activer | Processus dans lequel une ou plusieurs actions sont exécutées dans le but d’utiliser un rôle pour lequel un utilisateur est éligible. Il peut s’agir de procéder à une vérification de l’authentification multifacteur (MFA), de fournir une justification professionnelle ou de demander une approbation aux approbateurs désignés. |
| Accès juste-à-temps (JIT) | Modèle où les utilisateurs reçoivent des autorisations temporaires pour effectuer des tâches privilégiées. De cette façon, les utilisateurs malveillants ou non autorisés ne peuvent pas accéder aux ressources une fois que les autorisations ont expiré. L’accès est accordé uniquement au moment où les utilisateurs en ont besoin. |
| Principe des privilèges d’accès minimum | Pratique de sécurité recommandée qui consiste à accorder à chaque utilisateur les privilèges minimum dont il a besoin pour accomplir les tâches qu’il est autorisé à effectuer. Cette pratique réduit le nombre d’administrateurs généraux et utilise à la place des rôles d’administrateur spécifiques pour certains scénarios. |

Pour plus d’informations, consultez [Terminologie](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>Vue d’ensemble du fonctionnement de Privileged Identity Management

1. Privileged Identity Management est configuré afin que les utilisateurs soient éligibles pour des rôles privilégiés.
1. Quand un utilisateur éligible doit utiliser son rôle privilégié, il active le rôle dans Privileged Identity Management.
1. Selon les paramètres de Privileged Identity Management configurés pour le rôle, l’utilisateur doit effectuer certaines étapes (par exemple, l’exécution de l’authentification multifacteur, l’obtention d’une approbation ou la spécification d’un motif).
1. Une fois que l’utilisateur a correctement activé son rôle, il obtient le rôle pour une période de temps préconfigurée.
1. Les administrateurs peuvent afficher un historique de toutes les activités de Privileged Identity Management dans le journal d’audit. Ils peuvent également renforcer la sécurité de leurs organisations Azure ADet respecter les exigences en matière de conformité à l’aide de fonctionnalités Privileged Identity Management telles que les révisions d’accès et les alertes.

Pour plus d’informations, consultez [Qu’est-ce qu’Azure AD Privileged Identity Management ?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>Rôles qui peuvent être managés par Privileged Identity Management

**Rôles Azure AD** : Ces rôles correspondent se trouvent tous dans Azure Active Directory (par exemple, Administrateur général, Administrateur Exchange et Administrateur de la sécurité). Vous trouverez plus d’informations sur les rôles et leurs fonctionnalités dans [Autorisations des rôles d’administrateur dans Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Afin d’obtenir de l’aide pour déterminer les rôles à affecter à vos administrateurs, consultez [Rôles moins privilégiés par tâche](../users-groups-roles/roles-delegate-by-task.md).

**Rôles de ressources Azure** : Ces rôles sont liés à une ressource Azure, un groupe de ressources, un abonnement ou un groupe d’administration. Privileged Identity Management fournit un accès juste-à-temps à la fois aux rôles intégrés comme Propriétaire, Administrateur de l’accès utilisateur et Contributeur et aux [rôles personnalisés](../../role-based-access-control/custom-roles.md). Pour plus d’informations sur les rôles de ressources Azure, consultez [Contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/overview.md).

Pour plus d’informations, consultez [Rôles que vous ne pouvez pas manager dans Privileged Identity Management](pim-roles.md).

## <a name="plan-your-deployment"></a>Planifier votre déploiement

Cette section se concentre sur ce que vous devez faire avant de déployer Privileged Identity Management dans votre organisation. Il est essentiel de suivre les instructions et de comprendre les concepts de cette section, car ils vous guideront pour créer le meilleur plan adapté aux identités privilégiées de votre organisation.

### <a name="identify-your-stakeholders"></a>Identifier les participants

La section suivante vous permet d’identifier tous les participants impliqués dans le projet et qui doivent valider ou réviser ce projet, ou se tenir informés de celui-ci. Il comprend des tables distinctes pour le déploiement de Privileged Identity Management pour les rôles Azure AD et les Privileged Identity Management pour les rôles de ressources Azure. Ajoutez des participants au tableau suivant en fonction de votre organisation.

- SO = Valider ce projet
- R = Réviser ce projet et fournir une entrée
- I = Se tenir informé de ce projet

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Participants : Privileged Identity Management pour les rôles Azure AD

| Name | Role | Action |
| --- | --- | --- |
| Nom et e-mail | **Architecte de l’identité ou administrateur général Azure**<br/>Représentant de l’équipe de gestion des identités responsable de la définition de l’alignement de cette modification sur l’infrastructure principale de gestion des identités dans votre organisation. | SO/R/I |
| Nom et e-mail | **Propriétaire de service / Supérieur hiérarchique**<br/>Représentant des propriétaires du service informatique d’un service ou d’un groupe de services. Ils sont essentiels à la prise de décisions et au déploiement de Privileged Identity Management pour leur équipe. | SO/R/I |
| Nom et e-mail | **Responsable de la sécurité**<br/>Représentant de l’équipe de sécurité qui peut valider que le plan répond aux exigences de sécurité de votre organisation. | SO/R |
| Nom et e-mail | **Responsable du support informatique / Support technique**<br/>Représentant de l’organisation du support informatique qui peut fournir une entrée sur la capacité de prise en charge de cette modification du point de vue du support technique. | R/I |
| Nom et e-mail des utilisateurs pilotes | **Utilisateurs de rôles privilégiés**<br/>Groupe d’utilisateurs pour lesquels Privileged Identity Management est implémenté. Ils devront savoir comment activer leurs rôles une fois Privileged Identity Management implémenté. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>Participants : Azure AD Privileged Identity Management pour les rôles de ressources Azure

| Name | Role | Action |
| --- | --- | --- |
| Nom et e-mail | **Propriétaire de l’abonnement / de la ressource**<br/>Un représentant des propriétaires du service informatique de chaque abonnement ou ressource pour lequel ou laquelle vous voulez déployer Privileged Identity Management | SO/R/I |
| Nom et e-mail | **Responsable de la sécurité**<br/>Représentant de l’équipe de sécurité qui peut valider que le plan répond aux exigences de sécurité de votre organisation. | SO/R |
| Nom et e-mail | **Responsable du support informatique / Support technique**<br/>Représentant de l’organisation du support informatique qui peut fournir une entrée sur la capacité de prise en charge de cette modification du point de vue du support technique. | R/I |
| Nom et e-mail des utilisateurs pilotes | **Utilisateurs du rôle RBAC**<br/>Groupe d’utilisateurs pour lesquels Privileged Identity Management est implémenté. Ils devront savoir comment activer leurs rôles une fois Privileged Identity Management implémenté. | I |

### <a name="enable-privileged-identity-management"></a>Activer Privileged Identity Management

Dans le cadre du processus de planification, vous devez d’abord accepter et activer Privileged Identity Management en suivant notre article [commencer à utiliser Privileged Identity Management](pim-getting-started.md). L’activation de Privileged Identity Management vous donne accès à certaines fonctionnalités qui sont spécifiquement conçues pour faciliter votre déploiement.

Si votre objectif est de déployer Privileged Identity Management pour les ressources Azure, vous devez suivre notre article [découvrir les ressources Azure à gérer dans Privileged Identity Management](pim-resource-roles-discover-resources.md). Seuls les propriétaires d’abonnements et de groupes d’administration peuvent découvrir et intégrer ces ressources dans Privileged Identity Management. Au terme de l’intégration, la fonctionnalité PIM est accessible aux propriétaires à tous les niveaux, notamment le groupe d’administration, l’abonnement, le groupe de ressources et la ressource. Si vous êtes un administrateur général qui tente de déployer Privileged Identity Management pour vos ressources Azure, vous pouvez [élever l’accès pour gérer tous les abonnements Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) afin d’obtenir l’accès à toutes les ressources Azure dans l’annuaire pour la découverte. Toutefois, il est conseillé d’obtenir l’approbation de chacun des propriétaires d’abonnements avant de gérer leurs ressources avec Privileged Identity Management.

### <a name="enforce-principle-of-least-privilege"></a>Appliquer le principe des privilèges minimum

Il est important de vous assurer que vous avez appliqué le principe des privilèges minimum dans votre organisation pour les rôles Azure AD et les rôles de ressources Azure.

#### <a name="azure-ad-roles"></a>Rôles Azure AD

Pour les rôles Azure AD, il est courant pour les organisations d’affecter le rôle Administrateur général à un nombre important d’administrateurs quand la plupart d’entre eux n’ont besoin que d’un ou de deux rôles d’administrateur spécifiques. Les affectations de rôles privilégiés ont également tendance à ne pas être gérées.

> [!NOTE]
> Pièges courants dans la délégation de rôle :
>
> - L’administrateur responsable d’Exchange reçoit le rôle Administrateur général, même s’il n’a besoin que du rôle Administrateur Exchange pour effectuer ses tâches quotidiennes.
> - Le rôle Administrateur général est affecté à un administrateur Office, car l’administrateur a besoin à la fois du rôle Administrateur de la sécurité et du rôle Administrateur SharePoint, et il est plus facile de déléguer uniquement un rôle.
> - Le rôle Administrateur de la sécurité a été affecté à l’administrateur pour effectuer une tâche il y a longtemps, mais n’a jamais été supprimé.

Suivez les étapes ci-dessous afin d’appliquer le principe des privilèges minimum pour vos rôles Azure AD.

1. Appréhendez la précision des rôles en lisant et en comprenant les [rôles d’administrateur Azure AD disponibles](../users-groups-roles/directory-assign-admin-roles.md#available-roles). Vous et votre équipe devez également faire référence aux [rôles d’administrateur par tâche d’identité dans Azure AD](../users-groups-roles/roles-delegate-by-task.md), qui explique le rôle moins privilégié pour des tâches spécifiques.

1. Listez les utilisateurs avec des rôles privilégiés dans votre organisation. Vous pouvez utiliser l’[Assistant Privileged Identity Management](pim-security-wizard.md#run-the-wizard) pour accéder à une page similaire à celle-ci.

    ![Volet Découvrir les rôles privilégiés indiquant les utilisateurs disposant de rôles privilégiés](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Pour tous les administrateurs généraux dans votre organisation, découvrez pourquoi ils ont besoin du rôle. En vous basant sur la lecture de la documentation précédente, si le travail de la personne peut être effectué par un ou plusieurs rôles d’administrateur précis, vous devez les supprimer du rôle Administrateur général et effectuer des affectations en conséquence à l’intérieur d’Azure Active Directory (comme référence : actuellement, Microsoft a uniquement environ 10 administrateurs disposant du rôle Administrateur général. Pour plus d’informations, consultez [comment Microsoft utilise Privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. Pour tous les autres rôles Azure AD, passez en revue la liste des affectations, identifiez les administrateurs qui n’ont plus besoin du rôle et supprimez-les de leurs affectations.

Pour automatiser les deux dernières étapes, vous pouvez utiliser les révisions d’accès dans Privileged Identity Management. En suivant les étapes de [démarrer une révision d’accès des rôles Azure AD dans Privileged Identity Management](pim-how-to-start-security-review.md), vous pouvez configurer une révision d’accès pour chaque rôle Azure AD avec un ou plusieurs membres.

![Volet Créer une révision d’accès pour les rôles Azure AD](./media/pim-deployment-plan/create-access-review.png)

Vous devez définir les réviseurs sur **Membres (auto)** . Vous envoyez ainsi un e-mail à tous les membres du rôle pour leur demander de confirmer s’ils ont besoin de l’accès. Vous devez également activer **Exiger la raison lors de l’approbation** dans les paramètres avancés afin que les utilisateurs puissent indiquer pourquoi ils ont besoin du rôle. En fonction de ces informations, vous serez en mesure de supprimer des utilisateurs dans des rôles inutiles et de déléguer des rôles d’administrateur plus précis dans le cas d’administrateurs généraux.

Les révisions d’accès s’appuient sur les e-mails pour demander aux utilisateurs de revoir leur accès aux rôles. Si vous avez des comptes privilégiés sans e-mail lié, veillez à renseigner le champ de messagerie secondaire sur ces comptes. Pour plus d’informations, consultez [Attribut proxyAddresses dans Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Rôles de ressources Azure

Pour les abonnements et les ressources Azure, vous pouvez configurer un processus de révision d’accès similaire pour passer en revue les rôles dans chaque abonnement ou ressource. L’objectif de ce processus consiste à réduire les affectations de rôles Propriétaire et Administrateur de l’accès utilisateur attachées à chaque abonnement ou ressource, ainsi qu’à supprimer les affectations inutiles. Toutefois, les organisations délèguent souvent de telles tâches au propriétaire de chaque abonnement ou ressource, car il a une meilleure compréhension des rôles spécifiques (en particulier, des rôles personnalisés).

Si vous êtes un administrateur informatique avec le rôle Administrateur général qui tente de déployer Privileged Identity Management pour les ressources Azure dans votre organisation, vous pouvez [élever l’accès pour gérer tous les abonnements Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) afin d’accéder à chaque abonnement. Vous pouvez ensuite rechercher le propriétaire de chaque abonnement et travailler avec lui pour supprimer les affectations inutiles et réduire l’affectation de rôle Propriétaire.

Les utilisateurs avec le rôle Propriétaire pour un abonnement Azure peuvent également utiliser des [révisions d’accès pour les ressources Azure](pim-resource-roles-start-access-review.md) afin d’auditer et de supprimer des affectations de rôles inutiles similaires au processus décrit précédemment pour les rôles Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Identifier les attributions de rôle qui doivent être protégées par Privileged Identity Management

Après le nettoyage des attributions de rôle privilégié dans votre organisation, vous devez choisir les rôles à protéger avec Privileged Identity Management.

Si un rôle est protégé par Privileged Identity Management, les utilisateurs éligibles qui lui sont attribués doivent s’élever pour utiliser les privilèges octroyés par le rôle. Le processus d’élévation peut également inclure l’obtention de l’approbation, l’exécution de l’authentification multifacteur et/ou l’indication d’un motif d’activation. Privileged Identity Management pouvez également suivre les élévations de privilèges par le biais des notifications et des journaux d’événements d’audit Privileged Identity Management et Azure AD.

Le choix des rôles à protéger avec Privileged Identity Management peut être difficile et sera différent pour chaque organisation. Cette section présente nos conseils pratiques pour les rôles de ressources Azure et Azure AD.

#### <a name="azure-ad-roles"></a>Rôles Azure AD

Il est important de hiérarchiser la protection des rôles Azure AD qui comportent le plus grand nombre d’autorisations. En fonction des modèles d’utilisation parmi tous les clients Privileged Identity Management, les 10 principaux rôles Azure AD managés par Privileged Identity Management sont les suivants :

1. Administrateur général
1. Administrateur de sécurité
1. Administrateur d’utilisateurs
1. Administrateur Exchange
1. Administrateur SharePoint
1. Administrateur Intune
1. Lecteur de sécurité
1. Administrateur de services fédérés
1. Administrateur de facturation
1. Administrateur Skype Entreprise

> [!TIP]
> :heavy_check_mark: **Microsoft recommande** de gérer tous les rôles Administrateur général et Administrateur de la sécurité à l’aide de Privileged Identity Management dans un premier temps, car ce sont ceux qui peuvent être les plus nuisibles quand ils sont compromis.

Il est important de prendre en compte les données et autorisations qui sont les plus sensibles pour votre organisation. Par exemple, certaines organisations souhaitent protéger leur rôle Administrateur Power BI ou Administrateur Teams à l’aide de Privileged Identity Management, car ils ont la possibilité d’accéder aux données et/ou de modifier les workflows de base.

S’il existe des rôles avec des utilisateurs invités affectés, ils sont particulièrement vulnérables aux attaques.

> [!TIP]
> :heavy_check_mark: **Microsoft recommande** de gérer tous les rôles avec des utilisateurs invités à l’aide de Privileged Identity Management pour réduire les risques associés à des comptes d’utilisateurs invités compromis.

Les rôles de lecteur comme Lecteur d’annuaire, Lecteur du Centre de messages et Lecteur de sécurité sont parfois considérés comme étant moins importants par rapport à d’autres rôles parce qu’ils ne disposent pas d’autorisation en écriture. Toutefois, nous avons vu que certains clients protègent également ces rôles, car les attaquants qui ont obtenu l’accès à ces comptes peuvent être en mesure de lire des données sensibles, telles que des données personnelles. Vous devez prendre ce point en considération quand vous décidez si les rôles de lecteur dans votre organisation doivent être gérés à l’aide de Privileged Identity Management.

#### <a name="azure-resource-roles"></a>Rôles de ressources Azure

Quand vous choisissez les attributions de rôle qui doivent être managées à l’aide de Privileged Identity Management pour les ressources Azure, vous devez tout d’abord identifier les abonnements/ressources qui sont essentiels pour votre organisation. Des exemples de ces abonnements/ressources sont :

- Ressources qui hébergent les données les plus sensibles
- Ressources desquelles dépendent les applications orientées client de base

Si vous êtes un administrateur général qui éprouve des difficultés à décider quels abonnements/ressources sont les plus importants, nous vous conseillons de contacter les propriétaires d’abonnements de votre organisation pour dresser la liste des ressources gérées par chaque abonnement. Vous devez ensuite collaborer avec les propriétaires d’abonnements pour regrouper les ressources en fonction du niveau de gravité (bas, moyen, élevé) en cas de compromission. Vous devez hiérarchiser la gestion des ressources avec Privileged Identity Management en fonction de ce niveau de gravité.

> [!TIP]
> :heavy_check_mark: **Microsoft recommande** de collaborer avec les propriétaires d’abonnements/de ressources de services critiques pour configurer le workflow Privileged Identity Management pour tous les rôles dans les abonnements/ressources sensibles.

Privileged Identity Management pour les ressources Azure prend en charge les comptes de service associés à un délai. Vous devez traiter les comptes de service exactement comme un compte d’utilisateur normal.

Pour les abonnements/ressources qui ne sont pas aussi critiques, vous n’avez pas besoin de configurer Privileged Identity Management pour tous les rôles. Toutefois, vous devez toujours protéger les rôles Propriétaire et Administrateur de l’accès utilisateur avec Privileged Identity Management.

> [!TIP]
> :heavy_check_mark: **Microsoft recommande** de gérer les rôles Propriétaire et Administrateur de l’accès utilisateur de l’ensemble des abonnements/ressources à l’aide de Privileged Identity Management.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Identifier les affectations de rôles qui doivent être permanentes ou éligibles

Une fois que vous avez dressé la liste des rôles qui seront gérés par Privileged Identity Management, vous devez choisir les utilisateurs qui auront le rôle éligible et ceux qui auront le rôle actif en permanence. Les rôles actifs en permanence sont les rôles normaux attribués via les ressources Azure et Azure Active Directory, tandis que les rôles éligibles peuvent uniquement être attribués dans Privileged Identity Management.

> [!TIP]
> :heavy_check_mark: **Microsoft recommande** de n’avoir aucune affectation active en permanence pour les rôles Azure AD et les rôles de ressources Azure autre que les [deux comptes d’accès d’urgence de secours](../users-groups-roles/directory-emergency-access.md) recommandés, qui doivent avoir le rôle Administrateur général permanent.

Même si nous recommandons de n’avoir aucun administrateur permanent, il est parfois difficile pour les organisations d’atteindre immédiatement cet objectif. Voici des éléments à prendre en compte lors de cette décision :

- Fréquence d’élévation : si l’utilisateur n’a besoin de l’affectation de rôle privilégié qu’une seule fois, l’affectation ne doit pas être permanente. En revanche, si l’utilisateur a besoin du rôle pour ses tâches quotidiennes et si l’utilisation de Privileged Identity Management réduit considérablement sa productivité, le rôle permanent peut être envisagé.
- Cas spécifiques à votre organisation : si la personne qui reçoit le rôle éligible fait partie d’une équipe très éloignée ou est un cadre de haut rang au point que la communication et l’application du processus d’élévation s’avèrent difficiles, le rôle permanent peut être envisagé.

> [!TIP]
> :heavy_check_mark: **Microsoft recommande** de configurer des révisions d’accès récurrentes pour les utilisateurs avec des affectations de rôles permanentes (le cas échéant). Apprenez-en davantage sur la révision d’accès récurrente dans la dernière section de ce plan de déploiement

### <a name="draft-your-privileged-identity-management-settings"></a>Composer vos paramètres Privileged Identity Management

Avant d’implémenter votre solution Privileged Identity Management, il est conseillé de composer vos paramètres Privileged Identity Management pour chaque rôle privilégié utilisé par votre organisation. Cette section contient des exemples de paramètres Privileged Identity Management pour des rôles particuliers (ils sont indiqués uniquement pour référence et peuvent être différents pour votre organisation). Chacun de ces paramètres est expliqué en détail avec des recommandations de Microsoft après les tableaux.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Paramètres Privileged Identity Management pour les rôles Azure AD

| Role | Exiger une authentification multifacteur | Notification | Ticket d’incident | Exiger une approbation | Approbateur | Durée d’activation | Administrateur permanent |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Administrateur général | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Autres administrateurs généraux | 1 heure | Comptes d’accès d’urgence |
| Administrateur Exchange | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | None | 2 heures | None |
| Administrateur du support technique | :x: | :x: | :heavy_check_mark: | :x: | None | 8 heures | None |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Paramètres Privileged Identity Management pour les rôles de ressource Azure

| Role | Exiger une authentification multifacteur | Notification | Exiger une approbation | Approbateur | Durée d’activation | Administrateur actif | Expiration active | Expiration éligible |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Propriétaire d’abonnements critiques | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Autres propriétaires de l’abonnement | 1 heure | None | n/a | 3 mois |
| Administrateur de l’accès utilisateur d’abonnements moins critiques | :heavy_check_mark: | :heavy_check_mark: | :x: | None | 1 heure | None | n/a | 3 mois |
| Contributeur de machine virtuelle | :x: | :heavy_check_mark: | :x: | None | 3 heures | None | n/a | 6 mois |

Le tableau suivant décrit chacun des paramètres.

| Paramètre | Description |
| --- | --- |
| Role | Nom du rôle pour lequel vous définissez les paramètres. |
| Exiger une authentification multifacteur | Indique si l’utilisateur éligible doit effectuer une authentification multifacteur avant d’activer le rôle.<br/><br/> :heavy_check_mark: **Microsoft recommande** d’appliquer l’authentification multifacteur pour tous les rôles d’administrateur, en particulier si les rôles ont des utilisateurs invités. |
| Notification | Si la valeur est true, les rôles Administrateur général, Administrateur de rôle privilégié et Administrateur de la sécurité de l’organisation reçoivent une notification par e-mail quand un utilisateur éligible active le rôle.<br/><br/>**Remarque :** Certaines organisations ne disposent pas d’une adresse e-mail liée à leurs comptes d’administrateurs ; pour obtenir ces notifications par e-mail, vous devez définir une autre adresse e-mail afin que les administrateurs reçoivent ces e-mails. |
| Ticket d’incident | Indique si l’utilisateur éligible doit enregistrer un numéro de ticket d’incident lors de l’activation de son rôle. Ce paramètre permet à une organisation d’identifier chaque activation avec un numéro d’incident interne pour atténuer les activations indésirables.<br/><br/> :heavy_check_mark: **Microsoft recommande** de tirer parti des numéros de ticket d’incident pour lier Privileged Identity Management à votre système interne. Cela est particulièrement utile pour les approbateurs qui ont besoin de contexte pour l’activation. |
| Exiger une approbation | Indique si l’utilisateur éligible doit obtenir une approbation pour activer le rôle.<br/><br/> :heavy_check_mark: **Microsoft recommande** de configurer l’approbation pour les rôles avec le plus d’autorisations. Selon les modèles d’utilisation de tous les clients Privileged Identity Management, Administrateur général, Administrateur d’utilisateurs, Administrateur Exchange, Administrateur de la sécurité et Administrateur de mots de passe sont les rôles les plus courants avec configuration de l’approbation. |
| Approbateur | Si l’approbation est nécessaire pour activer le rôle éligible, liste les personnes qui doivent approuver la demande. Par défaut, Privileged Identity Management définit comme approbateur tous les utilisateurs qui sont des administrateurs de rôles privilégiés, qu’ils soient permanents ou éligibles.<br/><br/>**Remarque :** Si un utilisateur est à la fois éligible pour un rôle Azure AD et un approbateur du rôle, il ne sera pas en mesure de s’approuver.<br/><br/> :heavy_check_mark: **Microsoft recommande** de choisir comme approbateurs ceux qui sont les plus compétents sur le rôle spécifique et ses utilisateurs fréquents plutôt qu’un administrateur général. |
| Durée d’activation | Durée pendant laquelle un utilisateur est activé dans le rôle avant l’expiration. |
| Administrateur permanent | Liste des utilisateurs qui seront un administrateur permanent pour le rôle (activation jamais nécessaire).<br/><br/> :heavy_check_mark: **Microsoft recommande** de n’avoir aucun administrateur permanent pour tous les rôles à l’exception des administrateurs généraux. Vous trouverez plus d’informations à ce sujet dans la section relative aux personnes qui doivent devenir éligibles et à celles qui doivent être actives en permanence de ce plan. |
| Administrateur actif | Pour les ressources Azure, un administrateur actif correspond à la liste des utilisateurs qui n’ont jamais besoin d’activation pour utiliser le rôle. Il n’est pas référencé en tant qu’administrateur permanent comme dans les rôles Azure AD, car vous pouvez définir une heure d’expiration à laquelle l’utilisateur va perdre ce rôle. |
| Expiration active | Une affectation de rôle actif pour les rôles de ressources Azure expire après cette période configurée. Vous pouvez choisir entre 15 jours, 1 mois, 3 mois, 6 mois, 1 an, ou un rôle actif en permanence. |
| Expiration éligible | Une affectation de rôle éligible pour les rôles de ressources Azure expire après cette période configurée. Vous pouvez choisir entre 15 jours, 1 mois, 3 mois, 6 mois, 1 an, ou un rôle éligible en permanence. |

## <a name="implement-your-solution"></a>Implémenter votre solution

La base d’une planification appropriée est la base sur laquelle vous pouvez déployer correctement une application avec Azure Active Directory.  Elle assure une incorporation et une sécurité intelligentes qui simplifient l’intégration tout en réduisant le temps nécessaire à des déploiements réussis.  Cette combinaison garantit que votre application est intégrée en toute simplicité, tout en limitant les temps d’arrêt pour vos utilisateurs finaux.

### <a name="identify-test-users"></a>Identifier les utilisateurs de test

Utilisez cette section pour identifier un groupe ou des groupes d’utilisateurs pour valider l’implémentation. Selon les paramètres que vous avez sélectionnés dans la section de planification, identifiez les utilisateurs que vous souhaitez tester pour chaque rôle.

> [!TIP]
> :heavy_check_mark: **Microsoft recommande** de définir les propriétaires de services de chaque rôle Azure AD comme utilisateurs de test afin qu’ils se familiarisent avec le processus et deviennent des partisans internes du déploiement.

Dans ce tableau, identifiez les utilisateurs de test qui vont vérifier que les paramètres de chaque rôle fonctionnent.

| Nom de rôle | Utilisateurs de test |
| --- | --- |
| &lt;Nom du rôle&gt; | &lt;Utilisateurs pour tester le rôle&gt; |
| &lt;Nom du rôle&gt; | &lt;Utilisateurs pour tester le rôle&gt; |

### <a name="test-implementation"></a>Implémentation de test

Maintenant que vous avez identifié les utilisateurs de test, utilisez cette étape pour configurer Privileged Identity Management pour ceux-ci. Si votre organisation souhaite incorporer le workflow Privileged Identity Management dans votre propre application interne au lieu d’utiliser Privileged Identity Management dans le portail Azure, toutes les opérations dans Privileged Identity Management sont également prises en charge via notre [Graphique API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Configurer Privileged Identity Management pour les rôles Azure AD

1. [Configurez les paramètres de rôle Azure AD](pim-how-to-change-default-settings.md) selon ce que vous avez planifié.

1. Accédez à **Rôles Azure AD**, cliquez sur **Rôles**, puis sélectionnez le rôle que vous venez de configurer.

1. Pour le groupe d’utilisateurs de test, s’ils sont déjà des administrateurs permanents, vous pouvez les rendre éligibles en les recherchant et en les convertissant du statut permanent au statut éligible en cliquant sur les trois points de leur ligne. S’ils n’ont pas encore les affectations de rôles, vous pouvez [créer une affectation éligible](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Répétez les étapes 1 à 3 pour tous les rôles que vous souhaitez tester.

1. Une fois que vous avez configuré les utilisateurs de test, vous devez leur envoyer le lien pour savoir comment [activer leur rôle Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>Configurer Privileged Identity Management pour les rôles de ressources Azure

1. [Configurez les paramètres de rôle de ressource Azure](pim-resource-roles-configure-role-settings.md) pour un rôle dans un abonnement ou une ressource que vous souhaitez tester.

1. Accédez à **Ressources Azure** pour cet abonnement et cliquez sur **Rôles**, puis sélectionnez le rôle que vous venez de configurer.

1. Pour le groupe d’utilisateurs de test, s’ils sont déjà des administrateurs actifs, vous pouvez les rendre éligibles en les recherchant et en [mettant à jour leur attribution de rôle](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). S’ils n’ont pas encore le rôle, vous pouvez [affecter un nouveau rôle](pim-resource-roles-assign-roles.md#assign-a-role).

1. Répétez les étapes 1 à 3 pour tous les rôles que vous souhaitez tester.

1. Une fois que vous avez configuré les utilisateurs de test, vous devez leur envoyer le lien pour savoir comment [activer leur rôle de ressource Azure](pim-resource-roles-activate-your-roles.md).

Vous devez utiliser cette étape pour vérifier si toute la configuration que vous avez définie pour les rôles fonctionne correctement. Utilisez le tableau suivant pour documenter vos tests. Vous devez également utiliser cette étape pour optimiser la communication avec les utilisateurs affectés.

| Role | Comportement attendu lors de l’activation | Résultats réels |
| --- | --- | --- |
| Administrateur général | (1) Exiger une authentification multifacteur<br/>(2) Exiger une approbation<br/>(3) L’approbateur reçoit une notification et peut approuver<br/>(4) Le rôle expire au bout d’un délai prédéfini |  |
| Propriétaire de l’abonnement *X* | (1) Exiger une authentification multifacteur<br/>(2) L’affectation éligible expire après la période configurée |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Communiquer Privileged Identity Management aux parties prenantes affectées

Le déploiement de Privileged Identity Management introduit des étapes supplémentaires pour les utilisateurs des rôles privilégiés. Même si Privileged Identity Management réduit considérablement les problèmes de sécurité associés aux identités privilégiées, la modification doit être communiquée efficacement avant le déploiement à l’échelle de l’abonné. En fonction du nombre d’administrateurs impactés, les organisations choisissent souvent de créer un document interne, une vidéo ou un e-mail concernant la modification. Les éléments fréquemment inclus dans ces communications sont notamment :

- Qu’est-ce que PIM
- Quel est l’avantage pour l’organisation
- Qui sera concerné
- Quand est-ce que PIM sera déployé
- Quelles étapes supplémentaires seront nécessaires pour que les utilisateurs activent leur rôle
    - Vous devez envoyer des liens vers notre documentation :
    - [Activer les rôles Azure AD](pim-how-to-activate-role.md)
    - [Activer les rôles de ressources Azure](pim-resource-roles-activate-your-roles.md)
- Informations de contact ou lien du support technique pour tout problème associé à PIM

> [!TIP]
> :heavy_check_mark: **Microsoft recommande** de prendre du temps avec votre équipe de support/support technique pour lui présenter le workflow Privileged Identity Management (si votre organisation dispose d’une équipe de support informatique interne). Fournissez-lui la documentation appropriée ainsi que vos informations de contact.

### <a name="move-to-production"></a>Passer en production

Une fois que votre test est terminé et réussi, passez Privileged Identity Management en production en répétant toutes les étapes dans les phases de test pour tous les utilisateurs de chaque rôle que vous avez défini dans votre configuration Privileged Identity Management. Par Privileged Identity Management pour les rôles Azure AD, les organisations testent et déploient souvent Privileged Identity Management pour les administrateurs généraux avant de tester et de déployer Privileged Identity Management pour d’autres rôles. Dans le même temps, pour les ressources Azure, les organisations testent et déploient normalement Privileged Identity Management, un abonnement Azure à la fois.

### <a name="in-the-case-a-rollback-is-needed"></a>Dans le cas où une restauration est nécessaire

Si Privileged Identity Management ne fonctionne pas comme vous le souhaitez dans l’environnement de production, les étapes de restauration suivantes peuvent vous aider à revenir à un état correct connu avant la configuration de Privileged Identity Management :

#### <a name="azure-ad-roles"></a>Rôles Azure AD

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Ouvrez **Azure AD Privileged Identity Management**.
1. Cliquez sur **Rôles Azure AD**, puis sur **Rôles**.
1. Pour chaque rôle que vous avez configuré, cliquez sur les points de suspension ( **...** ) pour tous les utilisateurs avec une affectation éligible.
1. Cliquez sur l’option **Désigner comme permanent** afin de rendre l’affectation de rôle permanente.

#### <a name="azure-resource-roles"></a>Rôles de ressources Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Ouvrez **Azure AD Privileged Identity Management**.
1. Cliquez sur **Ressources Azure**, puis sur un abonnement ou une ressource que vous souhaitez restaurer.
1. Cliquez sur **Rôles**.
1. Pour chaque rôle que vous avez configuré, cliquez sur les points de suspension ( **...** ) pour tous les utilisateurs avec une affectation éligible.
1. Cliquez sur l’option **Désigner comme permanent** afin de rendre l’affectation de rôle permanente.

## <a name="next-steps-after-deploying"></a>Étapes suivantes après le déploiement

Le déploiement réussi de Privileged Identity Management en production est une étape importante en termes de sécurisation des identités privilégiées de votre organisation. Avec le déploiement de Privileged Identity Management sont fournies des fonctionnalités Privileged Identity Management supplémentaires que vous devez utiliser pour la sécurité et la conformité.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Utiliser des alertes Privileged Identity Management pour protéger votre accès privilégié

Vous devez employer la fonctionnalité d’alerte intégrée de Privileged Identity Management pour mieux protéger votre abonné. Pour plus d’informations, consultez [Alertes de sécurité](pim-how-to-configure-security-alerts.md#security-alerts). Ces alertes incluent notamment les suivantes : les administrateurs n’utilisent pas les rôles privilégiés, les rôles sont affectés en dehors de Privileged Identity Management, les rôles sont activés trop fréquemment, et bien plus encore. Pour protéger entièrement votre organisation, vous devez parcourir régulièrement votre liste d’alertes et résoudre les problèmes. Vous pouvez afficher et résoudre vos alertes de la façon suivante :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Ouvrez **Azure AD Privileged Identity Management**.
1. Cliquez sur **Rôles Azure AD**, puis sur **Alertes**.

> [!TIP]
> :heavy_check_mark: **Microsoft recommande** de traiter immédiatement toutes les alertes marquées avec le niveau de gravité élevé. Pour les alertes avec les niveaux de gravité moyen et bas, vous devez rester informé et apporter des modifications si vous pensez qu’il existe une menace de sécurité.

Si l’une des alertes spécifiques est inutile ou ne s’applique pas à votre organisation, vous pouvez toujours ignorer l’alerte dans la page des alertes. Vous pouvez toujours rétablir ce rejet ultérieurement dans la page des paramètres Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Configurer des révisions d’accès récurrentes pour auditer régulièrement les identités privilégiées de votre organisation

Les révisions d’accès représentent le meilleur moyen de demander aux utilisateurs auxquels des rôles privilégiés ont été affectés ou à des réviseurs spécifiques si chaque utilisateur a besoin de l’identité privilégiée. Les révisions d’accès sont très utiles si vous souhaitez réduire la surface d’attaque et garantir la conformité. Pour plus d’informations sur le démarrage d’une révision d’accès, consultez [Révisions d’accès des rôles Azure AD](pim-how-to-start-security-review.md) et [Révisions d’accès des rôles de ressources Azure](pim-resource-roles-start-access-review.md). Pour certaines organisations, la réalisation d’une révision d’accès périodique est nécessaire pour garantir la conformité aux lois et réglementations alors que pour d’autres, la révision d’accès représente le meilleur moyen d’appliquer le principe des privilèges minimum au sein de l’organisation.

> [!TIP]
> :heavy_check_mark: **Microsoft recommande** de configurer des révisions d’accès tous les trimestres pour l’ensemble de vos rôles de ressources Azure et Azure AD.

Dans la plupart des cas, le réviseur pour les rôles Azure AD est l’utilisateur lui-même tandis que le réviseur pour les rôles de ressources Azure est le propriétaire de l’abonnement, dans lequel se trouve le rôle. Toutefois, il arrive souvent que les sociétés aient des comptes privilégiés non liés à l’adresse e-mail d’une personne en particulier. Dans ce cas, personne ne lit ni ne révise l’accès.

> [!TIP]
> :heavy_check_mark: **Microsoft recommande** d’ajouter une adresse e-mail secondaire pour tous les comptes avec des affectations de rôles privilégiés qui ne sont liés à aucune adresse e-mail régulièrement consultée

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Tirer le meilleur parti de votre journal d’audit pour améliorer la sécurité et la conformité

Le journal d’audit est l’endroit où vous pouvez vous tenir informé et être conforme aux réglementations. Privileged Identity Management stocke actuellement un historique de 30 jours résumant tous les historiques de votre organisation dans son journal d’audit, notamment :

- Activation/désactivation de rôles éligibles
- Activités d’attribution de rôle à l’intérieur et en dehors de Privileged Identity Management
- Modifications dans les paramètres de rôle
- Activités de demande/d’approbation/de refus pour l’activation de rôle avec configuration de l’approbation
- Mise à jour des alertes

Vous pouvez accéder à ces journaux d’audit si vous êtes un administrateur général ou un administrateur de rôle privilégié. Pour plus d’informations, consultez [Historique d’audit pour les rôles Azure AD](pim-how-to-use-audit-log.md) et [Historique d’audit pour les rôles de ressources Azure](azure-pim-resource-rbac.md).

> [!TIP]
> :heavy_check_mark: **Microsoft recommande** d’avoir au moins un administrateur pour lire tous les événements d’audit une fois par semaine et exporter vos événements d’audit tous les mois.

Si vous souhaitez stocker automatiquement vos événements d’audit sur une plus longue période, le journal d’audit de Privileged Identity Management est synchronisé automatiquement dans les [journaux d’audit Azure AD](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: **Microsoft recommande** de configurer la [supervision des journaux Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) afin d’archiver les événements d’audit dans un compte de stockage Azure pour les besoins de sécurité et de conformité.
