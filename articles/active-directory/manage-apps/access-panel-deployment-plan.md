---
title: Planifier le déploiement d’un panneau d’accès Azure Active Directory
description: Instructions pour le déploiement du panneau d’accès Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d8b6c6d40aa81bf56baed59f90417f2147fa56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897078"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Planifier le déploiement d’un panneau d’accès Azure Active Directory

Le panneau d’accès d’Azure Active Directory (Azure AD) est un portail basé sur le web qui vous permet de réduire les coûts de support, d’accroître la productivité et la sécurité, et de réduire la frustration des utilisateurs. Le système inclut des rapports détaillés de suivi d’accès au système, et prévient les administrateurs de toute utilisation malveillante ou de tout abus.

En utilisant le panneau d’accès Azure AD, vous pouvez :

* Détecter et accéder à toutes les ressources Azure AD connectées de l’entreprise, telles que les applications
* Demander l’accès aux nouvelles applications et aux nouveaux groupes
* Gérer l’accès à ces ressources pour les autres personnes
* Gérer les réinitialisations de mot de passe en libre-service et les paramètres d’Azure Multi-Factor Authentication
* Gérer les appareils

Il permet également aux administrateurs de gérer les éléments suivants :

* Conditions d'utilisation
* Organisations
* Révisions d’accès


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Avantages de l’intégration du panneau d’accès Azure AD

Le panneau d’accès de Microsoft Azure Active Directory (Azure AD) offre aux entreprises les avantages suivants :

**Offre une expérience utilisateur intuitive** : Le panneau d’accès vous offre une plateforme unique pour toutes vos applications connectées à l’authentification unique Azure (SSO). Vous disposez d’un portail unifié pour trouver les paramètres existants et les nouvelles fonctionnalités telles que la gestion des groupes et la réinitialisation du mot de passe en libre-service, à mesure qu’elles sont ajoutées. L’expérience intuitive permet aux utilisateurs de continuer à travailler plus rapidement et à être plus productifs, tout en réduisant leur frustration.

**Augmente la productivité** : L’authentification unique (SSO) est activée pour toutes les applications utilisateur du panneau d’accès. L’activation de l’authentification unique sur les applications d’entreprise et Office 365 offre une expérience de connexion supérieure en réduisant ou en éliminant des invites de connexion excessives. Le panneau d’accès utilise l’appartenance libre-service et dynamique, et améliore la sécurité globale de votre système d’identité. Il vérifie pour cela que l’accès aux applications est géré par des personnes appropriées. Le panneau d’accès fait office de page d’accueil cohérente pour vous permettre de trouver rapidement des ressources et continuer à travailler.

**Vous aide à gérer les coûts** : L’activation du panneau d’accès dans Azure AD peut permettre de réduire les infrastructures locales. Cela permet également de réduire les coûts de support grâce à un portail cohérent pour trouver toutes vos applications, demander l’accès aux ressources et gérer vos comptes.

**Augmente la flexibilité et la sécurité** : Le panneau d’accès vous permet d’accéder à la sécurité et à la flexibilité qu’offre une plateforme cloud. Les administrateurs peuvent facilement modifier les paramètres des applications et des ressources et répondre aux nouvelles exigences de sécurité sans affecter les utilisateurs.

**Permet l’audit et le suivi de l’utilisation de façon robuste** : L’audit et le suivi de l’utilisation pour toutes les fonctionnalités de l’utilisateur vous permettent de savoir quand les utilisateurs utilisent leurs ressources, ce qui vous aide à évaluer la sécurité.

### <a name="licensing-considerations"></a>Considérations relatives aux licences

Le panneau d’accès est gratuit et ne nécessite aucune licence pour une utilisation de base. Toutefois, le nombre d’objets contenus dans votre répertoire et les fonctionnalités supplémentaires que vous souhaitez déployer peuvent nécessiter des licences supplémentaires. Les scénarios Azure AD ayant des exigences en matière de licences incluent les fonctionnalités de sécurité suivantes :

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Appartenance basée sur des groupes](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Réinitialisation de mot de passe en libre service](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Consultez le [guide complet des licences pour Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Conditions préalables pour le déploiement du panneau d’accès Azure AD

Avant de commencer ce projet, vérifiez que vous remplissez les conditions préalables suivantes :

* [Intégration de l’authentification unique de l’application](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Gestion de l’infrastructure d’utilisateurs et de groupes Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Planifier le déploiement du panneau d’accès Azure AD

Le tableau suivant présente les principaux cas d’utilisation d’un déploiement de panneau d’accès :

| Domaine| Description |
| - | - |
| Accès| Le portail du panneau d’accès est accessible depuis les appareils d’entreprise et personnels au sein du réseau d’entreprise. |
|Accès | Le portail du panneau d’accès est accessible depuis les appareils d’entreprise en dehors du réseau d’entreprise. |
| Audit| Les données d’utilisation sont téléchargées dans les systèmes de l’entreprise au moins tous les 29 jours. |
| Gouvernance| Le cycle de vie des affectations d’utilisateurs aux applications et aux groupes connectés à Azure AD est défini et surveillé. |
| Sécurité| L’accès aux ressources est contrôlé par le biais des attributions d’utilisateurs et de groupes. Seuls les utilisateurs autorisés peuvent gérer l’accès aux ressources. |
| Performances| Les chronologies de propagation des affectations d’accès sont documentées et surveillées. |
| Expérience utilisateur| Les utilisateurs connaissent les fonctionnalités du panneau d’accès et savent les utiliser.|
| Expérience utilisateur| Les utilisateurs peuvent gérer leur accès aux applications et aux groupes.|
| Expérience utilisateur| Les utilisateurs peuvent gérer leurs comptes. |
| Expérience utilisateur| Les utilisateurs connaissent la compatibilité du navigateur. |
| Support| Les utilisateurs peuvent trouver de l’aide pour les problèmes liés au panneau d’accès. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Meilleures pratiques pour le déploiement du panneau d’accès Azure AD

La fonctionnalité du panneau d’accès peut être activée graduellement. Nous vous recommandons d’utiliser l’ordre de déploiement suivant :

1. My Apps
   * Lanceur d’applications
   * Gestion des applications en libre-service
   * Intégration de Microsoft Office 365

1. Détection d’applications en libre-service
   * Réinitialisation de mot de passe libre-service
   * Paramètres de Multi-Factor Authentication
   * Gestion des appareils
   * Conditions d’utilisation
   * Gérer les organisations

1. Mes groupes
   * Gestion des groupes en libre service
1. Révisions d’accès
   * Gestion de la révision d’accès

Bien commencer avec My Apps présente aux utilisateurs le portail comme un emplacement commun pour accéder aux ressources. L’ajout de la découverte d’applications en libre-service s’appuie sur l’expérience My Apps. Mes groupes et les révisions d’accès s’appuient sur les fonctionnalités en libre-service.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Planifier les configurations pour le panneau d’accès Azure AD

Le tableau suivant répertorie plusieurs configurations importantes du panneau d’accès et les valeurs typiques que vous pouvez utiliser :

| Configuration| Valeurs typiques |
| - | - |
| Déterminer les groupes pilotes| Identifiez le groupe de sécurité Azure AD à utiliser et assurez-vous que tous les membres du pilote font partie du groupe. |
| Déterminez le ou les groupes à activer pour la production.| Identifiez les groupes de sécurité Azure AD ou les groupes Active Directory synchronisés sur Azure AD à utiliser. Veillez à ce que tous les membres du pilote fassent partie du groupe. |
| Permettre aux utilisateurs d’utiliser l’authentification unique pour certains types d’applications| SSO fédérée, OAuth, authentification unique par mot de passe, proxy d’application |
| Autoriser les utilisateurs à utiliser la réinitialisation de mot de passe en libre-service | Oui |
| Permettre aux utilisateurs d’utiliser Multi-Factor Authentication| Oui |
| Permettre aux utilisateurs d’utiliser la gestion de groupes en libre-service pour certains types de groupes| Groupes de sécurité, groupes Office 365 |
| Autoriser les utilisateurs à utiliser la gestion des applications en libre-service| Oui |
| Autoriser les utilisateurs à utiliser des révisions d’accès| Oui |

### <a name="plan-consent-strategy"></a>Stratégie de consentement au plan

Les utilisateurs ou les administrateurs doivent accepter les conditions d’utilisation et les stratégies de confidentialité de l’application. Si possible, en fonction de vos règles d’entreprise, utilisez le consentement de l’administrateur, qui offre aux utilisateurs une meilleure expérience.

Pour utiliser le consentement de l’administrateur, vous devez être un administrateur général de l’organisation, et les applications doivent être :

* Inscrites dans votre organisation

* Inscrites dans une autre organisation Azure AD et précédemment autorisées par au moins un utilisateur

Pour plus d’informations, consultez [Configurer le consentement de l’utilisateur final pour une application dans Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Impliquer les parties prenantes appropriées

Lorsque des projets technologiques échouent, cela est généralement dû à des attentes qui ne correspondent pas à l’impact, aux résultats et aux responsabilités réels. Pour éviter un tel cas de figure, [veillez à faire appel aux bonnes personnes](../fundamentals/active-directory-deployment-plans.md) et à ce que les rôles des parties prenantes soient bien compris.

### <a name="plan-communications"></a>Planifier les communications

La communication est essentielle à la réussite de tout nouveau service. Indiquez de manière proactive aux utilisateurs quand et comment leur expérience changera et la façon d’obtenir un support si nécessaire.

Bien que le panneau d’accès ne crée généralement pas de problèmes pour les utilisateurs, il est important de bien se préparer. Créez des guides et une liste de toutes les ressources pour votre service de support technique avant votre lancement.

#### <a name="communications-templates"></a>Modèles de communication

Microsoft fournit [des modèles personnalisables pour les e-mails et autres communications](https://aka.ms/APTemplates) pour le panneau d’accès. Vous pouvez adapter ces ressources pour les utiliser dans d’autres canaux de communication, en fonction de la culture de votre entreprise.

## <a name="plan-your-sso-configuration"></a>Planifier votre configuration d’authentification unique

Lorsqu’un utilisateur « se connecte » à une application, il passe par un processus d’authentification qui lui permet de prouver son identité. Sans l’authentification unique, il s’agit d’un mot de passe stocké au niveau de l’application. Cela implique que l’utilisateur connaisse ce mot de passe. Avec l’authentification unique, les informations d’identification des utilisateurs sont transmises à l’application afin qu’ils n’aient pas à entrer à nouveau les mots de passe pour chaque application.

Pour lancer des applications dans My Apps, l’authentification unique doit être activée.

Azure AD prend en charge trois moyens d’activer [l’authentification unique sur les applications](what-is-single-sign-on.md) :

* **Authentification unique fédérée** 
    * Permet aux applications de se tourner vers Azure AD pour l’authentification des utilisateurs au lieu de leur demander un mot de passe. 
    * Cela est pris en charge pour les applications utilisant des protocoles comme SAML 2.0, WS-Federation ou OpenID Connect. Il s’agit du mode d’authentification unique le plus riche.

* **Authentification unique par mot de passe** 
    * Permet de sécuriser le stockage et la lecture des mots de passe des applications avec une extension de navigateur web ou une application mobile. 
    * Utilise le processus de connexion existant fourni par l’application, mais permet aux administrateurs de gérer les mots de passe. L’utilisateur n’a pas besoin de connaître le mot de passe.

* **Authentification unique existante** 
    * Permet à Azure AD de tirer parti de l’authentification unique existante qui a été configurée pour l’application.
    * Permet à ces applications d’être liées aux portails du panneau d’accès Azure AD ou Office 365. 
    * Active la création de rapports supplémentaires dans Azure AD lorsque les applications sont lancées ici. 
    * Comprend l’utilisation du proxy d’application Azure et du mode d’authentification unique lié.

Découvrez comment configurer le mode d’authentification unique d’une application ici : [S’authentifier avec l’authentification unique auprès des applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pour une expérience optimale sur la page My Apps, commencez par l’intégration des applications cloud disponibles pour l’authentification unique fédérée. L’authentification unique fédérée permet aux utilisateurs d’avoir une expérience en un clic cohérente sur leurs surfaces de lancement d’application et a tendance à être plus robuste dans le contrôle de la configuration.

Utilisez l’authentification unique fédérée avec Azure AD (OpenID Connect/SAML) si elle est prise en charge par l’application plutôt que l’authentification unique basée sur les mots de passe ou sur ADFS.

Pour plus d’informations sur le déploiement et la configuration de vos applications SaaS, consultez le [plan de déploiement de l’authentification unique SaaS](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Planifier le déploiement de l’extension de navigateur My Apps

Lorsque des utilisateurs se connectent à des applications SSO basées sur un mot de passe, ils doivent installer et utiliser l’extension de connexion sécurisée My Apps. L’extension exécute un script qui transmet le mot de passe dans le formulaire de connexion de l’application. Les utilisateurs sont invités à installer l’extension lorsqu’ils lancent l’application SSO basée sur un mot de passe pour la première fois. Pour plus d’informations sur l’extension, consultez la documentation sur [l’installation de l’extension de navigateur du panneau d’accès](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav).

Si vous devez intégrer des applications SSO basées sur un mot de passe, vous devez définir un mécanisme de déploiement de l’extension à l’échelle avec les [navigateurs pris en charge](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Options disponibles :

* [Stratégie de groupe pour Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Configuration Manager pour Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [Téléchargement et configuration pilotés par l’utilisateur pour Chrome, Firefox, Microsoft Edge ou Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

En savoir plus : [Configurer l’authentification unique par mot de passe](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)

Les utilisateurs qui n’utilisent pas d’applications SSO basées sur un mot de passe tirent également parti de l’extension. Ces avantages incluent la possibilité de lancer une application à partir de sa barre de recherche, de rechercher l’accès aux applications récemment utilisées et d’avoir un lien vers la page My Apps.

Voici ce que l’utilisateur verra lors du premier lancement d’une application SSO basée sur un mot de passe :

![Capture d’écran de l’écran d’installation de l’extension de navigateur My Apps ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Planifier l’accès mobile

Un navigateur protégé avec la stratégie Intune (Microsoft Edge ou Intune Managed Browser) est nécessaire pour les utilisateurs mobiles qui lancent des applications SSO basées sur un mot de passe. Un navigateur protégé par une stratégie permet de transférer le mot de passe enregistré pour l’application. Microsoft Edge et Managed Browser fournissent un ensemble de fonctionnalités de protection des données web. Vous pouvez également utiliser Microsoft Edge pour les scénarios d’entreprise sur les appareils iOS et Android. Microsoft Edge prend en charge les mêmes scénarios de gestion qu’Intune Managed Browser et améliore l’expérience de l’utilisateur. En savoir plus : [Gérer l’accès web à l’aide d’un navigateur protégé par une stratégie Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser)

## <a name="plan-your-my-apps-deployment"></a>Planifier le déploiement de My Apps

La base du panneau d’accès est le lanceur d’applications My Apps, auquel les utilisateurs accèdent sur [https://myapps.microsoft.com](https://myapps.microsoft.com/). Les pages My Apps fournissent aux utilisateurs un emplacement unique pour commencer leur travail et accéder à leurs applications nécessaires. Ici, les utilisateurs trouvent une liste de toutes les applications pour lesquelles l’authentification unique est accessible. 

![Capture d’écran du panneau d’applications](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Les mêmes applications s’affichent dans le lanceur d’applications Office 365 quand les utilisateurs utilisent le portail Office 365.

Planifiez l’ordre dans lequel vous allez ajouter des applications au lanceur My Apps, et indiquez si vous allez les déployer progressivement ou en une seule fois. Pour ce faire, créez un inventaire des applications répertoriant le type d’authentification et les intégrations de l’authentification unique existantes pour chaque application.

#### <a name="add-applications-to-the-my-apps-panel"></a>Ajouter des applications au panneau My Apps

Toutes les applications Azure AD compatibles avec l’authentification unique peuvent être ajoutées au lanceur My Apps. Les autres applications sont ajoutées à l’aide de l’option Authentification unique liée. Vous pouvez configurer une vignette d’application qui établit un lien vers l’URL de votre application web existante. L’authentification unique liée vous permet de commencer à diriger les utilisateurs vers le portail My Apps sans avoir à migrer toutes les applications vers l’authentification unique Azure AD. Vous pouvez migrer vers des applications avec authentification unique Azure AD configurée sans interrompre l’expérience des utilisateurs.

#### <a name="use-my-apps-collections"></a>Utiliser des collections Mes applications

Par défaut, toutes les applications sont répertoriées sur une seule page. Toutefois, vous pouvez utiliser des collections pour regrouper des applications associées, puis les présenter sous un onglet distinct, ce qui les rend plus faciles à trouver. Par exemple, vous pouvez utiliser des collections pour créer des regroupements logiques d’applications pour des rôles de travail, des tâches, des projets, et autres ressources spécifiques. Pour plus d’informations, consultez [Comment utiliser des collections Mes applications pour personnaliser des panneaux d’accès utilisateur](access-panel-collections.md). 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Choisir d’utiliser My Apps ou un portail existant

Vos utilisateurs disposent peut-être déjà d’une application ou d’un portail autre que My Apps. Dans ce cas, décidez si vous souhaitez prendre en charge les deux portails ou si vous n’en utiliserez qu’un seul.

Si un portail existant est déjà utilisé comme point de départ pour les utilisateurs, vous pouvez intégrer la fonctionnalité My Apps à l’aide des « URL d’accès utilisateur ». Les URL d’accès utilisateur fonctionnent comme des liens directs vers les applications disponibles dans le portail My Apps. Ces URL peuvent être incorporées dans tout site web existant. Lorsqu’un utilisateur clique sur le lien, il ouvre l’application à partir du portail My Apps.

La propriété URL d’accès utilisateur se trouve dans la zone **Propriétés** de l’application dans le portail Azure.

![Capture d’écran du panneau d’applications](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Planifier la découverte et l’accès aux applications en libre-service

Une fois qu’un ensemble principal d’applications est déployé sur la page My Apps d’un utilisateur, nous vous recommandons d’activer les fonctionnalités de gestion des applications en libre-service. La découverte d’applications en libre-service permet aux utilisateurs :

* De rechercher de nouvelles applications à ajouter à My Apps. 
* D’ajouter des applications facultatives qu’ils ne savaient pas nécessaires au moment de l’installation.

Des flux de travail d’approbation sont disponibles pour une approbation explicite pour l’accès aux applications. Les utilisateurs qui sont des approbateurs reçoivent des notifications dans le portail My Apps quand une demande d’accès à l’application est en attente.

## <a name="plan-self-service-group-membership"></a>Planifier l’appartenance de groupes en libre-service 

Vous pouvez autoriser les utilisateurs à créer et gérer leurs propres groupes de sécurité ou Office 365 dans Azure AD. Le propriétaire du groupe peut approuver ou refuser des demandes d’appartenance et peut déléguer le contrôle de l’appartenance au groupe. Les fonctionnalités de gestion de groupes en libre-service ne sont pas disponibles pour les groupes de sécurité activés pour la messagerie électronique ou les listes de distribution.

Pour planifier l’appartenance de groupe en libre-service, déterminez si vous autorisez tous les utilisateurs de votre organisation à créer et à gérer des groupes, ou seulement un sous-ensemble d’utilisateurs. S’il s’agit d’un sous-ensemble d’utilisateurs, vous devez configurer un groupe auquel ces personnes sont ajoutées. Pour plus d’informations sur l’activation de ces scénarios, consultez [Configurer la gestion de groupes en libre-service dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management).

## <a name="plan-reporting-and-auditing"></a>Planifier la création de rapports et d’audits

Azure AD fournit des [rapports proposant des insights techniques et commerciaux](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). Collaborez avec vos propriétaires d’applications métier et techniques pour assumer la propriété de ces rapports et les utiliser régulièrement. Le tableau ci-dessous fournit quelques exemples de scénarios de création de rapports typiques.

|   | Gérer le risque.| Augmenter la productivité| Gouvernance et conformité |
|  - |- | - | - |
| Types de rapports|  Autorisations et utilisation des applications| Activité d’approvisionnement de compte| Vérifier qui accède aux applications |
| Actions potentielles| Auditer l’accès, révoquer les autorisations| Corriger les erreurs d’approvisionnement| Révoquer l’accès |

Azure AD conserve la plupart des données d’audit pendant 30 jours. Les données sont disponibles via le portail d’administration Azure ou l’API, afin que vous puissiez les télécharger dans vos systèmes d’analyse.

#### <a name="auditing"></a>Audit

Les journaux d’audit pour l’accès aux applications sont disponibles pendant 30 jours. Si l’audit de sécurité au sein de votre entreprise nécessite une rétention plus longue, les journaux doivent être exportés dans un outil SIEM (Security information Event and Management) comme Splunk ou ArcSight.

Pour l’audit, les rapports et les sauvegardes de récupération d’urgence, documentez la fréquence de téléchargement requise, le système cible et les responsables de la gestion de chaque sauvegarde. Vous n’avez pas nécessairement besoin de sauvegardes d’audit et de rapports distinctes. Votre sauvegarde de récupération d’urgence doit être une entité distincte.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Déployer des applications dans le panneau My Apps des utilisateurs

Une fois qu’une application a été configurée pour l’authentification unique, les groupes se voient attribuer l’accès. Les utilisateurs des groupes affectés auront accès à l’application et la verront dans My Apps et le lanceur d’applications Office 365.

Consultez [Assigner des utilisateurs et des groupes à une application dans Active Directory](methods-for-assigning-users-and-groups.md).

Si, pendant le test ou le déploiement, vous souhaitez ajouter des groupes, mais que vous n’autorisez pas encore les applications à s’afficher dans My Apps, consultez [Masquer une application de l’expérience utilisateur dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Déployer des applications Microsoft Office 365 sur My Apps

Pour les applications Office 365, les utilisateurs reçoivent une copie d’Office en fonction des licences qui leur sont attribuées. Une condition préalable à l’accès aux applications Office consiste à affecter aux utilisateurs les licences appropriées liées aux applications Office. Lorsque vous affectez une licence à un utilisateur, il voit automatiquement les applications associées à la licence dans la page My Apps et dans le lanceur d’applications Office 365.

Si vous souhaitez masquer un ensemble d’applications Office auprès des utilisateurs, vous avez la possibilité de masquer les applications à partir du portail My Apps, tout en autorisant l’accès à partir du portail Office 365. Recherchez ces paramètres dans la partie Paramètres utilisateur de l’application. En savoir plus : [Masquer une application de l’expérience utilisateur dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![Capture d’écran de la configuration du masquage des applications](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Déployer des fonctionnalités de libre-service pour les applications

L’accès aux applications en libre-service permet aux utilisateurs de découvrir et de demander l’accès aux applications. Les utilisateurs ont la liberté d’accéder aux applications dont ils ont besoin sans passer par un groupe informatique à chaque fois pour demander l’accès. Lorsqu’un utilisateur demande l’accès et qu’il est approuvé, soit automatiquement, soit manuellement par un propriétaire de l’application, il est ajouté à un groupe sur le système principal. La création de rapports note qui a demandé, approuvé ou supprimé un accès, et vous permet de contrôler la gestion des rôles affectés.

Vous pouvez déléguer l’approbation des demandes d’accès aux applications aux approbateurs de l’entreprise. L’approbateur de l’entreprise peut définir les mots de passe d’accès aux applications à partir de la page My Apps de l’approbateur de l’entreprise.

En savoir plus : [Guide pratique pour utiliser l’accès aux applications en libre-service](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)

![Capture d’écran de la configuration de la gestion des applications en libre-service](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Valider votre déploiement

Assurez-vous que le déploiement du panneau d’accès est soigneusement testé et qu’un plan de restauration est en place.

Les tests suivants doivent être effectués avec les appareils d’entreprise et les appareils personnels. Ces cas de test doivent également refléter les cas d’utilisation de votre entreprise. Voici quelques cas basés sur les exemples d’exigences d’entreprise de ce document ainsi que sur des scénarios techniques classiques. Ajoutez d’autres personnes spécifiques à vos besoins.

#### <a name="application-sso-access-test-case-examples"></a>Exemples de cas de test de l’authentification unique des applications :


| Cas métier| Résultat attendu |
| - | -|
| L’utilisateur se connecte au portail My Apps| L’utilisateur peut se connecter et voir ses applications |
| L’utilisateur lance une application avec authentification unique fédérée| L’utilisateur est automatiquement connecté à l’application |
| L’utilisateur lance une application avec authentification unique par mot de passe pour la première fois| L’utilisateur doit installer l’extension My Apps |
| L’utilisateur lance une application avec authentification unique par mot de passe une nouvelle fois| L’utilisateur est automatiquement connecté à l’application |
| L’utilisateur lance une application à partir du portail Office 365| L’utilisateur est automatiquement connecté à l’application |
| L’utilisateur lance une application à partir de Managed Browser| L’utilisateur est automatiquement connecté à l’application |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Exemples de cas de test des fonctionnalités de libre-service des applications


| Cas métier| Résultat attendu |
| - | - |
| L’utilisateur peut gérer l’appartenance à l’application| L’utilisateur peut ajouter/supprimer les membres qui ont accès à l’application |
| L’utilisateur peut modifier l’application| L’utilisateur peut modifier la description et les informations d’identification de l’application pour les applications avec authentification unique par mot de passe |

### <a name="rollback-steps"></a>Étapes de restauration

Il est important d’avoir un plan de repli en cas d’échec de votre déploiement. Si la configuration de l’authentification unique échoue pendant le déploiement, vous devez savoir comment [résoudre les problèmes avec l’authentification unique](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) et réduire l’impact sur vos utilisateurs. Dans les cas extrêmes, vous devrez peut-être [annuler l’utilisation de l’authentification unique](../manage-apps/plan-sso-deployment.md).


## <a name="manage-your-implementation"></a>Gérer l’implémentation

Nous vous recommandons d’utiliser le rôle le moins privilégié pour accomplir une tâche requise dans Azure Active Directory. [Passez en revue les différents rôles disponibles](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) et choisissez celui qui répond le mieux aux besoins de chaque utilisateur de l’application. Certains rôles devront peut-être être appliqués temporairement, puis supprimés une fois le déploiement terminé.

| Rôles| Rôles| Rôle Azure AD  |
| - | -| -|
| Administrateur de support technique| Support de niveau 1| None |
| Administrateur d’identité| Configurer et déboguer quand des problèmes impactent Azure AD| Administrateur général |
| Administrateur d’application| Attestation de l’utilisateur dans l’application, configuration des utilisateurs disposant d’autorisations| None |
| Administrateurs de l’infrastructure| Propriétaire de la substitution de certificat| Administrateur général |
| Chef d’entreprise/Partie prenante| Attestation de l’utilisateur dans l’application, configuration des utilisateurs disposant d’autorisations| None |

Vous pouvez utiliser [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) pour gérer vos rôles dans le but de fournir aux utilisateurs qui disposent d’autorisations sur l’annuaire des fonctionnalités supplémentaires au niveau des audits, du contrôle et des révisions d’accès.

### <a name="troubleshoot-access-panel-issues"></a>Résoudre les problèmes liés au panneau d’accès

Créez des guides de dépannage pour votre organisation de support avec des scénarios courants et pointant vers la documentation Microsoft quant à leur résolution. Vous pouvez créer des guides qui détaillent la prise en charge des niveaux utilisés par votre organisation.

Consultez les guides de dépannage ci-dessous pour référence :

[Les applications ne s’affichent pas](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Des applications inattendues s’affichent](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[L’utilisateur ne parvient pas à se connecter au site web du panneau d’accès](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problèmes liés à l’accès aux applications en libre-service](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problèmes avec l’extension de navigateur](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Étapes suivantes

[Planifier un déploiement d’Azure Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)
