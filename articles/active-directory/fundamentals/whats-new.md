---
title: Nouveautés Notes de publication - Azure Active Directory | Microsoft Docs
description: Découvrez les nouveautés d’Azure Active Directory, notamment les dernières notes de publication, les problèmes connus, les corrections de bogues, les fonctionnalités dépréciées et les modifications à venir.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 491908b651cd2b875fcfeed4c55d34f0e8c5cfa1
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802507"
---
# <a name="whats-new-in-azure-active-directory"></a>Nouveautés d’Azure Active Directory

>Soyez notifié de la disponibilité des mises à jour sur cette page en faisant un copier-coller de cette URL : `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` dans votre lecteur de flux ![icône RSS](./media/whats-new/feed-icon-16x16.png).

Azure AD bénéficie d’améliorations en continu. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues
- Fonctionnalités dépréciées
- Modifications planifiées

Cette page est mise à jour tous les mois. Donc, consultez-la régulièrement. Si vous recherchez des éléments antérieurs aux six derniers mois, vous les trouverez dans l’[Archive des nouveautés d’Azure Active Directory](whats-new-archive.md).

---

## <a name="march-2020"></a>Mars 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Comptes Azure Active Directory non gérés dans la mise à jour B2B pour mars 2021

**Type :** Modification planifiée  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
**À partir du 31 mars 2021**, Microsoft ne prendra plus en charge l’échange d’invitations en créant des locataires et des comptes Azure Active Directory (Azure AD) non managés pour les scénarios de collaboration B2B. Dans cette optique, nous vous encourageons à choisir l’[authentification au moyen d’un code secret à usage unique envoyé par e-mail](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode).

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

---
 
## <a name="january-2020"></a>Janvier 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Le nouveau portail Mes applications est désormais généralement disponible

**Type :** Modification planifiée  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 
Mettez à niveau votre organisation vers le nouveau portail Mes applications qui est désormais généralement disponible. Pour plus d’informations sur le nouveau portail et les regroupements, voir [Créer des collections sur le portail Mes applications](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Les espaces de travail dans Azure AD sont désormais appelés collections

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** My Apps   
**Fonctionnalité de produit :** Expériences d’utilisateur final
 
Les espaces de travail, les filtres que les administrateurs peuvent configurer pour organiser leurs applications utilisateurs, sont désormais appelés collections. Pour plus d’informations sur la façon de les configurer, voir [Créer des collections sur le portail Mes applications](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Inscription et connexion par téléphone à Azure AD B2C à l’aide d’une stratégie personnalisée (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C
 
Avec l’inscription et la connexion par téléphone, les développeurs et les entreprises peuvent autoriser leurs clients à s’inscrire et à se connecter à l’aide d’un mot de passe à usage unique envoyé au numéro de téléphone de l’utilisateur via SMS. Cette fonctionnalité permet également au client de modifier son numéro de téléphone s’il perd l’accès à son téléphone. Grâce à la puissance des stratégies personnalisées, l’inscription et la connexion par téléphone permettent aux développeurs et aux entreprises de mettre leur marque en avant en personnalisant des pages. Découvrez comment [Configurer l’inscription et la connexion par téléphone avec des stratégies personnalisées dans Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nouveaux connecteurs d’approvisionnement dans la Galerie d’applications Azure AD – Janvier 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, voir [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD – Janvier 2020

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En janvier 2020, nous avons ajouté à la galerie d’applications les 33 applications suivantes qui prennent en charge la fédération : 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot](https://leavebot.io/#home), [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [SmartWork](https://www.intumit.com/english/SmartWork.html), [Dotcom-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN - Azure AD SSO Gateway pour Oracle E-Business Suite - EBS, PeopleSoft et JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [Hosted MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB for Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient Conduct Manager Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login), [PingFlow Authentication](https://app-staging.pingview.io/), [ PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai](https://app.sandwai.com/), [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Deux nouvelles détections de protection des identités

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Nous avons ajouté deux nouveaux types de détections liés à l’authentification à la protection des données : Règles suspectes de manipulation de boîte de réception et Voyage impossible. Ces détections hors connexion sont découvertes par Microsoft Cloud App Security (MCAS) et influencent l’utilisateur et le risque de connexion dans Identity Protection. Pour plus d’informations sur ces détections, voir nos [types de risques de connexion](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Changement cassant : Des fragments d’URI ne sont pas véhiculés via la redirection de la connexion

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Depuis le 8 février 2020, quand une demande est envoyée à login.microsoftonline.com pour connecter un utilisateur, le service ajoute un fragment vide à la demande.  Cela évite une classe d’attaques de redirection en veillant à ce que le navigateur efface tout fragment existant dans la demande. Aucune application ne doit dépendre de ce comportement. Pour plus d’informations, voir [Changements cassants](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) dans la documentation de la plateforme Microsoft Identity.

---

## <a name="december-2019"></a>Décembre 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Intégrer l’approvisionnement de SAP SuccessFactors dans Azure AD et dans un AD local (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités

Vous pouvez désormais intégrer SAP SuccessFactors en tant que source d’identité faisant autorité dans Azure AD. Cette intégration vous permet d’automatiser le cycle de vie des identités de bout en bout, y compris l’utilisation d’événements basés sur les RH, tels que des embauches ou des résiliations, pour contrôler l’approvisionnement de comptes Azure AD.

Pour plus d’informations sur la configuration de l’approvisionnement entrant de SAP SuccessFactors pour Azure AD, voir le didacticiel [Configurer l’approvisionnement automatique de SAP SuccessFactors](https://aka.ms/SAPSuccessFactorsInboundTutorial).

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Prise en charge des e-mails personnalisés dans Azure AD B2C (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

Vous pouvez désormais utiliser Azure AD B2C pour créer des e-mails personnalisés lorsque vos utilisateurs s’inscrivent pour utiliser vos applications. En utilisant DisplayControls (actuellement en préversion) et un fournisseur de courrier tiers (tel que [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/) ou une API REST personnalisée), vous pouvez utiliser vos propres modèle d’e-mail, adresse **D** et texte d’objet, ainsi que des paramètres de localisation du support et de mot de passe à usage unique personnalisé.

Pour plus d’informations, voir [Vérification d’e-mail personnalisée dans Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Remplacement des stratégies de base par des paramètres de sécurité par défaut

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Protection et sécurité des identités

Dans le cadre d’un modèle sécurisé par défaut pour l’authentification, nous supprimons de tous les locataires les stratégies de protection de base existantes. Cette suppression devrait être terminée fin février. Ces stratégies de protection de base sont remplacées par des paramètres de sécurité par défaut. Si vous avez utilisé des stratégies de protection de base, vous devez planifier un passage à la nouvelle stratégie des paramètres de sécurité par défaut ou à l’accès conditionnel. Si vous n’avez pas utilisé ces stratégies, vous n’avez aucune action à effectuer.

Pour plus d’informations sur les nouveaux paramètres de sécurité par défaut, voir [Présentation des paramètres de sécurité par défaut](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults). Pour plus d’informations sur les stratégies d’accès conditionnel, voir [Stratégies d’accès conditionnel courantes](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>Novembre 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Prise en charge de l’attribut SameSite et de Chrome 80

**Type :** Modification planifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur

Dans le cadre d’un modèle sécurisé par défaut pour les cookies, le navigateur Chrome 80 change la façon dont il traite les cookies sans l’attribut `SameSite`. Tout cookie qui ne spécifie pas l’attribut `SameSite` sera traité comme s’il était défini sur `SameSite=Lax`, ce qui entraînera un blocage par Chrome de certains scénarios de partage de cookies entre domaines dont votre application peut dépendre. Pour conserver l’ancien comportement de Chrome, vous pouvez utiliser l’attribut `SameSite=None` et ajouter un attribut `Secure` supplémentaire de sorte que des cookies intersites ne soient accessibles que via des connexions HTTPs. Chrome est programmé pour accomplir ce changement au plus tard le 4 février 2020.

Nous recommandons à tous nos développeurs de tester leurs applications en suivant ces conseils :

- Définissez la valeur par défaut du paramètre **Utiliser un cookie sécurisé** sur **Oui**.

- Définissez la valeur par défaut de l’attribut **SameSite** sur **Aucun**.

- Ajoutez un attribut `SameSite` supplémentaire de **Sécurisé**.

Pour plus d’informations, voir [Modifications de cookie SameSite à venir dans ASP.NET et ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) et [Perturbation potentielle des sites web client et des produits et services Microsoft dans la version 79 et les versions ultérieures de Chrome](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Nouveau correctif logiciel pour Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Type :** Résolution  
**Catégorie de service :** Microsoft Identity Manager  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités

Un correctif logiciel cumulatif (build 4.6.34.0) est disponible pour Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Ce correctif cumulatif résout des problèmes et ajoute des améliorations décrites dans la section « Problèmes résolus et améliorations ajoutées dans cette mise à jour ».

Pour plus d’informations et pour télécharger le correctif logiciel, voir [Disponibilité d’un correctif cumulatif Microsoft Identity Manager 2016 Service Pack 2 (Build 4.6.34.0)](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Nouveau rapport activité d’application AD FS pour faciliter la migration d’applications vers Azure AD (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** SSO

Utilisez le nouveau rapport d’activité d’application des services de fédération Active Directory (AD FS) dans le Portail Azure pour identifier vos applications pouvant être migrées vers Azure AD. Il évalue la compatibilité de toutes les applications AD FS avec Azure AD, recherche tout problème et fournit des instructions sur la préparation d’applications individuelles pour la migration.

Pour plus d’informations, voir [Utiliser le rapport d’activité des applications AD FS (préversion) pour migrer des applications vers Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nouveau workflow permettant aux utilisateurs de demander le consentement de l’administrateur (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Contrôle d’accès

Le nouveau workflow de consentement de l’administrateur offre aux administrateurs une méthode pour accorder l’accès aux applications qui requièrent l’approbation de l’administrateur. Si un utilisateur tente d’accéder à une application mais n’est pas en mesure de donner son consentement, il peut désormais envoyer une demande d’approbation de l’administrateur. La demande est envoyée par courrier électronique et placée dans une file d’attente accessible à partir du portail Azure à tous les administrateurs désignés comme réviseurs. Une fois qu’un réviseur a effectué une action sur une demande en attente, les utilisateurs à l’origine de la demande sont avertis de l’action.

Pour plus d’informations, voir [Configurer le workflow du consentement de l’administrateur (préversion)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nouvelle expérience de configuration de jeton d’inscription Azure AD App pour la gestion de revendications facultatives (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Expérience de développement

Le nouveau panneau de **configuration de jeton d’inscription Azure AD App** sur le portail Azure présente désormais aux développeurs d’applications une liste dynamique de revendications facultatives pour leurs applications. Cette nouvelle expérience permet de simplifier les migrations d’applications Azure AD et de minimiser les erreurs de configuration de revendications facultatives.

Pour plus d’informations, voir [Fournir des revendications facultatives à votre application Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nouveau workflow d’approbation en deux étapes dans la gestion des droits d’utilisation Azure AD (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Gestion des droits d’utilisation

Nous avons introduit un nouveau workflow d’approbation en deux étapes qui vous permet de demander à deux approbateurs d’approuver la demande de package d’accès d’un utilisateur. Par exemple, vous pouvez le définir afin que le gestionnaire de l’utilisateur demandeur doive préalablement approuver, et vous pouvez également exiger qu’un propriétaire de ressource approuve. Si l’un des approbateurs n’approuve pas, l’accès n’est pas accordé.

Pour plus d’informations, voir [Modifier les paramètres de demande et d’approbation d’un package d’accès dans la gestion des droits d’utilisation Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Mises à jour de la page Mes applications avec les nouveaux espaces de travail (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Intégration tierce

Vous pouvez désormais personnaliser la manière dont les utilisateurs de votre organisation accèdent à l’expérience Mes applications actualisée et l’affichent. Cette nouvelle expérience comprend également la nouvelle fonctionnalité d’espaces de travail, qui permet aux utilisateurs de trouver et d’organiser plus facilement des applications.

Pour plus d’informations sur la nouvelle expérience Mes applications et la création d’espaces de travail, voir [Créer des espaces de travail sur le portail Mes applications](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Prise en charge d’ID social Google pour la collaboration dans Azure AD B2B (disponibilité générale)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** Authentification utilisateur

La nouvelle prise en charge de l’utilisation d’ID sociaux Google (comptes Gmail) dans Azure AD permet de simplifier la collaboration pour vos utilisateurs et partenaires. Vos partenaires n’ont plus besoin de créer et gérer un nouveau compte spécifique de Microsoft. Microsoft Teams prend désormais entièrement en charge les utilisateurs Google sur tous les clients, ainsi que sur les points de terminaison d’authentification communs et associés à des locataires.

Pour plus d’informations, voir [Ajouter Google comme fournisseur d’identité pour les utilisateurs invités B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Prise en charge mobile de Microsoft Edge pour l’accès conditionnel et l’authentification unique (disponibilité générale)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités

Azure AD pour Microsoft Edge sur iOS et Android prend désormais en charge l’authentification unique et l’accès conditionnel Azure AD :

- **Authentification unique (SSO) Microsoft Edge :** L’authentification unique est désormais disponible sur les clients natifs (tels que Microsoft Outlook et Microsoft Edge) pour toutes les applications connectées à Azure AD.

- **Accès conditionnel Microsoft Edge :** En vertu des stratégies d’accès conditionnel basées sur l’application, vos utilisateurs doivent utiliser des navigateurs protégés par Microsoft Intune, tels que Microsoft Edge.

Pour plus d’informations sur l’accès conditionnel et l’authentification unique avec Microsoft Edge, voir le billet de blog [Prise en charge mobile de Microsoft Edge pour l’accès conditionnel et l’authentification unique désormais généralement disponible](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179). Pour plus d’informations sur la configuration de vos applications clientes à l’aide d’un [accès conditionnel basé sur l’application](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) ou d’un [accès conditionnel basé sur l’appareil](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices), voir [Gérer l’accès web à l’aide d’un navigateur protégé par une stratégie Microsoft Intune](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Gestion des droits d’utilisation Azure AD (disponibilité générale)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Gestion des droits d’utilisation

La gestion des droits d’utilisation Azure AD est une nouvelle fonctionnalité de gouvernance des identités qui aide les organisations à gérer le cycle de vie des identités et des accès à grande échelle. Cette nouvelle fonctionnalité automatise les workflows de demande d’accès, les affectations d’accès, les révisions et les expirations pour des groupes, des applications et des sites SharePoint Online.

Avec la gestion des droits d’utilisation Azure AD, vous pouvez gérer plus efficacement l’accès des employés et des utilisateurs extérieurs à votre organisation qui doivent accéder à ces ressources.

Pour plus d’informations, voir [Qu’est-ce que la gestion des droits d’utilisation Azure AD ?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatiser l’approvisionnement de compte d’utilisateur pour ces applications SaaS nouvellement prises en charge

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce  

Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

[Service SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Priority Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, voir [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD – Novembre 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En novembre 2019, nous avons ajouté à la galerie d’applications les 21 nouvelles applications ci-après qui prennent en charge la fédération :

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access for Members (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Sign On (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [Foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets Idea & Innovation Management](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope User Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [Jisc Student Voter Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Galerie d’applications Azure AD nouvelles et améliorées

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** SSO

Nous avons mis à jour la galerie d’applications Azure AD pour faciliter la recherche d’applications pré-intégrées qui prennent en charge l’approvisionnement, OpenID Connect et SAML sur votre client Azure Active Directory.

Pour plus d’informations, vois [Ajouter une application à votre locataire Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Limite de longueur de définition de rôle d’application augmentée de 120 à 240 caractères

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** SSO

Nous avons entendu des clients dire que la longueur limitée à 120 caractères de la valeur de définition de rôle d’application dans certains services et applications était insuffisante. En réponse à cela, nous avons porté la longueur maximale de la valeur de définition de rôle à 240 caractères.

Pour plus d’informations sur l’utilisation des définitions de rôle spécifiques de l’application, voir [Ajouter des rôles d’application dans votre application et les recevoir dans le jeton](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---

## <a name="october-2019"></a>2 octobre 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Dépréciation de l’API identityRiskEvent pour la détection des risques par Azure AD Identity Protection  

**Type :** Modification planifiée  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Suite aux commentaires des développeurs, les abonnés à Azure AD Premium P2 peuvent désormais effectuer des requêtes complexes sur les données de détection des risques d’Azure AD Identity Protection à l’aide de la nouvelle API riskDetection pour Microsoft Graph. La version bêta de l’API [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) existante cessera de renvoyer des données vers le **10 janvier 2020**. Si votre organisation utilise l’API identityRiskEvent, vous devez passer à la nouvelle API riskDetection.

Pour plus d’informations sur la nouvelle API riskDetection, consultez la [Documentation de référence sur l’API de détection des risques](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Prise en charge du proxy d'application pour l'attribut SameSite et Chrome 80

**Type :** Modification planifiée  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès

À quelques semaines de la publication du navigateur Chrome 80, nous envisageons de mettre à jour la manière dont les cookies du proxy d’application traitent l’attribut **SameSite**. Avec Chrome 80, tous les cookies qui ne spécifieront pas l’attribut **SameSite** seront traités comme s'ils avaient été définis sur `SameSite=Lax`.

Pour éviter tout impact négatif lié à ce changement, nous mettons à jour l’accès au proxy d’application et les cookies de session en procédant comme suit :

- Définition de la valeur par défaut du paramètre **Utiliser un cookie sécurisé** sur **Oui**.

- Définition de la valeur par défaut de l’attribut **SameSite** sur **Aucun**.

    >[!NOTE]
    > Les cookies d’accès au proxy d’application ont toujours été transmis exclusivement via des canaux sécurisés. Ces modifications s’appliquent uniquement aux cookies de session.

Pour plus d’informations sur les paramètres de cookies du proxy d'application, consultez [Paramètres de cookies pour l’accès aux applications locales dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Les inscriptions d’applications (héritées) et la gestion des applications convergées à partir du portail d’inscription des applications (apps.dev.microsoft.com) ne seront plus disponibles.

**Type :** Modification planifiée  
**Catégorie de service :** N/A  
**Fonctionnalité de produit :** Expérience de développement

Dans un proche avenir, les utilisateurs disposant de comptes Azure AD ne seront plus en mesure d’inscrire et de gérer les applications convergées à l’aide du portail d’inscription des applications (apps.dev.microsoft.com), ni d’inscrire et de gérer les applications dans l'expérience des inscriptions d'applications (héritées) du portail Azure.

Pour en savoir plus sur la nouvelle expérience en matière d'inscriptions d’applications, consultez le [Guide de formation sur les inscriptions d’applications dans le portail Azure](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Les utilisateurs ne sont plus tenus de se réinscrire lors de la migration de l’authentification multifacteur par utilisateur à l’authentification multifacteur basée sur l’accès conditionnel.

**Type :** Résolution  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités

Nous avons résolu un problème connu qui obligeait les utilisateurs à se réinscrire s’ils étaient désactivés de l’authentification multifacteur par utilisateur, puis activés pour l’authentification multifacteur basée sur l’accès conditionnel.

Pour contraindre les utilisateurs à se réinscrire, vous pouvez sélectionner l’option **Réinscription à l'authentification multifacteur requise** à partir des méthodes d’authentification de l’utilisateur dans le portail Azure AD. Pour plus d’informations sur la migration des utilisateurs de l’authentification multifacteur (MFA) par utilisateur vers l’authentification MFA basée sur l’accès conditionnel, consultez [Convertir des utilisateurs de l’authentification multifacteur par utilisateur à l’authentification multifacteur basée sur l’accès conditionnel](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nouvelles fonctionnalités de transformation et d’envoi de revendications dans votre jeton SAML

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** SSO

Nous avons ajouté des fonctionnalités supplémentaires pour vous aider à personnaliser et à envoyer des revendications dans votre jeton SAML. Ces fonctionnalités sont les suivantes :

- Fonctions de transformation des revendications supplémentaires pour vous aider à modifier la valeur que vous envoyez dans la revendication.

- Possibilité d’appliquer plusieurs transformations à une même revendication.

- Possibilité de spécifier la source de la revendication en fonction du type d’utilisateur et du groupe auquel appartient l’utilisateur.

Pour plus d’informations sur ces nouvelles fonctionnalités, notamment sur leur utilisation, consultez [Personnaliser des revendications émises dans le jeton SAML pour les applications d’entreprise](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nouvelle page Mes connexions pour les utilisateurs finaux dans Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Monitoring et création de rapports

Nous avons ajouté une nouvelle page **Mes connexions**https://mysignins.microsoft.com) pour permettre aux utilisateurs de votre organisation d’afficher leur historique de connexion récent afin d'y détecter toute activité inhabituelle. Cette nouvelle page permet à vos utilisateurs de voir :

- Si quelqu’un tente de deviner leur mot de passe.

- Si un attaquant s’est connecté à leur compte et depuis quel emplacement.

- Les applications auxquelles l’attaquant a tenté d’accéder.

Pour plus d’informations, consultez le billet de blog [Les utilisateurs peuvent désormais consulter leur historique afin d'y détecter toute activité inhabituelle](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066).

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migration d'Azure AD Domain Services (Azure AD DS) des réseaux virtuels classiques aux réseaux virtuels Azure Resource Manager

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Services de domaine Azure AD  
**Fonctionnalité de produit :** Services de domaine Azure AD

Nous sommes porteurs d'une bonne nouvelle pour nos clients bloqués sur des réseaux virtuels classiques. Vous pouvez désormais effectuer une migration unique à partir d’un réseau virtuel classique vers un réseau virtuel existant Resource Manager. Après avoir migré vers le réseau virtuel Resource Manager, vous pourrez tirer parti de fonctionnalités supplémentaires et mises à niveau, telles que les stratégies de mot de passe affinées, les notifications par e-mail et les journaux d’audit.

Pour plus d'informations, consultez [Préversion - Migrer Azure Active Directory Domain Services depuis le modèle de réseau virtuel classique vers Resource Manager](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Mise à jour de la disposition de contrat de page Azure AD B2C

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

Nous avons apporté de nouvelles modifications à la version 1.2.0 du contrat de page Azure AD B2C. Dans cette version mise à jour, vous pouvez maintenant contrôler l’ordre de chargement des éléments, ce qui permet aussi de mettre fin au scintillement qui survient lorsque la feuille de style (CSS) est chargée.

Pour obtenir la liste complète des modifications apportées au contrat de page, consultez le [Journal des modifications de version](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Mise à jour de la page Mes applications avec de nouveaux espaces de travail (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Contrôle d’accès

Vous pouvez désormais personnaliser la manière dont les utilisateurs de votre organisation affichent et accèdent à la nouvelle expérience Mes applications, notamment l’utilisation de la nouvelle fonctionnalité Espaces de travail pour faciliter la recherche d’applications. La nouvelle fonctionnalité Espaces de travail fait office de filtre pour les applications auxquelles les utilisateurs de votre organisation ont déjà accès.

Pour plus d’informations sur le déploiement de la nouvelle expérience Mes applications et la création d’espaces de travail, voir [Créer des espaces de travail sur le portail Mes applications (préversion)](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Prise en charge du modèle de facturation basée sur les utilisateurs actifs mensuels (disponibilité générale)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

Azure AD B2C prend désormais en charge la facturation basée sur les utilisateurs actifs mensuels. Cette facturation repose sur le nombre d’utilisateurs uniques avec activité d’authentification pendant un mois civil. Les clients existants peuvent à tout moment opter pour cette nouvelle méthode de facturation.

Depuis le 1er novembre 2019, tous les nouveaux clients sont automatiquement facturés à l’aide de cette méthode. Cette méthode de facturation offre aux clients des avantages en termes de coût et de planification.

Pour plus d’informations, consultez [Mise à niveau vers le modèle de facturation basée sur les utilisateurs actifs mensuels](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD – Octobre 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En octobre 2019, nous avons ajouté à notre galerie d’applications les 35 nouvelles applications ci-dessous, qui prennent en charge la fédération :

[In Case of Crisis – Mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [Tact](https://tact.ai/assistant/), [OpusCapita Cash Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [Contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate – Europe](https://www.hirevue.com/), [HireVue Coordinate - USOnly](https://www.hirevue.com/), [HireVue Coordinate - US](https://www.hirevue.com/), [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [Mail Luck!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teamie](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigate IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), [Speaking Email for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome Care Navigation System](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Élément de menu de sécurité consolidé dans le portail Azure AD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Vous pouvez désormais accéder à toutes les fonctionnalités de sécurité Azure AD disponibles à partir du nouvel élément de menu **Sécurité** et de la barre **Rechercher** du portail Azure. En outre, la nouvelle page d’accueil **Sécurité**, appelée **Sécurité - Prise en main**, propose des liens vers notre documentation publique, des conseils de sécurité, ainsi que des guides de déploiement.

Le nouveau menu **Sécurité** comprend ce qui suit :

- Accès conditionnel
- Identity Protection
- Security Center
- Identity Secure Score
- Méthodes d’authentification
- MFA
- Rapports sur les risques - Utilisateurs à risque, connexions risquées, détections de risques
- Et bien plus...

Pour plus d’informations, consultez [Sécurité - Prise en main](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Stratégie d’expiration des groupes Office 365 améliorée avec renouvellement automatique

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités

La stratégie d’expiration des groupes Office 365 a été améliorée pour renouveler automatiquement les groupes utilisés de manière active par leurs membres. Les groupes sont renouvelés sur la base de l'activité des utilisateurs dans toutes les applications Office 365, y compris Outlook, SharePoint et Teams.

Cette amélioration permet de réduire les notifications d’expiration de votre groupe et de veiller à ce que les groupes actifs continuent d'être disponibles. Si vous disposez déjà d’une stratégie d’expiration active pour vos groupes Office 365, vous n’avez rien à faire pour activer cette nouvelle fonctionnalité.

Pour plus d'informations, consultez [Configurer la stratégie d’expiration pour les groupes Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Expérience de création Azure AD Domain Services (Azure AD DS) mise à jour

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Services de domaine Azure AD  
**Fonctionnalité de produit :** Services de domaine Azure AD

Nous avons mis à jour Azure AD Domain Services (Azure AD DS) pour y inclure une nouvelle expérience de création améliorée, qui vous permet de créer un domaine géré en seulement trois clics. En outre, vous pouvez désormais charger et déployer Azure AD DS à partir d’un modèle.

Pour plus d’informations, consultez [Didacticiel : Créer et configurer une instance Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---
