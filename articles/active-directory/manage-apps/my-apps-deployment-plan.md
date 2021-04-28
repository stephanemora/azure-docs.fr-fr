---
title: Planifier la configuration de Mes applications Azure Active Directory
description: Guide de planification pour utiliser efficacement Mes applications dans votre organisation.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: baselden
ms.openlocfilehash: 9f05e14de9d6ebe76f722d1e4b21bc2085a74888
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108125133"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Planifier la configuration de Mes applications Azure Active Directory

> [!NOTE]
> Cet article est destiné aux professionnels de l’informatique qui doivent planifier la configuration du portail Mes applications de leur organisation. 
>
> **Pour la documentation destinée à l’utilisateur final, consultez [Se connecter et démarrer des applications à partir du portail Mes applications](../user-help/my-apps-portal-end-user-access.md)** .

Azure Active Directory (Azure AD) My Apps est un portail web qui vous aide à lancer et à gérer des applications. La page Mes applications fournit aux utilisateurs un emplacement unique pour commencer leur travail et trouver toutes les applications auxquelles ils ont accès. Les utilisateurs accèdent à Mes applications à l’adresse [https://myapps.microsoft.com](https://myapps.microsoft.com/).

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>Pourquoi configurer Mes applications ?

Par défaut, le portail Mes applications est accessible aux utilisateurs et ne peut pas être désactivé. Il est important de le configurer pour qu’ils aient la meilleure expérience possible et que le portail reste utile. 

Toute application figurant dans la liste des applications d’entreprise d’Azure Active Directory apparaît lorsque les deux conditions suivantes sont réunies :

* La propriété de visibilité de l’application a la valeur true. 

* L’application est attribuée à un utilisateur ou à un groupe quelconque. Elle apparaît pour les utilisateurs attribués.

La configuration du portail garantit que les bonnes personnes peuvent facilement trouver les bonnes applications.

 
### <a name="how-is-the-my-apps-portal-used"></a>Comment le portail Mes applications est-il utilisé ?

Les utilisateurs accèdent au portail Mes applications pour :

* Détecter toutes les applications connectées à Azure AD de leur organisation auxquelles ils ont accès et y accéder.

   * Il est préférable de s’assurer que les applications sont configurées pour l’authentification unique (SSO) afin de fournir aux utilisateurs la meilleure expérience.

* Demander l’accès aux nouvelles applications qui sont configurées pour le libre-service.

* Créer des collections personnelles d’applications.

* Gérer l’accès aux applications pour les autres utilisateurs lorsqu’ils sont affectés au rôle de propriétaire du groupe ou que l’on leur délègue le contrôle du groupe utilisé pour accorder l’accès aux applications.

Les administrateurs peuvent configurer les éléments suivants :

* [Les expériences de consentement](../manage-apps/configure-user-consent.md) ,y compris les conditions d’utilisation du service.

* [La détection des applications en libre-service et les demandes d’accès](../manage-apps/access-panel-manage-self-service-access.md).

* [Les collections d’applications](../manage-apps/access-panel-collections.md).

* L’attribution d’icônes aux applications.

* Des noms conviviaux pour les applications.

* La personnalisation de l’entreprise intégrée sur Mes applications.

 

## <a name="plan-consent-configuration"></a>Planifier la configuration du consentement

### <a name="user-consent-for-applications"></a>Consentement de l’utilisateur pour les applications 

Pour qu’un utilisateur puisse se connecter à une application et que l’application puisse accéder aux données de votre organisation, un utilisateur ou un administrateur doit accorder les permissions d’application. Vous pouvez configurer si le consentement de l’utilisateur est autorisé et dans quelles conditions. **Microsoft vous recommande d’autoriser uniquement le consentement de l’utilisateur pour les applications d’éditeurs vérifiés.**

Pour plus d’informations, consultez [Configurer le consentement des utilisateurs finaux aux applications](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>Consentement du propriétaire du groupe pour les applications qui accèdent aux données

Les propriétaires de groupes et d’équipes peuvent autoriser des applications telles que des applications publiées par des fournisseurs tiers à accéder aux données de votre organisation associées à un groupe. Pour en savoir plus, consultez [Consentement propre à la ressource dans Microsoft Teams](/microsoftteams/resource-specific-consent). 

Vous pouvez configurer si vous souhaitez autoriser ou désactiver cette fonctionnalité.

Pour plus d’informations, consultez [Configurer les autorisations du consentement de groupe](../manage-apps/configure-user-consent-groups.md).

### <a name="plan-communications"></a>Planifier les communications

La communication est essentielle à la réussite de tout nouveau service. Indiquez de manière proactive aux utilisateurs quand et comment leur expérience changera et la façon d’obtenir un support si nécessaire.

Bien que Mes applications ne crée généralement pas de problèmes pour les utilisateurs, il est important de bien se préparer. Créez des guides et une liste de toutes les ressources pour votre service de support technique avant votre lancement.

#### <a name="communications-templates"></a>Modèles de communication

Microsoft fournit [des modèles personnalisables pour les e-mails et autres communications](https://aka.ms/APTemplates) pour Mes applications. Vous pouvez adapter ces ressources pour les utiliser dans d’autres canaux de communication, en fonction de la culture de votre entreprise.

 

## <a name="plan-your-sso-configuration"></a>Planifier votre configuration d’authentification unique

Il est préférable d’activer l’authentification unique pour toutes les applications dans le portail Mes applications afin que les utilisateurs aient une expérience transparente sans avoir à entrer leurs informations d’identification.

Azure AD prend en charge plusieurs options d’authentification unique. 

* Pour plus d’informations, consultez [Options d’authentification unique dans Azure AD](sso-options.md).

* Pour plus d’informations sur l’utilisation d’Azure AD en tant que fournisseur d'identité pour une application, consultez la [Série de démarrages rapides sur la gestion des applications](../manage-apps/view-applications-portal.md).

### <a name="use-federated-sso-if-possible"></a>Utiliser l’authentification unique fédérée si possible

Pour une expérience utilisateur optimale sur la page Mes applications, commencez par l’intégration des applications cloud disponibles pour l’authentification unique fédérée (OpenID Connect ou SAML). L’authentification unique fédérée permet aux utilisateurs d’avoir une expérience en un clic cohérente sur toutes les surfaces de lancement d’applications et tend à être plus robuste dans le contrôle de la configuration.

Pour plus d’informations sur la configuration de vos applications SaaS pour l’authentification unique, consultez le [plan de déploiement de l’authentification unique SaaS]../Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>Considérations relatives aux circonstances spéciales de l’authentification unique

> [!TIP]
> Pour une meilleure expérience utilisateur, utilisez l’authentification unique fédérée avec Azure AD (OpenID Connect/SAML) si elle est prise en charge par l’application plutôt que l’authentification unique basée sur les mots de passe ou sur ADFS.

Pour vous connecter à des applications SSO basée sur un mot de passe ou à des applications qui sont accessibles par Proxy d’application d’Azure AD, les utilisateurs doivent installer et utiliser l’extension de connexion sécurisée Mes applications. Les utilisateurs sont invités à installer l’extension lorsqu’ils lancent l’application SSO basée sur un mot de passe ou Proxy d’application pour la première fois. 

![Capture d’écran](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

Pour plus d’informations sur l’extension, consultez [Installation de l’extension de navigateur Mes applications](../user-help/my-apps-portal-end-user-access.md).

Si vous devez intégrer ces applications, vous devez définir un mécanisme de déploiement de l’extension à grande échelle avec les [navigateurs pris en charge](../user-help/my-apps-portal-end-user-access.md). Les options sont les suivantes :

* [Téléchargement et configuration pilotés par l’utilisateur pour Chrome, Firefox, Microsoft Edge ou Internet Explorer](../user-help/my-apps-portal-end-user-access.md)

* [Configuration Manager pour Internet Explorer](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

L’extension permet aux utilisateurs de lancer une application à partir de sa barre de recherche, de rechercher l’accès aux applications récemment utilisées et d’avoir un lien vers la page Mes applications.

![Capture d’écran de la recherche d’extension Mes applications](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>Planifier l’accès mobile

Pour les applications qui utilisent l’authentification unique basée sur mot de passe ou qui sont accessibles à l’aide de [Proxy d’application Microsoft Azure AD](../manage-apps/application-proxy.md), vous devez utiliser la version mobile de Microsoft Edge. Pour les autres applications, n’importe quel navigateur mobile peut être utilisé. 

### <a name="linked-sso"></a>Authentification unique liée

Les applications peuvent être ajoutées à l’aide de l’option Authentification unique liée. Vous pouvez configurer une vignette d’application qui établit un lien vers l’URL de votre application web existante. L’authentification unique liée vous permet de commencer à diriger les utilisateurs vers le portail My Apps sans avoir à migrer toutes les applications vers l’authentification unique Azure AD. Vous pouvez migrer progressivement vers des applications avec authentification unique Azure AD configurée sans interrompre l’expérience des utilisateurs.

## <a name="plan-the-user-experience"></a>Planifier l’expérience utilisateur

Par défaut, toutes les applications auxquelles l’utilisateur a accès et toutes les applications configurées pour la détection en libre-service s’affichent dans le panneau Mes applications de l’utilisateur. Pour de nombreuses organisations, il peut s’agir d’une très longue liste, qui peut devenir fastidieuse si elle n’est pas organisée.

### <a name="plan-my-apps-collections"></a>Planifier les collections Mes applications

Chaque application Azure AD à laquelle un utilisateur a accès apparaît sur Mes applications dans la collection Toutes les applications. Utilisez des collections pour regrouper des applications associées, puis les présenter sous un onglet distinct, ce qui les rend plus faciles à trouver. Par exemple, vous pouvez utiliser des collections pour créer des regroupements logiques d’applications pour des rôles de travail, des tâches, des projets, et autres ressources spécifiques. 

Les utilisateurs finaux peuvent également personnaliser leur expérience en :

* créant leurs propres collections d’applications.

* [masquant et en réorganisant des collections d’applications](access-panel-collections.md).

![Capture d’écran de la configuration en libre-service](./media/my-apps-deployment-plan/collections.png)

Il existe une option permettant de masquer les applications à partir du portail Mes applications, tout en autorisant l’accès à partir d’autres emplacements, tels que le portail Microsoft 365. En savoir plus : [Masquer une application de l’expérience utilisateur dans Azure Active Directory](hide-application-from-user-portal.md).

> [!IMPORTANT]
> Seules 950 applications auxquelles un utilisateur a accès sont accessibles par le biais de Mes applications. Cela comprend les applications masquées par l’utilisateur ou l’administrateur. 

### <a name="plan-self-service-group-management-membership"></a>Planifier l’appartenance à la gestion de groupes en libre-service

Vous pouvez autoriser les utilisateurs à créer et gérer leurs propres groupes de sécurité ou Microsoft 365 dans Azure AD. Le propriétaire du groupe peut approuver ou refuser des demandes d’appartenance et peut déléguer le contrôle de l’appartenance au groupe. Les fonctionnalités de gestion de groupes en libre-service ne sont pas disponibles pour les groupes de sécurité activés pour la messagerie électronique ou les listes de distribution.

Pour planifier l’appartenance de groupe en libre-service, déterminez si vous autorisez tous les utilisateurs de votre organisation à créer et à gérer des groupes, ou seulement un sous-ensemble d’utilisateurs. S’il s’agit d’un sous-ensemble d’utilisateurs, vous devez configurer un groupe auquel ces personnes sont ajoutées. 

Pour plus d’informations sur l’activation de ces scénarios, consultez [Configurer la gestion de groupes en libre-service dans Azure Active Directory](../enterprise-users/groups-self-service-management.md).

### <a name="plan-self-service-application-access"></a>Planifier l’accès aux applications en libre-service

Vous pouvez permettre aux utilisateurs de découvrir des applications et d’en demander l’accès via le panneau Mes applications. Pour ce faire, vous devez d’abord : 

* activer la gestion de groupes en libre-service ;

* activer l’application pour l’authentification unique ;

* créer un groupe pour l’accès aux applications.

![Capture d’écran de la configuration en libre-service de Mes applications](./media/my-apps-deployment-plan/my-apps-self-service.png)

Lorsque les utilisateurs demandent l’accès, ils demandent l’accès au groupe sous-jacent, et les propriétaires du groupe peuvent être autorisés à gérer l’appartenance au groupe et donc l’accès aux applications. Des flux de travail d’approbation sont disponibles pour une approbation explicite pour l’accès aux applications. Les utilisateurs qui sont des approbateurs reçoivent des notifications dans le portail My Apps quand une demande d’accès à l’application est en attente.

## <a name="plan-reporting-and-auditing"></a>Planifier la création de rapports et d’audits

Azure AD fournit des [rapports proposant des insights techniques et commerciaux]../reports-monitoring/overview-reports.md). Collaborez avec vos propriétaires d’applications métier et techniques pour assumer la propriété de ces rapports et les utiliser régulièrement. Le tableau ci-dessous fournit quelques exemples de scénarios de création de rapports typiques.

| Exemple| Gérer le risque.| Augmenter la productivité| Gouvernance et conformité |
| - | - | - | -|
| Types de rapports| Autorisations et utilisation des applications| Activité d’approvisionnement de compte| Vérifier qui accède aux applications |
| Actions potentielles| Auditer l’accès, révoquer les autorisations| Corriger les erreurs d’approvisionnement| Révoquer l’accès |


Azure AD conserve la plupart des données d’audit pendant 30 jours. Les données sont disponibles via le portail d’administration Azure ou l’API, afin que vous puissiez les télécharger dans vos systèmes d’analyse.

#### <a name="auditing"></a>Audit

Les journaux d’audit pour l’accès aux applications sont disponibles pendant 30 jours. Si votre organisation nécessite une rétention plus longue, exportez les journaux vers un outil SIEM (Informations de sécurité et gestion d’événements), tel que Splunk ou ArcSight.

Pour l’audit, les rapports et les sauvegardes de récupération d’urgence, documentez la fréquence de téléchargement requise, le système cible et les responsables de la gestion de chaque sauvegarde. Vous n’avez pas nécessairement besoin de sauvegardes d’audit et de rapports distinctes. Votre sauvegarde de récupération d’urgence doit être une entité distincte.

## <a name="validate-your-deployment"></a>Valider votre déploiement

Assurez-vous que le déploiement de Mes applications est soigneusement testé et qu’un plan de restauration est en place.

Effectuez les tests suivants avec les appareils d’entreprise et les appareils personnels. Ces cas de test doivent également refléter les cas d’utilisation de votre entreprise. Voici quelques cas basés sur des scénarios techniques classiques. Ajoutez d’autres personnes spécifiques à vos besoins.

#### <a name="application-sso-access-test-case-examples"></a>Exemples de cas de test de l’authentification unique des applications :


| Cas métier| Résultat attendu |
| - | - |
| L’utilisateur se connecte au portail My Apps| L’utilisateur peut se connecter et voir ses applications |
| L’utilisateur lance une application avec authentification unique fédérée| L’utilisateur est automatiquement connecté à l’application |
| L’utilisateur lance une application avec authentification unique par mot de passe pour la première fois| L’utilisateur doit installer l’extension My Apps |
| L’utilisateur lance une application avec authentification unique par mot de passe une nouvelle fois| L’utilisateur est automatiquement connecté à l’application |
| L’utilisateur lance une application à partir du portail Microsoft 365| L’utilisateur est automatiquement connecté à l’application |
| L’utilisateur lance une application à partir de Managed Browser| L’utilisateur est automatiquement connecté à l’application |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Exemples de cas de test des fonctionnalités de libre-service des applications


| Cas métier| Résultat attendu |
| - | - |
| L’utilisateur peut gérer l’appartenance à l’application| L’utilisateur peut ajouter/supprimer les membres qui ont accès à l’application |
| L’utilisateur peut modifier l’application| L’utilisateur peut modifier la description et les informations d’identification de l’application pour les applications avec authentification unique par mot de passe |


### <a name="rollback-steps"></a>Étapes de restauration

Il est important d’avoir un plan de repli en cas d’échec de votre déploiement. Si la configuration de l’authentification unique échoue pendant le déploiement, vous devez savoir comment [résoudre les problèmes avec l’authentification unique](../hybrid/tshoot-connect-sso.md) et réduire l’impact sur vos utilisateurs. Dans les cas extrêmes, vous devrez peut-être [annuler l’utilisation de l’authentification unique](plan-sso-deployment.md).

## <a name="manage-your-implementation"></a>Gérer l’implémentation

Utilisez le rôle le moins privilégié pour accomplir une tâche requise dans Azure Active Directory. [Passez en revue les différents rôles disponibles](../roles/permissions-reference.md) et choisissez celui qui répond le mieux aux besoins de chaque utilisateur de l’application. Certains rôles devront peut-être être appliqués temporairement, puis supprimés une fois le déploiement terminé.

| Rôles| Rôles| Rôle Azure AD |
| - | - | - |
| Administrateur de support technique| Support de niveau 1| None |
| Administrateur d’identité| Configurer et déboguer quand des problèmes impactent Azure AD| Administrateur général |
| Administrateur d’application| Attestation de l’utilisateur dans l’application, configuration des utilisateurs disposant d’autorisations| None |
| Administrateurs de l’infrastructure| Propriétaire de la substitution de certificat| Administrateur général |
| Chef d’entreprise/Partie prenante| Attestation de l’utilisateur dans l’application, configuration des utilisateurs disposant d’autorisations| None |


Vous pouvez utiliser [Privileged Identity Management](../privileged-identity-management/pim-configure.md) pour gérer vos rôles dans le but de fournir aux utilisateurs qui disposent d’autorisations sur l’annuaire des fonctionnalités supplémentaires au niveau des audits, du contrôle et des révisions d’accès.

## <a name="next-steps"></a>Étapes suivantes

[Planifier un déploiement d'Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

[Planifier un déploiement de proxy d’application](application-proxy-deployment-plan.md)