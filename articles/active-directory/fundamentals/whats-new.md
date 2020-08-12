---
title: Nouveautés Notes de publication - Azure Active Directory | Microsoft Docs
description: Découvrez les nouveautés d’Azure Active Directory, notamment les dernières notes de publication, les problèmes connus, les corrections de bogues, les fonctionnalités dépréciées et les modifications à venir.
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
ms.date: 06/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6694043f083de30f3bca10051fc7012ef1471c37
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87798815"
---
# <a name="whats-new-in-azure-active-directory"></a>Nouveautés d’Azure Active Directory

>Soyez notifié de la disponibilité des mises à jour sur cette page en faisant un copier-coller de cette URL : `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` dans votre lecteur de flux ![icône RSS](./media/whats-new/feed-icon-16x16.png).

Azure AD bénéficie d’améliorations en continu. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues
- Fonctionnalités dépréciées
- Modifications planifiées

Cette page est mise à jour tous les mois. Donc, consultez-la régulièrement. Si vous recherchez des éléments antérieurs aux six derniers mois, vous les trouverez dans l’[Archive des nouveautés d’Azure Active Directory](whats-new-archive.md).

---

## <a name="july-2020"></a>Juillet 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>En tant qu’administrateur informatique, je souhaite cibler des applications clientes à l’aide de l’accès conditionnel

**Type :** Modification planifiée   
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Avec la version GA de la condition des applications clientes dans l’accès conditionnel, les nouvelles stratégies s’appliquent désormais par défaut à toutes les applications clientes, y compris les clients d’authentification hérités. Les stratégies existantes resteront inchangées, mais le bouton *ConfigurerOui/Non* sera supprimé des stratégies existantes pour identifier facilement les applications clientes qui sont appliquées par la stratégie. 

Lorsque vous créez une nouvelle stratégie, veillez à exclure les utilisateurs et les comptes de service qui utilisent encore l’authentification héritée. Si ce n’est pas le cas, ils seront bloqués. [Plus d’informations](https://aka.ms/caclientapps)
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Prochains correctifs de conformité SCIM

**Type :** Modification planifiée  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Le service de provisionnement Azure AD utilise la norme SCIM pour l’intégration avec les applications. Notre implémentation de la norme SCIM est en cours d’évolution et nous nous attendons à apporter des modifications à notre comportement sur la façon dont nous effectuons les opérations PATCH, ainsi que la définition de la propriété « active » sur une ressource. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-scim-compatibility)
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Le paramètre propriétaire du groupe sur le portail d’administration Azure sera modifié

**Type :** Modification planifiée  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

Les paramètres de propriétaire sur la page des paramètres généraux des groupes peuvent être configurés pour restreindre les privilèges d’attribution de propriétaire à un groupe limité d’utilisateurs dans le portail d’administration Azure et le panneau d’accès. Nous aurons bientôt la possibilité d’affecter des privilèges de propriétaire de groupe non seulement sur ces deux portails d’expérience utilisateur, mais également d’appliquer la stratégie sur le serveur principal pour fournir un comportement cohérent entre les points de terminaison, tels que PowerShell et Microsoft Graph. 

Nous allons commencer à désactiver le paramètre actuel pour les clients qui ne l’utilisent pas et offriront une option d’étendue aux utilisateurs pour le privilège de propriétaire de groupe dans les prochains mois. Pour obtenir des conseils sur la mise à jour des paramètres de groupe, consultez modifier les informations de votre groupe à l’aide d’[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Le service d’inscription Azure Active Directory met fin à la prise en charge de TLS 1.0 et 1.1

**Type :** Modification planifiée  
**Catégorie de service :** Gestion et inscription des appareils  
**Fonctionnalité de produit :** plateforme

Transport Layer Security (TLS) 1.2 et les serveurs et clients de mise à jour communiqueront bientôt avec Azure Active Directory Device Registration service. Le support de TLS 1.0 et 1.1 pour la communication avec Azure AD Device Registration service sera retiré :
- Le 31 août 2020, dans tous les clouds souverains (GCC High, DoD, etc.)
- Le 30 octobre 2020, dans tous les clouds commerciaux

[En savoir plus](https://docs.microsoft.com/azure/active-directory/devices/reference-device-registration-tls-1-2) sur le protocole TLS 1.2 pour Azure AD Registration Service.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Connexions Windows Hello entreprise visibles dans les journaux de connexion Azure AD

**Type :** Résolution  
**Catégorie de service :** Signalement  
**Fonctionnalité de produit :** Monitoring et création de rapports
 
Windows Hello Entreprise permet aux utilisateurs finaux de se connecter à des ordinateurs Windows en un geste (par exemple, un code confidentiel ou une biométrie). Les administrateurs Azure AD peuvent souhaiter différencier les connexions Windows Hello Entreprise des autres connexions Windows dans le cadre de l’authentification sans mot de passe d’une organisation. 

Les administrateurs peuvent maintenant voir si une authentification Windows a utilisé Windows Hello Entreprise en consultant l’onglet Détails de l’authentification pour un événement de connexion Windows dans le volet Connexions Azure AD dans le portail Azure. Les authentifications Windows Hello Entreprise incluront « WindowsHelloForBusiness » dans le champ méthode d’authentification. Pour plus d’informations sur l’interprétation des journaux de connexion, reportez-vous à la [Documentation des journaux de connexion](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Résolutions pour le comportement de suppression des groupes et améliorations des performances

**Type :** Résolution  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Auparavant, lorsqu'un groupe passait de « in-scope » à « out of scope » et qu'un administrateur cliquait sur « restart » avant que la modification ne soit terminée, l'objet du groupe n'était pas supprimé. À présent, l’objet de groupe sera supprimé de l’application cible lorsqu’il sort de l’étendue (désactivé, supprimé, non assigné ou échec du filtre d’étendue). [Plus d’informations](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles)
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Préversion publique : Les administrateurs peuvent désormais ajouter du contenu personnalisé à l’e-mail aux réviseurs lors de la création d’une révision d’accès

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités
 
Lorsqu’une nouvelle révision d’accès est créée, le réviseur reçoit un e-mail lui demandant d’effectuer la révision d’accès. La plupart de nos clients ont demandé la possibilité d’ajouter du contenu personnalisé à l’e-mail, comme des informations de contact, ou d’autres contenus de support supplémentaires pour guider le réviseur. 

Désormais disponible en préversion publique, les administrateurs peuvent spécifier du contenu personnalisé dans l’e-mail envoyé aux réviseurs en ajoutant du contenu dans la section « avancé » des révisions d’accès Azure AD. Pour des conseils sur la création d’une révision d’accès des groupes, consultez [Créer une révision d’accès des groupes et applications dans les révisions d’accès Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Flux du code d’autorisation pour les applications à page unique disponible

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Expérience de développement
 
Du fait des restrictions sur les cookies tiers dans les navigateurs modernes, telles que ITP de Safari, les application à page unique (SPA) doivent utiliser le flux du code d’autorisation, plutôt que le flux implicite, pour maintenir l’authentification unique. MSAL.js v 2.x prend désormais en charge le flux du code d’autorisation. 

Des mises à jour correspondantes étant présentes sur le portail Azure, vous pouvez actualiser votre SPA pour qu’il soit de type « spa » et utilise le flux du code d’authentification. Pour plus de conseils, consultez [Connecter des utilisateurs et obtenir un jeton d’accès dans une application à page unique JavaScript à l’aide du flux de code d’authentification](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code).
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Le Proxy d’application Azure AD prend désormais en charge le Client Web Services Bureau à distance

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès

Le Proxy d’application Azure AD prend désormais en charge le Client Web Services Bureau à distance (RDS). Le client web RDS permet aux utilisateurs d'accéder à l'infrastructure Bureau distant via tout navigateur compatible HTLM5 tel que Microsoft Edge, Internet Explorer 11, Google Chrome, etc. Les utilisateurs peuvent interagir avec des applications ou des bureaux distants, comme ils le feraient avec un appareil local depuis n’importe où. En utilisant le Proxy d’application Azure AD, vous pouvez augmenter la sécurité de votre déploiement des services Bureau à distance en appliquant la pré-authentification et les stratégies d’accès conditionnel pour tous les types d’applications clientes riches. Pour plus d’informations, consultez [Publier le Bureau à distance avec le Proxy d’application d’Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Flux utilisateur Azure AD B2C de nouvelle génération en préversion publique

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C
 
L'expérience de flux d'utilisateurs simplifiée offre la parité des fonctions avec les fonctions d'aperçu et est le foyer de toutes les nouvelles fonctionnalités. Les utilisateurs pourront activer de nouvelles fonctionnalités au sein du même flux d'utilisateurs, ce qui réduira la nécessité de créer plusieurs versions à chaque nouvelle sortie de fonctionnalité. Enfin, la nouvelle expérience utilisateur conviviale simplifie la sélection et la création de flux utilisateur. Essayez-la dès maintenant en [créant flux d’utilisateur](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows). 

Pour plus d'informations sur les flux d'utilisateurs, voir [Versions des flux d'utilisateurs dans Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-versions#:~:text=%20%20%20%20User%20flow%20%20%2caccount.%20Usi%20...%20%201%20more%20rows%20).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Juillet 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En juillet 2020, nous avons ajouté les 55 applications suivantes à notre galerie d’applications avec prise en charge de la fédération :

[Clap Your Hands](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor Vault](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnects receptionist](https://msteams.peterconnects.com/), [AlohaCloud](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom](https://start.cocoom.com/), [COINS Construction Cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [Rever](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Titanfile](https://docs.microsoft.com/azure/active-directory/saas-apps/titanfile-tutorial), [Wootric](https://docs.microsoft.com/azure/active-directory/saas-apps/wootric-tutorial), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US),  [OpenText Directory Services](https://docs.microsoft.com/azure/active-directory/saas-apps/opentext-directory-services-tutorial), [Datasite](https://docs.microsoft.com/azure/active-directory/saas-apps/datasite-tutorial), [BlogIn](https://docs.microsoft.com/azure/active-directory/saas-apps/blogin-tutorial), [IntSights](https://docs.microsoft.com/azure/active-directory/saas-apps/intsights-tutorial), [kpifire](https://docs.microsoft.com/azure/active-directory/saas-apps/kpifire-tutorial), [Textline](https://docs.microsoft.com/azure/active-directory/saas-apps/textline-tutorial), [Cloud Academy - SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-academy-sso-tutorial), [Community Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/community-spark-tutorial), [Chatwork](https://docs.microsoft.com/azure/active-directory/saas-apps/chatwork-tutorial), [CloudSign](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudsign-tutorial), [C3M Cloud Control](https://docs.microsoft.com/azure/active-directory/saas-apps/c3m-cloud-control-tutorial), [SmartHR](https://smarthr.jp/), [NumlyEngage™](https://docs.microsoft.com/azure/active-directory/saas-apps/numlyengage-tutorial), [Michigan Data Hub Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/michigan-data-hub-single-sign-on-tutorial), [Egress](https://docs.microsoft.com/azure/active-directory/saas-apps/egress-tutorial), [SendSafely](https://docs.microsoft.com/azure/active-directory/saas-apps/sendsafely-tutorial), [Eletive](https://app.eletive.com/), [Right-Hand Cybersecurity ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [Verme](https://docs.microsoft.com/azure/active-directory/saas-apps/verme-tutorial), [Lenses.io](https://docs.microsoft.com/azure/active-directory/saas-apps/lensesio-tutorial), [Momenta](https://docs.microsoft.com/azure/active-directory/saas-apps/momenta-tutorial), [Uprise](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudcords-tutorial), [TellMe Bot](https://tellme365liteweb.azurewebsites.net/), [Inspire](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/), [Smartschool (School Management System)](https://smart-schoolapp.com/frmLoginForm), [Zepto - Intelligent timekeeping](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](https://docs.microsoft.com/azure/active-directory/saas-apps/skedda-tutorial), [WhosOnLocation](https://docs.microsoft.com/azure/active-directory/saas-apps/whos-on-location-tutorial), [Coggle](https://docs.microsoft.com/azure/active-directory/saas-apps/coggle-tutorial), [Kemp LoadMaster](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/browserstack-single-sign-on-tutorial)

La documentation de toutes ces applications est disponible ici https://aka.ms/AppsTutorial

Pour lister votre application dans la galerie d’applications Azure AD, veuillez lire les informations détaillées ici : https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Nouveaux connecteurs de provisionnement dans la galerie d’applications Azure AD - Juillet 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce

Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour la nouvelle application nouvellement intégrée [LinkedIn Learning](https://docs.microsoft.com/azure/active-directory/saas-apps/linkedin-learning-provisioning-tutorial).

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, voir [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Afficher les attributions de rôle dans toutes les étendues et la possibilité de les télécharger dans un fichier CSV

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 
Vous pouvez maintenant afficher les attributions de rôle sur toutes les étendues d’un rôle sous l’onglet « Rôles et administrateurs » dans le portail Azure AD. Vous pouvez également télécharger ces attributions de rôle pour chaque rôle dans un fichier CSV. Pour obtenir des conseils sur l’affichage et l’ajout d’attributions de rôles, consultez [Afficher et affecter des rôles d’administrateur dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Désapprobation du développement de logiciels Azure Multi-Factor Authentication (kit de développement logiciel (SDK) Azure MFA)

**Type :** Déprécié  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Le développement de logiciels Azure Multi-Factor Authentication (kit de développement logiciel (SDK) Azure MFA) a atteint sa fin de vie le 14 novembre 2018, comme annoncé pour la première fois en novembre 2017. Microsoft va arrêter le service SDK en vigueur le 30 septembre 2020. Les appels passés au kit de développement logiciel (SDK) échouent.

Si votre organisation utilise le kit de développement logiciel (SDK) Azure MFA, vous devez migrer le 30 septembre 2020 :
- Kit de développement logiciel (SDK) Azure MFA pour MIM :  Si vous utilisez le kit de développement logiciel (SDK) avec MIM, vous devez migrer vers le serveur Azure MFA et activer Privileged Access Management (PAM) à la suite de ces [instructions](https://docs.microsoft.com/microsoft-identity-manager/working-with-mfaserver-for-mim).   
- Kit de développement logiciel (SDK) Azure MFA pour les applications personnalisées : Envisagez d’intégrer votre application dans Azure AD et d’utiliser l’accès conditionnel pour appliquer l’authentification multifacteur. Pour commencer, consultez cette [page](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration). 

---

## <a name="june-2020"></a>Juin 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Condition de risque d’utilisateur dans la stratégie d’accès conditionnel

**Type :** Modification planifiée  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 

La prise en charge des risques d’utilisateur dans une stratégie d’accès conditionnel Azure AD vous permet de créer plusieurs stratégies basées sur les risques de l’utilisateur. Différents niveaux de risque d’utilisateur minimum peuvent être nécessaires pour différents utilisateurs et applications. En fonction du risque de l’utilisateur, vous pouvez créer des stratégies pour bloquer l’accès, exiger une authentification multifacteur, la modification sécurisée du mot de passe ou une redirection vers Microsoft Cloud App Security pour appliquer une stratégie de session, par exemple un audit supplémentaire.

La condition de risque d’utilisateur requiert Azure AD Premium P2 car elle utilise Azure Identity Protection, qui est une offre P2. Pour plus d’informations sur l’accès conditionnel, consultez [Documentation relative à l’Accès conditionnel Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>L’authentification unique SAML prend désormais en charge les applications qui requièrent la définition de SPNameQualifier à la demande

**Type :** Résolution  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** SSO
 
Certaines applications SAML requièrent que SPNameQualifier soit retourné à la demande dans le sujet de l’assertion. Azure AD répond maintenant correctement lorsqu’un SPNameQualifier est demandé dans la stratégie NameID de la demande. Cette règle s’applique également à la connexion initiée par le fournisseur de service, et prochainement à la connexion initiée par le fournisseur d'identité.  Pour en savoir plus sur le protocole SAML dans Azure Active Directory, consultez [Protocole SAML d’authentification unique](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B collaboration prend en charge l’invitation des utilisateurs MSA et Google dans les locataires Azure Government

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 

Les locataires Azure Government qui utilisent les fonctionnalités de collaboration B2B peuvent désormais inviter des utilisateurs disposant d’un compte Microsoft ou Google. Pour déterminer si votre locataire peut utiliser ces fonctionnalités, suivez les instructions fournies dans [Comment savoir si B2B Collaboration est disponible dans mon locataire Azure US Government ?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>L’objet utilisateur dans MS Graph v1 comprend maintenant les propriétés externalUserState et externalUserStateChangedDateTime

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 

Les propriétés externalUserState et externalUserStateChangedDateTime peuvent être utilisées pour rechercher les invités B2B qui n’ont pas encore accepté leurs invitations, ainsi que l’automatisation des builds, notamment la suppression d’utilisateurs qui n’ont pas accepté leurs invitations après un certain nombre de jours. Ces propriétés sont désormais disponibles dans MS Graph v1. Pour obtenir des conseils sur l’utilisation de ces propriétés, reportez-vous à [Type de ressource utilisateur](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>La gestion des sessions d’authentification dans l’accès conditionnel Azure AD est désormais mise à la disposition générale

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Les fonctionnalités de gestion des sessions d’authentification vous permettent de configurer la fréquence à laquelle vos utilisateurs doivent fournir des informations d’identification de connexion ainsi que des informations d’identification après la fermeture et la réouverture des navigateurs, pour offrir plus de sécurité et de flexibilité à votre environnement.
 
Par ailleurs, la gestion des sessions d’authentification ne s'appliquait auparavant qu'à l'authentification à un facteur sur les appareils joints à Azure AD, joints à Azure AD Hybride et inscrits auprès d’Azure AD. Désormais, la gestion des sessions d’authentification s’applique également à MFA. Pour plus d’informations, consultez [Configurer la gestion de session d’authentification avec l’accès conditionnel](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Juin 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En juin 2020, nous avons ajouté les 29 nouvelles applications suivantes à notre galerie d’applications avec prise en charge de la fédération :

[Shopify Plus](https://docs.microsoft.com/azure/active-directory/saas-apps/shopify-plus-tutorial), [Ekarda](https://docs.microsoft.com/azure/active-directory/saas-apps/ekarda-tutorial), [MailGates](https://docs.microsoft.com/azure/active-directory/saas-apps/mailgates-tutorial), [BullseyeTDP](https://docs.microsoft.com/azure/active-directory/saas-apps/bullseyetdp-tutorial), [Raketa](https://docs.microsoft.com/azure/active-directory/saas-apps/raketa-tutorial), [Segment](https://docs.microsoft.com/azure/active-directory/saas-apps/segment-tutorial), [Ai Auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [Proto.io](https://docs.microsoft.com/azure/active-directory/saas-apps/proto.io-tutorial), [Gatekeeper](https://www.gatekeeperhq.com/), [Hub Planner](https://docs.microsoft.com/azure/active-directory/saas-apps/hub-planner-tutorial), [Ansira-Partner Go-to-Market Toolbox](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation on Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/ibm-digital-business-automation-on-cloud-tutorial), [Kisi Physical Security](https://docs.microsoft.com/azure/active-directory/saas-apps/kisi-physical-security-tutorial), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](https://docs.microsoft.com/azure/active-directory/saas-apps/intelligencebank-tutorial), [pymetrics](https://docs.microsoft.com/azure/active-directory/saas-apps/pymetrics-tutorial), [Zero](https://www.teamzero.com/), [InStation](https://instation.invillia.com/), [edX for Business SAML 2.0 Integration](https://docs.microsoft.com/azure/active-directory/saas-apps/edx-for-business-saml-integration-tutorial), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](https://docs.microsoft.com/azure/active-directory/saas-apps/smartkargo-tutorial), [PKIsigning platform](https://platform.pkisigning.nl/), [SiteIntel](https://docs.microsoft.com/azure/active-directory/saas-apps/siteintel-tutorial), [Field iD](https://docs.microsoft.com/azure/active-directory/saas-apps/field-id-tutorial), [Curricula SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/curricula-saml-tutorial), [Perforce Helix Core - Helix Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/perforce-helix-core-tutorial), [MyCompliance Cloud](https://cloud.metacompliance.com/), [Smallstep SSH](https://smallstep.com/sso-ssh/)  

La documentation de toutes ces applications est disponible ici https://aka.ms/AppsTutorial. Pour lister votre application dans la galerie d’applications Azure AD, veuillez lire les informations détaillées ici : https://aka.ms/AzureADAppRequest.

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>Les connecteurs d’API pour l’inscription en libre-service des identités externes sont désormais en préversion publique

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Les connecteurs d’API d’identités externes vous permettent de tirer parti des API Web pour intégrer l’inscription en libre-service à des systèmes cloud externes. Cela signifie que vous pouvez désormais appeler des API Web en tant qu’étapes spécifiques dans un flux d’inscription pour déclencher des flux de travail personnalisés basés sur le cloud. Par exemple, vous pouvez utiliser des connecteurs d’API pour :

- Intégrer à des flux de travaux d’approbation personnalisés.
- Effectuer une vérification d’identité
- Valider des données d’entrée utilisateur
- Remplacer des attributs utilisateur
- Exécuter une logique métier personnalisée

Pour plus d’informations sur toutes les expériences possibles avec les connecteurs d’API, consultez [Utiliser des connecteurs d’API pour personnaliser et étendre l’inscription en libre-service](https://docs.microsoft.com/azure/active-directory/b2b/api-connectors-overview) ou [Personnaliser l’inscription en libre-service d’identités externes avec des intégrations d’API Web](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Provisionner à la demande et faire accéder les utilisateurs à vos applications en quelques secondes

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Le service de provisionnement Azure AD fonctionne actuellement de façon cyclique. Le service s’exécute toutes les 40 minutes. La [fonctionnalité de provisionnement à la demande](https://aka.ms/provisionondemand) vous permet de choisir un utilisateur et de le configurer en quelques secondes. Cette fonctionnalité vous permet de résoudre rapidement les problèmes de provisionnement, sans avoir à relancer le cycle de provisionnement. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Nouvelle autorisation pour utiliser la gestion des droits d’utilisation Azure AD dans Graph

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Une nouvelle autorisation déléguée EntitlementManagement.Read.All est désormais disponible pour une utilisation avec l’API de gestion des droits dans Microsoft Graph version bêta. Pour en savoir plus sur les API disponibles, consultez [Utilisation de l’API de gestion des droits Azure AD](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

---

### <a name="identity-protection-apis-available-in-v10"></a>API de protection des données disponibles dans la version 1.0

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Les API riskyUsers et riskDetections Microsoft Graph sont désormais mises à la disposition générale. Elles sont maintenant disponibles au niveau du point de terminaison v1.0, et nous vous invitons à les utiliser en production. Pour plus d’informations, consultez la [documentation Microsoft Graph](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Les étiquettes de sensibilité permettant d’appliquer des stratégies à des groupes Microsoft 365 sont désormais mises à la disposition générale

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration
 

Vous pouvez maintenant créer des étiquettes de sensibilité et utiliser les paramètres d’étiquette pour appliquer des stratégies à des groupes Microsoft 365, notamment la confidentialité (publique ou privée) et la stratégie d’accès utilisateur externe. Vous pouvez créer une étiquette avec la stratégie de confidentialité privée et une stratégie d’accès utilisateur externe pour ne pas autoriser l’ajout d’utilisateurs invités. Lorsqu’un utilisateur applique cette étiquette à un groupe, le groupe est privé et aucun utilisateur invité n’est autorisé à y être ajouté. 

Les étiquettes de sensibilité sont importantes pour protéger les données critiques de votre entreprise et vous permettre de gérer les groupes à grande échelle, de manière conforme et sécurisée. Pour obtenir des conseils sur l’utilisation des étiquettes de sensibilité, consultez [Attribuer des étiquettes de sensibilité aux groupes Office 365 dans Azure Active Directory (préversion)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-assign-sensitivity-labels).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Mises à jour de la prise en charge de Microsoft Identity Manager pour les clients Azure AD Premium

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Microsoft Identity Manager  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
La prise en charge Azure est désormais disponible pour les composants d’intégration Azure AD de Microsoft Identity Manager 2016, jusqu’à la fin de la prise en charge étendue de Microsoft Identity Manager 2016. Pour en savoir plus, consultez [Mise à jour du support pour les clients Azure AD Premium utilisant Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>L’utilisation des conditions d’appartenance de groupe dans la configuration des revendications SSO a été augmentée

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** SSO
 
Auparavant, le nombre de groupes disponibles lors d’une modification conditionnelle des revendications en fonction de l’appartenance à un groupe dans une configuration d’application unique était limité à 10. L’utilisation des conditions d’appartenance de groupe dans la configuration des revendications SSO a maintenant été augmentée à un maximum de 50 groupes. Pour plus d’informations sur la configuration des revendications, reportez-vous à [Configuration des revendications SSO des applications d’entreprise](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Activation de la mise en forme de base sur le composant Texte de la page de connexion dans la marque de société.

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
La fonctionnalité Marque de société sur l’expérience de connexion Azure AD/Microsoft 365 a été mise à jour pour permettre au client d’ajouter des liens hypertexte et une mise en forme simple, y compris une police en gras, le soulignement et l’italique. Pour obtenir des conseils sur l’utilisation de cette fonctionnalité, voir [Personnaliser la page de connexion Azure Active Directory de votre organisation](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="provisioning-performance-improvements"></a>Améliorations apportées aux performances de provisionnement

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Le service de provisionnement a été mis à jour pour réduire le temps nécessaire à l’exécution d’un [cycle incrémentiel](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles). Cela signifie que les utilisateurs et les groupes seront provisionnés dans leurs applications plus rapidement qu’auparavant. Toutes les tâches de provisionnement créées après le 10/06/2020 bénéficient automatiquement des améliorations de performances. Toutes les applications configurées pour un provisionnement avant le 10/06/2020 devront redémarrer une fois après cette date pour tirer parti des améliorations de performances. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Annonce de la dépréciation d’ADAL et de la parité MS Graph

**Type :** Déprécié  
**Catégorie de service :** N/A  
**Fonctionnalité de produit :** Gestion du cycle de vie des appareils

Maintenant que Microsoft Authentication Libraries (MSAL) est disponible, nous n’ajouterons plus de nouvelles fonctionnalités à Azure Active Directory Authentication Libraries (ADAL) et arrêterons les correctifs de sécurité le 30 juin 2022. Pour plus d’informations sur la migration vers MSAL, reportez-vous à [Migrer des applications vers la bibliothèque d’authentification Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-migration).

En outre, nous avons terminé la préparation de toutes les fonctionnalités Azure AD Graph disponibles via MS Graph. Ainsi, les API Graph Azure AD recevront uniquement des correctifs de bogues et de sécurité jusqu’au 30 juin 2022. Pour plus d’informations, consultez [Mettre à jour vos applications pour utiliser la bibliothèque d’authentification Microsoft et l’API Microsoft Graph](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
 
## <a name="may-2020"></a>Mai 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Mise hors service des propriétés dans les API signIns, riskyUsers et riskDetections

**Type :** Modification planifiée  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Actuellement, les types énumérés sont utilisés pour représenter la propriété riskType à la fois dans l’API riskDetections et dans riskyUserHistoryItem (en préversion). Les types énumérés sont également utilisés pour la propriété riskEventTypes dans l’API signIns. À l’avenir, nous représenterons ces propriétés sous forme de chaînes. 

Les clients doivent effectuer la transition vers la propriété riskEventType dans la version bêta des API riskDetections et riskyUserHistoryItem, et pour la propriété riskEventTypes_v2 dans la version bêta de l’API signIns d’ici au 9 septembre 2020. À cette date, nous mettrons hors service les propriétés riskType et riskEventTypes actuelles. Pour plus d’informations, reportez-vous à [Modifications apportées aux propriétés d’événements à risque et aux API de protection d’identité sur Microsoft Graph](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/).

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Dépréciation de la propriété riskEventTypes dans l’API signIns v1.0 sur Microsoft Graph

**Type :** Modification planifiée  
**Catégorie de service :** Signalement  
**Fonctionnalité de produit :** Protection et sécurité des identités

Les types énumérés basculeront vers les types chaîne s’ils représentent des propriétés d’événement à risque dans Microsoft Graph en septembre 2020. En plus de l’impact sur les API en préversion, cette modification affectera également l’API signIns en production.

Nous avons introduit une nouvelle propriété riskEventsTypes_v2 (chaîne) pour l’API signIns v1.0. Nous mettrons hors service la propriété riskEventTypes (enum) actuelle le 11 juin 2022, conformément à notre stratégie de dépréciation de Microsoft Graph. Les clients doivent passer à la propriété riskEventTypes_v2 dans l’API signIns v1.0 avant le 11 juin 2022. Pour plus d’informations, consultez [Dépréciation de la propriété riskEventTypes dans l’API signIns v1.0 sur Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//).

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Modifications à venir concernant les notifications par e-mail MFA

**Type :** Modification planifiée  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités
 

Nous apportons les modifications suivantes aux notifications par e-mail pour la MFA cloud :

Des notifications par e-mail vont être envoyées à partir de l’adresse suivante : azure-noreply@microsoft.com et msonlineservicesteam@microsoftonline.com. Nous mettons à jour le contenu des e-mails d’alerte de fraude afin de mieux indiquer les étapes à suivre pour débloquer les utilisations.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Nouvelle inscription en libre-service pour les utilisateurs des domaines fédérés qui ne peuvent pas accéder à Microsoft teams parce qu’ils ne sont pas synchronisés à Azure Active Directory.

**Type :** Modification planifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 

Actuellement, les utilisateurs qui se trouvent sur des domaines fédérés dans Azure AD, mais qui ne sont pas synchronisés avec le locataire, ne peuvent pas accéder à Teams. À partir de la fin juin, cette nouvelle possibilité leur permettra de le faire par l’extension de la fonctionnalité d’inscription vérifiée par e-mail. Les utilisateurs qui peuvent se connecter à un fournisseur d’identité fédéré, mais qui ne possèdent pas encore d’objet utilisateur dans Azure ID, seront en mesure d’obtenir cet objet créé automatiquement, et d’être authentifié pour Teams. Leur objet utilisateur sera désigné en tant que « inscription en libre-service ». Il s’agit d’une extension de la capacité existante à effectuer une inscription automatique vérifiée par e-mail, à laquelle les utilisateurs des domaines gérés peuvent procéder et avec laquelle ils peuvent être contrôlés à l’aide du même indicateur. Cette modification s’effectuera lors du déploiement se déroulant dans les deux prochains mois. Retrouvez les mises à jour de la documentation [ici](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Correctif à venir : Le document de découverte OIDC pour le cloud Azure Government est mis à jour pour faire référence aux points de terminaison Graph appropriés.

**Type :** Modification planifiée  
**Catégorie de service :** Clouds souverains  
**Fonctionnalité de produit :** Authentification utilisateur
 
À partir de juin, le document de découverte OIDC, [Microsoft Identity Platform and OpenID Connect Protocol](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) (Plateforme d’identités Microsoft et protocole OpenID Connect), relatif au point de terminaison [Cloud Azure Government](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) (login.microsoftonline.us), va commencer à retourner le point de terminaison [Graph du cloud national](https://docs.microsoft.com/graph/deployments) approprié (https://graph.microsoft.us ou https://dod-graph.microsoft.us), en fonction du locataire fourni.  Il présente actuellement le champ « msgraph_host » incorrect du point de terminaison Graph (graph.microsoft.com).  

Ce correctif de bogue sera progressivement déployé sur une période de 2 mois environ.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Les utilisateurs d’Azure Government ne seront plus en mesure de se connecter à login.microsoftonline.com

**Type :** Planifier la modification  
**Catégorie de service :** Clouds souverains  
**Fonctionnalité de produit :** Authentification utilisateur
 
Le 1er juin 2018, l’adresse de l’autorité officielle Azure Active Directory (AAD) pour Azure Government a changée de https://login-us.microsoftonline.com en https://login.microsoftonline.us. Si vous possédez une application au sein d’un locataire Azure Government, vous devez mettre à jour votre application pour qu’elle connecte les utilisateurs sur le point de terminaison .us.

À partir du 5 mai, Azure AD commencera à appliquer le changement de point de terminaison en empêchant les utilisateurs d’Azure Government de se connecter à des applications hébergées dans des locataires Azure Government à l’aide du point de terminaison public (microsoftonline.com). Les applications impactées commenceront à afficher une erreur AADSTS900439-USGClientNotSupportedOnPublicEndpoint. 

Un déploiement progressif de cette modification va avoir lieu, dont la mise en application est prévue de s’achever sur l’ensemble des applications en juin 2020. Pour plus d’informations, consultez le [billet de blog Azure Government](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/).

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>La requête SAML Single logout envoie désormais NameID au format correct

**Type :** Résolution  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Lorsqu’un utilisateur clique sur la déconnexion (par exemple, dans le portail MyApps), Azure AD envoie un message SAML Single Logout à chaque application qui est active dans la session utilisateur et pour laquelle une URL de déconnexion est configurée. Ces messages contiennent un NameID dans un format persistant.

Si le jeton de connexion SAML d’origine a utilisé un format différent pour NameID (par exemple, e-mail/UPN), l’application SAML ne peut pas mettre en corrélation le NameID du message de déconnexion avec une session existante (puisque les NameID utilisés dans les deux messages sont différents), ce qui a provoqué le rejet du message de déconnexion par l’application SAML, et le maintien de l’utilisateur connecté. Ce correctif met le message de déconnexion en adéquation avec le NameID configuré pour l’application.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>Le rôle d’administrateur d’identité hybride est désormais disponible avec le provisionnement cloud

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement cloud Azure AD  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Les administrateurs informatiques peuvent commencer à utiliser le nouveau rôle d’« administrateur hybride » en tant que rôle le moins privilégié pour configurer le provisionnement cloud Azure ADConnect. Avec ce nouveau rôle, vous n’avez plus besoin d’utiliser le rôle d’administrateur général pour installer et configurer le provisionnement cloud. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-by-task#connect)
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Mai 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En mai 2020, nous avons ajouté les 36 applications suivantes à notre galerie d’applications avec prise en charge de la fédération :

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell Teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [ASC Recording Insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-Engaged](https://b-engaged.se/), [Competella Contact Center Workgroup](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft Identity](https://identity.imagesoftinc.com/), [My IBISWorld](https://identity.imagesoftinc.com/), [insuite](https://docs.microsoft.com/azure/active-directory/saas-apps/insuite-tutorial), [Change Process Management](https://docs.microsoft.com/azure/active-directory/saas-apps/change-process-management-tutorial), [Cyara CX Assurance Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/cyara-cx-assurance-platform-tutorial), [Smart Global Governance](https://docs.microsoft.com/azure/active-directory/saas-apps/smart-global-governance-tutorial), [Prezi](https://docs.microsoft.com/azure/active-directory/saas-apps/prezi-tutorial), [Mapbox](https://docs.microsoft.com/azure/active-directory/saas-apps/mapbox-tutorial), [Datava Enterprise Service Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/datava-enterprise-service-platform-tutorial), [Whimsical](https://docs.microsoft.com/azure/active-directory/saas-apps/whimsical-tutorial), [Trelica](https://docs.microsoft.com/azure/active-directory/saas-apps/trelica-tutorial), [EasySSO for Confluence](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-confluence-tutorial), [EasySSO for BitBucket](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bitbucket-tutorial), [EasySSO for Bamboo](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bamboo-tutorial), [Torii](https://docs.microsoft.com/azure/active-directory/saas-apps/torii-tutorial), [Axiad Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/axiad-cloud-tutorial), [Humanage](https://docs.microsoft.com/azure/active-directory/saas-apps/humanage-tutorial), [ColorTokens ZTNA](https://docs.microsoft.com/azure/active-directory/saas-apps/colortokens-ztna-tutorial), [CCH Tagetik](https://docs.microsoft.com/azure/active-directory/saas-apps/cch-tagetik-tutorial), [ShareVault](https://docs.microsoft.com/azure/active-directory/saas-apps/sharevault-tutorial), [Vyond](https://docs.microsoft.com/azure/active-directory/saas-apps/vyond-tutorial), [TextExpander](https://docs.microsoft.com/azure/active-directory/saas-apps/textexpander-tutorial), [Anyone Home CRM](https://docs.microsoft.com/azure/active-directory/saas-apps/anyone-home-crm-tutorial), [askSpoke](https://docs.microsoft.com/azure/active-directory/saas-apps/askspoke-tutorial), [ice Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/ice-contact-center-tutorial)

La documentation de toutes ces applications est disponible ici https://aka.ms/AppsTutorial.

Pour lister votre application dans la galerie d’applications Azure AD, veuillez lire les informations détaillées ici https://aka.ms/AzureADAppRequest.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Disponibilité générale du mode rapport seul pour l’accès conditionnel

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités

Le [mode rapport seul pour l’accès conditionnel Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) vous permet d’évaluer le résultat d’une stratégie sans appliquer de contrôles d’accès. Vous pouvez tester les stratégies de rapport seul au sein de votre organisation afin de bien comprendre leur impact avant de les activer, ce qui rend leur déploiement à la fois plus sûr et plus facile. Ces derniers mois, nous avons constaté une adoption forte du mode rapport seul ; plus de 26 millions d’utilisateurs opèrent déjà dans le cadre d’une stratégie de rapport seul. Faisant suite à l’annonce d’aujourd’hui, les nouvelles stratégies d’accès conditionnel Azure AD seront créées par défaut en mode rapport seul. Cela signifie que vous pouvez surveiller l’impact de vos stratégies à partir du moment de leur création. Et ceux d’entre vous qui utilisent les API MS Graph peuvent également [gérer les stratégies de rapport seul par programmation](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="self-service-sign-up-for-guest-users"></a>Inscription en libre-service pour les utilisateurs invités

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Avec les identités externes dans Azure AD, vous pouvez autoriser des personnes extérieures à votre organisation à accéder à vos applications et ressources tout en leur permettant de se connecter à l’aide de l’identité de leur choix. Lorsque vous partagez une application avec des utilisateurs externes, vous ne savez pas toujours à l’avance qui aura besoin d’y accéder. Avec l’[inscription en libre-service](https://docs.microsoft.com/azure/active-directory/b2b/self-service-sign-up-overview), vous pouvez autoriser les utilisateurs invités à s’inscrire et à obtenir un compte invité pour vos applications métier. Le flux d’inscription peut être créé et personnalisé pour prendre en charge les identités Azure AD et celles des réseaux sociaux. Vous pouvez également collecter des informations supplémentaires sur l’utilisateur lors de l’inscription.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Disponibilité générale du classeur Insights et rapports sur l’accès conditionnel

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités

Le [classeur Insights et rapports](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) donne aux administrateurs une vue récapitulative de l’accès conditionnel Azure AD dans leur locataire. Avec la possibilité de sélectionner une stratégie individuelle, les administrateurs peuvent mieux comprendre l’activité de chaque stratégie, et superviser les modifications en temps réel. Le classeur diffuse les données stockées dans Azure Monitor, que vous pouvez configurer en quelques minutes en [suivant ces instructions](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Pour améliorer la visibilité du tableau de bord, nous avons déplacé celui-ci vers le nouvel onglet Insights et rapports dans le menu de l’accès conditionnel Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Le panneau Détails de la stratégie pour l’accès conditionnel est en préversion publique

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités

Le nouveau [panneau Détails de la stratégie](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) affiche les affectations, conditions et contrôles qui ont été respectés lors de l’évaluation de la stratégie d’accès conditionnel. Vous pouvez accéder au panneau en sélectionnant une ligne sous les onglets Accès conditionnel ou Rapport seul des Détails de connexion.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>De nouvelles fonctionnalités de requête pour les objets d’annuaire dans Microsoft Graph sont en préversion publique

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** MS Graph **Fonctionnalité de produit :** Expérience de développement

De nouvelles fonctionnalités sont introduites pour les API des objets d’annuaire de Microsoft Graph, ce qui permet d’effectuer des opérations de comptage, de recherche, de filtrage et de tri. Les développeurs pourront ainsi interroger rapidement nos objets d’annuaire sans recourir à des solutions de contournement, telles que les filtrage et tri en mémoire. Apprenez-en davantage dans ce [billet de blog](https://aka.ms/CountFilterMSGraphAAD).

Nous sommes actuellement en préversion publique et aimerions avoir votre avis sur ce point. Merci de nous transmettre vos commentaires en répondant à ce [court sondage](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Configurer l’authentification unique SAML à l’aide de l’API Microsoft Graph (Bêta)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** SSO
 
La prise en charge de la création et de la configuration d’une application depuis la galerie Azure AD, à l’aide des API Microsoft Graph dans la version Bêta, est désormais disponible. Si vous avez besoin de configurer l’authentification unique SAML pour plusieurs instances d’une application, gagnez du temps en utilisant les API Microsoft Graph pour [automatiser la configuration de l’authentification unique SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nouveaux connecteurs de provisionnement dans la galerie d’applications Azure AD - Mai 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 
Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

* [8x8](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8-provisioning-tutorial)
* [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-provisioning-tutorial)
* [MediusFlow](https://docs.microsoft.com/azure/active-directory/saas-apps/mediusflow-provisioning-tutorial)
* [New Relic by Organization](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-by-organization-provisioning-tutorial)
* [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, voir [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="saml-token-encryption-is-generally-available"></a>Disponibilité générale du chiffrement de jeton SAML

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** SSO
 
Le [chiffrement de jeton SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption) permet aux applications d’être configurées pour recevoir des assertions SAML chiffrées. La fonctionnalité est désormais en disponibilité générale dans tous les clouds.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Disponibilité générale des revendications de nom de groupe dans les jetons d’application

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** SSO
 
Les revendications de groupe émises dans un jeton peuvent maintenant être limitées aux seuls groupes attribués à l’application.  Cela est particulièrement important lorsque les utilisateurs sont membres d’un grand nombre de groupes, et qu’il existe un risque de dépassement des limites de taille des jetons. Avec cette nouvelle capacité en place, la possibilité d’[ajouter des noms de groupe à des jetons](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims) est mise en disponibilité générale.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Workday Writeback prend désormais en charge la définition d’attributs de numéro de téléphone professionnel

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Nous avons amélioré l’application de provisionnement Workday Writeback pour qu’elle prenne dorénavant en charge la réécriture des attributs de numéros de téléphone mobile et fixe professionnels. Outre l’e-mail et le nom d’utilisateur, vous pouvez à présent configurer l’application de provisionnement Workday Writeback pour transmettre des valeurs de numéro de téléphone d’Azure AD à Workday. Pour plus d’informations sur la configuration de la réécriture des numéros de téléphone, reportez-vous au tutoriel de l’application [Workday Writeback](https://aka.ms/WorkdayWriteback). 

---

### <a name="publisher-verification-preview"></a>Vérification de l’éditeur (préversion)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Expérience de développement
 
La vérification de l’éditeur (préversion) permet aux administrateurs et aux utilisateurs finaux de s’assurer de l’authenticité des développeurs d’applications qui s’intègrent à la plateforme d’identités Microsoft. Pour plus d’informations, reportez-vous à [Vérification de l’éditeur (préversion)](https://docs.microsoft.com/azure/active-directory/develop/publisher-verification-overview).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Flux du code d’autorisation pour les applications à page unique

**Type :** Fonctionnalité modifiée **Catégorie de service :** Authentification **Fonctionnalité de produit :** Expérience de développement

Du fait [des restrictions sur les cookies tiers dans les navigateurs modernes, telles que ITP de Safari](https://docs.microsoft.com/azure/active-directory/develop/reference-third-party-cookies-spas), les application à page unique (SPA) doivent utiliser le flux du code d’autorisation, plutôt que le flux implicite, pour maintenir l’authentification unique. MSAL.js v 2.x prend désormais en charge le flux du code d’autorisation. Des mises à jour correspondantes étant présentes sur le portail Azure, vous pouvez actualiser votre SPA pour qu’il soit de type « spa » et utilise le flux du code d’authentification. Pour obtenir des instructions, reportez-vous à [Démarrage rapide : Connecter des utilisateurs et obtenir un jeton d’accès dans une application à page unique JavaScript à l’aide du flux de code d’authentification](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Le filtrage amélioré des appareils est en préversion publique

**Type :** Fonctionnalité modifiée   
**Catégorie de service :** Gestion des appareils **Fonctionnalité de produit :** Gestion du cycle de vie des appareils
 
Auparavant, les seuls filtres que vous pouviez utiliser étaient « Enabled » (Activé) et « Activity date » (Date d’activité). À présent, vous pouvez [filtrer votre liste d’appareils sur plus de propriétés](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal#device-list-filtering-preview), y compris le type de système d’exploitation, le type de jointure, la conformité, etc. Ces ajouts doivent simplifier la localisation d’un appareil particulier.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>La nouvelle expérience d’inscriptions d’applications pour Azure AD B2C est désormais en disponibilité générale

**Type :** Fonctionnalité modifiée   
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
La nouvelle expérience d’inscriptions d’applications pour Azure AD B2C est désormais en disponibilité générale. 

Auparavant, vous deviez gérer vos applications B2C destinées aux consommateurs séparément du reste de vos applications, en utilisant l’expérience « Applications » héritée. Cela signifiait différentes expériences de création d’applications à différents emplacements dans Azure.

La nouvelle expérience affiche toutes les inscriptions d’applications Azure AD B2C et les inscriptions d’applications Azure AD dans un même emplacement, et permet de les gérer de manière cohérente. Que vous deviez gérer une application accessible aux clients, ou une application qui accède à Microsoft Graph pour manager par programmation des ressources Azure AD B2C, vous n’avez besoin d’apprendre qu’une seule façon de faire les choses.

Vous pouvez afficher la nouvelle expérience en accédant au service Azure AD B2C et en sélectionnant le panneau d’inscriptions des applications. L’expérience est également accessible à partir du service Azure Active Directory.

L’expérience d’inscriptions d’applications Azure AD B2C repose sur l’[expérience d’inscription d’applications générale](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) pour les locataires Azure AD, mais elle est personnalisée pour Azure AD B2C. L’expérience « Applications » héritée sera dépréciée à l’avenir.

Pour plus d’informations, consultez [La nouvelle expérience d’inscription d’applications pour Azure AD B2C](https://aka.ms/b2cappregtraining).

---

## <a name="april-2020"></a>Avril 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>L’expérience d’inscription combinées des informations de sécurité est désormais généralement disponible

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** Authentifications (connexions)

**Fonctionnalité de produit :** Protection et sécurité des identités

L’expérience d’inscription combinée pour l’authentification multifacteur (MFR, Multi-Factor Authentication) et la réinitialisation de mot de passe en libre-service (SSPR, Self-Service Password Reset) est désormais généralement disponible. Cette nouvelle expérience d’inscription permet aux utilisateurs de s’inscrire pour la MFA et la SSPR en un seul processus pas à pas. Lorsque vous déployez la nouvelle expérience pour votre organisation, les utilisateurs peuvent s’inscrire en moins de temps et avec moins de tracas. Consultez le billet de blog [ici](https://bit.ly/3etiRyQ).

---

### <a name="continuous-access-evaluation"></a>Évaluation continue de l’accès

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** Authentifications (connexions)

**Fonctionnalité de produit :** Protection et sécurité des identités

L’évaluation continue de l’accès est une nouvelle fonctionnalité de sécurité qui permet une mise en œuvre en quasi-temps réel de stratégies sur des parties se fiant à l’utilisation des jetons d’accès Azure AD lorsque certains événements tels que la suppression d’un compte d’utilisateur se produisent dans Azure AD. Nous déployons cette fonctionnalité en priorité pour les clients Teams et Outlook. Pour plus d’informations, consultez notre [blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) et la [documentation](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation).

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>Connexion SMS : Les employés de terrain peuvent se connecter à des applications adossées à Azure AD sans introduire de mot de passe, mais simplement leur numéro de téléphone

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** Authentifications (connexions)

**Fonctionnalité de produit :** Authentification utilisateur

Office lance une série d’applications métier avant tout mobiles qui s’adressent à des organisations non traditionnelles et à des employés de grandes organisations pour qui l’e-mail n’est pas la méthode de communication principale. Ces applications ciblent les employés de terrain, les travailleurs sans bureau, les agents de terrain ou les effectifs de vente au détail qui n’ont pas forcément d’adresse e-mail professionnelle, ou d’accès à un ordinateur ou à d’autres ressources informatiques. Ce projet permet à ces employés de se connecter à des applications d’entreprise en entrant un numéro de téléphone et un code. Pour en savoir, consultez notre [documentation administrateur](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin) et notre [documentation utilisateur final](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Inviter les utilisateurs internes à adopter B2B Collaboration

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** B2B

**Fonctionnalité de produit :**

Nous développons la fonctionnalité d’invitation B2B permettant d’inviter des comptes internes existants à utiliser désormais des informations d’identification pour la collaboration B2B. Pour ce faire, vous pouvez transmettre l’objet utilisateur à l’API Invite en plus de paramètres standard tels que l’adresse e-mail invitée. L’ID d’objet, l’UPN, l’appartenance à un groupe, l’affectation d’applications et autres informations des utilisateurs restent intacts, mais ceux-ci utilisent désormais la solution B2B pour s’authentifier avec les informations d’identification de leur locataire de base plutôt qu’avec les informations d’identification internes qu’ils utilisaient avant l’invitation. Pour plus de détails, consultez la [documentation](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Disponibilité générale du mode rapport seul pour l’accès conditionnel

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** Accès conditionnel

**Fonctionnalité de produit :** Protection et sécurité des identités

Le [mode rapport seul pour l’accès conditionnel Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) vous permet d’évaluer le résultat d’une stratégie sans appliquer de contrôles d’accès. Vous pouvez tester les stratégies de rapport seul au sein de votre organisation afin de bien comprendre leur impact avant de les activer, ce qui rend leur déploiement à la fois plus sûr et plus facile. Ces derniers mois, nous avons constaté une adoption forte du mode rapport seul, avec plus de 26 millions d’utilisateurs opérant déjà dans le cadre d’une stratégie de rapport seul. À la suite de cette annonce, les nouvelles stratégies d’accès conditionnel Azure AD seront créées par défaut en mode rapport seul. Cela signifie que vous pouvez surveiller l’impact de vos stratégies à partir du moment de leur création. Et ceux d’entre vous qui utilisent les API MS Graph peuvent également [gérer les stratégies de rapport seul par programmation](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Disponibilité générale du classeur Insights et rapports de l’accès conditionnel

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** Accès conditionnel

**Fonctionnalité de produit :** Protection et sécurité des identités

Le [classeur Insights et rapports](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) de l’accès conditionnel donne aux administrateurs une vue récapitulative de l’accès conditionnel Azure AD dans leur locataire. Avec la possibilité de sélectionner une stratégie individuelle, les administrateurs peuvent mieux comprendre ce que fait chaque stratégie et surveiller les modifications en temps réel. Le classeur diffuse les données stockées dans Azure Monitor, que vous pouvez configurer en quelques minutes en [suivant ces instructions](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Pour améliorer la visibilité du tableau de bord, nous avons déplacé celui-ci vers le nouvel onglet Insights et rapports dans le menu de l’accès conditionnel Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Le panneau Détails de la stratégie pour l’accès conditionnel est en préversion publique

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** Accès conditionnel

**Fonctionnalité de produit :** Protection et sécurité des identités

Le nouveau [panneau Détails de la stratégie](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) affiche les affectations, conditions et contrôles qui ont été respectés lors de l’évaluation de la stratégie d’accès conditionnel. Vous pouvez accéder au panneau en sélectionnant une ligne sous les onglets **Accès conditionnel** ou **Rapport seul** des Détails de connexion.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Avril 2020

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** Applications d’entreprise

**Fonctionnalité de produit :** Intégration tierce

En avril 2020, nous avons ajouté à la galerie d’applications les 31 nouvelles applications suivantes qui prennent en charge la fédération des identités : 

[SincroPool Apps](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [Float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial), [LMS365](https://lms.365.systems/), [IWT Procurement Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial), [Lunni](https://lunni.fi/), [EasySSO for Jira](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial), [Office 365 Mover](https://app.mover.io/login), [Speaker Engage](https://speakerengage.com/login.php), [Honestly](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial), [Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial), [DutyFlow](https://app.dutyflow.nl/), [AlertMedia](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial), [gr8 People](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial), [Pendo](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial), [HighGround](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial), [Harmony](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial), [Timetabling Solutions](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial), [SynchroNet CLICK](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial), [empower](https://www.made-in-office.com/en/), [Fortes Change Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial), [Litmus](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial), [GroupTalk](https://recorder.grouptalk.com/), [Frontify](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial), [MongoDB Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial), [TickitLMS Learn](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial), [COCO](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [Nitro Productivity Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) , [Trend Micro Web Security(TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Disponibilité de la prise en charge de requête delta Microsoft Graph pour oAuth2PermissionGrant en préversion publique

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** MS Graph

**Fonctionnalité de produit :** Expérience de développement

La requête delta pour oAuth2PermissionGrant est disponible en préversion publique. Vous pouvez maintenant suivre des modifications sans devoir continuellement interroger Microsoft Graph. [En savoir plus.](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Disponibilité générale de la prise en charge de requête delta Microsoft Graph pour les contacts professionnels

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** MS Graph

**Fonctionnalité de produit :** Expérience de développement

La requête delta pour les contacts professionnels est en disponibilité générale. Vous pouvez à présent suivre les modifications dans les applications de production sans devoir interroger continuellement Microsoft Graph. Remplacez tout code interrogeant continuellement les données orgContact par une requête delta afin d’améliorer considérablement les performances. [En savoir plus.](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Disponibilité générale de la prise en charge de requête delta Microsoft Graph pour les applications

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** MS Graph

**Fonctionnalité de produit :** Expérience de développement

La requête delta pour les applications est généralement disponible. Vous pouvez maintenant suivre les modifications dans les applications de production sans devoir interroger continuellement Microsoft Graph. Remplacez tout code interrogeant continuellement les données d’application par une requête delta afin d’améliorer considérablement les performances. [En savoir plus.](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Disponibilité de la prise en charge de requête delta Microsoft Graph pour les unités administratives en préversion publique

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** MS Graph

**Fonctionnalité de produit :** La requête delta de l’expérience développeur pour les unités administratives est disponible en préversion publique. Vous pouvez maintenant suivre des modifications sans devoir continuellement interroger Microsoft Graph. [En savoir plus.](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Gestion des numéros de téléphone d’authentification et bien plus encore dans les nouvelles API bêta de Microsoft Graph

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** MS Graph

**Fonctionnalité de produit :** Expérience de développement

Ces API constituent un outil essentiel pour la gestion des méthodes d’authentification de vos utilisateurs. Vous pouvez désormais préinscrire et gérer par programme les authentificateurs utilisés pour l’authentification multifacteur et la réinitialisation de mot de passe en libre-service. Il s’agit de l’une des fonctionnalités les plus demandées dans les espaces Azure MFA, SSPR et Microsoft Graph. Les nouvelles API que nous avons publiées dans cette vague vous donnent la possibilité d’effectuer les opérations suivantes :

- Lire, ajouter, mettre à jour et supprimer les téléphones d’authentification d’un utilisateur
- Réinitialiser le mot de passe d’un utilisateur
- Activer et désactiver la connexion par SMS

Pour plus d’informations, consultez [Présentation de l’API Méthodes d’authentification Azure AD](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### <a name="administrative-units-public-preview"></a>Préversion publique des unités administratives

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** RBAC

**Fonctionnalité de produit :** Contrôle d’accès

Les unités administratives vous permettent d’accorder des autorisations d’administrateur limitées à un service, une région ou un autre segment de votre organisation que vous définissez. Vous pouvez utiliser des unités administratives pour déléguer des autorisations à des administrateurs régionaux ou définir une stratégie à un niveau précis. Par exemple, un administrateur de comptes d’utilisateur peut mettre à jour des informations de profil, réinitialiser des mots de passe et attribuer des licences aux utilisateurs uniquement dans son unité administrative.

Les unités administratives permettent à un administrateur central d’effectuer les opérations suivantes :

- Créer une unité administrative pour la gestion décentralisée des ressources
- Attribuer un rôle disposant d’autorisations administratives uniquement sur les utilisateurs Azure AD d’une unité administrative
- Remplir les unités administratives d’utilisateurs et de groupes en fonction des besoins

Pour plus d’informations, consultez [Gestion des unités administratives dans Azure Active Directory (préversion)](https://aka.ms/AdminUnitsDocs)

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Rôles intégrés Administrateur d’imprimantes et Technicien en charge des imprimantes

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** RBAC

**Fonctionnalité de produit :** Contrôle d’accès

**Administrateur d’imprimantes** : Les utilisateurs ayant ce rôle peuvent inscrire des imprimantes et gérer tous les aspects liés aux configurations de celles-ci dans la solution Impression universelle de Microsoft, dont les paramètres du connecteur d’impression universelle. Ils peuvent consentir à toutes les demandes d’autorisation d’impression déléguée. Les administrateurs d’imprimantes ont également accès aux rapports d’impression. 

**Technicien en charge des imprimantes** : Les utilisateurs ayant ce rôle peuvent inscrire des imprimantes et gérer leur statut dans la solution Impression universelle de Microsoft. Ils peuvent également lire toutes les informations du connecteur. L’une des tâches clés qu’un technicien en charge des imprimantes ne peut pas accomplir est la définition d’autorisations utilisateur sur les imprimantes et le partage d’imprimantes. [En savoir plus.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Rôle intégré d’administrateur d’identité hybride

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** RBAC

**Fonctionnalité de produit :** Contrôle d’accès

Les utilisateurs de ce rôle peuvent activer, configurer et gérer les services et paramètres liés à l’activation de l’identité hybride dans Azure AD. Ce rôle permet de configurer Azure AD sur l’une des trois méthodes d’authentification prises en charge (synchronisation de hachage du mot de passe, authentification directe ou fédération -AD FS ou fournisseur de fédération tiers-), et de déployer une infrastructure locale associée pour les activer. L’infrastructure locale comprend des agents d’approvisionnement et d’authentification directe. Ce rôle donne la possibilité d’activer l’authentification unique transparente (S-SSO) pour permettre une authentification transparente sur des appareils autres que Windows 10 ou des ordinateurs autres que Windows Server 2016. En outre, ce rôle donne la possibilité de voir les journaux de connexion et d’accéder à des informations en lien avec l’intégrité et l’analytique à des fins de surveillance et de dépannage. [En savoir plus.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Rôle intégré d’administrateur réseau

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** RBAC

**Fonctionnalité de produit :** Contrôle d’accès

Les utilisateurs titulaires de ce rôle peuvent passer en revue, depuis l’emplacement où ils se trouvent, les recommandations de Microsoft relatives à l’architecture du périmètre réseau, basées sur la télémétrie du réseau. Les performances réseau pour Office 365 s’appuient sur une architecture de périmètre de réseau client entreprise soigneuse, qui est généralement spécifique de l’emplacement de l’utilisateur. Ce rôle permet de modifier les emplacements utilisateur découverts et la configuration des paramètres réseau pour ces emplacements afin de faciliter les mesures de télémétrie et les recommandations de conception améliorées. [En savoir plus.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Activité en bloc et téléchargements dans l’expérience du portail d’administration Azure AD

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** User Management

**Fonctionnalité de produit :** Répertoire

Vous pouvez désormais conduire des activités en bloc sur des utilisateurs et des groupes dans Azure AD en téléchargeant un fichier CSV via le portail d’administration d’Azure AD. Vous pouvez créer, supprimer et inviter des utilisateurs. Vous pouvez ajouter et supprimer des membres dans un groupe.

Vous pouvez également télécharger des listes de ressources Azure AD à partir du portail d’administration d’Azure AD. Vous pouvez télécharger les listes des utilisateurs et des groupes figurant dans l’annuaire, ainsi que la liste des membres d’un groupe particulier.

Pour plus d’informations, consultez les articles suivants :

- [Créer des utilisateurs](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add) ou [Inviter des utilisateurs](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [Supprimer des utilisateurs](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete) ou [Restaurer des utilisateurs supprimés](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [Télécharger la liste des utilisateurs](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download) ou [Télécharger la liste des groupes](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- [Ajouter (importer) des membres](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [Supprimer des membres](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members) ou [Télécharger la liste des membres](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members) d’un groupe

---

### <a name="my-staff-delegated-user-management"></a>Gestion des utilisateurs délégués de Mon personnel

**Type :** Nouvelle fonctionnalité

**Catégorie de service :** User Management

**Fonctionnalité de produit :**

Le portail Mon personnel permet aux responsables sur le terrain, tels que des responsables de magasin, de s’assurer que les membres de leur personnel sont en mesure d’accéder à leurs comptes Azure AD. Au lieu de s’appuyer sur un support technique central, les organisations peuvent déléguer à un responsable sur le terrain des tâches courantes telles que la réinitialisation de mot de passe ou la modification de numéros de téléphone. Grâce au portail Mon personnel, un utilisateur qui ne parvient plus à accéder à son compte peut récupérer l’accès en quelques clics, sans intervention du support technique ou du personnel informatique. Pour plus d’informations, consultez [Gestion des utilisateurs avec Mon personnel (préversion)](https://aka.ms/MyStaffAdminDocs) et [Déléguer la gestion des utilisateurs avec Mon personnel (préversion)](https://aka.ms/MyStaffUserDocs).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Expérience d’utilisateur final mise à niveau dans les révisions d’accès

**Type :** Fonctionnalité modifiée

**Catégorie de service :** Révisions d’accès

**Fonctionnalité de produit :** Gouvernance des identités

Nous avons mis à jour l’expérience du réviseur pour les révisions d’accès Azure AD dans le portail Mes applications. Depuis la fin du mois d’avril, les réviseurs connectés à l’interface réviseur des révisions d’accès Azure AD voient s’afficher une bannière qui leur permet d’essayer l’expérience mise à jour dans le portail Mon accès. Notez que l’expérience des révisions d’accès mise à jour offre les mêmes fonctionnalités que l’expérience antérieure, mais avec une interface utilisateur améliorée s’ajoutant aux nouvelles fonctionnalités pour permettre aux utilisateurs d’être productifs. [Vous pouvez en savoir plus sur l’expérience mise à jour ici](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review). Cette préversion publique sera disponible jusqu’à la fin du mois de juillet 2020. À partir de cette date, les réviseurs qui n’auront pas adopté l’expérience en préversion seront automatiquement dirigés vers le portail Mon accès pour effectuer des révisions d’accès. Si vous souhaitez que vos réviseurs basculent définitivement vers l’expérience en préversion dans le portail Mon accès dès maintenant, [veuillez introduire une demande ici](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Les applications d’approvisionnement des utilisateurs entrants et d’écriture différée de Workday prennent désormais en charge les dernières versions de l’API Workday Web Services (WWS).

**Type :** Fonctionnalité modifiée

**Catégorie de service :** Provisionnement d’applications

**Fonctionnalité de produit :** 

Sur la base des commentaires des clients, nous avons mis à jour les applications d’approvisionnement des utilisateurs entrants et d’écriture différée de Workday dans la galerie d’applications d’entreprise pour prendre en charge les dernières versions de l’API Workday Web Services (WWS). Suite à cette modification, les clients peuvent spécifier la version de l’API WWS qu’ils souhaitent utiliser dans la chaîne de connexion. Les clients peuvent ainsi récupérer davantage d’attributs de RH disponibles dans les versions de Workday. L’application d’écriture différée de Workday utilise désormais le service web recommandé Change_Work_Contact_Info de Workday pour surmonter les limitations de Maintain_Contact_Info.

Si aucune version n’est spécifiée dans la chaîne de connexion, par défaut, les applications d’approvisionnement entrant de Workday continueront d’utiliser WWS v 21.1. Pour basculer vers les dernières API Workday pour l’approvisionnement d’utilisateurs entrants, les clients doivent mettre à jour la chaîne de connexion comme indiqué [dans le didacticiel](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles), et mettre à jour les XPATH utilisés pour les attributs Workday, comme décrit dans le [Guide de référence sur les attributs Workday](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30). 

Pour utiliser la nouvelle API pour l’écriture différée, aucune modification n’est requise dans l’application d’approvisionnement d’écriture différée de Workday. Du côté de Workday, assurez-vous que le compte d’utilisateur de système d’intégration (ISU) de Workday dispose des autorisations nécessaires pour appeler le processus métier Change_Work_Contact comme indiqué dans la section [Configurer des autorisations de stratégie de sécurité de processus métier](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions) du didacticiel. 

Nous avons mis à jour notre [didacticiel](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) pour refléter la prise en charge de la nouvelle version de l’API.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Utilisateurs titulaires du rôle d’accès par défaut désormais dans l’étendue de l’approvisionnement

**Type :** Fonctionnalité modifiée

**Catégorie de service :** Provisionnement d’applications

**Fonctionnalité de produit :** Gestion du cycle de vie des identités

Historiquement, les utilisateurs pourvus du rôle d’accès par défaut étaient hors de portée du provisionnement. Certains clients souhaitaient que les utilisateurs disposant de ce rôle figurent dans l’étendue du provisionnement. Depuis le 16 avril 2020, toutes les nouvelles configurations d’approvisionnement permettent d’approvisionner les utilisateurs titulaires du rôle d’accès par défaut. Progressivement, nous allons modifier le comportement des configurations d’approvisionnement existantes pour prendre en charge l’approvisionnement d’utilisateurs avec ce rôle. [En savoir plus.](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>IU d’approvisionnement mise à jour

**Type :** Fonctionnalité modifiée

**Catégorie de service :** Provisionnement d’applications

**Fonctionnalité de produit :** Gestion du cycle de vie des identités

Nous avons actualisé notre expérience d’approvisionnement pour créer une vue de gestion plus ciblée. Lorsque vous accédez au panneau d’approvisionnement d’une application d’entreprise déjà configurée, vous pouvez facilement surveiller la progression de l’approvisionnement et gérer des actions telles que le démarrage, l’arrêt et le redémarrage de l’approvisionnement. [En savoir plus.](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Validation de règle de groupe dynamique désormais disponible en préversion publique

**Type :** Fonctionnalité modifiée

**Catégorie de service :** Gestion des groupes

**Fonctionnalité de produit :** Collaboration

Azure Active Directory (Azure AD) offre désormais la possibilité de valider les règles de groupe dynamique. Sous l’onglet **Valider les règles**, vous pouvez valider votre règle dynamique par rapport à des exemples de membres du groupe pour vérifier que la règle fonctionne comme prévu. Lors de la création ou de la mise à jour des règles de groupe dynamiques, les administrateurs veulent savoir si un utilisateur ou un appareil sera membre du groupe. Cela permet d’évaluer si un utilisateur ou un appareil répond aux critères de la règle et facilite la résolution des problèmes quand l’appartenance n’est pas attendue.

Pour plus d’informations, consultez [Valider une règle d’appartenance à un groupe dynamique (préversion)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Score d’identité sécurisée – Mises à jour des actions d’amélioration des paramètres de sécurité par défaut et de l’authentification multifacteur

**Type :** Fonctionnalité modifiée

**Catégorie de service :** N/A

**Fonctionnalité de produit :** Protection et sécurité des identités

**Prise en charge des paramètres de sécurité par défaut pour les actions d’amélioration d’Azure AD :** Le service Degré de sécurisation Microsoft mettra à jour les actions d’amélioration pour prendre en charge les [paramètres de sécurité par défaut dans Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), facilitant la protection de votre organisation à l’aide de paramètres de sécurité préconfigurés pour contrer des attaques courantes. Cela aura une incidence sur les actions d’amélioration suivantes :

- Vérifier que tous les utilisateurs peuvent utiliser une authentification multifacteur pour un accès sécurisé
- Exiger l'authentification multifacteur pour les rôles administratifs
- Activer une stratégie pour bloquer l’authentification héritée
 
**Mises à jour de l’action d’amélioration de l’authentification multifacteur :** Afin de refléter la nécessité pour les entreprises de garantir une sécurité optimale tout en appliquant des stratégies adaptées à leur activité, le service Degré de sécurisation Microsoft a supprimé trois actions d’amélioration centrées sur l’authentification multifacteur, et en a ajouté deux.

Actions d’amélioration supprimées :

- Inscrire tous les utilisateurs pour l’authentification multifacteur
- Exiger MFA pour tous les utilisateurs
- Exiger l'authentification multifacteur pour les rôles privilégiés Azure AD

Actions d’amélioration ajoutées :

- Vérifier que tous les utilisateurs peuvent utiliser une authentification multifacteur pour un accès sécurisé
- Exiger l'authentification multifacteur pour les rôles administratifs

Ces nouvelles actions d’amélioration nécessitent l’inscription de vos utilisateurs ou administrateurs pour l’authentification multifacteur dans votre annuaire, ainsi que la mise en place d’un ensemble approprié de stratégies adaptées aux besoins de votre organisation. L'objectif principal est de disposer d'une certaine flexibilité tout en permettant à l'ensemble de vos utilisateurs et administrateurs de s'authentifier à l'aide de plusieurs facteurs ou d'invites de vérification d'identité basée sur les risques. Cela peut prendre la forme de l’instauration de plusieurs stratégies qui appliquent des décisions étendues ou de la définition de paramètres de sécurité par défaut (à compter du 16 mars) qui permettent à Microsoft de décider quand exiger l’authentification multifacteur des utilisateurs. [En savoir plus sur les nouveautés du service Degré de sécurisation Microsoft](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---

## <a name="march-2020"></a>Mars 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Comptes Azure Active Directory non gérés dans la mise à jour B2B pour mars 2021

**Type :** Modification planifiée  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
**À partir du 31 mars 2021**, Microsoft ne prendra plus en charge l’échange d’invitations en créant des locataires et des comptes Azure Active Directory (Azure AD) non managés pour les scénarios de collaboration B2B. Dans cette optique, nous vous encourageons à choisir l’[authentification au moyen d’un code secret à usage unique envoyé par e-mail](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode).

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Les utilisateurs dotés du rôle d’accès par défaut seront concernés par le provisionnement.

**Type :** Modification planifiée  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Historiquement, les utilisateurs pourvus du rôle d’accès par défaut étaient hors de portée du provisionnement. Certains clients souhaitaient que les utilisateurs disposant de ce rôle figurent dans l’étendue du provisionnement. Nous travaillons au déploiement d’une modification, pour que toute nouvelle configuration de provisionnement autorise les utilisateurs dotés du rôle d’accès par défaut à être provisionnés. Progressivement, nous allons modifier le comportement des configurations de provisionnement existantes pour prendre en charge le provisionnement des utilisateurs avec ce rôle. Aucune action du client n’est nécessaire. Nous allons poster une mise à jour dans notre [documentation](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) dès que cette modification sera en place.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>La solution Azure AD B2B Collaboration sera disponible dans Microsoft Azure, gérée par les locataires 21Vianet (Azure Chine 21Vianet).

**Type :** Modification planifiée  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Les fonctionnalités d’Azure AD B2B Collaboration seront rendues disponibles dans Microsoft Azure, gérées par les locataires 21Vianet (Azure Chine 21Vianet), ce qui permettra aux utilisateurs d’un locataire Azure Chine 21Vianet de collaborer de façon transparente avec les utilisateurs d’autres locataires Azure Chine 21Vianet. [En savoir plus sur Azure AD B2B Collaboration](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Redéfinition de l’e-mail d’invitation à Azure AD B2B Collaboration

**Type :** Modification planifiée  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Les [e-mails](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) qui sont envoyés par le service d’invitation d’Azure AD B2B Collaboration pour convier les utilisateurs à rejoindre l’annuaire seront remaniés afin de rendre plus claires les informations d’invitation et les étapes de l’utilisateur à suivre.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Les modifications apportées à la stratégie HomeRealmDiscovery s’afficheront dans les journaux d’audit

**Type :** Résolution  
**Catégorie de service :** Audit  
**Fonctionnalité de produit :** Monitoring et création de rapports
 
Nous avons résolu un bogue dans lequel les modifications apportées à la stratégie [HomeRealmDiscovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) n’étaient pas incluses dans les journaux d’audit. Vous pouvez désormais voir quand et comment la stratégie a été modifiée, et par qui. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Mars 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En mars 2020, nous avons ajouté à la galerie d’applications les 51 applications suivantes qui prennent en charge la fédération : 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho One China](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [o](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial), [iPoint Service Provider](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), [contexxt.ai SPHERE](https://contexxt-sphere.com/login), [Wisdom By Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial), [Flare Digital Signage](https://spark-dev.pixelnebula.com/login), [s](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial), [SpectrumU](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial), [BizzContact](https://bizzcontact.app/), [Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial), [MarketSignShare](http://www.signshare.com/), [CrossKnowledge Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [Netvision Compas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial), [FCM HUB](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial), [RIB A/S Byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757), [GoLinks](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial), [Datadog](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial), [Zscaler B2B User Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial), [LIFT](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial), [Planview Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial), [WatchTeams](https://www.devfinition.com/), [Aster](https://demo.asterapp.io/login), [Skills Workflow](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial), [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx), [IP Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [InVision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial), [Pipedrive](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial), [Showcase Workshop](https://app.showcaseworkshop.com/), [Greenlight Integration Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial), [Greenlight Compliant Access Management](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [Grok Learning](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), [Miradore Online](https://login.online.miradore.com/), [Khoros Care](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial), [AskYourTeam](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial), [TruNarrative](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial), [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [Bizagi Studio for Digital Process Automation](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial), [insuiteX](https://www.insuite.jp/), [sybo](https://www.systexsoftware.com.tw/), [Britive](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial), [WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial), [E-days](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial), [Kollective SDN](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/), [Playvox](https://my.playvox.com/login), [Korn Ferry 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial), [Campus Café](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial), [Catchpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial), [Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B Collaboration disponible dans les locataires Azure Government

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Les fonctionnalités d’Azure AD B2B Collaboration sont désormais disponibles entre certains locataires Azure Government.  Pour déterminer si votre locataire est en mesure d’utiliser ces fonctionnalités, suivez les instructions fournies dans [Comment savoir si B2B Collaboration est disponible dans mon locataire Azure US Government ?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>L’intégration d’Azure Monitor pour les journaux Azure est à présent disponible dans Azure Government

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Signalement  
**Fonctionnalité de produit :** Monitoring et création de rapports
 
L’intégration d’Azure Monitor aux journaux Azure AD est à présent disponible dans Azure Government. Vous pouvez router les journaux Azure AD (journaux d’audit et de connexion) sur un compte de stockage, Event Hub et Log Analytics. Consultez la [documentation détaillée](https://aka.ms/aadlogsinamd) ainsi que les [plans de déploiement pour la création de rapports et la supervision](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) des scénarios Azure AD.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Actualisation d’Identity Protection dans Azure Government

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Nous sommes ravis d’annoncer que nous venons de déployer l’expérience actualisée d’[Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs)  dans le portail [Microsoft Azure Government](https://portal.azure.us/). Pour plus d’informations, consultez notre [billet sur le blog d’annonces](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Reprise d’activité après sinistre : Téléchargement et stockage de votre configuration de provisionnement

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Le service de provisionnement Azure AD fournit un ensemble complet de fonctionnalités de configuration. Les clients doivent être en mesure d’enregistrer leur configuration afin de pouvoir s’y référer ultérieurement, ou de revenir à une version correcte connue. Nous avons ajouté la possibilité de télécharger votre configuration de provisionnement sous la forme d’un fichier JSON que vous chargez ensuite lorsque vous en avez besoin. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration)

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (réinitialisation de mot de passe en libre-service) exige désormais deux portes pour les administrateurs dans Microsoft Azure géré par 21Vianet (Azure Chine 21Vianet) 

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Réinitialisation du mot de passe en libre-service  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Auparavant, dans Microsoft Azure géré par 21Vianet (Azure Chine 21Vianet), les administrateurs qui utilisaient la réinitialisation de mot de passe en libre-service (SSPR) pour réinitialiser leurs mots de passe n’avaient besoin que d’une seule « porte » (défi) pour prouver leur identité. En général, dans les clouds publics et les autres clouds nationaux, les administrateurs doivent utiliser deux portes pour prouver leur identité lors de l’utilisation de SSPR. Pour autant, comme nous ne prenions en charge ni les appels téléphoniques ni les SMS dans Azure Chine 21Vianet, nous avions autorisé la réinitialisation de mot de passe à une porte par les administrateurs.

Nous sommes en cours de création d’une parité des fonctionnalités SSPR entre Azure Chine 21Vianet et le cloud public. Désormais, les administrateurs devront utiliser deux portes lors de l’utilisation de SSPR. Les SMS, appels téléphoniques ainsi que les codes et notifications de l’application Authenticator seront pris en charge. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences)

---

### <a name="password-length-is-limited-to-256-characters"></a>La longueur du mot de passe est limitée à 256 caractères

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Pour garantir la fiabilité du service Azure AD, la longueur des mots de passe utilisateur est désormais limitée à 256 caractères. Les utilisateurs dotés de mots de passe plus longs seront invités à modifier leur mot de passe lors d’une prochaine connexion, soit en contactant leur administrateur, soit en utilisant la fonctionnalité de réinitialisation de mot de passe en libre-service.

Cette modification a été activée le 13 mars 2020, à 10 H 00 PST (18:00 UTC), et l’erreur est AADSTS 50052, InvalidPasswordExceedsMaxLength. Pour plus d’informations, consultez l’[avis de changement cassant](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters).

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Les journaux de connexion Azure AD sont désormais disponibles pour tous les locataires gratuits via le portail Azure

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Signalement  
**Fonctionnalité de produit :** Monitoring et création de rapports
 
À partir de maintenant, les clients qui disposent de locataires gratuits peuvent accéder aux [journaux de connexion Azure AD depuis le portail Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins), sur une durée de 7 jours. Auparavant, les journaux de connexion étaient uniquement disponibles pour les clients détenteurs de licences Azure Active Directory Premium. Avec cette modification, tous les locataires peuvent désormais accéder à ces journaux par l’intermédiaire du portail.

> [!NOTE]
> Les clients ont toujours besoin d’une licence Premium (Azure Active Directory Premium P1 ou P2) pour accéder aux journaux de connexion par le biais de l’API Microsoft Graph et d’Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Dépréciation de l’option Groupes à l’échelle de l’annuaire dans les Paramètres généraux des groupes sur le portail Azure

**Type :** Déprécié  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

Pour fournir aux clients un moyen plus souple de créer des groupes au niveau de l’annuaire, tout en répondant mieux à leurs besoins, nous avons remplacé l’option **Groupes à l’échelle de l’annuaire** dans **Groupes** > **Paramètres généraux** du portail Azure par un lien vers la [documentation du groupe dynamique](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership). Nous avons amélioré notre documentation pour inclure des instructions supplémentaires permettant aux administrateurs de créer des groupes de tous les utilisateurs qui incluent ou excluent des utilisateurs invités.

---

## <a name="february-2020"></a>Février 2020

### <a name="upcoming-changes-to-custom-controls"></a>Modifications à venir des contrôles personnalisés

**Type :** Modification planifiée  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Nous envisageons de remplacer la préversion actuelle des contrôles personnalisés par une approche qui permet aux fonctionnalités d’authentification fournies par le partenaire de fonctionner de manière transparente avec les expériences de l’utilisateur final et de l’administrateur Azure Active Directory. Aujourd’hui, les solutions MFA partenaires font face aux limitations suivantes : elles fonctionnent uniquement après l’entrée d’un mot de passe. Elles ne servent pas d’authentification MFA pour l’authentification avancée dans d’autres scénarios clés, et ne s’intègrent pas aux fonctions de gestion des informations d’identification de l’utilisateur final ou d’administration. La nouvelle implémentation autorisera les facteurs d’authentification fournis par les partenaires à fonctionner avec les facteurs intégrés pour les principaux scénarios, notamment l’inscription, l’utilisation, les revendications MFA, l’authentification avancée, la création de rapports et la journalisation. 

Les contrôles personnalisés continueront d’être pris en charge dans la préversion en même temps que la nouvelle conception, jusqu’à ce que la disponibilité générale soit atteinte. À ce stade, nous donnerons à nos clients le temps de migrer vers la nouvelle conception. En raison des limitations de l’approche actuelle, nous n’intégrerons pas de nouveaux fournisseurs tant que la nouvelle conception ne sera pas disponible. Travaillant en étroite collaboration avec les clients et les fournisseurs, nous communiquerons la chronologie des opérations à mesure que nous approcherons de la finalisation. [Plus d’informations](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#)

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Score d'identité sécurisée - Mises à jour des actions d'amélioration de l'authentification multifacteur (MFA)

**Type :** Modification planifiée  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Afin de refléter la nécessité pour les entreprises de garantir une sécurité optimale tout en appliquant des stratégies adaptées à leur activité, le service Degré de sécurisation Microsoft supprime trois actions d'amélioration centrées sur l'authentification multifacteur (MFA) et en ajoute deux.

Les actions d'amélioration suivantes vont être supprimées :

- Inscrire tous les utilisateurs à l'authentification multifacteur
- Exiger MFA pour tous les utilisateurs
- Exiger l'authentification multifacteur pour les rôles privilégiés Azure AD

Les actions d'amélioration suivantes vont être ajoutées :

- Vérifier que tous les utilisateurs peuvent utiliser l'authentification multifacteur pour garantir un accès sécurisé
- Exiger l'authentification multifacteur pour les rôles administratifs

Ces nouvelles actions d'amélioration nécessiteront l'inscription de vos utilisateurs ou administrateurs à l'authentification multifacteur dans votre répertoire, ainsi que l'établissement d'un ensemble de stratégies adaptées aux besoins de votre organisation. L'objectif principal est de disposer d'une certaine flexibilité tout en permettant à l'ensemble de vos utilisateurs et administrateurs de s'authentifier à l'aide de plusieurs facteurs ou d'invites de vérification d'identité basée sur les risques. Cela peut prendre la forme d'une configuration par défaut de la sécurité qui permet à Microsoft de décider quand imposer l'authentification multifacteur aux utilisateurs, ou de l'établissement de différentes stratégies qui appliquent des décisions à large portée. Dans le cadre de ces mises à jour des actions d'amélioration, les stratégies de protection de base ne seront plus incluses dans les calculs de scoring. [En savoir plus sur les nouveautés du service Degré de sécurisation Microsoft](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Sélection des références SKU Azure AD Domain Services

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Services de domaine Azure AD  
**Fonctionnalité de produit :** Services de domaine Azure AD
 
Certains clients d’Azure AD Domain Services souhaitaient bénéficier d’une flexibilité accrue en matière de sélection des niveaux de performances de leurs instances. Le 1er février 2020, nous sommes passés d'un modèle dynamique (dans lequel Azure AD détermine le niveau de performance et le niveau tarifaire en fonction du nombre d'objets) à un modèle d'auto-sélection. Les clients peuvent désormais choisir un niveau de performance adapté à leur environnement. Cette modification nous permet également de proposer de nouveaux scénarios, comme les forêts de ressources, et des fonctionnalités Premium, comme les sauvegardes quotidiennes. Le nombre d’objets est désormais illimité pour toutes les références SKU, mais nous continuerons à proposer des suggestions de nombre d’objets pour chaque niveau.

**Aucune action immédiate du client n'est nécessaire.** Pour les clients existants, le niveau dynamique qui était utilisé le 1er février 2020 détermine le nouveau niveau par défaut. Ce changement n'a aucun impact sur les prix ou sur les performances. À l'avenir, les clients Azure AD DS devront évaluer les exigences de performance en fonction de l'évolution de la taille de leur répertoire et des caractéristiques de leur charge de travail. Le basculement entre les niveaux de service s'effectuera toujours sans temps d'arrêt, et nous cesserons de déplacer automatiquement les clients vers de nouveaux niveaux en fonction de la croissance de leur répertoire. En outre, il n'y aura aucune augmentation de prix, et la nouvelle tarification sera conforme à notre modèle de facturation actuel. Pour plus d'informations, consultez la [documentation relative aux références SKU Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) et la [page consacrée à la tarification d'Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nouvelles applications fédérées disponibles dans la galerie Azure AD App - Février 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En février 2020, nous avons ajouté à la galerie d’applications les 31 nouvelles applications suivantes qui prennent en charge la fédération : 

[IamIP Patent Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [NS1 SSO For Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), [In Case of Crisis - Online Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC Cloud Design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [Beekeeper Azure AD Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [Korn Ferry Assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada Command](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB Navigate](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [New Relic (Limited Release)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), [Ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [Template Chooser for Teams](https://links.officeatwork.com/templatechooser-download-teams), [Beesy](https://www.beesy.me/index.php/site/login), [Health Support System](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [MURAL](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink for Teachers and Schools](https://www.thinglink.com/), [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [Reprints Desk - Article Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nouveaux connecteurs d'approvisionnement dans la Galerie d'applications Azure AD - Février 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, voir [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Prise en charge d'Azure AD pour les clés de sécurité FIDO2 dans les environnements hybrides

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Nous avons le plaisir de vous annoncer le lancement de la préversion publique de la prise en charge d'Azure AD pour les clés de sécurité FIDO2 dans les environnements hybrides. Les utilisateurs peuvent désormais utiliser des clés de sécurité FIDO2 pour se connecter à leurs appareils Windows 10 joints à Azure AD Hybride et se connecter de manière transparente à leurs ressources locales et cloud. La prise en charge des environnements hybrides était la fonctionnalité la plus demandée par nos clients sans mot de passe depuis le lancement de la préversion publique de la prise en charge de FIDO2 dans les appareils joints à Azure AD. L'authentification sans mot de passe à l'aide de technologies avancées telles que la biométrie et le chiffrement par clé publique/privée offre commodité et facilité d'utilisation tout en étant sécurisée. Grâce à cette préversion publique, vous pouvez désormais avoir recours à un mode d'authentification moderne, comme les clés de sécurité FIDO2, pour accéder aux ressources Active Directory traditionnelles. Pour plus d'informations, accédez à [Authentification unique auprès de ressources locales](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises). 

Commencez par visiter [Activer les clés de sécurité FIDO2 pour votre locataire](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) pour obtenir des instructions pas à pas. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>La nouvelle expérience Mon compte est désormais généralement disponible

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Mon profil/compte  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 
Mon compte, le guichet unique pour tous les besoins de gestion des comptes d'utilisateurs finaux, est désormais généralement disponible. Les utilisateurs finaux peuvent accéder à ce nouveau site via une URL, ou à partir de l'en-tête de la nouvelle expérience Mes applications. Pour en savoir plus sur toutes les fonctionnalités en libre-service offertes par la nouvelle expérience, consultez la [Présentation du portail Mon compte](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Remplacement de l'URL du site Mon compte par myaccount.microsoft.com

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Mon profil/compte  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 
Le mois prochain, l'URL de la nouvelle expérience d'utilisateur final Mon compte sera remplacée par `https://myaccount.microsoft.com`. Pour en savoir plus sur cette expérience et sur les différentes fonctionnalités en libre-service qu'elle offre aux utilisateurs finaux, consultez [Aide du portail Mon compte](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

