---
title: Nouveautés Notes de publication - Azure Active Directory | Microsoft Docs
description: Découvrez les nouveautés d’Azure Active Directory, notamment les dernières notes de publication, les problèmes connus, les corrections de bogues, les fonctionnalités déconseillées et les modifications à venir.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50d8ef544029e7097d90651707da24d603bcecc9
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108137956"
---
# <a name="whats-new-in-azure-active-directory"></a>Nouveautés d’Azure Active Directory

>Soyez notifié de la disponibilité des mises à jour sur cette page en faisant un copier-coller de cette URL : `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` dans votre lecteur de flux ![icône RSS](./media/whats-new/feed-icon-16x16.png).

Azure AD bénéficie d’améliorations en continu. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues
- Fonctionnalités dépréciées
- Modifications planifiées

Cette page est mise à jour tous les mois. Donc, consultez-la régulièrement. Si vous recherchez des éléments antérieurs aux six derniers mois, vous les trouverez dans l’[Archive des Nouveautés d’Azure Active Directory](whats-new-archive.md).

---

## <a name="march-2021"></a>Mars 2021

### <a name="guidance-on-how-to-enable-support-for-tls-12-in-your-environment-in-preparation-for-upcoming-azure-ad-tls-1011-deprecation"></a>Conseils sur la façon d’activer la prise en charge de TLS 1.2 dans votre environnement, en vue de la prochaine dépréciation de TLS 1.0/1.1 dans Azure AD

**Type :** Modification planifiée  
**Catégorie de service :** N/A  
**Fonctionnalité de produit :** Standards

Azure Active Directory dépréciera les protocoles suivants dans toutes les régions du monde Azure Active Directory à compter du 30 juin 2021 :


- TLS 1.0
- TLS 1.1
- Suite de chiffrement 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Les environnements affectés sont les suivants :

- Azure Commercial Cloud
- Office 365 GCC et WW

Pour obtenir des instructions supplémentaires, consultez [Activer la prise en charge de TLS 1.2 dans votre environnement en vue de la dépréciation de TLS 1.1 et 1.0 dans Azure AD](/troubleshoot/azure/active-directory/enable-support-tls-environment).

---

### <a name="public-preview---azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>Préversion publique - La gestion des droits d’utilisation Azure AD prend désormais en charge la multigéographie SharePoint Online

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Les organisations qui utilisent la multigéographie SharePoint Online peuvent désormais inclure des sites d’environnements multigéographie spécifiques dans leurs packages d’accès de gestion des droits d’utilisation. [Plus d’informations](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site-preview)

---

### <a name="public-preview---restore-deleted-apps-from-app-registrations"></a>Préversion publique -Restaurer des applications supprimées à partir d’Inscriptions d’applications

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Expérience de développement
 
Les clients peuvent désormais afficher, restaurer et supprimer définitivement des inscriptions d’applications supprimées à partir du portail Azure. Cela s’applique uniquement aux applications associées à un annuaire, et non aux applications d’un compte Microsoft personnel. [Plus d’informations](../develop/howto-restore-app.md)
 
---

### <a name="public-preview---new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>Préversion publique - Nouvelle « action utilisateur » dans l’Accès conditionnel pour l’inscription ou la jonction d’appareils

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
 Une nouvelle action utilisateur nommée « Inscrire ou joindre des appareils » est disponible dans Accès conditionnel. Cette action utilisateur vous permet de contrôler les stratégies d’authentification multifacteur (MFA) pour l’inscription d’appareil Azure AD. 

Actuellement, cette action utilisateur vous permet uniquement d’activer MFA en tant que contrôle lorsque les utilisateurs inscrivent ou joignent des appareils à Azure AD. Les autres contrôles qui ne s’appliquent pas à l’inscription d’appareil Azure AD, ou qui en dépendent, sont désactivés avec cette action utilisateur. [Plus d’informations](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) 
 
---

### <a name="public-preview---optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>Préversion publique - Optimiser les groupes de connecteurs afin d’utiliser le service cloud de proxy d’application le plus proche

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès
 
Avec cette nouvelle fonctionnalité, les groupes de connecteurs peuvent être affectés au service de proxy d’application régional le plus proche dans lequel une application est hébergée. Cela peut améliorer les performances des applications dans les scénarios où elles sont hébergées dans des régions autres que la région du locataire d’accueil. [Plus d’informations](../manage-apps/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview) 
 
---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>Préversion publique - Inscription en libre service External Identities dans AAD à l’aide des comptes e-mail à code secret à usage unique

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C

Les utilisateurs externes pourront désormais utiliser des comptes e-mail à code secret à usage unique pour s’inscrire auprès d’applications métier et internes Azure AD. [Plus d’informations](../external-identities/one-time-passcode.md)

---

### <a name="public-preview---availability-of-ad-fs-sign-ins-in-azure-ad"></a>Préversion publique - Disponibilité des connexions AD FS dans Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Monitoring et création de rapports
 
L’activité de connexion AD FS peut désormais être intégrée à la création de rapports d’activité Azure AD, fournissant ainsi une vue unifiée de l’infrastructure d’identité hybride. À l’aide du rapport sur les connexions Azure AD, de Log Analytics et des classeurs Azure Monitor, il est possible d’effectuer une analyse approfondie pour les scénarios de connexion AAD et AD FS tels que les verrouillages de compte AD FS, les tentatives d’entrée de mots de passe incorrects et les pics de tentatives de connexion inattendues.

Pour plus d’informations, consultez [Connexions AD FS dans Azure AD avec Connect Health](../hybrid/how-to-connect-health-ad-fs-sign-in.md).

---

### <a name="general-availability---staged-rollout-to-cloud-authentication"></a>Disponibilité générale - Déploiement par étapes vers l’authentification cloud

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** AD Connect  
**Fonctionnalité de produit :** Authentification utilisateur
 
Le déploiement par étapes vers l’authentification cloud est désormais en disponibilité générale. La fonctionnalité de déploiement par étapes vous permet de tester de manière sélective des groupes d’utilisateurs avec des méthodes d’authentification cloud, telles que l’authentification directe (PTA) ou la synchronisation du hachage de mot de passe (PHS). Entre-temps, tous les autres utilisateurs des domaines fédérés continuent à utiliser les services de fédération, tels qu’AD FS ou tout autre service de fédération pour authentifier les utilisateurs. [Plus d’informations](../hybrid/how-to-connect-staged-rollout.md)

---

### <a name="general-availability---user-type-attribute-can-now-be-updated-in-the-azure-admin-portal"></a>Disponibilité générale - L’attribut Type d’utilisateur peut désormais être mis à jour dans le portail d’administration Azure

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** expérience utilisateur et gestion  
**Fonctionnalité de produit :** User Management
 
Les clients peuvent désormais mettre à jour le type des utilisateurs Azure AD lorsqu’ils mettent à jour leurs informations de profil utilisateur à partir du portail d’administration Azure. Le type d’utilisateur peut également être mis à jour à partir de Microsoft Graph. Pour en savoir plus, consultez [Ajouter ou mettre à jour les informations du profil utilisateur](active-directory-users-profile-azure-portal.md).
 
---

### <a name="general-availability---replica-sets-for-azure-active-directory-domain-services"></a>Disponibilité générale - Jeux de réplicas pour Azure Active Directory Domain Services

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Services de domaine Azure AD  
**Fonctionnalité de produit :** Services de domaine Azure AD
 
La capacité des jeux de réplicas dans Azure AD Domain Services est désormais en disponibilité générale. [Plus d’informations](../../active-directory-domain-services/concepts-replica-sets.md)
 
---

### <a name="general-availability---collaborate-with-your-partners-using-email-one-time-passcode-in-the-azure-government-cloud"></a>Disponibilité générale - Collaborez avec vos partenaires à l’aide de code secret à usage unique par e-mail dans le cloud Azure Government

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Les organisations du cloud Microsoft Azure Government peuvent désormais autoriser leurs invités à accepter leurs invitations avec un code secret à usage unique par e-mail. Cela permet de s’assurer que les utilisateurs invités qui n’ont pas de compte Azure AD, Microsoft ou Gmail dans le cloud Azure Government peuvent toujours collaborer avec leurs partenaires en demandant et en entrant un code temporaire pour se connecter aux ressources partagées. [Plus d’informations](../external-identities/one-time-passcode.md#note-for-azure-us-government-customers)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---march-2021"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Mai 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En mars 2021, nous avons ajouté les 37 applications suivantes à notre galerie d’applications avec prise en charge de la fédération :

[Bambuser Live Video Shopping](https://lcx.bambuser.com/), [DeepDyve Inc](https://www.deepdyve.com/azure-sso), [Moqups](../saas-apps/moqups-tutorial.md), [RICOH Spaces Mobile](https://ricohspaces.app/welcome), [Flipgrid](https://auth.flipgrid.com/), [hCaptcha Enterprise](../saas-apps/hcaptcha-enterprise-tutorial.md), [SchoolStream ASA](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx), [TransPerfect GlobalLink Dashboard](../saas-apps/transperfect-globallink-dashboard-tutorial.md), [SimplificaCI](https://app.simplificaci.com.br/), [Thrive LXP](../saas-apps/thrive-lxp-tutorial.md), [Lexonis TalentScape](../saas-apps/lexonis-talentscape-tutorial.md), [Exium](../saas-apps/exium-tutorial.md), [Sapient](../saas-apps/sapient-tutorial.md), [TrueChoice](../saas-apps/truechoice-tutorial.md), [RICOH Spaces](https://ricohspaces.app/welcome), [Saba Cloud](../saas-apps/learning-at-work-tutorial.md), [Acunetix 360](../saas-apps/acunetix-360-tutorial.md), [Exceed.ai](../saas-apps/exceed-ai-tutorial.md), [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-tutorial.md), [Enterprise Vault.cloud for Outlook](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb), [Smartlook](../saas-apps/smartlook-tutorial.md), [Accenture Academy](../saas-apps/accenture-academy-tutorial.md), [Onshape](../saas-apps/onshape-tutorial.md), [Tradeshift](../saas-apps/tradeshift-tutorial.md), [JuriBlox](../saas-apps/juriblox-tutorial.md), [SecurityStudio](../saas-apps/securitystudio-tutorial.md), [ClicData](https://app.clicdata.com/), [Evergreen](../saas-apps/evergreen-tutorial.md), [Patchdeck](https://patchdeck.com/ad_auth/authenticate/), [FAX.PLUS](../saas-apps/fax.plus-tutorial.md), [ValidSign](../saas-apps/validsign-tutorial.md), [AWS Single Sign-on](../saas-apps/aws-single-sign-on-tutorial.md), [Nura Space](https://dashboard.nuraspace.com/login), [Broadcom DX SaaS](../saas-apps/broadcom-dx-saas-tutorial.md), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [SendPro Enterprise](../saas-apps/sendpro-enterprise-tutorial.md), [FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

Pour accéder à la documentation de ces applications, cliquez https://aka.ms/AppsTutorial

Pour référencer votre application dans la Galerie d’applications Azure AD, lisez les informations détaillées ici : https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---march-2021"></a>Nouveaux connecteurs de provisionnement dans la galerie d’applications Azure AD - Mars 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce

Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [AWS Single Sign-on](../saas-apps/aws-single-sign-on-provisioning-tutorial.md)
- [Bpanda](../saas-apps/bpanda-provisioning-tutorial.md)
- [Britive](../saas-apps/britive-provisioning-tutorial.md)
- [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-provisioning-tutorial.md)
- [Grammarly](../saas-apps/grammarly-provisioning-tutorial.md)
- [LogicGate](../saas-apps/logicgate-provisioning-tutorial.md)
- [SecureLogin](../saas-apps/secure-login-provisioning-tutorial.md)
- [TravelPerk](../saas-apps/travelperk-provisioning-tutorial.md)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, voir [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="introducing-ms-graph-api-for-company-branding"></a>Présentation de l’API MS Graph pour la personnalisation de l’entreprise

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** MS Graph  
**Fonctionnalité de produit :** B2B/B2C

[L’API MS Graph pour la personnalisation de l’entreprise](/graph/api/resources/organizationalbrandingproperties) est disponible pour l’expérience de connexion Azure AD ou Microsoft 365, afin d’autoriser la gestion par programmation des paramètres de personnalisation.

---

### <a name="general-availability---header-based-authentication-sso-with-application-proxy"></a>Disponibilité générale - Authentification unique basée sur l’en-tête avec proxy d’application

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès
 
La prise en charge native par le proxy d’application Azure AD de l’authentification basée sur l’en-tête est désormais en disponibilité générale. Avec cette fonctionnalité, vous pouvez configurer les attributs utilisateur requis en tant qu’en-têtes HTTP pour l’application sans avoir besoin de composants supplémentaires pour le déploiement. [Plus d’informations](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)

---

### <a name="two-way-sms-for-mfa-server-is-no-longer-supported"></a>Les SMS bidirectionnels pour Serveur MFA ne sont plus pris en charge

**Type :** Déprécié  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités
 

Les SMS bidirectionnels pour Serveur MFA ont été initialement dépréciés en 2018, et ne seront plus pris en charge à compter du 24 février 2021. Les administrateurs doivent activer une autre méthode pour les utilisateurs qui continuent d’utiliser les SMS bidirectionnels.

Des notifications par e-mail et des notifications Service Health dans le portail Azure ont été envoyées aux administrateurs désignés les 8 décembre 2020 et 28 janvier 2021. Les alertes sont allées aux rôles RBAC Propriétaire, Copropriétaire, Administrateur et Administrateur de service liés aux abonnements. [Plus d’informations](../authentication/how-to-authentication-two-way-sms-unsupported.md)
 
---
 
## <a name="february-2021"></a>Février 2021

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>E-mail d’authentification par code secret à usage unique par défaut à partir d’octobre 2021

**Type :** Modification planifiée  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 

À partir du 31 octobre 2021, [l’authentification par code secret à usage unique](../external-identities/one-time-passcode.md) de Microsoft Azure Active Directory deviendra la méthode par défaut pour l’invitation de comptes et de locataires pour les scénarios de collaboration B2B. À ce stade, Microsoft n’autorise plus l’échange d’invitations à l’aide de comptes de Azure Active Directory non gérés. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>Les autorisations non demandées mais consenties ne seront plus ajoutées aux jetons si elles déclenchent un accès conditionnel

**Type :** Modification planifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** plateforme
 
Actuellement, les applications qui utilisent des [autorisations dynamiques](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent) reçoivent toutes les autorisations auxquelles elles ont consenti à accéder. Cela comprend les applications qui ne sont pas demandées, même si elles déclenchent un accès conditionnel. Par exemple, cela peut entraîner une application qui demande uniquement `user.read` qui a également le consentement de `files.read`, pour être obligée de passer l’accès conditionnel attribué pour l’autorisation `files.read`. 

Pour réduire le nombre d’invites d’accès conditionnel inutiles, Azure AD change la façon dont les étendues non demandées sont fournies aux applications. Les applications ne déclenchent que l’accès conditionnel pour les autorisations qu’elles demandent explicitement. Pour plus d’informations, consultez [Nouveautés de l’authentification](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes).
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>Préversion publique : utiliser une Passe d’accès temporaire pour enregistrer les informations d’identification sans mot de passe

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités

La Passe d’accès temporaire est un code secret à durée limitée qui sert d’informations d’identification fortes et permet l’intégration d’informations d’identification et de récupération sans mot de passe lorsqu’un utilisateur a perdu ou oublié son application de facteur d’authentification fort (par exemple, la clé de sécurité FIDO2 ou Microsoft Authenticator) et doit se connecter pour inscrire de nouvelles méthodes d’authentification fortes. [Plus d’informations](../authentication/howto-authentication-temporary-access-pass.md)

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>Préversion publique : maintenir la connexion dans la préversion publique pour la prochaine génération de flux utilisateur

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

La nouvelle génération de flux d’utilisateurs B2C prend désormais en charge la fonctionnalité [Maintenir la connexion](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) qui permet aux clients d’étendre la durée de vie de la session pour les utilisateurs de leurs applications web et natives à l’aide d’un cookie persistant.  La fonctionnalité maintient la session active même lorsque l’utilisateur ferme et ouvre à nouveau le navigateur. Elle est révoquée lorsque l’utilisateur se déconnecte.

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>Préversion publique : inscription en libre service External Identities dans AAD à l’aide des comptes MSA

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Les utilisateurs externes peuvent désormais utiliser des comptes Microsoft pour se connecter aux applications de premier tiers et métier Azure AD. [Plus d’informations](../external-identities/self-service-sign-up-overview.md)

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>Préversion publique : réinitialisation de l’état d’acceptation d’un utilisateur invité

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Les clients peuvent à présent réinviter des utilisateurs invités externes existants à réinitialiser leur état d’acceptation, ce qui permet au compte d’utilisateur invité de rester sans qu’aucun accès ne soit perdu. [Plus d’informations](../external-identities/reset-redemption-status.md)
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>Préversion publique : les API de synchronisation (approvisionnement) prennent désormais en charge les permissions d’application

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Les clients peuvent désormais utiliser application.readwrite.ownedby comme permission d’application pour appeler les API de synchronisation. Attention ! Cette fonction est prise en charge uniquement pour l’approvisionnement par Azure AD des applications tiers (par exemle, AWS, Data Bricks, etc.). Elle n’est actuellement pas prise en charge pour l’approvisionnement RH (Workday/Successfactors) ou la synchronisation Cloud (AD vers Azure AD). [Plus d’informations](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true)
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>Disponibilité générale : rôle intégré Administrateur d’authentification Azure Policy

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 
Les utilisateurs disposant de ce rôle peuvent configurer la stratégie de méthodes d’authentification, les paramètres d’authentification multifacteur à l’ensemble du locataire et la stratégie de protection par mot de passe. Ce rôle accorde l’autorisation de gérer les paramètres de protection par mot de passe : les configurations de verrouillage intelligent et la mise à jour de la liste des mots de passe interdits personnalisés. [Plus d’informations](../roles/permissions-reference.md#authentication-policy-administrator)

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>Disponibilité générale : les collections d’utilisateurs sur Mes applications sont désormais disponibles !

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 
Les utilisateurs peuvent désormais créer leurs propres regroupements d’applications dans le lanceur d’applications Mes applications. Ils peuvent également réorganiser et masquer les regroupements que leurs administrateurs ont partagés avec eux. [Plus d’informations](../user-help/my-apps-portal-user-collections.md)

---

### <a name="general-availability---autofill-in-authenticator"></a>Disponibilité générale : remplissage automatique dans Authenticator

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Application Microsoft Authenticator  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Microsoft Authenticator fournit des fonctionnalités d’authentification multifacteur (MFA) et de gestion des comptes, et à présent, il remplit également automatiquement les mots de passe sur les sites et les applications consultés par les utilisateurs sur leur appareil mobile (iOS et Android). 

Pour utiliser la fonction de remplissage automatique sur Authenticator, les utilisateurs doivent ajouter leur compte Microsoft personnel à Authenticator et l’utiliser pour synchroniser leurs mots de passe. Pour l’instant, les comptes professionnels ou scolaires ne peuvent pas être utilisés pour synchroniser les mots de passe. [Plus d’informations](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins)

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>Disponibilité générale : inviter des utilisateurs internes à une collaboration B2B

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Les clients peuvent désormais inviter des invités internes à utiliser la collaboration B2B au lieu d’envoyer une invitation à un compte interne existant. Cela permet aux clients de conserver l’ID d’objet, l’UPN, les appartenances aux groupes et les affectations d’applications de cet utilisateur. [Plus d’informations](../external-identities/invite-internal-users.md)

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>Disponibilité générale : rôle intégré Administrateur de nom de domaine

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 
Les utilisateurs disposant de ce rôle peuvent gérer (lire, ajouter, vérifier, mettre à jour et supprimer) des noms de domaine. Ils peuvent également lire les informations du répertoire sur les utilisateurs, les groupes et les applications, car ces objets possèdent des dépendances de domaine. 

Pour les environnements locaux, les utilisateurs disposant de ce rôle peuvent configurer des noms de domaine pour la fédération afin que les utilisateurs associés soient toujours authentifiés localement. Ces utilisateurs peuvent ensuite se connecter à des services Azure AD avec leurs mots de passe locaux par le biais de l’authentification unique. Les paramètres de fédération doivent être synchronisés via Azure AD Connect, afin que les utilisateurs disposent également des autorisations nécessaires pour gérer Azure AD Connect. [Plus d’informations](../roles/permissions-reference.md#domain-name-administrator)
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Nouvelles applications fédérées disponibles dans la Galerie d’applications Azure AD (février 2021)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En février 2021, nous avons ajouté les 37 applications suivantes à notre Galerie d’applications avec prise en charge de la fédération :

[Extension Loop Messenger](https://loopworks.com/loop-flow-messenger/), [Adaptateur Silverfort Azure AD](http://www.silverfort.com/), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [Nura Space](https://dashboard.nuraspace.com/login), [Yooz EU](https://eu1.getyooz.com/?kc_idp_hint=microsoft), [UXPressia](https://uxpressia.com/users/sign-in), [plateforme d’intégration et de pré-intégration introDus](http://app.introdus.dk/login), [Happybot](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All), [LeaksID](https://app.leaksid.com/), [ShiftWizard](http://www.shiftwizard.com/), [PingFlow SSO](https://app.pingview.io/), [Swiftlane](https://admin.swiftlane.com/login), [Quasydoc SSO](https://www.quasydoc.eu/login), [Fenwick Gold Account](https://businesscentral.dynamics.com/), [SeamlessDesk](https://www.seamlessdesk.com/login), [Learnsoft LMS & TMS](http://www.learnsoft.com/), [P-TH+](https://p-th.jp/), [myViewBoard](https://api.myviewboard.com/auth/microsoft/), [Tartabit IoT Bridge](https://bridge-us.tartabit.com/), [AKASHI](../saas-apps/akashi-tutorial.md), [Rewatch](../saas-apps/rewatch-tutorial.md), [Zuddl](../saas-apps/zuddl-tutorial.md), [Gestion des places de parking Parkalot](../saas-apps/parkalot-car-park-management-tutorial.md), [HSB ThoughtSpot](../saas-apps/hsb-thoughtspot-tutorial.md), [IBMid](../saas-apps/ibmid-tutorial.md), [SharingCloud](../saas-apps/sharingcloud-tutorial.md), [PoolParty Semantic Suite](../saas-apps/poolparty-semantic-suite-tutorial.md), [GlobeSmart](../saas-apps/globesmart-tutorial.md), [Samsung Knox and Business Services](../saas-apps/samsung-knox-and-business-services-tutorial.md), [Penji](../saas-apps/penji-tutorial.md), [Kendis (plateforme de mise à l’échelle agile)](../saas-apps/kendis-scaling-agile-platform-tutorial.md), [Maptician](../saas-apps/maptician-tutorial.md), [Olfeo SAAS](../saas-apps/olfeo-saas-tutorial.md), [Sigma Computing](../saas-apps/sigma-computing-tutorial.md), [CloudKnox (plateforme de gestion des autorisations)](../saas-apps/cloudknox-permissions-management-platform-tutorial.md), [Klaxoon SAML](../saas-apps/klaxoon-saml-tutorial.md), [Enablon](../saas-apps/enablon-tutorial.md)

Pour accéder à la documentation de ces applications, cliquez https://aka.ms/AppsTutorial

Pour référencer votre application dans la Galerie d’applications Azure AD, lisez les informations détaillées ici : https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Nouveaux connecteurs d’approvisionnement dans la Galerie d'applications Azure AD (février 2021)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 

Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

Pour plus d’informations, consultez [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>Disponibilité générale : 10 rôles Azure Active Directory maintenant renommés

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 
10 rôles Azure AD intégrés ont été renommés afin d’être alignés dans le centre d’[administration Microsoft 365](/microsoft-365/admin/microsoft-365-admin-center-preview), le [Portail Azure AD](https://portal.azure.com/)et [Microsoft Graph](https://developer.microsoft.com/graph/). Pour en savoir plus sur les nouveaux rôles, consultez [Autorisations des rôles d’administrateur dans Azure Active Directory](../roles/permissions-reference.md#all-roles).

![Tableau répertoriant les noms de rôles dans MS API Graph et le portail Azure, ainsi que le nom final proposé sur l’API, Portail Azure et Mac.](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>Nouvelle personnalisation de la société dans l’enregistrement combiné MFA/SSPR

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** expérience utilisateur et gestion  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 
Dans le passé, les logos de la société n’étaient pas utilisés sur les pages de connexion Azure Active Directory. La personnalisation de la société est désormais située en haut à gauche de l’enregistrement combiné MFA/SSPR. La personnalisation de la société est également incluse dans Mes connexions et la page Informations de sécurité. [Plus d’informations](../fundamentals/customize-branding.md)

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>Disponibilité générale : le gestionnaire de second niveau peut être défini en tant qu’approbateur de remplacement

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Une option supplémentaire lorsque vous sélectionnez des approbateurs est désormais disponible dans la gestion des droits d’utilisation. Si vous sélectionnez « Gestionnaire en tant qu’approbateur » pour le premier approbateur, vous disposez d’une seconde option, « Gestionnaire de second niveau en tant qu’approbateur de substitution », sélectionnable dans le champ d’approbateur de substitution. Si vous sélectionnez cette option, vous devez ajouter un approbateur de secours auquel transférer la demande au cas où le système ne peut pas trouver le gestionnaire de second niveau. [Plus d’informations](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="authentication-methods-activity-dashboard"></a>Tableau de bord de l’activité des méthodes d’authentification

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Signalement  
**Fonctionnalité de produit :** Monitoring et création de rapports
 

Le tableau de bord actualisé d’activités des méthodes d’authentification donne aux administrateurs une vue d’ensemble de l’activité d’inscription et d’utilisation de la méthode d’authentification dans leur locataire. Le rapport résume le nombre d’utilisateurs inscrits pour chaque méthode, ainsi que les méthodes utilisées pendant la connexion et la réinitialisation du mot de passe. [Plus d’informations](../authentication/howto-authentication-methods-activity.md)
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>L’actualisation et la configuration des jetons de sessions dans Configurable Token Lifetime (CTL) sont supprimées

**Type :** Déprécié  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Authentification utilisateur
 
L’actualisation et la configuration des durées de vie des jetons de session dans CTL sont supprimées. Azure Active Directory n’honore plus la configuration des jetons d’actualisation et de session dans les stratégies existantes. [Plus d’informations](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens)
 
---
 
## <a name="january-2021"></a>Janvier 2021

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>Le jeton secret est un champ obligatoire lors de la configuration du provisionnement

**Type :** Modification planifiée  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités

Auparavant, le champ jeton secret pouvait rester vide lors de la configuration du provisionnement sur l’application personnalisée/BYOA. Cette fonction n’était destinée qu’à des fins de test. Nous allons mettre à jour l’interface utilisateur pour rendre le champ obligatoire. 

Les clients peuvent contourner cette exigence à des fins de test à l’aide d’un indicateur de fonctionnalité dans l’URL du navigateur. [En savoir plus](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery)
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-frontline-workers-at-scale"></a>Préversion publique - Personnaliser et configurer des appareils Android partagés pour les rôles de travail de première ligne à grande échelle

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion et inscription des appareils  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Les équipes Azure AD et Microsoft Endpoint Manager se sont associées pour offrir la possibilité de personnaliser, de mettre à l’échelle et de sécuriser vos appareils de rôles de travail de première ligne.

Les fonctionnalités en préversion ci-dessous vous permettront d’effectuer les opérations suivantes :
- Provisionner des appareils Android partagés à grande échelle avec Microsoft Endpoint Manager
- Sécuriser votre accès aux équipes de travail en utilisant l’accès conditionnel basé sur les appareils
- Personnaliser les expériences de connexion pour les équipes de travail avec l’écran d’accueil géré

Pour en savoir plus, consultez [Personnaliser et configurer des appareils partagés pour les rôles de travail en première ligne à grande échelle](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708).

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Préversion publique - Les journaux de provisionnement peuvent maintenant être téléchargés au format CSV ou JSON

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités

Les clients peuvent télécharger les journaux de provisionnement au format CSV ou JSON par le biais de l’interface utilisateur et de l’API Graph. Pour en savoir plus, consultez [Provisionnement des rapports dans le portail Azure Active Directory](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Préversion publique : Attribuer des groupes cloud aux rôles personnalisés et aux rôles délimités par unité d’administration Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 
Les clients peuvent attribuer un groupe cloud aux rôles personnalisés ou à un rôle délimité par unité d’administration Azure AD. Pour en savoir plus sur cette fonctionnalité, consultez [Utiliser des groupes cloud pour gérer les attributions de rôles dans Azure Active Directory](../roles/groups-concept.md).

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Disponibilité générale - Synchronisation cloud Azure AD Connect (anciennement provisionnement cloud)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Synchronisation cloud Azure AD Connect  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
La synchronisation cloud Azure AD Connect est désormais mise à la disposition générale pour tous les clients.

Le cloud Azure AD Connect transfère la lourde tâche de la logique de transformation sur le cloud, ce qui réduit votre empreinte sur site. En outre, plusieurs déploiements d’agents légers sont disponibles pour une plus grande disponibilité de la synchronisation. [En savoir plus](https://aka.ms/cloudsyncGA)
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Disponibilité générale - Rôles intégrés Administrateur de simulation d’attaque et Créateur de charge utile d’attaque

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 
Deux nouveaux rôles dans le contrôle d’accès en fonction du rôle peuvent être attribués aux utilisateurs : Administrateur de simulation d’attaque et Créateur de charge utile d’attaque. 

Les utilisateurs dotés du rôle [Administrateur de simulation d’attaque](../roles/permissions-reference.md#attack-simulation-administrator) ont accès à toutes les simulations du locataire et peuvent :
- créer et gérer tous les aspects de la création de simulation d’attaque ;
- lancer/planifier une simulation ;
-  passer en revue les résultats de la simulation. 

Les utilisateurs dotés du rôle [Créateur de charge utile d’attaque](../roles/permissions-reference.md#attack-payload-author) peuvent créer des charges utiles d’attaque, mais pas les lancer ou les planifier. Les charges utiles d'attaque sont ensuite à la disposition de tous les administrateurs du locataire, qui peuvent les utiliser pour créer une simulation.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Disponibilité générale - Rôle intégré Lecteur de rapports de synthèse sur l’utilisation

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 
Les utilisateurs dotés du rôle Lecteur de rapports de synthèse sur l’utilisation peuvent accéder aux données agrégées au niveau locataire et aux insights associés dans Utilisation et Score de productivité du Centre d’administration Microsoft 365. Toutefois, ils ne peuvent accéder à aucun détail ou insight au niveau de l’utilisateur. 

Dans le Centre d’administration Microsoft 365 pour les deux rapports, nous faisons une distinction entre les données agrégées au niveau locataire et les détails au niveau de l’utilisateur. Ce rôle ajoute une couche supplémentaire de protection aux données identifiables de l’utilisateur individuel. [En savoir plus](../roles/permissions-reference.md#usage-summary-reports-reader)

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Disponibilité générale - Octroi Exiger la stratégie de protection des applications dans l’accès conditionnel Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
L’octroi d’accès conditionnel Azure AD pour « Exiger la stratégie de protection des applications » est désormais en disponibilité générale. 

La stratégie offre les fonctionnalités suivantes :
- Autorise l’accès uniquement lors de l’utilisation d’une application mobile qui prend en charge la protection des applications Intune
- Autorise l’accès uniquement lorsqu’un utilisateur a une stratégie de protection des applications Intune remise à l’application mobile

En savoir plus sur la configuration d’une stratégie d’accès conditionnel pour la protection des applications [ici](../conditional-access/app-protection-based-conditional-access.md).
 
---

### <a name="general-availability---email-one-time-passcode"></a>Disponibilité générale - Code secret à usage unique par e-mail

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
La fonctionnalité Code secret à usage unique par e-mail permet à des organisations dans le monde de collaborer avec n’importe qui en envoyant un lien ou une invitation par e-mail. Les utilisateurs invités peuvent confirmer leur identité à l’aide du code secret à usage unique envoyé à leur adresse e-mail pour accéder aux ressources de leur partenaire. [En savoir plus](../external-identities/one-time-passcode.md) 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Nouveaux connecteurs de provisionnement dans la Galerie d’applications Azure AD - Janvier 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 
Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

Pour plus d’informations, consultez [Qu’est ce que le provisionnement automatique des utilisateurs dans les applications SaaS dans Azure AD ?](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Nouvelles applications fédérées disponibles dans la Galerie d’applications Azure AD - Janvier 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En janvier 2021, nous avons ajouté les 29 applications suivantes à notre Galerie d’applications avec prise en charge de la fédération :

[mySCView](https://dev.myscview.com/), [Talentech](https://talentech.com/contact/), [Bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [Mio](https://app.m.io/auth/install/microsoft?scopetype=hub), [Sovelto Easy](https://login.soveltoeasy.fi/), [Supportbench](https://account.supportbench.net/agent/login/),[Bienvenue Formation](https://formation.bienvenue.pro/login), [AIDA Healthcare SSO](https://aidaforparents.com/login/organizations), [International SOS Assistance Products](../saas-apps/international-sos-assistance-products-tutorial.md), [NAVEX One](../saas-apps/navex-one-tutorial.md), [LabLog](../saas-apps/lablog-tutorial.md), [Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md), [EPHOTO DAM](../saas-apps/ephoto-dam-tutorial.md), [Notion](../saas-apps/notion-tutorial.md), [Syndio](../saas-apps/syndio-tutorial.md), [Yello Enterprise](../saas-apps/yello-enterprise-tutorial.md), [Timeclock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md), [Nalco E-data](https://www.ecolab.com/), [Vacancy Filler](https://app.vacancy-filler.co.uk/VFMVC/Account/Login), [Synerise AI Growth Ecosystem](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md), [Imperva Data Security](../saas-apps/imperva-data-security-tutorial.md), [Illusive Networks](../saas-apps/illusive-networks-tutorial.md), [Proware](../saas-apps/proware-tutorial.md), [Splan Visitor](../saas-apps/splan-visitor-tutorial.md), [Aruba User Experience Insight](../saas-apps/aruba-user-experience-insight-tutorial.md), [Contentsquare SSO](../saas-apps/contentsquare-sso-tutorial.md), [Perimeter 81](../saas-apps/perimeter-81-tutorial.md), [Burp Suite Enterprise Edition](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

La documentation de toutes ces applications est disponible ici https://aka.ms/AppsTutorial

Pour référencer votre application dans la galerie d’applications Azure AD, lisez les informations détaillées ici : https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Préversion publique - Le gestionnaire de second niveau peut être défini en tant qu’approbateur de remplacement

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Une option supplémentaire lorsque vous sélectionnez des approbateurs est désormais disponible dans la gestion des droits d’utilisation. Si vous sélectionnez « Gestionnaire en tant qu’approbateur » pour le premier approbateur, vous disposez d’une seconde option, « Gestionnaire de second niveau en tant qu’approbateur de substitution », sélectionnable dans le champ d’approbateur de substitution. Si vous sélectionnez cette option, vous devez ajouter un approbateur de secours auquel transférer la demande au cas où le système ne peut pas trouver le gestionnaire de second niveau. [En savoir plus](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Disponibilité générale - Accéder à Teams directement à partir du portail Mon Accès

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Vous pouvez désormais lancer Teams directement à partir du portail Mon Accès. 

Pour ce faire, connectez-vous à Mon Accès (https://myaccess.microsoft.com/) ), accédez à « Packages d’accès », puis à l’onglet « Actif » pour voir tous les packages d’accès auxquels vous avez déjà accès. Lorsque vous développez le package d’accès sélectionné et que vous pointez sur Teams, vous pouvez le lancer en cliquant sur le bouton « Ouvrir ». [En savoir plus](../governance/entitlement-management-request-access.md)
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>Journalisation et invites d’utilisateur final améliorées pour les utilisateurs invités à risque

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités
 

La journalisation et les invites d’utilisateur final pour les utilisateurs invités à risque ont été mises à jour. En savoir plus dans [Utilisateurs Identity Protection et B2B](../identity-protection/concept-identity-protection-b2b.md).
 
---
 
## <a name="december-2020"></a>Décembre 2020

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Préversion publique - Inscription et connexion par téléphone à Azure AD B2C à l’aide d’une stratégie intégrée

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C
 
L’inscription et la connexion par téléphone B2C à l’aide de la stratégie intégrée permettent aux administrateurs informatiques et aux développeurs de l’organisation d’autoriser leurs utilisateurs finaux à se connecter et à s’inscrire à l’aide d’un numéro de téléphone dans les flux d’utilisateurs. Lisez la section [Configurer l’inscription et la connexion par téléphone pour les flux d’utilisateurs (préversion)](../../active-directory-b2c/phone-authentication-user-flows.md) pour en savoir plus.

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Disponibilité générale : les valeurs par défaut de sécurité sont désormais activées pour tous les nouveaux locataires par défaut

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Pour protéger les comptes d’utilisateur, tous les nouveaux locataires créés à partir du 12 novembre 2020 sont fournis avec les paramètres par défaut de sécurité activés. Les valeurs par défaut de la sécurité appliquent plusieurs stratégies, notamment :
- Requiert que tous les utilisateurs et administrateurs s’inscrivent pour l’authentification multifacteur via l’application Microsoft Authenticator.
- Requiert que les rôles d’administrateur critiques utilisent l’authentification MFA chaque fois qu’ils se connectent. Tous les autres utilisateurs seront invités à utiliser MFA chaque fois que nécessaire. 
- L’authentification héritée sera bloquée sur l’ensemble du locataire. 

Pour plus d’informations, lisez [Présentation des paramètres de sécurité par défaut](../fundamentals/concept-fundamentals-security-defaults.md).

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Disponibilité générale : prise en charge des groupes avec jusqu’à 250 000 membres dans AADConnect

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** AD Connect  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Microsoft a déployé un nouveau point de terminaison (API) pour Azure AD Connect qui améliore les performances des opérations des services de synchronisation pour Azure Active Directory. Lorsque vous utilisez le nouveau [point de terminaison v2](../hybrid/how-to-connect-sync-endpoint-api-v2.md), vous constatez des gains de performances perceptibles lors de l’exportation et de l’importation vers Azure AD. Ce nouveau point de terminaison prend en charge les scénarios suivants :

- Synchronisation des groupes avec 250 000 membres maximum
- Gains de performances lors de l’exportation et de l’importation Azure AD

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Disponibilité générale : gestion des droits d’utilisation pour les locataires dans le Cloud Azure Chine

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 

Les fonctionnalités de la gestion des droits d’utilisation sont désormais disponibles pour tous les locataires dans le Cloud Azure Chine. Pour plus d’informations, consultez notre site de [documentation sur la gouvernance des identités](https://docs.azure.cn/zh-cn/active-directory/governance/).

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Nouveaux connecteurs de provisionnement dans la galerie d’applications Azure AD - Décembre 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce

Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, voir [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Décembre 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En décembre 2020, nous avons ajouté les 18 applications suivantes à notre galerie d’applications avec prise en charge de la fédération :

[AwareGo](../saas-apps/awarego-tutorial.md), [HowNow SSO](https://gethownow.com/), [ZyLAB ONE Legal Hold](https://www.zylab.com/en/product/legal-hold), [Guider](http://www.guider-ai.com/), [Softcrisis](https://www.softcrisis.se/sv/), [Pims 365](http://www.omega365.com/pims), [InformaCast](../saas-apps/informacast-tutorial.md), [RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md), [vonage](../saas-apps/vonage-tutorial.md), [Count Me In - Operations Dashboard](../saas-apps/count-me-in-operations-dashboard-tutorial.md), [ProProfs Knowledge Base](../saas-apps/proprofs-knowledge-base-tutorial.md), [RightCrowd Workforce Management](../saas-apps/rightcrowd-workforce-management-tutorial.md), [JLL TRIRIGA](../saas-apps/jll-tririga-tutorial.md), [Shutterstock](../saas-apps/shutterstock-tutorial.md), [FortiWeb Web Application Firewall](../saas-apps/linkedin-talent-solutions-tutorial.md), [LinkedIn Talent Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md), [Equinix Federation App](../saas-apps/equinix-federation-app-tutorial.md), [KFAdvance](../saas-apps/kfadvance-tutorial.md)

La documentation de toutes ces applications est disponible ici https://aka.ms/AppsTutorial

Pour référencer votre application dans la galerie d’applications Azure AD, lisez les informations détaillées ici : https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Accéder à Teams directement à partir du portail Mon Accès

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion de l’accès utilisateur **Fonctionnalité produit :** Gestion des droits d’utilisation

Vous pouvez désormais lancer Teams directement à partir du portail Mon Accès. Pour ce faire, connectez-vous à [Mon Accès](https://myaccess.microsoft.com/), accédez à **Packages d’accès**, puis à l’onglet **Actif** pour voir tous les packages d’accès auxquels vous avez déjà accès. Lorsque vous développez le package d’accès et que vous pointez sur Teams, vous pouvez le lancer en cliquant sur le bouton **Ouvrir**. 

Pour en savoir plus sur l’utilisation du portail Mon Accès, accédez à [Demander l’accès à un package d’accès dans la gestion des droits d’utilisation Azure AD](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal).

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Préversion publique - Le gestionnaire de second niveau peut être défini en tant qu’approbateur de remplacement

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation

Une option supplémentaire est désormais disponible dans le processus d’approbation de la Gestion des droits d’utilisation. Si vous sélectionnez Manager en tant qu’approbateur pour le Premier approbateur, vous disposez d’une seconde option : Manager de second niveau en tant qu’approbateur de substitution, sélectionnable dans le champ d’approbateur de substitution. Lorsque vous sélectionnez cette option, vous devez ajouter un approbateur de secours auquel transférer la demande au cas où le système ne peut pas trouver le gestionnaire de second niveau.

Pour plus d’informations, consultez [Modifier les paramètres d’approbation d’un package d’accès dans la gestion des droits d’utilisation Azure AD](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).

--- 

## <a name="november-2020"></a>Novembre 2020

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Dépréciation d’Azure Active Directory TLS 1.0, TLS 1.1 et 3DES

**Type :** Modification planifiée  
**Catégorie de service :** Toutes les applications Azure AD  
**Fonctionnalité de produit :** Standards

Azure Active Directory dépréciera les protocoles suivants dans toutes les régions du monde Azure Active Directory à compter du 30 juin 2021 :

- TLS 1.0
- TLS 1.1
- Suite de chiffrement 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Les environnements affectés sont les suivants :
- Azure Commercial Cloud
- Office 365 GCC et WW

Annonce : toutes les combinaisons client-serveur et navigateur-serveur doivent utiliser TLS 1.2 et des suites de chiffrement modernes afin de maintenir une connexion sécurisée à Azure Active Directory pour les services Azure, Office 365 et Microsoft 365. Cette modification est liée à l’article [Azure Active Directory TLS 1.0 & 1.1, and 3DES Cipher Suite Deprecation in US Gov Cloud](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

Pour obtenir des conseils sur la suppression des dépendances envers les protocoles dépréciés, consultez [Activer la prise en charge de TLS 1.2 dans votre environnement en vue de la dépréciation de TLS 1.1 et 1.0 dans Azure AD](/troubleshoot/azure/active-directory/enable-support-tls-environment).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Novembre 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En novembre 2020, nous avons ajouté les 52 applications suivantes à notre galerie d’applications avec prise en charge de la fédération :

[Travel & Expense Management](https://app.expenseonce.com/Account/Login), [Tribeloo](../saas-apps/tribeloo-tutorial.md), [Itslearning File Picker](https://pmteam.itslearning.com/), [Crises Control](../saas-apps/crises-control-tutorial.md), [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [Edmentum - Study Island](https://app.studyisland.com/cfw/login/), [Virtual Risk Manager](../saas-apps/virtual-risk-manager-tutorial.md), [TIMU](../saas-apps/timu-tutorial.md), [Looker Analytics Platform](../saas-apps/looker-analytics-platform-tutorial.md), [Talview - Recruit](https://recruit.talview.com/login), Real Time Translator, [Klaxoon](https://access.klaxoon.com/login), [Podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [expensemanager](https://api.expense-manager.com/), [Netsparker Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [En-trak Tenant Experience Platform](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md), [Builterra](https://portal.builterra.com/), [EVA Check-in](https://my.evacheckin.com/organization), [HowNow WebApp SSO](../saas-apps/hownow-webapp-sso-tutorial.md), [Coupa Risk Assess](../saas-apps/coupa-risk-assess-tutorial.md), [Lucid (All Products)](../saas-apps/lucid-tutorial.md), [GoBright](https://portal.brightbooking.eu/), [SailPoint IdentityNow](../saas-apps/sailpoint-identitynow-tutorial.md),[Resource Central](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [Jedox](../saas-apps/jedox-tutorial.md), [Cequence Application Security](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [WorkWare](../saas-apps/workware-tutorial.md), [ProdPad](../saas-apps/prodpad-tutorial.md), [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md), [AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), [Freight Measure](https://www.gpcsl.com/freight.html), [Terraform Cloud](../saas-apps/terraform-cloud-tutorial.md), [Nature Research](../saas-apps/nature-research-tutorial.md), [Play Digital Signage](https://login.playsignage.com/login), [RemotePC](../saas-apps/remotepc-tutorial.md), [Prolorus](../saas-apps/prolorus-tutorial.md), [Hirebridge ATS](../saas-apps/hirebridge-ats-tutorial.md), [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), [Sunrise Software Relations CRM](https://cloud.relations-crm.com/), [Procaire](../saas-apps/procaire-tutorial.md), [Mentor® by eDriving: Business](https://www.edriving.com/), [Gradle Enterprise](https://gradle.com/)

La documentation de toutes ces applications est disponible ici https://aka.ms/AppsTutorial

Pour référencer votre application dans la galerie d’applications Azure AD, lisez les informations détaillées ici : https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Préversion publique : rôles personnalisés pour les applications d’entreprise

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 
 [Les rôles RBAC personnalisés pour la gestion déléguée des applications d’entreprise](../roles/custom-available-permissions.md) sont désormais en préversion publique. Ces nouvelles autorisations s’appuient sur les rôles personnalisés pour la gestion de l’inscription des applications, ce qui permet un contrôle précis de l’accès de vos administrateurs. Au fil du temps, des autorisations supplémentaires pour déléguer la gestion d’Azure AD seront publiées.

Voici quelques scénarios de délégation courants :
- affectation des utilisateurs et groupes autorisés à accéder aux applications d’authentification unique SAML
- création d’applications de la galerie Azure AD
- lecture et mise à jour des configurations SAML de base pour les applications d’authentification unique SAML
- gestion des certificats de signature pour les applications d’authentification unique SAML
- mise à jour des adresses e-mail de notification d’expiration des certificats de connexion pour les applications d’authentification unique SAML
- mise à jour de la signature de jeton SAML et de l’algorithme de connexion pour les applications d’authentification unique SAML
- création, suppression et mise à jour des attributs et revendications utilisateur pour les applications d’authentification unique SAML
- capacité à activer, désactiver et redémarrer des travaux de provisionnement
- mises à jour du mappage des attributs
- capacité à lire les paramètres de provisionnement associés à l’objet
- capacité à lire les paramètres de provisionnement associés au principal de service
- capacité à autoriser l’accès aux applications pour le provisionnement

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Préversion publique - Le Proxy d’application Azure AD prend en charge en mode natif l’accès avec l’authentification unique aux applications qui utilisent des en-têtes pour l’authentification

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès
 
Le Proxy d’application Azure Active Directory (Azure AD) prend en charge en mode natif l’accès en authentification unique aux applications qui utilisent des en-têtes pour l’authentification. Vous pouvez configurer les valeurs d’en-tête que votre application requiert dans Azure AD. Les valeurs d’en-tête seront envoyées à l’application via un Proxy d’application. Pour en savoir plus, consultez [Authentification unique basée sur l’en-tête pour les applications locales avec le Proxy d’application Azure AD](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Disponibilité générale : inscription et connexion par téléphone à Azure AD B2C à l’aide d’une stratégie personnalisée

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

Avec l’inscription et la connexion par téléphone, les développeurs et les entreprises peuvent autoriser leurs clients à s’inscrire et à se connecter à l’aide d’un mot de passe à usage unique envoyé au numéro de téléphone de l’utilisateur via SMS. Cette fonctionnalité permet également au client de modifier son numéro de téléphone s’il perd l’accès à son téléphone. Grâce à la puissance des stratégies personnalisées, ces procédures permettent aux développeurs et aux entreprises de mettre leur marque en avant en personnalisant des pages. Découvrez comment [Configurer l’inscription et la connexion par téléphone avec des stratégies personnalisées dans Azure AD B2C](../../active-directory-b2c/phone-authentication-user-flows.md).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Nouveaux connecteurs de provisionnement dans la galerie d’applications Azure AD - Novembre 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 
Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic - Tac Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Pour plus d’informations, consultez [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Préversion publique : connexion par e-mail avec ProxyAddresses maintenant déployable via un déploiement intermédiaire

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Les administrateurs de clients peuvent désormais utiliser le déploiement intermédiaire pour déployer la connexion par e-mail avec ProxyAddresses sur des groupes Azure AD spécifiques. Cela vous permet de tester la fonctionnalité avant de la déployer sur tout le client via la stratégie de découverte du domaine d’accueil. Les instructions de déploiement de la connexion par e-mail avec ProxyAddresses via le déploiement intermédiaire sont répertoriées dans la [documentation](../authentication/howto-authentication-use-email-signin.md).
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Préversion limitée : diagnostic de connexion

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Signalement  
**Fonctionnalité de produit :** Monitoring et création de rapports
 
Avec la préversion initiale du diagnostic de connexion, les administrateurs peuvent maintenant consulter les connexions utilisateur. Les administrateurs peuvent recevoir des informations contextuelles, spécifiques et pertinentes ainsi que des conseils sur ce qui s’est produit lors d’une connexion et sur la manière de résoudre les problèmes. Le diagnostic est disponible à la fois au niveau d’Azure AD et dans les panneaux de diagnostic et de résolution de l’accès conditionnel. Les scénarios de diagnostic couverts dans cette version sont Accès conditionnel, Multi-Factor Authentication et la connexion réussie.

Pour plus d’informations, consultez la section [Qu’est-ce que le diagnostic de connexion dans Azure Active Directory](../reports-monitoring/overview-sign-in-diagnostics.md).
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Amélioration des propriétés de connexion inhabituelles

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

  Les détections des propriétés de connexion inhabituelles ont été mises à jour. Les clients peuvent remarquer une augmentation des détections de propriétés de connexion inhabituelles à haut risque. Pour plus d’informations, consultez [Qu’est-ce que le risque ?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>Actualisation de la préversion publique de l’agent de provisionnement cloud maintenant disponible (version : 1.1.281.0)

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Provisionnement cloud Azure AD  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
L’agent de provisionnement cloud a été publié préversion publique et est désormais disponible via le portail. Cette version contient plusieurs améliorations, notamment la prise en charge de GMSA pour vos domaines, ce qui offre une meilleure sécurité, des cycles de synchronisation initiaux améliorés et la prise en charge des grands groupes. Pour plus d’informations, consultez l’[historique](../app-provisioning/provisioning-agent-release-version-history.md) des versions. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>Le point de terminaison de l’API de clé de récupération BitLocker se trouve maintenant sous /informationProtection

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion de l’accès aux appareils  
**Fonctionnalité de produit :** Gestion du cycle de vie des appareils
 
Auparavant, vous pouviez récupérer les clés BitLocker via le point de terminaison /bitlocker. Nous allons déprécier ce point de terminaison, et les clients devront commencer à consommer l’API qui se trouve désormais sous /informationProtection. 

Consultez [API de récupération BitLocker](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) pour obtenir des mises à jour de la documentation qui reflète ces modifications.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Disponibilité générale de la prise en charge du proxy d’application pour le client web HTML5 des services Bureau à distance

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès
 
La prise en charge du proxy d'application Azure AD pour le client web des services Bureau à distance (RDS) est désormais en disponibilité générale. Le client web RDS permet aux utilisateurs d'accéder à l'infrastructure Bureau distant via tout navigateur compatible HTLM5 tel que Microsoft Edge, Internet Explorer 11, Google Chrome, etc. Les utilisateurs peuvent interagir avec des applications ou des bureaux distants, comme ils le feraient avec un appareil local depuis n’importe où. 

En utilisant le Proxy d’application Azure AD, vous pouvez augmenter la sécurité de votre déploiement des services Bureau à distance en appliquant la pré-authentification et les stratégies d’accès conditionnel pour tous les types d’applications clientes riches. Pour en savoir plus, consultez [Publier le Bureau à distance avec le Proxy d’application d’Azure AD](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Le nouveau service Groupe dynamique amélioré est en préversion publique

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration
 
Le service Groupe dynamique amélioré est maintenant en préversion publique. Les nouveaux clients qui créent des groupes dynamiques dans leurs locataires utiliseront le nouveau service. Le temps nécessaire à la création d’un groupe dynamique sera proportionnel à la taille du groupe créé et non plus à la taille du locataire. Cette mise à jour améliore de manière significative les performances des clients importants lorsque les clients créent des groupes plus petits. 

Le nouveau service vise également à finaliser en quelques minutes l’ajout et la suppression de membres en raison de modifications d’attributs. En outre, les échecs de traitement uniques ne bloquent pas le traitement du locataire. Pour en savoir plus sur la création de groupes dynamiques, consultez notre [documentation](../enterprise-users/groups-create-rule.md).
 
---
## <a name="october-2020"></a>Octobre 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Agents hybrides locaux Azure AD impactés par les changements des certificats Azure TLS

**Type :** Modification planifiée  
**Catégorie de service :** N/A  
**Fonctionnalité de produit :** plateforme

Microsoft met à jour les services Azure pour qu’ils utilisent des certificats TLS issus d’un autre ensemble d’autorités de certification racines. Cette mise à jour est due au fait que les certificats d’autorité de certification actuels ne sont pas conformes à l’une des réglementations de référence du CA/Browser Forum. Ce changement a un impact sur les agents hybrides Azure AD installés localement qui ont des environnements renforcés disposant d’une liste fixe de certificats racines. Ils devront être mis à jour pour approuver les nouveaux émetteurs de certificats.

Ce changement entraîne une interruption du service si vous n’agissez pas tout de suite. Ces agents comprennent les [connecteurs de proxy d’application](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) pour l’accès à distance à un site local, les agents d’[authentification directe](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) qui permettent à vos utilisateurs de se connecter aux applications à l’aide des mêmes mots de passe, et les agents de la [préversion du provisionnement cloud](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) qui effectuent la synchronisation d’AD vers Azure AD. 

Si vous disposez d’un environnement avec des règles de pare-feu définies pour autoriser les appels sortants uniquement vers un téléchargement de liste de révocation de certificats spécifique, vous devez autoriser les URL de liste de révocation de certificats et OCSP suivantes. Pour plus d’informations sur le changement d’URL de liste de révocation de certificats et OCSP afin de permettre l’accès, consultez [Changements des certificats Azure TLS](../../security/fundamentals/tls-certificate-changes.md).

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Les événements de provisionnement sont supprimés des journaux d’audit et publiés uniquement dans les journaux de provisionnement

**Type :** Modification planifiée  
**Catégorie de service :** Signalement  
**Fonctionnalité de produit :** Monitoring et création de rapports
 
L’activité effectuée par le [service de provisionnement](../app-provisioning/user-provisioning.md) SCIM est journalisée à la fois dans les journaux d’audit et dans les journaux de provisionnement. Cela comprend les activités comme la création d’un utilisateur dans ServiceNow, la création d’un groupe dans GSuite ou l’importation d’un rôle à partir d’AWS. À l’avenir, ces événements seront publiés uniquement dans les journaux de provisionnement. Ce changement est implémenté pour éviter les événements en double dans les journaux et les coûts supplémentaires liés à l’utilisation de journaux dans Log Analytics par les clients. 

Nous fournirons une mise à jour quand une date de fin sera atteinte. Cette dépréciation n’est pas prévue pour l’année 2020. 

> [!NOTE]
> Cela n’affecte pas les événements figurant dans les journaux d’audit en dehors des événements de synchronisation émis par le service de provisionnement. Les événements comme la création d’une application, d’une stratégie d’accès conditionnel, d’un utilisateur dans l’annuaire, etc., continueront d’être émis dans les journaux d’audit. [Plus d’informations](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context)
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Agents hybrides locaux Azure AD impactés par les changements des certificats Azure TLS (Transport Layer Security)

**Type :** Modification planifiée  
**Catégorie de service :** N/A  
**Fonctionnalité de produit :** plateforme
 
Microsoft met à jour les services Azure pour qu’ils utilisent des certificats TLS issus d’un autre ensemble d’autorités de certification racines. Il y aura une mise à jour parce que les certificats d’autorité de certification actuels ne respectent pas l’une des réglementations de référence du CA/Browser Forum. Ce changement a un impact sur les agents hybrides Azure AD installés localement qui ont des environnements renforcés disposant d’une liste fixe de certificats racines. Ces agents devront être mis à jour pour approuver les nouveaux émetteurs de certificats.

Ce changement entraîne une interruption du service si vous n’agissez pas tout de suite. Ces agents sont notamment les suivants : 
- [Connecteurs de proxy d’application](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) pour l’accès à distance à des applications locales 
- Agents d’[authentification directe](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) qui permettent à vos utilisateurs de se connecter à des applications à l’aide des mêmes mots de passe
- Agents de la [préversion du provisionnement cloud](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) qui effectuent la synchronisation d’AD vers Azure AD. 

Si vous disposez d’un environnement avec des règles de pare-feu définies pour autoriser les appels sortants uniquement vers un téléchargement de liste de révocation de certificats spécifique, vous devez autoriser les URL de liste de révocation de certificats et OCSP. Pour plus d’informations sur le changement d’URL de liste de révocation de certificats et OCSP afin de permettre l’accès, consultez [Changements des certificats Azure TLS](../../security/fundamentals/tls-certificate-changes.md).
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Dépréciation d’Azure Active Directory TLS 1.0, TLS 1.1 et 3DES dans le cloud du secteur public des États-Unis

**Type :** Modification planifiée  
**Catégorie de service :** Toutes les applications Azure AD  
**Fonctionnalité de produit :** Standards
 
Azure Active Directory dépréciera les protocoles suivants à compter du 31 mars 2021 :
- TLS 1.0
- TLS 1.1
- Suite de chiffrement 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Toutes les combinaisons client-serveur et navigateur-serveur doivent utiliser TLS 1.2 et des suites de chiffrement modernes afin de maintenir une connexion sécurisée à Azure Active Directory pour les services Azure, Office 365 et Microsoft 365.

Les environnements affectés sont les suivants :
- Azure US Gov
- [Office 365 GCC High et DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)

Pour obtenir des conseils sur la suppression des dépendances envers les protocoles dépréciés, consultez [Activer la prise en charge de TLS 1.2 dans votre environnement en vue de la dépréciation de TLS 1.1 et 1.0 dans Azure AD](/troubleshoot/azure/active-directory/enable-support-tls-environment).
 
---

### <a name="assign-applications-to-roles-on-administrative-unit-and-object-scope"></a>Attribuer des applications à des rôles sur l’étendue de l’unité administrative et de l’objet

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 
Cette fonctionnalité permet d’attribuer une application (SPN) à un rôle administrateur sur l’étendue de l’unité administrative. Pour en savoir plus, reportez-vous à [Attribuer des rôles dont l’étendue est délimitée à une unité administrative](../roles/admin-units-assign-roles.md).

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Vous pouvez maintenant désactiver et supprimer des utilisateurs invités quand l’accès à une ressource leur est refusé

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités
 
Désactiver et supprimer est un contrôle avancé dans les révisions d’accès Azure AD pour permettre aux organisations de mieux gérer les invités externes dans des groupes et des applications. Si des invités sont refusés dans une révision d’accès, **la désactivation et la suppression** les empêchent automatiquement de se connecter pendant 30 jours. Au bout de 30 jours, ils sont complètement retirés du locataire.

Pour plus d’informations sur cette fonctionnalité, consultez [Désactiver et supprimer des identités externes à l’aide des révisions d’accès Azure AD](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Les créateurs de révision d’accès peuvent ajouter des messages personnalisés dans les e-mails envoyés aux réviseurs

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités
 
Dans les révisions d’accès Azure AD, les administrateurs qui créent des révisions peuvent désormais écrire un message personnalisé aux réviseurs. Les réviseurs voient le message dans l’e-mail qu’ils reçoivent, les invitant à effectuer la révision. Pour en savoir plus sur l’utilisation de cette fonctionnalité, consultez l’étape 14 de la section [Créer une ou plusieurs révisions d’accès](../governance/create-access-review.md#create-one-or-more-access-reviews).

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Nouveaux connecteurs de provisionnement dans la galerie d’applications Azure AD - Octobre 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 
Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, voir [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Assistant d’intégration pour Azure AD B2C

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C
 
L’expérience de l’Assistant d’intégration (préversion) est maintenant disponible pour les inscriptions d’applications Azure AD B2C. Cette expérience vous guide dans la configuration de votre application pour les scénarios courants. Découvrez-en plus sur les [Bonnes pratiques et recommandations sur la plateforme d’identité Microsoft](../develop/identity-platform-integration-checklist.md).
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Afficher l’ID de modèle de rôle dans l’interface utilisateur du portail Azure

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Rôles Azure  
**Fonctionnalité de produit :** Contrôle d’accès
 

Vous pouvez maintenant afficher l’ID de modèle de chaque rôle Azure AD dans le portail Azure. Dans Azure AD, sélectionnez la **description** du rôle sélectionné. 

Il est recommandé aux clients d’utiliser des ID de modèle de rôle dans leur code et leur script PowerShell plutôt que le nom complet. L’utilisation d’ID de modèle de rôle est prise en charge dans les objets [directoryRoles](/graph/api/resources/directoryrole) et [roleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta&preserve-view=true). Pour plus d’informations sur les ID de modèle de rôle, consultez [Rôles intégrés d’Azure AD](../roles/permissions-reference.md).

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>Les connecteurs d’API pour Azure AD B2C flux d’utilisateurs d’inscription sont maintenant en préversion publique

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C
 

Les connecteurs d’API peuvent désormais être utilisés avec Azure Active Directory B2C. Les connecteurs d’API vous permettent d’utiliser des API web pour personnaliser vos flux d’utilisateurs d’inscription et les intégrer à des systèmes cloud externes. Vous pouvez utiliser des connecteurs d’API pour :

- Effectuer une intégration à des workflows d’approbation personnalisés
- Valider des données d’entrée utilisateur
- Remplacer des attributs utilisateur 
- Exécuter une logique métier personnalisée 

 Pour en savoir plus, consultez la documentation [Utiliser des connecteurs d’API pour personnaliser et étendre les flux d’utilisateurs d’inscription](../../active-directory-b2c/api-connectors-overview.md).

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Propriété État pour les organisations connectées dans la gestion des droits d’utilisation

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion des annuaires **Fonctionnalité produit :** Gestion des droits d’utilisation
 

 Toutes les organisations connectées auront désormais une propriété supplémentaire appelée « État ». L’état contrôle la manière dont l’organisation connectée sera utilisée dans les stratégies qui font référence à « toutes les organisations connectées configurées ». La valeur est soit « configurée » (ce qui signifie que l’organisation est dans l’étendue des stratégies qui utilisent la clause « Tout »), soit « proposée » (ce qui signifie que l’organisation n’est pas dans l’étendue).  

Les organisations connectées créées manuellement auront le paramètre par défaut « configuré ». En attendant, celles créées automatiquement (par le biais de stratégies qui autorisent les utilisateurs d’Internet à demander l’accès) sont définies par défaut sur « proposé ».  Toutes les organisations connectées créées avant le 9 septembre 2020 seront définies sur « configuré ». Les administrateurs peuvent mettre à jour cette propriété si nécessaire. [Plus d’informations](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically)
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Les identités externes Azure Active Directory ont désormais des paramètres de sécurité avancés Premium pour B2C

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C
 
L’accès conditionnel basé sur les risques et les fonctionnalités de détection des risques d’Identity Protection sont désormais disponibles dans [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Grâce à ces fonctionnalités de sécurité avancées, les clients peuvent :
- Tirer parti d’insights intelligents pour évaluer les risques liés aux applications B2C et aux comptes d’utilisateur final. Les détections incluent les déplacements atypiques, les adresses IP anonymes, les adresses IP liées aux programmes malveillants et à Azure AD Threat Intelligence. Des rapports sur le portail et les API sont également disponibles.
- Gérez automatiquement les risques en configurant des stratégies d’authentification adaptative pour les utilisateurs B2C. Les développeurs d’applications et les administrateurs peuvent réduire les risques en temps réel en exigeant l’authentification multifacteur (MFA, Multi-Factor Authentication) ou le blocage de l’accès en fonction du niveau de risque utilisateur détecté, avec des contrôles supplémentaires disponibles suivant la localisation, le groupe et l’application.
- Effectuer une intégration à des flux d’utilisateurs Azure AD B2C et à des stratégies personnalisées. Les conditions peuvent être déclenchées à partir de flux d’utilisateurs intégrés dans Azure AD B2C ou peuvent être incorporées dans des stratégies personnalisées B2C. Comme avec d’autres aspects du flux d’utilisateurs B2C, la messagerie de l’expérience utilisateur final peut être personnalisée. La personnalisation dépend des alternatives de voix, de marque et d’atténuation de l’organisation.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Octobre 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En octobre 2020, nous avons ajouté à notre galerie d’applications les 27 nouvelles applications suivantes qui prennent en charge la fédération :

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee - Productivity Superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys Cloud Integration pour Azure](https://apps.mypurecloud.com/msteams-integration/), [Portail Zone Technologie](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker](https://console.datawiza.com/), [ZOKRI](https://app.zokri.com/), [CheckProof](../saas-apps/checkproof-tutorial.md), [Ecochallenge.org](https://events.ecochallenge.org/users/login), [atSpoke](http://atspoke.com/login), [Appointment Reminder](https://app.appointmentreminder.co.nz/account/login), [Cloud.Market](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [Greetly](https://app.greetly.com/), [OrgVitality SSO](../saas-apps/orgvitality-sso-tutorial.md), [Web Cargo Air](../saas-apps/web-cargo-air-tutorial.md), [Loop Flow CRM](../saas-apps/loop-flow-crm-tutorial.md), [Starmind](../saas-apps/starmind-tutorial.md), [Workstem](https://hrm.workstem.com/login), [Retail Zipline](../saas-apps/retail-zipline-tutorial.md), [Hoxhunt](../saas-apps/hoxhunt-tutorial.md), [MEVISIO](../saas-apps/mevisio-tutorial.md), [Samsara](../saas-apps/samsara-tutorial.md), [Nimbus](../saas-apps/nimbus-tutorial.md), [Pulse Secure virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

La documentation de toutes ces applications est disponible ici https://aka.ms/AppsTutorial

Pour référencer votre application dans la galerie d’applications Azure AD, lisez les informations détaillées ici : https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>Les journaux de provisionnement peuvent désormais être envoyés en streaming vers Log Analytics

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Signalement  
**Fonctionnalité de produit :** Monitoring et création de rapports
 

Publiez vos journaux de provisionnement dans Log Analytics afin d’effectuer les opérations suivantes :
- Stocker les journaux de provisionnement pendant plus de 30 jours
- Définir des alertes et des notifications personnalisées
- Générer des tableaux de bord pour visualiser les journaux
- Exécuter des requêtes complexes pour analyser les journaux 

Pour découvrir comment utiliser la fonctionnalité, consultez [Découvrir comment le provisionnement s’intègre aux journaux Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>Les journaux de provisionnement peuvent désormais être consultés par les propriétaires d’applications

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Signalement  
**Fonctionnalité de produit :** Monitoring et création de rapports
 
Vous pouvez désormais autoriser les propriétaires d’applications à superviser l’activité effectuée par le service de provisionnement et à résoudre les problèmes sans leur fournir un rôle privilégié ou faire du service informatique un goulot d’étranglement. [Plus d’informations](../reports-monitoring/concept-provisioning-logs.md)
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>Renommage de 10 rôles Azure Active Directory

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Rôles Azure  
**Fonctionnalité de produit :** Contrôle d’accès
 
Certains rôles intégrés Azure Active Directory (AD) ont des noms qui diffèrent de ceux qui apparaissent dans le Centre d’administration Microsoft 365, le portail Azure AD et Microsoft Graph. Cette incohérence peut entraîner des problèmes dans les processus automatisés. Avec cette mise à jour, nous renommons 10 noms de rôles pour les rendre cohérents. Le tableau suivant contient les nouveaux noms de rôles :

![Tableau répertoriant les noms de rôles dans MS API Graph et le portail Azure, ainsi que le nouveau nom de rôle proposé dans le centre d’administration M365, le portail Azure et l’API.](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Prise en charge d’Azure Active Directory B2C pour le flux de code d’authentification pour les applications monopages (SPA) utilisant MSAL.js 2.x

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C
 
MSAL.js version 2.x prend désormais en charge le flux de code d’autorisation pour les applications web monopages (SPA). Azure AD B2C prend désormais en charge l’utilisation du type d’application SPA sur le portail Azure et l’utilisation du flux de code d’autorisation MSAL.js avec PKCE pour les applications monopages. Cela permettra aux applications monopages utilisant Azure AD B2C de maintenir l’authentification unique avec les navigateurs les plus récents et de respecter les dernières recommandations concernant les protocoles d’authentification. Commencez avec le tutoriel [Inscrire une application monopage (SPA) dans Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-spa.md).

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Mises à jour pour mémoriser Multi-Factor Authentication (MFA) sur un paramètre d’appareil approuvé

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités
 

Nous avons récemment mis à jour la [mémorisation de Multi-Factor Authentication (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) sur une fonctionnalité d’appareil approuvé pour étendre l’authentification jusqu’à 365 jours. Les licences Azure Active Directory (Azure AD) Premium peuvent également utiliser la [stratégie de fréquence de connexion pour l’accès conditionnel](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) qui offre plus de souplesse pour les paramètres de réauthentification.

Pour une expérience utilisateur optimale, nous vous recommandons d’utiliser la fréquence de connexion pour l’accès conditionnel afin d’étendre les durées de vie des sessions sur les appareils approuvés, les localisations ou les sessions à faible risque comme alternative à la mémorisation de Multi-Factor Authentication (MFA) sur un paramètre d’appareil approuvé. Pour commencer, consultez nos [dernières instructions sur l’optimisation de l’expérience de réauthentification](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

---