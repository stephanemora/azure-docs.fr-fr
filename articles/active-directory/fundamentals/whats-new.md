---
title: Nouveautés Notes de publication - Azure Active Directory | Microsoft Docs
description: Découvrez les nouveautés d’Azure Active Directory, notamment les dernières notes de publication, les problèmes connus, les corrections de bogues, les fonctionnalités dépréciées et les modifications à venir.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 455be9ba95975ba5c3bd870cbe01ca916d85ef41
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544421"
---
# <a name="whats-new-in-azure-active-directory"></a>Nouveautés d’Azure Active Directory

>Être averti sur la façon de revenir sur cette page pour les mises à jour par copier-coller cette URL : `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` dans votre ![icône de lecteur de flux RSS](./media/whats-new/feed-icon-16x16.png) lecteur de flux.

Azure AD bénéficie d’améliorations en continu. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues
- Fonctionnalités dépréciées
- Modifications planifiées

Cette page est mise à jour tous les mois. Donc, consultez-la régulièrement. Si vous recherchez des éléments antérieurs aux six derniers mois, vous les trouverez dans l’[Archive des nouveautés d’Azure Active Directory](whats-new-archive.md).

---

## <a name="april-2019"></a>Avril 2019

### <a name="azure-active-directory-azure-ad-entitlement-management-is-now-available-public-preview"></a>Gestion des habilitations Azure Active Directory (Azure AD) est désormais disponible (version préliminaire publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gouvernance des identités  
**Fonctionnalité de produit :** Gouvernance des identités

Gestion des droits Azure AD, désormais en version préliminaire publique, aide les clients à déléguer la gestion des packages de l’accès, qui définit la façon dont les employés et les partenaires commerciaux peuvent demander l’accès, qui doit approuver, et la durée pendant laquelle ils ont accès. Packages de l’accès peuvent gérer l’appartenance dans Azure AD et Office 365 groupes, les attributions de rôles dans les applications d’entreprise et les attributions de rôles pour les sites SharePoint Online. En savoir plus sur la gestion des habilitations à le [vue d’ensemble de la gestion des droits Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Pour en savoir plus sur l’éventail des fonctionnalités de gouvernance d’Azure AD Identity, y compris Privileged Identity Management, les révisions d’accès et les conditions d’utilisation, consultez [What ' s gouvernance d’Azure AD Identity ?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configurer une stratégie d’affectation de noms pour les groupes Office 365 dans le portail Azure AD (version préliminaire publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

Les administrateurs peuvent configurer maintenant une stratégie d’affectation de noms pour les groupes Office 365, à l’aide du portail Azure AD. Cette modification permet d’appliquer des conventions de nommage cohérentes pour les groupes Office 365 créées ou modifiées par les utilisateurs de votre organisation. 

Vous pouvez configurer une stratégie d’affectation de noms pour les groupes Office 365 de deux manières différentes :

- Définir des préfixes ou suffixes, qui sont automatiquement ajoutés à un nom de groupe.

- Charger un jeu personnalisé de mots bloqués pour votre organisation, qui ne sont pas autorisés dans les noms de groupe (par exemple, « PDG, paie, RH »).

Pour plus d’informations, consultez [appliquer une stratégie d’affectation de noms pour les groupes Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Journaux d’activité de AD Azure sont désormais disponibles dans Azure Monitor (disponibilité générale)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Pour faire face à vos commentaires sur les visualisations avec les journaux d’activités d’Azure AD, nous introduisons une nouvelle fonctionnalité Insights dans le journal Analytique. Cette fonctionnalité vous permet d’obtenir des informations sur vos ressources Azure AD à l’aide de nos modèles interactifs, appelés classeurs. Intégrées dans les classeurs peuvent fournir des détails pour les applications ou utilisateurs et incluent :

- **Se connecte.** Fournit des détails pour les applications et les utilisateurs, y compris l’emplacement de connexion, le système d’exploitation en cours d’utilisation ou client de navigateur et version et le nombre de connexions réussies ou échoués.

- **L’authentification héritée et l’accès conditionnel.** Fournit des détails pour les applications et les utilisateurs à l’aide de l’authentification héritée, y compris l’utilisation de l’authentification multifacteur déclenchée par les stratégies d’accès conditionnel, les applications à l’aide de stratégies d’accès conditionnel, et ainsi de suite.

- **Analyse de l’échec de connexion.** Vous aide à déterminer si vos erreurs de connexion sont produisent en raison d’une action de l’utilisateur, les problèmes de stratégie ou votre infrastructure.

- **Rapports personnalisés.** Vous pouvez créer ou modifier des classeurs existants pour vous aider à personnaliser la fonctionnalité d’aperçu pour votre organisation.

Pour plus d’informations, consultez [l’utilisation des classeurs Azure Monitor pour les rapports Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - avril 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En avril 2019, nous avons ajouté la que prise en charge de ces nouvelles applications avec fédération 21 à la galerie d’applications :

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks unique Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [transitent](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [ Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Se connecter](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMM](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel connecter](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (en fonction du rôle SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity gestion](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Révisions d’accès nouvelle option de fréquence et la sélection du rôle

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités

Révisions d’accès de nouvelles mises à jour dans Azure AD vous permettent de :

- Modifier la fréquence de votre accès révisions à **semestrielle**, en plus des options existantes hebdomadaire, mensuel, trimestriel et annuel.

- Sélectionnez plusieurs Azure AD et passez en revue des rôles de ressources Azure lors de la création d’un accès unique. Dans ce cas, tous les rôles sont configurés avec les mêmes paramètres et tous les réviseurs sont informés en même temps.

Pour plus d’informations sur la création d’une révision d’accès, consultez [créer une révision d’accès de groupes ou révisions d’accès des applications dans Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-mail alerte système (s) est en cours de transition, envoi de nouvelles informations d’expéditeur de courrier électronique pour certains clients

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** AD Sync  
**Fonctionnalité de produit :** Plateforme

Azure AD Connect est en cours de transition de notre messagerie alerte système (s), potentiellement montrant certains clients à un nouvel expéditeur de courrier électronique. Pour résoudre ce problème, vous devez ajouter `azure-noreply@microsoft.com` à la liste verte de votre organisation, ou vous ne pourrez continuer à recevoir des alertes importantes de votre Office 365, Azure ou vos services de synchronisation.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Modifications de suffixe UPN sont à présent réussies entre domaines fédérés dans Azure AD Connect

**Type :** Résolution  
**Catégorie de service :** AD Sync  
**Fonctionnalité de produit :** Plateforme

Vous pouvez maintenant modifier suffixe UPN de l’utilisateur d’un domaine fédéré à un autre domaine fédéré dans Azure AD Connect. Ce correctif signifie que vous devez n’est plus rencontrer le message d’erreur FederatedDomainChangeError pendant le cycle de synchronisation ou recevoir une notification électronique indiquant « Impossible de mettre à jour cet objet dans Azure Active Directory, car l’attribut [ FederatedUser.UserPrincipalName], n’est pas valide. Mise à jour de la valeur dans vos services d’annuaire locaux ».

Pour plus d’informations, consultez [dépannage des erreurs pendant la synchronisation](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Sécurité accrue à l’aide de la stratégie d’accès conditionnel basé sur la protection des applications dans Azure AD (version préliminaire publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités

Application basée sur la protection accès conditionnel est désormais disponible à l’aide de la **exiger la protection d’application** stratégie. Cette nouvelle stratégie permet d’accroître la sécurité de votre organisation en vous aidant à éviter :

- Utilisateurs de l’accès aux applications sans licence Microsoft Intune.

- Utilisateurs de l’impossibilité d’obtenir une stratégie de protection d’application Microsoft Intune.

- Utilisateurs de l’accès aux applications sans une stratégie de protection d’application Microsoft Intune configurée.

Pour plus d’informations, consultez [comment exiger la stratégie de protection des applications pour accéder aux applications de cloud avec l’accès conditionnel](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nouvelle prise en charge pour Azure AD unique de l’authentification et l’accès conditionnel dans Microsoft Edge (version préliminaire publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités

Nous avons amélioré notre prise en charge d’Azure AD pour Microsoft Edge, sans oublier la nouvelle prise en charge pour Azure AD unique l’authentification et l’accès conditionnel. Si vous avez déjà utilisé Microsoft Intune Managed Browser, vous pouvez maintenant utiliser Microsoft Edge à la place.

Pour plus d’informations sur la configuration et de gérer vos appareils et les applications à l’aide de l’accès conditionnel, consultez [nécessitent géré d’appareils pour accéder aux applications de cloud avec l’accès conditionnel](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) et [requièrent les applications clientes pour cloud approuvées accès aux applications avec accès conditionnel](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Pour plus d’informations sur la façon de gérer l’accès à l’aide de Microsoft Edge avec des stratégies Microsoft Intune, consultez [accès Internet de gérer à l’aide d’un navigateur protégé par la stratégie de Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Mars 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Infrastructure d’expérience d’identité et la stratégie personnalisée prise en charge dans Azure Active Directory B2C est désormais disponible (GA)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

Vous pouvez désormais créer des stratégies personnalisées dans Azure AD B2C, y compris les tâches suivantes, qui sont de prise en charge à grande échelle et par notre contrat SLA Azure :

- Créer et charger des parcours utilisateur d’authentification personnalisée à l’aide de stratégies personnalisées.

- Décrire des parcours utilisateur étape par étape comme des échanges entre des fournisseurs de revendications.

- Définir le branchement conditionnel dans des parcours utilisateur.

- Transformez et mapper les revendications pour une utilisation dans les décisions en temps réel et les communications.

- Utiliser des services compatibles avec l’API REST dans votre parcours utilisateur d’authentification personnalisée. Par exemple, avec les fournisseurs de messagerie, CRM et les systèmes d’autorisation de propriétaire.

- Fédérer avec les fournisseurs d’identité qui sont compatibles avec le protocole OpenIDConnect. Par exemple, avec Azure AD mutualisé, les fournisseurs de comptes sociaux ou fournisseurs de vérification à deux facteurs.

Pour plus d’informations sur la création de stratégies personnalisées, consultez [Remarques sur le développement de stratégies personnalisées dans Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) et lire [billet de blog de Alex Simon, y compris les études de cas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - mars 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

Dans 2019 mars, nous avons ajouté la que prise en charge de ces nouvelles applications avec fédération 14 à la galerie d’applications :

[ISEC7 Mobile Exchange délégué](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Explication de système d’audit](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool les performances comptent](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [le TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nouvelles Zscaler et Atlassian dans la galerie Azure AD - mars 2019 de connecteurs d’approvisionnement

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce

Automatiser la création, la mise à jour et suppression de comptes d’utilisateur pour les applications suivantes :

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler Two](https://aka.ms/ZscalerTwoProvisioning), [Zscaler trois](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud ](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Pour plus d’informations sur comment sécuriser au mieux votre organisation via l’approvisionnement de compte automatique des utilisateurs, consultez [automatiser l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Restaurer et gérer vos groupes Office 365 supprimés dans le portail Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

Vous pouvez désormais afficher et gérer vos groupes Office 365 supprimés à partir du portail Azure AD. Cette modification vous permet de voir quels groupes sont disponibles pour la restauration, ainsi que de vous laisser définitivement supprimer tous les groupes qui ne sont pas requis par votre organisation.

Pour plus d’informations, consultez [restauration expiré ou les groupes supprimés](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>L’authentification unique est désormais disponible pour les applications sécurisées par Azure AD SAML en local via le Proxy d’Application (version préliminaire publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès

Vous pouvez désormais fournir une expérience d’authentification unique (SSO) en local, les applications authentifiées SAML, ainsi que de l’accès à distance à ces applications via le Proxy d’Application. Pour plus d’informations sur la configuration SAML SSO avec vos applications en local, consultez [SAML authentification unique pour les applications sur site avec le Proxy d’Application (version préliminaire)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Les applications clientes dans des boucles de la demande seront interrompues pour améliorer la fiabilité et l’expérience utilisateur

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur

Les applications clientes peuvent incorrectement émettre des centaines des mêmes requêtes de connexion sur une courte période de temps. Ces demandes, qu’ils soient réussies ou non, contribuent tous à une expérience utilisateur médiocre et charges de travail renforcées pour le fournisseur d’identité, accroître la latence pour tous les utilisateurs et réduire la disponibilité du fournisseur d’identité.

Cette mise à jour envoie un `invalid_grant` erreur : `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` à des applications clientes qui émettent des demandes dupliquées plusieurs fois pendant une courte période de temps, dépasse le cadre du fonctionnement normal. Les applications clientes qui rencontrent ce problème doivent afficher une invite interactive, en demandant à l’utilisateur de se reconnecter. Pour plus d’informations sur cette modification et sur la résolution de votre application si elle rencontre cette erreur, consultez [quelles sont les nouveautés pour l’authentification ?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nouvelle expérience utilisateur, les journaux d’Audit désormais disponible

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Nous avons créé une nouvelle annonce Azure **journaux d’Audit** page pour aider à améliorer la lisibilité et la façon dont vous recherchez vos informations. Pour afficher les nouvelles **journaux d’Audit** page, sélectionnez **journaux d’Audit** dans le **activité** section d’Azure AD.

![Nouvelle page de journaux d’Audit, avec les informations de l’exemple](media/whats-new/audit-logs-page.png)

Pour plus d’informations sur la nouvelle **journaux d’Audit** page, consultez [auditer les rapports d’activité dans le portail Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nouveaux avertissements et des conseils permettant d’empêcher le verrouillage administrateur accidentelle de stratégies d’accès conditionnel mal configurés

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités

Pour aider à empêcher les administrateurs d’accidentellement procéder eux-mêmes au verrouillage en dehors de leurs propres clients par le biais des stratégies d’accès conditionnel mal configurés, nous avons créé de nouveaux avertissements et des conseils mis à jour dans le portail Azure. Pour plus d’informations sur les nouvelles instructions, consultez [quelles sont les dépendances de service dans l’accès conditionnel Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Termes du contrat de l’utilisateur final améliorée des expériences d’utilisation sur les appareils mobiles

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Gouvernance

Nous avons mis à jour nos conditions existantes d’expériences d’utilisation pour aider à améliorer la façon dont vous passez en revue et acceptez les conditions d’utilisation sur un appareil mobile. Vous pouvez maintenant effectuer un zoom avant et arrière, revenez en arrière, télécharger les informations et sélectionner des liens hypertexte. Pour plus d’informations sur les conditions de mise à jour d’utilisation, consultez [Azure Active Directory fonctionnalité conditions d’utilisation](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nouveaux journaux d’activités d’Azure AD le téléchargement disponible

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Vous pouvez maintenant télécharger de grandes quantités de journaux d’activité directement depuis le portail Azure. Cette mise à jour vous permet de :

- Téléchargez jusqu'à 250 000 lignes.

- Recevez une notification une fois le téléchargement terminé.

- Personnaliser votre nom de fichier.

- Déterminer votre format de sortie, JSON ou CSV.

Pour plus d’informations sur cette fonctionnalité, consultez [Guide de démarrage rapide : Télécharger un rapport d’audit à l’aide du portail Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Changement cassant : Mises à jour de l’évaluation de la condition par Exchange ActiveSync (EAS)

**Type :** Modification planifiée  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Contrôle d’accès

Nous sommes en train de mettre à jour de la manière dont Exchange ActiveSync (EAS) évalue les conditions suivantes :

- Emplacement de l’utilisateur, selon les pays, région ou l’adresse IP

- Risque à la connexion

- Plateforme d’appareil.

Si vous avez précédemment utilisé ces conditions dans vos stratégies d’accès conditionnel, n’oubliez pas que le comportement de la condition peut changer. Par exemple, si vous avez utilisé précédemment la condition d’emplacement utilisateur dans une stratégie, vous constaterez peut-être la stratégie maintenant ignorée basée sur l’emplacement de votre utilisateur.

---

## <a name="february-2019"></a>Février 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Chiffrement de jeton configurable SAML Azure AD (préversion publique) 

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Authentification unique

Vous pouvez maintenant configurer n’importe quelle application SAML pris en charge pour recevoir des jetons SAML chiffrés. En cas de configuré et utilisé avec une application, Azure AD chiffre les assertions SAML émises à l’aide d’une clé publique obtenue à partir d’un certificat stocké dans Azure AD.

Pour plus d’informations sur la configuration de votre chiffrement des jetons SAML, consultez [chiffrement des jetons configurer Azure AD SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Créer une révision d'accès pour les groupes ou les applications à l'aide des Révisions d'accès Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance

Vous pouvez désormais inclure plusieurs groupes ou de révision pour l’appartenance au groupe ou d’une affectation de l’application d’accéder aux applications dans un annuaire Azure AD unique. Révisions d’accès avec plusieurs groupes ou applications sont configurées avec les mêmes paramètres et tous les réviseurs inclus sont informés en même temps.

Pour plus d’informations sur la façon de créer une révision d’accès à l’aide de révisions d’accès Azure AD, consultez [créer une révision d’accès des groupes ou des applications dans les révisions d’accès Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nouvelles applications fédérées disponibles dans la galerie d'applications Azure AD - Février 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En février 2019, nous avons ajouté la que prise en charge de ces nouvelles applications avec fédération 27 à la galerie d’applications :

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [accidentelles](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Qmlativ Skyward](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Cliquez sur autorisations, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [SISMIQUES ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Partage un rêve](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods intégration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [LMS de connaissances](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [Campus de l’unité d’organisation](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope données](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud par Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [Plateforme de productivité ClickUp](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Inscription MFA/SSPR combinée améliorée

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Réinitialisation de mot de passe en libre-service  
**Fonctionnalité de produit :** Authentification utilisateur
 
En réponse aux commentaires des clients, nous avons amélioré l’expérience de préversion de l’inscription MFA/SSPR combiné, vous aidez les utilisateurs à inscrire plus rapidement leurs informations de sécurité pour l’authentification Multifacteur et SSPR. 

**Pour activer l’expérience améliorée pour vos utilisateurs dès aujourd'hui, procédez comme suit :**

1. Comme un administrateur général ou un administrateur de l’utilisateur, connectez-vous au portail Azure et accédez à **Azure Active Directory > Paramètres utilisateur > Gérer les paramètres des fonctionnalités en version préliminaire de panneau accès**. 

2. Dans le **utilisateurs pouvant utiliser la version préliminaire des fonctionnalités de l’inscription et la gestion des informations de sécurité – Actualiser** option, choisissez d’activer les fonctionnalités pour un **groupe sélectionné d’utilisateurs** ou pour **tous les utilisateurs** .

Sur les prochaines semaines, nous supprimerons la possibilité d’activer l’ancienne combiné MFA/SSPR d’inscription aperçu expérience pour les clients qui n’est pas déjà activé.

**Pour voir si le contrôle est supprimé de votre client, procédez comme suit :**

1. Comme un administrateur général ou un administrateur de l’utilisateur, connectez-vous au portail Azure et accédez à **Azure Active Directory > Paramètres utilisateur > Gérer les paramètres des fonctionnalités en version préliminaire de panneau accès**.  

2. Si le **utilisateurs pouvant utiliser les fonctionnalités en version préliminaire de l’inscription et la gestion des informations de sécurité** option est définie sur **aucun**, l’option sera supprimée à partir de votre client.

Indépendamment de si vous avez précédemment activé l’inscription MFA/SSPR combinée ancien Aperçu amélioré des utilisateurs ou non, l’ancienne expérience sera désactivée à une date ultérieure. Pour cette raison, il est vivement que vous déplacer vers la nouvelle expérience améliorée dès que possible.

Pour plus d’informations sur l’expérience d’inscription améliorée, consultez le [améliorations sympas à Azure AD combinées MFA et d’expérience de l’inscription de réinitialisation de mot de passe](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Expérience de gestion de stratégie mise à jour pour les flux utilisateur

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

Nous avons mis à jour le processus de création et la gestion de stratégie de flux d’utilisateurs (précédemment connu sous le nom intégrés stratégies) plus facilités. Cette nouvelle expérience est désormais la valeur par défaut pour l’ensemble de vos locataires Azure AD.

Vous pouvez fournir des commentaires supplémentaires et des suggestions en utilisant le sourire ou Smiley mécontent icônes dans le **envoyez-nous vos commentaires** zone en haut de l’écran du portail.

Pour plus d’informations sur la nouvelle expérience de gestion de stratégie, consultez le [Azure AD B2C a maintenant la personnalisation de JavaScript et de nombreuses nouvelles fonctionnalités plus](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Choisir des versions d'éléments de page spécifiques fournies par Azure AD B2C

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

Vous pouvez maintenant choisir une version spécifique des éléments de page fourni par Azure AD B2C. En sélectionnant une version spécifique, vous pouvez tester vos mises à jour avant qu’ils s’affichent sur une page et vous pouvez obtenir un comportement prévisible. En outre, vous pouvez désormais choisir d’appliquer des versions de page spécifique pour autoriser des personnalisations de JavaScript. Pour activer cette fonctionnalité, accédez à la **propriétés** page dans votre flux d’utilisateurs.

Pour plus d’informations sur le choix des versions spécifiques d’éléments de page, consultez le [Azure AD B2C a maintenant la personnalisation de JavaScript et de nombreuses nouvelles fonctionnalités plus](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Exigences de mot de passe de l'utilisateur configurable pour B2C (DG)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

Vous pouvez maintenant configurer la complexité du mot de passe de votre organisation pour vos utilisateurs finaux, au lieu de devoir utiliser votre natif stratégie de mot de passe Azure AD. À partir de la **propriétés** Panneau de votre utilisateur flux (précédemment appelés vos stratégies intégrées), vous pouvez choisir une complexité de mot de passe de **Simple** ou **fort**, ou vous pouvez créer un **personnalisé** ensemble d’exigences.

Pour plus d’informations sur la configuration d’exigence de complexité de mot de passe, consultez [configurer les exigences de complexité des mots de passe dans Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nouveaux modèles par défaut pour les expériences d'authentification de marque personnalisée

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

Vous pouvez utiliser nos nouveaux modèles par défaut, situés sur le **Page dispositions** Panneau de votre flux d’utilisateurs (précédemment appelé stratégies intégrées), pour créer un personnalisé marque l’expérience d’authentification pour vos utilisateurs.

Pour plus d’informations sur l’utilisation des modèles, consultez [Azure AD B2C a maintenant la personnalisation de JavaScript et de nombreuses nouvelles fonctionnalités plus](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Janvier 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Collaboration Active Directory B2B à l'aide de l'authentification par code secret à usage unique (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C

Nous avons introduit l'authentification par code secret à usage unique (OTP) pour les utilisateurs invités B2B qui ne peuvent pas être authentifiés par d'autres moyens, comme Azure AD, un compte Microsoft (MSA) ou la fédération Google. Avec cette nouvelle méthode d'authentification, les utilisateurs invités n'ont pas besoin de créer de nouveau compte Microsoft. Lors de l'échange d'une invitation ou de l'accès à une ressource partagée, l'utilisateur invité peut demander qu'un code temporaire soit envoyé à une adresse e-mail. À l'aide de ce code temporaire, l'utilisateur invité peut continuer à se connecter.

Pour plus d'informations, consultez [Authentification à l'aide d'un code secret à usage unique envoyé par e-mail (préversion)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) et le blog [Azure AD permet à tous les utilisateurs de partager et de collaborer en toute simplicité sans compte](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nouveaux paramètres de cookies pour le proxy d'application Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès

Nous avons introduit trois nouveaux paramètres de cookies, disponibles pour vos applications publiées via le proxy d’application :

- **Utiliser un cookie HTTPOnly.** Définit l'indicateur **HTTPOnly** sur l'accès du proxy d'application et les cookies de session. Cette option offre des avantages supplémentaires en termes de sécurité, et permet notamment d'éviter de copier ou de modifier les cookies via des scripts côté client. Nous vous recommandons d’activer cet indicateur (sélectionnez **Oui**) pour bénéficier de ces avantages supplémentaires.

- **Utiliser un cookie sécurisé.** Définit l'indicateur **Sécurisé** sur l'accès du proxy d'application et les cookies de session. Cette option offre des avantages supplémentaires en termes de sécurité, et veille à ce que les cookies soient uniquement transmis via des canaux sécurisés TLS, tels que HTTPS. Nous vous recommandons d’activer cet indicateur (sélectionnez **Oui**) pour bénéficier de ces avantages supplémentaires.

- **Utiliser un cookie persistant.** Empêche l'expiration des cookies d'accès lorsque le navigateur web est fermé. La durée de vie de ces cookies correspond à la durée de vie du jeton d'accès. Cela étant, les cookies sont réinitialisés une fois le délai d'expiration atteint ou si l’utilisateur supprime manuellement le cookie. Nous vous recommandons de conserver le paramètre par défaut **Non**, et de n'activer le paramètre que pour les applications plus anciennes qui ne partagent pas de cookies entre processus.

Pour plus d’informations sur les nouveaux cookies, consultez [Paramètres de cookies pour l’accès aux applications locales dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nouvelles applications fédérées disponibles dans la galerie d'applications Azure AD - Janvier 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En janvier 2019, nous avons ajouté à notre galerie d’applications les 35 applications suivantes qui prennent en charge la fédération :

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent Palette](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco PARAPLUIE](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Administrateur de l’accès Internet](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [rappel d’Expiration](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR visionneuse](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [verbe](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Fermez](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [Système GTNexus SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES pour Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 pour Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn transporter ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas plateforme](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nouvelles améliorations apportées à Azure AD Identity Protection (préversion publique)

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Nous sommes heureux d’annoncer l'ajout des améliorations suivantes à l’offre de préversion publique d’Azure AD Identity Protection, notamment :

- Interface utilisateur mise à jour et mieux intégrée

- API supplémentaires

- Évaluation des risques renforcée via Machine Learning

- Alignement des produits pour les utilisateurs et les connexions à risque

Consultez [Qu’est-ce qu’Azure Active Directory Identity Protection (version actualisée) ?](https://aka.ms/IdentityProtectionDocs) pour plus d'informations et partager vos idées via des invites intégrées au produit.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nouvelle fonctionnalité Verrou d'application dans l'application Microsoft Authenticator sur les appareils iOS et Android

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Application Microsoft Authenticator  
**Fonctionnalité de produit :** Protection et sécurité des identités

Pour sécuriser les codes secrets à usage unique, informations et paramètres d'application, vous pouvez activer la fonctionnalité Verrou d'application dans l'application Microsoft Authenticator. Si vous activez Verrou d'application, vous êtes invité à vous authentifier à l'aide de votre code PIN ou verrou biométrique chaque fois que vous ouvrez l’application Microsoft Authenticator.

Pour plus d’informations, consultez [Forum aux questions sur l’application Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Fonctionnalités d'exportation améliorées dans Azure AD Privileged Identity Management (PIM)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Privileged Identity Management  
**Fonctionnalité de produit :** Privileged Identity Management

Les administrateurs Privileged Identity Management (PIM) peuvent désormais exporter toutes les affectations de rôles actifs et éligibles pour une ressource spécifique, ce qui comprend les affectations de rôles pour toutes les ressources enfants. Auparavant, il était difficile pour les administrateurs d’obtenir une liste complète des attributions de rôle d’un abonnement, et ils devaient les exporter pour chaque ressource spécifique.

Pour plus d'informations, consultez [Afficher l’historique d’audit et d’activité pour les rôles de ressources Azure dans PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Novembre/décembre 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Des utilisateurs retirés de l’étendue de synchronisation ne basculent plus vers des comptes uniquement cloud

**Type :** Résolution  
**Catégorie de service :** User Management  
**Fonctionnalité de produit :** Répertoire

>[!Important]
>Nous avons entendu et nous comprenons votre frustration provoquée par ce correctif. Nous avons donc annulé cette modification jusqu’à ce que nous soyons en mesure de rendre l’implémentation de ce correctif plus facile dans votre organisation.

Nous avons résolu un bogue qui faisait que l’indicateur DirSyncEnabled d’un utilisateur passait erronément à **False** lorsque l’objet Active Directory Domain Services (AD DS) était exclu de l’étendue de synchronisation, puis déplacé vers la Corbeille dans Azure AD lors du cycle de synchronisation suivant. Ce correctif a pour effet que, si l’utilisateur est exclu de l’étendue de la synchronisation, puis restauré à partir de la Corbeille Azure AD, le compte d’utilisateur reste synchronisé à partir de l’AD local, comme prévu, et ne peut pas être géré dans le cloud car sa source d’autorité demeure en tant qu’AD local.

Avant ce correctif, un problème se produisait quand l’indicateur DirSyncEnabled était commuté sur False. Cela donnait la fausse impression que ces comptes avaient été convertis en objets uniquement cloud, et que les comptes pouvaient être gérés dans le nuage. Cependant, les comptes conservaient toujours leur source d’autorité comme locale, et toutes les propriétés synchronisées (attributs fantômes) provenant de l’AD local. Cette situation entraînait plusieurs problèmes dans Azure AD et dans d’autres charges de travail cloud (telles qu’Exchange Online) qui s’attendaient à traiter ces comptes comme synchronisés à partir d’AD, mais se comportaient désormais comme des comptes uniquement cloud.

À ce stade, la seule façon de convertir véritablement un compte synchronisé à partir d’AD vers un compte uniquement cloud consiste à désactiver la synchronisation d’annuaires au niveau du locataire, ce qui déclenche une opération de serveur principal pour transférer la source d’autorité. Ce type de modification de source d’autorité nécessite (sans que ce soit limitatif) un nettoyage de tous les attributs associés locaux (tels que LastDirSyncTime et les attributs fantômes), et l’envoi d’un signal à d’autres charges de travail cloud pour que son objet respectif soit également converti en un compte uniquement cloud.

Ce correctif empêche donc des mises à jour directes de l’attribut ImmutableID d’un utilisateur synchronisé à partir d’Active Directory, qui étaient requises dans certains scénarios par le passé. Par conception, l’ImmutableID d’un objet dans Azure AD, comme son nom l’indique, est censé être immuable. De nouvelles fonctionnalités implémentées dans Azure AD Connect Health et le client de synchronization Azure AD Connect sont disponibles pour de tels scénarios :

- **Mise à jour d’ImmutableID à grande échelle pour de nombreux utilisateurs en suivant une approche par étapes**
  
  Par exemple, vous devez effectuer une migration inter-forêts d’AD DS un peu longue. Solution : Utilisez Azure AD Connect pour **configurer l’ancre source**, puis, lorsque l’utilisateur migre, copiez les valeurs d’ImmutableID existantes d’Azure AD dans l’attribut ms-DS-Consistency-Guid de l’utilisateur d’AD DS local de la nouvelle forêt. Pour plus d’informations, voir [Utilisation de ms-DS-ConsistencyGuid en tant qu’attribut sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Mises à jour d’ImmutableID à grande échelle pour de nombreux utilisateurs en une fois**

  Par exemple, lors de l’implémentation d’Azure AD Connect, vous commettez une erreur qui vous amène à devoir modifier l’attribut SourceAnchor. Solution : Désactivez la synchronisation d’annuaires au niveau locataire, et effacez toutes les valeurs d’ImmutableID non valides. Pour plus d’informations, voir [Désactiver la synchronisation d’annuaires pour Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Rétablissement de correspondance entre l’utilisateur local et un utilisateur existant dans Azure AD** Par exemple, un utilisateur recréé dans AD DS génère un doublon dans le compte Azure AD au lieu qu’une correspondance soit rétablie avec un compte Azure AD existant (objet orphelin). Solution : Utilisez Azure AD Connect Health dans le portail Microsoft Azure pour remapper l’ancre source/l’ImmutableID. Pour plus d’informations, voir [Scénario d’objet orphelin](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Modification critique : Mises à jour du schéma des journaux d’activité de connexion et d’audit via Azure Monitor

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Nous publions actuellement à la fois des flux de journaux d’audit et de connexion via Azure Monitor. Vous pouvez donc en toute transparence intégrer les fichiers journaux avec vos outils SIEM ou Log Analytics. En réponse à vos commentaires et en vue de l’annonce de la disponibilité générale de cette fonctionnalité, nous apportons les modifications suivantes à notre schéma. Ces modifications de schéma et les mises à jour de documentation connexes se produiront d’ici la première semaine de janvier.

#### <a name="new-fields-in-the-audit-schema"></a>Nouveaux champs du schéma d’audit
Nous ajoutons un nouveau champ **Type d’opération**, pour fournir le type d’opération effectuée sur la ressource. Par exemple, **Ajouter**, **Mettre à jour** ou **Supprimer**.

#### <a name="changed-fields-in-the-audit-schema"></a>Champs modifiés du schéma d’audit
Les champs suivants font l’objet de modifications dans le schéma d’audit :

|Nom du champ|Ce qui a changé|Anciennes valeurs|Nouvelles valeurs|
|----------|------------|----------|----------|
|Catégorie|Il s’agissait du champ **Nom de service** qui se nomme à présent **Catégories d’audit**. **Nom du service** a été renommé en **loggedByService**.|<ul><li>Approvisionnement des comptes</li><li>Annuaire principal</li><li>Réinitialisation de mot de passe en libre service</li></ul>|<ul><li>User Management</li><li>Gestion des groupes</li><li>Gestion des applications</li></ul>|
|targetResources|Inclut **TargetResourceType** au niveau supérieur.|&nbsp;|<ul><li>Stratégie</li><li>Application</li><li>Utilisateur</li><li>Groupe</li></ul>|
|loggedByService|Fournit le nom du service ayant généré le journal d’audit.|Null|<ul><li>Approvisionnement des comptes</li><li>Annuaire principal</li><li>Réinitialisation de mot de passe libre-service</li></ul>|
|Résultat|Fournit le résultat des journaux d’audit. Auparavant, nous présentions une énumération, mais nous montrons maintenant la valeur réelle.|<ul><li>0</li><li>1</li></ul>|<ul><li>Succès</li><li>Échec</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Champs modifiés du schéma de connexion
Les champs suivants font l’objet de modifications dans le schéma de connexion :

|Nom du champ|Ce qui a changé|Anciennes valeurs|Nouvelles valeurs|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Ce champ était nommé **conditionalaccessPolicies**. Il s’intitule à présent **conditionalaccessPolicies**.|Aucune modification|Aucune modification|
|conditionalAccessStatus|Fournit le résultat de l’état de la stratégie d’accès conditionnel au moment de la connexion. Auparavant, nous présentions une énumération, mais nous montrons maintenant la valeur réelle.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Succès</li><li>Échec</li><li>Non appliqué</li><li>Désactivé</li></ul>|
|appliedConditionalAccessPolicies: result|Fournit le résultat de l’état individuel de la stratégie d’accès conditionnel au moment de la connexion. Auparavant, nous présentions une énumération, mais nous montrons maintenant la valeur réelle.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Succès</li><li>Échec</li><li>Non appliqué</li><li>Désactivé</li></ul>|

Pour plus d’informations sur le schéma, consultez [Interpréter le schéma des journaux d’audit Azure Active Directory dans Azure Monitor (préversion)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema).

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Améliorations de la protection des identités apportées dans le modèle Machine Learning supervisé et le moteur d’indice de risque

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Indices de risque

Les améliorations apportées à l’utilisateur Identity Protection et au moteur d’évaluation des risques de connexion peuvent contribuer à améliorer la couverture et l’exactitude du risque utilisateur. Les administrateurs peuvent constater que le niveau de risque utilisateur n’est plus directement lié au niveau de risque de détections spécifiques, et qu’il existe une augmentation du nombre et du niveau des événements de connexion à risque.

Les détections de risque sont à présent évaluées par le modèle Machine Learning supervisé, qui calcule le risque utilisateur à l’aide des fonctionnalités supplémentaires des connexions utilisateur et d’un modèle de détections. Selon ce modèle, l’administrateur peut rechercher les utilisateurs présentant des indices de risque élevés, même si les détections associées à cet utilisateur sont de risque faible ou moyen. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Les administrateurs peuvent réinitialiser leur mot de passe à l’aide de l’application Microsoft Authenticator (préversion publique)

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Réinitialisation de mot de passe en libre-service  
**Fonctionnalité de produit :** Authentification utilisateur

Les administrateurs Azure AD peuvent à présent réinitialiser leur mot de passe en utilisant les notifications d’application Microsoft Authenticator ou un code à partir d’un jeton matériel ou d’une application d’authentificateur mobile. Pour réinitialiser leur mot de passe, les administrateurs peuvent à présent utiliser deux des méthodes suivantes :

- Notification de l’application Microsoft Authenticator

- Autre application mobile d’authentificateur/code de jeton matériel

- Email

- appel téléphonique

- SMS

Pour plus d’informations sur l’utilisation de l’application Microsoft Authenticator pour réinitialiser les mots de passe, consultez [Réinitialisation de mot de passe en libre-service Azure AD - Application Mobile et SSPR (préversion)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nouveau rôle Administrateur d’appareil cloud Azure AD (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion et inscription des appareils  
**Fonctionnalité de produit :** Contrôle d’accès

Les administrateurs peuvent attribuer des utilisateurs au nouveau rôle Administrateur d’appareil cloud pour effectuer des tâches d’administration des appareils cloud. Les utilisateurs attribués au rôle Administrateur d’appareil cloud peuvent activer, désactiver et supprimer des appareils dans Azure AD et lire des clés BitLocker Windows 10 (le cas échéant) dans le portail Azure.

Pour plus d’informations sur les rôles et les autorisations, consultez [Attribution de rôles d’administrateur dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Gérer vos appareils avec le nouveau timestamp d’activité dans Azure AD (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion et inscription des appareils  
**Fonctionnalité de produit :** Gestion du cycle de vie des appareils

Nous sommes conscients qu’au fil du temps, vous devez actualiser et mettre hors service des appareils de votre organisation dans Azure AD, pour éviter d’avoir des appareils obsolètes dans votre environnement. Pour faciliter ce processus, Azure AD met maintenant à jour vos appareils avec un nouveau timestamp d’activité, pour vous aider à gérer le cycle de vie de vos appareils.

Pour en savoir plus sur la récupération et l’utilisation de ce timestamp, consultez la page [Procédure : Gérer les appareils obsolètes dans Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Les administrateurs peuvent demander aux utilisateurs d’accepter les conditions d’utilisation sur chaque appareil

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Gouvernance
 
Les administrateurs peuvent maintenant activer l’option **Require users to consent on every device (Demander le consentement des utilisateurs sur chaque appareil)** pour demander aux utilisateurs d’accepter vos conditions d’utilisation sur chaque appareil à partir duquel ils utilisent votre locataire.

Pour plus d’informations, consultez la section [Conditions d’utilisation par appareil de la page Fonctionnalité Conditions d’utilisation d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Les administrateurs peuvent configurer l’expiration des conditions d’utilisation selon une planification périodique

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Gouvernance
 

Les administrateurs peuvent maintenant activer l’option **Faire expirer les consentements** pour faire expirer les conditions d’utilisation de tous vos utilisateurs en fonction de votre planification périodique spécifiée. La planification peut être une fois par an, deux fois par an, tous les trimestres ou tous les mois. Une fois que les conditions d’utilisation expirent, les utilisateurs doivent acceptent.

Pour plus d’informations, consultez la section [Ajouter des conditions d’utilisation de la page Fonctionnalité Conditions d’utilisation d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Les administrateurs peuvent configurer l’expiration des conditions d’utilisation selon la planification de chaque utilisateur

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Gouvernance

Les administrateurs peuvent maintenant spécifier la durée après laquelle l’utilisateur doit réaccepter les conditions d’utilisation. Par exemple, les administrateurs peuvent spécifier que les utilisateurs doivent réaccepter les conditions d’utilisation tous les 90 jours.

Pour plus d’informations, consultez la section [Ajouter des conditions d’utilisation de la page Fonctionnalité Conditions d’utilisation d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nouveaux e-mails Azure Active Directory Privileged Identity Management (PIM) pour les rôles Azure Active Directory

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Privileged Identity Management  
**Fonctionnalité de produit :** Privileged Identity Management
 
Les clients qui utilisent Azure AD Privileged Identity Management (PIM) peuvent à présent recevoir un e-mail de synthèse hebdomadaire, comprenant notamment les informations suivantes pour les sept derniers jours :

- Vue d’ensemble des principales attributions de rôles éligibles et permanentes

- Nombre d’utilisateurs activant des rôles

- Nombre d’utilisateurs attribués à des rôles dans PIM

- Nombre d’utilisateurs attribués à des rôles en dehors de PIM

- Nombre d’utilisateurs « rendus permanents » dans PIM

Pour plus d’informations sur PIM et les notifications par e-mail disponibles, consultez [Notifications par e-mail dans PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Disponibilité générale de la gestion des licences basée sur les groupes

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Répertoire

La gestion des licences basée sur les groupes passe de la préversion publique à la disponibilité générale. Dans le cadre de cette disponibilité générale, nous avons accru la scalabilité de cette fonctionnalité et ajouté la possibilité de retraiter les attributions de la gestion des licences basée sur les groupes pour un seul utilisateur, ainsi que la possibilité d’utiliser la gestion des licences basée sur les groupes avec des licences Office 365 E3/A3.

Pour plus d’informations sur la gestion des licences basée sur les groupes, consultez la page sur la [gestion des licences basée sur les groupes dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD (novembre 2018)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En novembre 2018, nous avons ajouté dans notre galerie d’applications les 26 nouvelles applications ci-après qui prennent en charge la fédération :

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://www.drivedollar.com/Account/Login), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps - Classic Test](https://test.plexonline.com/signon), [Plex Apps – Classic](https://www.plexonline.com/signon), [Plex Apps - UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/), [CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration) 

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Octobre 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Les journaux d’activité Azure AD fonctionnent maintenant avec Azure Log Analytics (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Nous sommes heureux d’annoncer que vous pouvez maintenant transférer vos journaux d’activité Azure AD vers Azure Log Analytics ! Grâce à cette fonctionnalité très demandée, vous pouvez encore mieux accéder aux analyses de votre activité, de vos opérations et de votre sécurité, et elle vous aide à surveiller votre infrastructure. Pour plus d’informations, consultez le blog [Journaux d’activité Azure Active Directory dans Azure Log Analytics désormais disponible](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nouvelles applications fédérées disponibles dans la galerie d'applications Azure AD (octobre 2018)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En octobre 2018, nous avons ajouté à notre galerie d’applications les 14 nouvelles applications ci-dessous, qui prennent en charge la fédération :

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Notifications par e-mail pour Azure AD Domain Services

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Services de domaine Azure AD  
**Fonctionnalité de produit :** Services de domaine Azure AD

Azure AD Domain Services fournit sur le portail Azure des alertes sur les erreurs de configuration ou les problèmes avec votre domaine géré. Ces alertes incluent des guides pas à pas pour que vous puissiez essayer de résoudre les problèmes sans avoir à contacter le support.

En octobre, vous serez en mesure de personnaliser les paramètres de notification pour votre domaine managé afin d’en cas de nouvelles alertes, un e-mail soit envoyé à un groupe de personnes désigné, ce qui évite de devoir vérifier en permanence le portail pour détecter les mises à jour.

Pour plus d’informations, consultez [Paramètres de notification dans Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Le portail Azure AD assure la prise en charge à l’aide de l’API du domaine ForceDelete pour supprimer des domaines personnalisés. 

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion de répertoires  
**Fonctionnalité de produit :** Répertoire

Nous sommes heureux de vous annoncer que vous pouvez maintenant utiliser l'API de domaine ForceDelete pour supprimer vos noms de domaine personnalisés en renommant de façon asynchrone les références, comme les utilisateurs, les groupes et les applications de votre nom de domaine personnalisé (contoso.com) pour revenir au nom de domaine par défaut initial (contoso.onmicrosoft.com).

Ce changement vous permet de supprimer plus rapidement vos noms de domaine personnalisés si votre organisation n'utilise plus le nom, ou si vous devez utiliser le nom de domaine avec un autre AD Azure.

Pour plus d’informations, consultez [Supprimer un nom de domaine personnalisé](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---
