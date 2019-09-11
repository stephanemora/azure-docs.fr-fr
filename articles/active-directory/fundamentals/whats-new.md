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
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e679ab21127551a9d7004f4ec65045610879f42b
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305127"
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

## <a name="august-2019"></a>Août 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Planifier la modification : Dépréciation des packs de contenu Power BI

**Type :** Modification planifiée  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

À partir du 1er octobre 2019, Power BI commencera à déprécier tous les packs de contenu, y compris le pack de contenu Azure AD Power BI. En guise d’alternative à ce pack de contenu, vous pouvez utiliser des classeurs Azure AD pour obtenir des informations sur les services liés à Azure AD. D’autres classeurs seront bientôt accessibles, y compris des classeurs pour les stratégies d’accès conditionnel en mode rapport uniquement, des insights basés sur le consentement de l’application, et plus.

Pour plus d’informations sur les classeurs, découvrez [comment utiliser des classeurs Azure Monitor pour créer des rapports Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Pour plus d’informations sur la dépréciation des packs de contenu, consultez le billet de blog [Annonce de la disponibilité générale des applications de modèle Power BI](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>De nouveaux rôles personnalisés sont disponibles pour la gestion de l’inscription des applications (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès

Les rôles personnalisés (disponibles avec un abonnement Azure AD P1 ou P2) peuvent désormais vous fournir un accès affiné, en vous permettant de créer des définitions de rôles avec des autorisations spécifiques, puis d’attribuer ces rôles à des ressources spécifiques. Actuellement, vous créez des rôles personnalisés en utilisant des autorisations pour gérer les inscriptions d’applications, puis en affectant le rôle à une application spécifique. Pour plus d’informations sur les rôles personnalisés, consultez [Rôles d’administrateur personnalisés dans Azure Active Directory (préversion)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Si vous avez besoin de la prise en charge d’autorisations ou de ressources supplémentaires et que vous ne les voyez pas actuellement, vous pouvez envoyer des commentaires sur notre [site de retours relatifs à Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) et nous ajouterons votre demande à notre feuille de route de mise à jour.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Les nouveaux journaux d’approvisionnement peuvent vous aider à surveiller et à résoudre les problèmes de votre déploiement d’approvisionnement d’application (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités

De nouveaux journaux de provisionnement sont disponibles pour vous aider à surveiller et à résoudre les problèmes de déploiement de l’approvisionnement des utilisateurs et des groupes. Ces nouveaux fichiers journaux incluent des informations sur les éléments suivants :

- Quels groupes ont été créés avec succès dans [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Quels rôles ont été importés à partir [d’Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-azure-ad-single-sign-on) ?
- Les employés qui n’ont pas été importés à partir de [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Pour en savoir plus, voir [Approvisionnement des rapports dans le portail Azure Active Directory (préversion)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nouveaux rapports de sécurité pour tous les administrateurs Azure AD (disponibilité générale)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Par défaut, tous les administrateurs Azure AD seront bientôt en mesure d’accéder à des rapports de sécurité modernes au sein d’Azure AD. Jusqu’à la fin du mois de septembre, vous pourrez utiliser la bannière en haut des rapports de sécurité modernes pour revenir aux anciens rapports.

Les rapports de sécurité modernes fournissent des fonctionnalités supplémentaires par rapport aux versions antérieures, notamment ce qui suit :

- Filtrage et tri avancés
- Actions en bloc, telles que le rejet de risque utilisateur
- Confirmation d’entités compromises ou sûres
- État du risque, à savoir : À risque, Ignoré, Corrigé ou Compromission confirmée.
- Nouvelles détections liées aux risques (disponibles pour les abonnés Azure AD Premium)

Pour plus d’informations, consultez [Utilisateurs à risque](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report), [Connexions à risque](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report) et [Détections de risques](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections-report).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>L’identité gérée affectée par l’utilisateur est disponible pour les machines virtuelles et les groupes de machines virtuelles identiques (disponibilité générale)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identités gérées pour les ressources Azure  
**Fonctionnalité de produit :** Expérience de développement

Les identités gérées affectées par l’utilisateur sont désormais généralement disponibles pour les machines virtuelles et les groupes de machines virtuelles identiques. Dans le cadre de cet ajout, Azure peut créer une identité dans le locataire Azure AD approuvé par l’abonnement utilisé et peut être affecté à une ou plusieurs instances de service Azure. Pour en savoir plus sur les identités managées affectées par l’utilisateur, consultez la section [Que sont les identités managées pour les ressources Azure ?](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Les utilisateurs peuvent réinitialiser leur mot de passe à l’aide d’une application mobile ou d’un jeton matériel (disponibilité générale)

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Réinitialisation de mot de passe en libre-service  
**Fonctionnalité de produit :** Authentification utilisateur

Les utilisateurs qui ont inscrit une application mobile auprès de votre organisation peuvent désormais réinitialiser leur propre mot de passe en approuvant une notification de l’application Microsoft Authenticator ou en entrant un code à partir de leur application mobile ou d’un jeton matériel.

Pour plus d’informations, découvrez le [fonctionnement de ce processus : Réinitialisation de mot de passe en libre-service Azure AD](https://aka.ms/authappsspr). Pour plus d’informations sur l’expérience utilisateur, consultez [Vue d’ensemble de la réinitialisation d’un mot de passe professionnel ou scolaire](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignore le cache partagé MSAL.NET pour les scénarios On-Behalf-Of

**Type :** Résolution  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur

Depuis la version 5.0.0-preview de la bibliothèque d’authentification Azure AD (ADAL.NET), les développeurs d’applications doivent [sérialiser un cache par compte pour les applications web et les API web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). À défaut, certains scénarios utilisant le [flux On-Behalf-Of](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow) avec certains cas d’usage spécifiques de `UserAssertion` peuvent entraîner une élévation de privilège. Pour éviter cette vulnérabilité, ADAL.NET ignore désormais la bibliothèque d’authentification Microsoft pour le cache partagé dotnet (MSAL.NET) dans les scénarios On-Behalf-Of.

Pour plus d’informations sur ce problème, consultez le document qui traite de la [vulnérabilité liée à l’élévation de privilège de la bibliothèque d’authentification Active Directory](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nouvelles applications fédérées disponibles dans la galerie Azure AD App - Août 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En août 2019, nous avons ajouté à notre galerie d’applications les 26 applications suivantes qui prennent en charge la fédération :

[Civic Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport's Inativ Portal (Europe)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Academy Attendance](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [deliver.media™ Portal](https://portal.deliver.media), [Frontline Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD connect](https://www.stashcat.com), [Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Watch by Colors](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB Navigate Strategic Care](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>De nouvelles versions des modules PowerShell AzureAD et AzureADPreview sont disponibles

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Répertoire

De nouvelles mises à jour des modules PowerShell AzureAD et AzureADPreview sont disponibles :

- Un nouveau paramètre `-Filter` a été ajouté au paramètre `Get-AzureADDirectoryRole` dans le module AzureAD. Ce paramètre vous permet de filtrer sur les rôles d’annuaire retournés par l’applet de commande.
- De nouvelles applets de commande ont été ajoutées au module AzureADPreview pour vous aider à définir et assigner des rôles personnalisés dans Azure AD, avec notamment ce qui suit :

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Améliorations apportées à l’interface utilisateur du générateur de règles de groupe dynamique dans le portail Azure

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

Nous avons apporté des améliorations à l’interface utilisateur du générateur de règles de groupe dynamique, disponible dans le portail Azure, pour vous aider à configurer plus facilement une nouvelle règle ou à modifier les règles existantes. Cette amélioration de la conception vous permet de créer des règles avec cinq expressions au lieu d’une seule. Nous avons également mis à jour la liste des propriétés d’appareil pour supprimer les propriétés d’appareil déconseillées.

Pour plus d’informations, consultez gérer les [règles d’appartenance dynamique](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nouvelle autorisation d’application Microsoft Graph disponible pour une utilisation avec des révisions d’accès

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités

Nous avons introduit une nouvelle autorisation d’application Microsoft Graph, `AccessReview.ReadWrite.Membership`, qui permet aux applications de créer et de récupérer automatiquement les révisions d’accès pour les appartenances aux groupes et les affectations d’applications. Cette autorisation peut être utilisée par vos tâches planifiées ou dans le cadre de votre automation, sans nécessiter de contexte d’utilisateur connecté.

Pour plus d’informations, consultez [Exemple de création de révisions d’accès Azure AD avec les autorisations d’application Microsoft Graph sur le blog PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Les journaux d’activité Azure AD sont désormais disponibles pour les instances Government Cloud dans Azure Monitor

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Nous avons le plaisir de vous annoncer que les journaux d’activité Azure AD sont désormais disponibles pour les instances Government Cloud dans Azure Monitor. Vous pouvez désormais envoyer des journaux Azure AD à votre compte de stockage ou à un Event Hub pour les intégrer à vos outils SIEM, tels que [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk) et [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Pour plus d’informations sur la configuration d’Azure Monitor, consultez [Journaux d’activité Azure Active Directory dans Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Mettez à jour vos utilisateurs vers la nouvelle expérience d’informations de sécurité améliorée

**Type :** Fonctionnalité modifiée  
**Catégorie de service :**  Authentifications (connexions)   
**Fonctionnalité de produit :** Authentification utilisateur

Le 25 septembre 2019, nous allons désactiver l’ancienne expérience d’informations de sécurité non améliorée pour l’inscription et la gestion des informations de sécurité utilisateur. Seule la [nouvelle version améliorée](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271) sera disponible. Cela signifie que vos utilisateurs ne seront plus en mesure d’utiliser l’ancienne expérience.

Pour plus d’informations sur l’expérience d’informations de sécurité améliorée, consultez notre [documentation administrateur](https://aka.ms/securityinfodocs)  et notre [documentation utilisateur](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Pour activer cette nouvelle expérience, vous devez :

1. Connectez-vous au portail Azure en tant qu’Administrateur général ou Administrateur d’utilisateurs.

2. Accédez à  **Azure Active Directory > Paramètres utilisateur > Gérer les paramètres des fonctionnalités préliminaires du volet d’accès**.

3. Dans la zone **Les utilisateurs peuvent utiliser les fonctionnalités en préversion pour inscrire et gérer les informations de sécurité - amélioré**, sélectionnez **Sélectionné**, puis choisissez un groupe d’utilisateurs ou **Tout** pour activer cette fonctionnalité pour tous les utilisateurs du locataire.

4. Sous la zone **Users can use preview features for registering and managing security **info**** (les utilisateurs peuvent utiliser les fonctionnalités en préversion pour inscrire et gérer les informations de sécurité), sélectionnez **None** (Aucun).

5. Enregistrez vos paramètres.

    Une fois que vous avez enregistré vos paramètres, vous n’avez plus accès à l’ancienne expérience d’informations de sécurité.

>[!Important]
>Si vous n’effectuez pas ces étapes avant le 25 septembre 2019, votre client Azure Active Directory passera automatiquement à l’expérience améliorée. En cas de questions, veuillez nous contacter à l’adresse registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Les demandes d’authentification utilisant des connexions POST seront plus rigoureusement validées

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Standards

À partir du 2 septembre 2019, les demandes d’authentification à l’aide de la méthode POST seront plus rigoureusement validées par rapport aux normes HTTP. Plus précisément, les espaces et les guillemets doubles (") ne seront plus supprimés des valeurs du formulaire de demande. Ces modifications ne devraient pas bloquer les clients existants et permettront de s’assurer que les demandes envoyées à Azure AD sont gérées de manière fiable à chaque fois.

Pour plus d’informations, consultez les [avis relatifs aux changements cassants d’Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Juillet 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Planifier la modification : Mise à jour du service proxy d’application pour prendre en charge uniquement le protocole TLS 1.2

**Type :** Modification planifiée  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès

Pour vous aider à bénéficier de notre chiffrement le plus puissant, nous allons commencer à restreindre l’accès au service proxy d’application aux protocoles TLS 1.2. Cette limitation sera initialement déployée vers les clients qui utilisent déjà les protocoles TLS 1.2. Elle sera donc sans incidence sur vous. L’abandon complet des protocoles TLS 1.0 et TLS 1.1 sera terminé le 31 août 2019. Les clients qui utilisent toujours TLS 1.0 et TLS 1.1 recevront un préavis pour pouvoir se préparer à ce changement.

Pour maintenir la connexion au service proxy d’application tout au long de cette modification, nous vous recommandons de vous assurer que vos combinaisons client-serveur et navigateur-serveur sont à jour pour utiliser TLS 1.2. Nous vous recommandons également de veiller à inclure tous les systèmes client utilisés par vos employés pour accéder aux applications publiées via la service proxy d’application.

Pour plus d’informations, consultez [Ajouter une application locale pour un accès à distance via le service Proxy d’application d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Planifier la modification : Mises à jour de conception prochainement disponibles pour la Galerie d’applications

**Type :** Modification planifiée  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Authentification unique

De nouvelles modifications d’interface utilisateur vont être apportées à la conception de la zone **Ajouter à partir de la galerie** du panneau **Ajouter une application**. Ces modifications vous aideront à trouver plus facilement vos applications qui prennent en charge l’approvisionnement automatique, OpenID Connect, Security Assertion Markup Language (SAML) et l’authentification unique (SSO) par mot de passe.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Planifier la modification : Suppression de l’adresse IP de serveur MFA de l’adresse IP Office 365

**Type :** Modification planifiée  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités

Nous supprimons l’adresse IP de serveur MFA du [service web d’adresse IP et d’URL Office 365](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Si vous utilisez actuellement ces pages pour mettre à jour les paramètres de votre pare-feu, vous devez également veiller à inclure la liste des adresses IP évoquée dans la section **Configuration requise du serveur Azure Multi-Factor Authentication** de l’article [Prise en main du serveur Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Les jetons réservés aux applications exigent à présent que l’application cliente existe dans le locataire de ressource

**Type :** Résolution  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur

Le 26 juillet 2019, nous avons modifié la manière dont nous fournissons les jetons d’application uniquement par l’[octroi des informations d’identification du client](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Auparavant, les applications pouvaient recevoir des jetons pour appeler d’autres applications, que l’application client se trouve ou non dans le locataire. Nous avons mis à jour ce comportement afin que les ressources à locataire unique, parfois appelées API web, ne puissent être appelées que par des applications clientes existant dans le locataire de ressource.

Si votre application ne se trouve pas dans le locataire de ressource, vous recevez le message d’erreur suivant : `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`. Pour résoudre ce problème, vous devez créer le principal du service de l’application cliente dans le locataire, à l’aide du [point de terminaison de consentement de l’administrateur](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) ou via [PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), ce qui garantit que votre locataire a octroyé la permission d’application pour opérer dans le locataire.

Pour plus d’informations, voir [Quelles sont les nouveautés en matière d’authentification ?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Un consentement existant entre le client et l’API n’est toujours pas requis. Les applications doivent toujours effectuer leurs propres vérifications d’autorisation.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nouvelle connexion sans mot de passe à Azure AD à l’aide de clés de sécurité FIDO2

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur

Les clients Azure AD peuvent désormais définir des stratégies pour gérer les clés de sécurité FIDO2 pour les utilisateurs et groupes de leur organisation. Les utilisateurs finaux peuvent également enregistrer eux-mêmes leurs clés de sécurité, les utiliser pour se connecter à leurs comptes Microsoft sur des sites web à l’aide d’appareils compatibles FIDO, ainsi que pour se connecter à leurs appareils Windows 10 connectés à Azure AD.

Pour plus de détails, voir la section [Activer l’authentification sans mot de passe pour Azure AD (préversion)](/azure/active-directory/authentication/concept-authentication-passwordless) qui traite des informations relatives à l’administrateur, et la section [Configurer des informations de sécurité pour utiliser une clé de sécurité (préversion)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) qui traite des informations relatives à l’utilisateur final.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nouvelles applications fédérées disponibles dans la galerie Azure AD App – Juillet 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En juillet 2019, nous avons ajouté à notre galerie d’applications les 18 nouvelles applications suivantes qui prennent en charge la fédération :

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Bright Pattern Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [Clever Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access for Ethidex Compliance Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [Hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatiser l’approvisionnement de compte d’utilisateur pour ces applications SaaS nouvellement prises en charge

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Monitoring et création de rapports

Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, consultez [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nouvelle balise de service Azure AD Domain Services pour groupe de sécurité réseau

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Services de domaine Azure AD  
**Fonctionnalité de produit :** Services de domaine Azure AD

Si vous en avez assez de gérer de longues listes d’adresses et de plages d’adresses IP, vous pouvez utiliser la nouvelle étiquette de service réseau **AzureActiveDirectoryDomainServices** dans votre groupe de sécurité réseau Azure pour sécuriser le trafic entrant vers votre sous-réseau de réseau virtuel Azure AD Domain Services.

Pour plus d’informations sur cette nouvelle étiquette de service, voir [Groupes de sécurité réseau pour Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nouveaux audits de sécurité pour Azure AD Domain Services (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Services de domaine Azure AD  
**Fonctionnalité de produit :** Services de domaine Azure AD

Nous sommes heureux d’annoncer le mise en production de l’audit de sécurité du service de domaine Azure AD en préversion publique. L’audit de sécurité vous aide à recueillir des informations essentielles sur vos services d’authentification en diffusant en continu les événements d’audit de sécurité vers des ressources ciblées, dont Stockage Azure, des espaces de travail Log Analytics et Azure Event Analytics, en utilisant le portail du service de domaine Azure AD.

Pour plus d’informations, voir [Activer les audits de sécurité pour Azure Active Directory Domain Services (préversion)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Utilisation et enseignements des nouvelles méthodes d’authentification (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Réinitialisation de mot de passe en libre-service  
**Fonctionnalité de produit :** Monitoring et création de rapports

Les nouveaux rapports sur l’utilisation et les enseignements des méthodes d’authentification peuvent vous aider à comprendre comment des fonctionnalités telles que Microsoft Azure Multi-Factor Authentication et la réinitialisation du mot de passe en libre-service sont inscrites et utilisées dans votre organisation, en indiquant notamment le nombre d’utilisateurs inscrits pour chaque fonctionnalité, la fréquence de réinitialisation du mot de passe en libre-service et la méthode utilisée pour la réinitialisation.

Pour plus d’informations, voir [Utilisation et enseignements des méthodes d’authentification (préversion)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nouveaux rapports de sécurité disponibles pour tous les administrateurs Azure AD (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Tous les administrateurs Azure AD peuvent désormais sélectionner la bannière en haut des rapports de sécurité existants, tels que le rapport **Utilisateurs avec indicateur de risque**, pour commencer à utiliser la nouvelle expérience de sécurité, comme indiqué dans les rapports **Utilisateurs à risque** et **Connexions à risque**. Au fil du temps, tous les rapports de sécurité seront migrés des anciennes versions vers les nouvelles versions, les nouveaux rapports offrant les fonctionnalités supplémentaires suivantes :

- Filtrage et tri avancés

- Actions en bloc, telles que le rejet de risque utilisateur

- Confirmation d’entités compromises ou sûres

- État du risque, à savoir : À risque, Ignoré, Corrigé ou Compromission confirmée.

Pour plus d’informations, consultez [rapport Utilisateurs à risque](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report) et [rapport Connexions à risque](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nouveaux audits de sécurité pour Azure AD Domain Services (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Services de domaine Azure AD  
**Fonctionnalité de produit :** Services de domaine Azure AD

Nous sommes heureux d’annoncer le mise en production de l’audit de sécurité du service de domaine Azure AD en préversion publique. L’audit de sécurité vous aide à recueillir des informations essentielles sur vos services d’authentification en diffusant en continu les événements d’audit de sécurité vers des ressources ciblées, dont Stockage Azure, des espaces de travail Log Analytics et Azure Event Analytics, en utilisant le portail du service de domaine Azure AD.

Pour plus d’informations, voir [Activer les audits de sécurité pour Azure Active Directory Domain Services (préversion)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nouvelle fédération directe B2B utilisant SAML/WS-FED (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C

Une fédération directe vous aide à travailler avec des partenaires dont la solution d’identité gérée par l’informatique n’est pas Azure AD, en utilisant des systèmes d’identité qui prennent en charge les normes SAML ou WS-FED. Après que vous avez défini une relation de fédération directe avec un partenaire, tout nouvel utilisateur de ce domaine que vous invitez peut collaborer avec vous à l’aide de son compte organisationnel existant, ce qui contribue à rendre l’expérience utilisateur de vos invités plus transparente.

Pour plus d’informations, voir [Fédération directe avec AD FS et des fournisseurs tiers pour les utilisateurs invités (préversion)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatiser l’approvisionnement de compte d’utilisateur pour ces applications SaaS nouvellement prises en charge

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Monitoring et création de rapports

Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, voir [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nouvelle vérification des noms de groupes en double dans le portail Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

À présent, lorsque vous créez ou mettez à jour un nom de groupe à partir du portail Azure AD, nous vérifions que vous ne dupliquez pas un nom de groupe existant dans votre ressource. Si nous déterminons que le nom est déjà utilisé pour un autre groupe, vous êtes invité à modifier votre nom.

Pour plus d’informations, voir [Gérer des groupes dans le portail Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD prend désormais en charge les paramètres de requête statique dans les URI de réponse (redirection)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur

Les applications Azure AD peuvent désormais inscrire et utiliser des URI de réponse (redirection) avec des paramètres de requête statique (par exemple, `https://contoso.com/oauth2?idp=microsoft`) pour les demandes OAuth 2.0. Le paramètre de requête statique est soumis à la correspondance des chaînes pour les URI de réponse, comme n’importe quelle autre partie de l’URI de réponse. Si aucune chaîne inscrite ne correspond à l’URI de redirection décodée par URL, la demande est rejetée. Si l’URI de réponse est trouvé, la chaîne entière est utilisée pour rediriger l’utilisateur, y compris le paramètre de requête statique.

Les URI de réponse dynamique restent interdits parce qu’ils constituent un risque pour la sécurité, et ne peuvent pas être utilisé pour conserver des informations d’état dans une demande d’authentification. Pour cela, utilisez le paramètre `state`.

Actuellement, les écrans d’inscription d’application du portail Azure bloquent toujours les paramètres de requête. Toutefois, vous pouvez modifier manuellement le manifeste de l’application pour ajouter et tester des paramètres de requête de test dans votre application. Pour plus d’informations, voir [Quelles sont les nouveautés en matière d’authentification ?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Journaux d’activité (API MS Graph) pour Azure AD désormais disponibles via des cmdlets PowerShell

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Nous sommes heureux d’annoncer que des journaux d’activité Azure AD (rapports d’audit et de connexion) sont désormais disponibles via le module Azure AD PowerShell. Auparavant, vous pouviez créer vos propres scripts à l’aide de points de terminaison de l’API MS Graph, et nous avons désormais étendu cette fonctionnalité aux cmdlets PowerShell.

Pour plus d’informations sur l’utilisation de ces cmdlets, voir [Cmdlets Azure AD PowerShell pour la création de rapports.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Contrôles de filtre mis à jour pour les journaux d’audit et de connexion dans Azure AD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Nous avons mis à jour les rapports de journal d’audit et de connexion afin que vous puissiez appliquer différents filtres sans devoir les ajouter en tant que colonnes sur les écrans de rapport. En outre, vous pouvez maintenant décider du nombre de filtres que vous souhaitez afficher à l’écran. Ces mises à jour interagissent pour faciliter la lecture des rapports et adapter leur étendue à vos besoins.

Pour plus d’informations sur ces mises à jour, voir [Filtrer les journaux d’audit](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) et [Filtrer les activités de connexion](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Juin 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nouvelle API riskDetections pour Microsoft Graph (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Nous sommes heureux d’annoncer que la nouvelle API riskDetections pour Microsoft Graph est désormais disponible en préversion publique. Vous pouvez utiliser cette nouvelle API pour afficher une liste des détections de risque au niveau des utilisateurs et des connexions dans le cadre du dispositif de protection des identités de votre organisation. Vous pouvez également utiliser cette API pour interroger plus efficacement vos détections de risque, avec notamment des détails sur le type de détection, son état, son niveau et bien plus encore.

Pour plus d’informations, voir [Documentation de référence sur l’API de détection des risques](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nouvelles applications fédérées disponibles dans la galerie Azure AD App - Juin 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En juin 2019, nous avons ajouté à notre galerie d’applications les 22 applications suivantes qui prennent en charge la fédération :

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager for Oracle Retail Merchandising, Oracle Access Manager for Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatiser l’approvisionnement de compte d’utilisateur pour ces applications SaaS nouvellement prises en charge

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Monitoring et création de rapports

Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, consultez [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Afficher la progression en temps réel du service d’approvisionnement Azure AD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités

Nous avons mis à jour le mécanisme d’approvisionnement d’Azure AD et y avons inclus une nouvelle barre de progression indiquant l’avancement du processus d’approvisionnement des utilisateurs. Cette mise à jour fournit désormais également des informations sur le nombre d’utilisateurs approvisionnés au cours du cycle actuel et à ce jour.

Pour plus d’informations, consultez [Vérifier l’état de l’approvisionnement d’utilisateurs](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>La marque de société s’affiche désormais sur les écrans de déconnexion et d’erreur

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur

Nous avons mis à jour Azure AD afin que la marque de votre société s’affiche sur les écrans de déconnexion et d’erreur, ainsi que sur la page de connexion. Vous n’avez rien à faire pour activer cette fonctionnalité. Azure AD utilise simplement les ressources que vous avez déjà configurées dans la section **Marque de société** du Portail Azure.

Pour plus d’informations sur la configuration de la marque de votre société, consultez [Personnaliser les pages Azure Active Directory de votre organisation](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Le serveur d’authentification multifacteur (MFA) Azure n’est plus disponible pour les nouveaux déploiements

**Type :** Déconseillé  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités

À compter du 1er juillet 2019, Microsoft ne proposera plus le serveur MFA pour les nouveaux déploiements. Les nouveaux clients qui veulent une authentification multifacteur dans leur organisation doivent utiliser la fonction Microsoft Azure Multi-Factor Authentication basée sur le cloud. Les clients ayant activé le serveur MFA avant le 1er juillet ne verront aucune modification. Vous serez toujours en mesure de télécharger la version la plus récente, d’obtenir les mises à jour ultérieures et de générer des informations d’identification d’activation.

Pour plus d’informations, consultez [Prise en main du serveur Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Pour plus d’informations sur la fonction Azure Multi-Factor Authentication basée sur le cloud, consultez [Planification d’un déploiement Azure multi-Factor Authentication basé sur le cloud](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Mai 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Modification du service : Future prise en charge uniquement pour les protocoles TLS 1.2 sur le service Proxy d’application

**Type :** Modification planifiée  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès

Pour fournir un chiffrement de qualité à nos clients, nous autorisons l’accès uniquement aux protocoles TLS 1.2 sur le service Proxy d’application. Cette modification est progressivement déployée chez les clients utilisant déjà exclusivement des protocoles TLS 1.2, vous ne devriez donc voir aucun changement.

L’abandon des protocoles TLS 1.0 et TLS 1.1 se produira sur le 31 août 2019, mais nous vous avertirons de nouveau afin que vous ayez le temps de vous y préparer. Pour vous préparer à cette modification, vérifiez que vos combinaisons client-serveur et navigateur-serveur, y compris les clients dont vos utilisateurs e servent pour accéder aux applications publiées via le Proxy d’application, sont mises à jour pour utiliser le protocole TLS 1.2 pour maintenir la connexion au service Proxy d’application. Pour plus d’informations, consultez [Ajouter une application locale pour un accès à distance via le service Proxy d’application d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Utiliser le rapport d’utilisation et d’insights pour afficher les données de connexion liées aux applications

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Monitoring et création de rapports

Vous pouvez maintenant utiliser le rapport d’utilisation et d’insights, situé dans la section **Applications d’entreprise** du Portail Azure, pour obtenir une vue orientée application de vos données de connexion, notamment des informations sur :

- Les applications principalement utilisées dans votre organisation

- Les applications présentant le plus grand nombre d’échecs de connexion

- Les erreurs de connexion principales pour chaque application

Pour plus d’informations sur cette fonctionnalité, consultez [Rapport d’utilisation et d’insights dans le Portail Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report).

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatiser l’approvisionnement de vos utilisateurs dans les applications cloud avec Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Monitoring et création de rapports

Suivez ces nouveaux didacticiels pour utiliser le service d’approvisionnement Azure AD pour automatiser la création, la suppression et la mise à jour des comptes utilisateur pour les applications cloud suivantes :

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Vous pouvez également suivre ce nouveau [didacticiel Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), qui fournit des informations sur l’approvisionnement d’objets de groupe.

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, consultez [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Identity Secure Score est désormais disponible dans Azure AD (disponibilité générale)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** N/A  
**Fonctionnalité de produit :** Protection et sécurité des identités

Vous pouvez désormais surveiller et améliorer votre stratégie de sécurité relative aux identités avec la fonctionnalité Identity Secure Score d’Azure AD. Cette fonctionnalité utilise un tableau de bord unique qui offre les avantages suivants :

- Évaluer votre méthode de sécurité relative aux identités de façon objective, avec un score compris entre 1 et 223

- Planifier les améliorations à apporter à la sécurité des identités

- Évaluer la réussite de vos améliorations en matière de sécurité

Pour plus d’informations sur la fonctionnalité Identity Security Score, consultez [Qu’est-ce que le degré de sécurisation Identity Secure Score dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nouvelle expérience d’inscription des applications désormais disponible (disponibilité générale)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Expérience de développement

La nouvelle expérience de la fonction [Inscriptions d’applications](https://aka.ms/appregistrations) est désormais en disponibilité générale. Cette nouvelle expérience inclut toutes les fonctionnalités clés du Portail Azure et du portail d’inscription des applications que vous connaissez déjà, et les améliore :

- **Meilleure gestion des applications.** Au lieu de voir vos applications sur des portails différents, vous pouvez maintenant voir toutes vos applications au même emplacement.

- **Inscription simplifiée des applications.** Avec une meilleure expérience de navigation et une sélection des autorisations repensée, il est désormais plus facile d’inscrire et de gérer vos applications.

- **Informations plus détaillées.** Vous trouverez des informations encore plus détaillées sur votre application, notamment des guides de démarrage rapide et bien plus encore.

Pour plus d’informations, consultez [Plateforme d’identités Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) et le billet de blog annonçant qu’une [nouvelle expérience d’inscription des applications est désormais disponible](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/). (annonce de blog).

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nouvelles fonctionnalités disponibles dans l’API Risky Users pour la protection des identités

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Nous sommes heureux d’annoncer que vous pouvez maintenant utiliser l’API Risky Users pour récupérer l’historique des risques liés aux utilisateurs, ignorer les utilisateurs à risque et confirmer l’état de compromission des utilisateurs. Cette modification vous permet de mettre à jour plus efficacement l’état des risques liés à vos utilisateurs et de comprendre l’historique des risques associé.

Pour plus d’informations, consultez la [documentation de référence sur l’API Risky Users](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Mai 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En mai 2019, nous avons ajouté à notre galerie d’applications les 21 applications suivantes qui prennent en charge la fédération :

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Expériences de création et de gestion des groupes améliorée dans le Portail Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

Nous avons apporté des améliorations concernant la gestion des groupes dans le Portail Azure AD. Ces améliorations permettent aux administrateurs de mieux gérer les listes de groupes, les listes de membres et de fournir des options de création supplémentaires.

Les améliorations incluent :

- Filtrage de base par type d’appartenance et par type de groupe.

- Ajout de nouvelles colonnes, telles que de la source et l’adresse de messagerie.

- Possibilité de sélectionner plusieurs listes de groupes, membres et propriétaires pour faciliter la suppression.

- Possibilité de choisir une adresse de messagerie et d’ajouter des propriétaires lors de la création de groupe.

Pour plus d’informations, consultez [Créer un groupe de base et ajouter des membres avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configurer une stratégie d’affectation de noms pour les groupes Office 365 dans le Portail Azure AD (disponibilité générale)

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

Les administrateurs peuvent désormais configurer une stratégie d’affectation de noms pour les groupes Office 365, depuis le Portail Azure AD. Cette modification permet d’appliquer des conventions d’affectation de noms cohérentes aux groupes Office 365 créés ou modifiés par les utilisateurs de votre organisation.

Vous pouvez appliquer une stratégie de nommage aux groupes Office 365 de deux manières différentes :

- Définir des préfixes ou des suffixes, qui sont automatiquement ajoutés à un nom de groupe.

- Charger un jeu personnalisé de mots bloqués pour votre organisation, qui ne sont pas autorisés dans les noms de groupe (par exemple « PDG, paie, RH »).

Pour plus d’informations, consultez [Appliquer une stratégie de nommage pour les groupes Office 365 dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Les points de terminaison d’API Microsoft Graph sont désormais disponibles pour les journaux d’activité Azure AD (disponibilité générale)

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Nous sommes heureux d’annoncer la disponibilité générale de la prise en charge des points de terminaison d’API Microsoft Graph pour les journaux d’activité Azure AD. Avec cette version, vous pouvez maintenant utiliser la version 1.0 des API des journaux d’audit et des journaux de connexion Azure AD.

Pour plus d’informations, consultez la [vue d’ensemble de l’API des journaux d’audit Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Les administrateurs peuvent désormais utiliser l’accès conditionnel pour le processus d’inscription combinée (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités  

Les administrateurs peuvent désormais créer des stratégies d’accès conditionnel pour une utilisation dans la page d’inscription combinée. Cela inclut l’application de stratégies pour autoriser l’inscription si :

- Les utilisateurs se trouvent sur un réseau approuvé.

- Les utilisateurs ont un niveau faible de risque à la connexion.

- Les utilisateurs utilisent un appareil géré.

- Les utilisateurs acceptent les conditions d’utilisation de l’organisation.

Pour plus d’informations sur l’accès conditionnel et la réinitialisation de mot de passe, consultez le billet de blog portant sur [l’accès conditionnel pour l’expérience d’inscription combinée Azure AD avec authentification multifacteur et réinitialisation du mot de passe](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Pour plus d’informations sur les stratégies d’accès conditionnel pour le processus d’inscription combinée, consultez [Stratégies d’accès conditionnel pour l’inscription combinée](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Pour plus d’informations sur les conditions d’utilisation d’Azure AD, consultez [Fonctionnalité Conditions d’utilisation d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Avril 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Une nouvelle fonction de détection des informations sur les menaces Azure AD est désormais disponible dans Azure AD Identity Protection

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Azure AD Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Une fonction de détection des informations sur les menaces Azure AD est désormais intégrée à la fonctionnalité mise à jour Azure AD Identity Protection. Cette nouvelle fonction permet de détecter une activité utilisateur inhabituelle pour un utilisateur ou une activité spécifique qui correspond à des modèles d’attaque connus selon les sources d’information sur les menaces internes et externes de Microsoft.

Pour plus d’informations sur la version actualisée d’Azure AD Identity Protection, consultez le billet de blog concernant les [quatre principales améliorations apportées à Azure AD Identity Protection désormais disponibles en préversion publique](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) et l’article [Présentation d’Azure Active Directory Identity Protection (actualisée)](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2). (article). Pour plus d’informations sur la détection d’informations sur les menaces Azure AD, consultez l’article [Détections de risques Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence).

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>La gestion des droits d’utilisation Azure AD est désormais disponible (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gouvernance des identités  
**Fonctionnalité de produit :** Gouvernance des identités

La gestion des droits d’utilisation Azure AD, désormais en préversion publique, permet aux clients de déléguer la gestion des packages d’accès. La façon dont les employés et les partenaires commerciaux peuvent demander un accès, qui doit l’approuver, et la durée de validité de ce dernier sont déterminés par ce biais. Les packages d’accès permettent de gérer l’appartenance aux groupes Azure AD et Office 365, les affectations de rôles dans les applications d’entreprise et les affectations de rôles pour les sites SharePoint Online. Pour en savoir plus sur la gestion des droits d’utilisation, consultez la [vue d’ensemble de la gestion des droits d’utilisation Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Pour en savoir plus sur l’éventail des fonctionnalités d’Azure AD Identity Governance, y compris la fonction Privileged Identity Management, les révisions d’accès et les conditions d’utilisation, consultez [Qu’est-ce qu’Azure AD Identity Governance ?](../governance/identity-governance-overview.md)

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configurer une stratégie d’affectation de noms pour les groupes Office 365 dans le Portail Azure AD (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

Les administrateurs peuvent désormais configurer une stratégie d’affectation de noms pour les groupes Office 365, depuis le Portail Azure AD. Cette modification permet d’appliquer des conventions d’affectation de noms cohérentes aux groupes Office 365 créés ou modifiés par les utilisateurs de votre organisation.

Vous pouvez appliquer une stratégie de nommage aux groupes Office 365 de deux manières différentes :

- Définir des préfixes ou des suffixes, qui sont automatiquement ajoutés à un nom de groupe.

- Charger un jeu personnalisé de mots bloqués pour votre organisation, qui ne sont pas autorisés dans les noms de groupe (par exemple « PDG, paie, RH »).

Pour plus d’informations, consultez [Appliquer une stratégie de nommage pour les groupes Office 365 dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Les journaux d’activité Azure AD sont désormais disponibles dans Azure Monitor (disponibilité générale)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Nous avons tenu compte de vos retours concernant la visualisation des journaux d’activité Azure AD, et nous introduisons donc une nouvelle fonctionnalité Insights dans Log Analytics. Cette fonctionnalité vous permet d’obtenir des informations sur vos ressources Azure AD à l’aide de nos modèles interactifs, appelés classeurs. Ces classeurs préintégrés peuvent fournir des détails sur les applications ou les utilisateurs :

- **Connexions.** Fournit des détails sur les applications et les utilisateurs, y compris l’emplacement de connexion, le système d’exploitation ou le client et la version de navigateur en cours d’utilisation, ainsi que le nombre de connexions ayant réussi ou échoué.

- **Authentification héritée et accès conditionnel.** Fournit des détails sur les applications et les utilisateurs faisant appel à une authentification héritée, y compris l’authentification multifacteur déclenchée par les stratégies d’accès conditionnel, les applications utilisant des stratégies d’accès conditionnel, et ainsi de suite.

- **Analyse des échecs de connexion.** Vous aide à déterminer si vos erreurs de connexion sont causées par une action de l’utilisateur, des problèmes de stratégie ou votre infrastructure.

- **Rapports personnalisés.** Vous pouvez créer ou modifier des classeurs existants afin de personnaliser la fonctionnalité Insights pour votre organisation.

Pour plus d’informations, découvrez [comment utiliser des classeurs Azure Monitor pour créer des rapports Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Avril 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En avril 2019, nous avons ajouté à la galerie d’applications les 21 applications suivantes qui prennent en charge la fédération :

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (Role-based SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nouvelle option de fréquence des révisions d’accès et sélection de plusieurs rôles

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités

Les nouvelles mises à jour des révisions d’accès Azure AD vous permettent d’effectuer les actions suivantes :

- Modifier la fréquence de vos révisions d’accès et la définir sur une fréquence **semestrielle**, en plus des options existantes de fréquence hebdomadaire, mensuelle, trimestrielle et annuelle.

- Sélectionnez plusieurs rôles de ressource Azure et Azure AD lors de la création d’une révision d’accès unique. Dans ce cas, tous les rôles sont configurés avec les mêmes paramètres et tous les réviseurs sont informés en même temps.

Pour plus d’informations sur la création d’une révision d’accès, consultez [Créer une révision d’accès de groupes ou d’applications dans Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Les systèmes d’alerte par e-mail d’Azure AD Connect sont en transition, affichant un nouvel expéditeur pour certains clients

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** AD Sync  
**Fonctionnalité de produit :** Plateforme

Azure AD Connect est en passe de modifier ses systèmes d’alerte par e-mail, et certains clients peuvent potentiellement voir s’afficher un nouvel expéditeur. Pour résoudre ce problème, vous devez ajouter `azure-noreply@microsoft.com` à liste verte de votre organisation ou vous ne pourrez pas continuer à recevoir des alertes importantes d’Office 365, d’Azure ou de vos services de synchronisation.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Les modifications de suffixe UPN fonctionnent à présent entre domaines fédérés dans Azure AD Connect

**Type :** Résolution  
**Catégorie de service :** AD Sync  
**Fonctionnalité de produit :** Plateforme

Vous pouvez maintenant modifier avec succès le suffixe UPN d’un utilisateur d’un domaine fédéré vers un autre domaine dans Azure AD Connect. Avec ce correctif, vous ne devriez plus rencontrer le message d’erreur FederatedDomainChangeError pendant le cycle de synchronisation ni recevoir de notification électronique indiquant « Impossible de mettre à jour cet objet dans Azure Active Directory, car l’attribut [FederatedUser.UserPrincipalName] n’est pas valide. Mettez à jour la valeur dans vos services d’annuaire locaux ».

Pour plus d’informations, consultez [Résolution des erreurs lors de la synchronisation](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Sécurité accrue avec la stratégie d’accès conditionnel basée sur la protection des applications dans Azure AD (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités

L’accès conditionnel basé sur la protection des applications est désormais disponible avec la stratégie **Exiger une protection des applications**. Cette nouvelle stratégie contribue à augmenter la sécurité de votre organisation en évitant les situations suivantes :

- Que les utilisateurs accèdent aux applications sans licence Microsoft Intune.

- Que les utilisateurs ne soient pas en mesure d’obtenir une stratégie de protection des applications Microsoft Intune.

- Que les utilisateurs accèdent à des applications sans stratégie de protection des applications Microsoft Intune configurée.

Pour plus d’informations, consultez [Comment exiger une stratégie de protection des applications pour l’accès conditionnel aux applications cloud](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nouvelle prise en charge de l’authentification unique Azure AD et de l’accès conditionnel à Microsoft Edge (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités

Nous avons amélioré notre support Azure AD pour Microsoft Edge, notamment en fournissant une nouvelle prise en charge de l’accès conditionnel et de l’authentification unique Azure AD. Si vous avez déjà utilisé Microsoft Intune Managed Browser, vous pouvez désormais utiliser Microsoft Edge à la place.

Pour plus d’informations sur la configuration et la gestion de vos appareils et applications à l’aide de l’accès conditionnel, consultez [Demander des appareils managés pour l’accès conditionnel à des applications cloud](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) et [Demander des applications clientes approuvées pour l’accès conditionnel à des applications cloud](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Pour plus d’informations sur la gestion de l’accès avec Microsoft Edge à l’aide de Microsoft Intune, consultez [Gérer l’accès à Internet à l’aide d’un navigateur Microsoft Intune protégé par une stratégie](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Mars 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Identity Experience Framework et la prise en charge de stratégie personnalisée dans Azure Active Directory B2C sont désormais disponibles (GA)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

Vous pouvez désormais créer des stratégies personnalisées dans Azure AD B2C, notamment les tâches suivantes, qui sont prises en charge à l’échelle et dans le cadre de notre Accord de niveau de service (SLA) Azure :

- Créer et charger des parcours utilisateur d’authentification personnalisés à l’aide de stratégies personnalisées.

- Décrire des parcours utilisateur étape par étape comme des échanges entre des fournisseurs de revendications.

- Définir le branchement conditionnel dans des parcours utilisateur.

- Transformer et mapper les revendications pour une utilisation dans les décisions et communications en temps réel.

- Utiliser des services compatibles avec l’API REST dans vos parcours utilisateur d’authentification personnalisés. Par exemple, avec les fournisseurs e-mail, les CRM et les systèmes d’autorisation propriétaires.

- Se fédérer avec des fournisseurs d’identité conformes au protocole OpenIDConnect. Par exemple, avec Azure AD multilocataire, les fournisseurs de comptes de réseaux sociaux ou les fournisseurs de vérification en deux étapes.

Pour plus d'informations sur la création de stratégies personnalisées, consultez [Notes pour les développeurs sur les stratégies personnalisées dans Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) et lisez le [billet de blog d’Alex Simon, notamment les études de cas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Mars 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En mars 2019, nous avons ajouté à notre galerie d’applications les 14 applications suivantes qui prennent en charge la fédération :

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Explanation-Based Auditing System](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool Performance Matters](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nouveaux connecteurs d’approvisionnement Zscaler et Atlassian dans la galerie d’applications Azure AD - Mars 2019

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce

Automatisez la création, la mise à jour et la suppression des comptes utilisateur pour les applications suivantes :

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler Two](https://aka.ms/ZscalerTwoProvisioning), [Zscaler Three](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, consultez [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Restaurer et gérer vos groupes Office 365 supprimés dans le Portail Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration

Vous pouvez désormais afficher et gérer vos groupes Office 365 supprimés à partir du Portail Azure AD. Cette modification vous permet de voir quels groupes sont disponibles pour la restauration, ainsi que de supprimer définitivement tous les groupes qui ne sont pas nécessaires à votre organisation.

Pour plus d’informations, consultez [Restaurer les groupes expirés ou supprimés](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>L’authentification unique est désormais disponible pour les applications locales sécurisées par SAML Azure AD via le proxy d’application (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès

Vous pouvez désormais fournir une expérience d’authentification unique (SSO) pour les applications locales authentifiées par SAML, ainsi que l’accès à distance à ces applications via le proxy d’application. Pour plus d’informations sur la configuration de l’authentification unique SAML avec vos applications locales, consultez [Authentification unique SAML pour les applications locales avec le proxy d’application (préversion)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Les applications clientes dans des boucles de demande seront interrompues pour améliorer la fiabilité et l’expérience utilisateur

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur

Les applications clientes peuvent émettre de manière incorrecte des centaines de demandes de connexion identiques sur une courte période. Ces demandes, qu’elles soient réussies ou non, contribuent à une mauvaise expérience utilisateur et à des charges de travail accrues pour l’IDP, augmentant la latence pour tous les utilisateurs et réduisant la disponibilité de l’IDP.

Cette mise à jour envoie une erreur `invalid_grant` : `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` aux applications clientes qui émettent des demandes dupliquées plusieurs fois pendant une courte période, au-delà de l’étendue de fonctionnement normal. Les applications clientes qui rencontrent ce problème doivent afficher une invite interactive demandant à l’utilisateur de se reconnecter. Pour plus d’informations sur cette modification et sur la façon de résoudre votre application si elle rencontre cette erreur, consultez [Nouveautés pour l’authentification](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nouvelle expérience utilisateur Journaux d’audit désormais disponible

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Nous avons créé une nouvelle page **Journaux d’audit** Azure AD pour aider à améliorer la lisibilité et la manière dont vous recherchez vos informations. Pour afficher la nouvelle page **Journaux d’audit**, sélectionnez **Journaux d’audit** dans la section **Activité** d’Azure AD.

![Nouvelle page Journaux d’audit, avec exemple d’informations](media/whats-new/audit-logs-page.png)

Pour plus d’informations sur la nouvelle page **Journaux d’audit**, consultez [Rapports d’activité d’audit dans le Portail Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nouveaux avertissements et conseils pour empêcher le verrouillage accidentel de l’administrateur à partir de stratégies d’accès conditionnel mal configurées

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités

Pour éviter que les administrateurs ne bloquent leur accès à leurs propres locataires par le biais de stratégies d’accès conditionnel mal configurées, nous avons créé de nouveaux avertissements et des instructions mises à jour dans le Portail Azure. Pour plus d’informations sur les nouvelles instructions, consultez [Que sont les dépendances de service dans l’accès conditionnel Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Expériences des conditions d’utilisation de l’utilisateur final améliorées sur les appareils mobiles

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Gouvernance

Nous avons mis à jour nos expériences de conditions d’utilisation existantes pour aider à améliorer la manière dont vous révisez et acceptez les conditions d’utilisation sur un appareil mobile. Vous pouvez désormais effectuer un zoom avant et arrière, revenir en arrière, télécharger les informations et sélectionner des liens hypertexte. Pour plus d’informations sur les conditions d’utilisation mises à jour, consultez [Fonctionnalité Conditions d’utilisation Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nouvelle expérience de téléchargement des journaux d’activité Azure AD disponible

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Vous pouvez désormais télécharger de grandes quantités de journaux d’activité directement à partir du portail Azure. Cette mise à jour vous permet de :

- Télécharger jusqu’à 250 000 lignes.

- Recevoir une notification une fois le téléchargement effectué.

- Personnaliser votre nom de fichier.

- Déterminer votre format de sortie, JSON ou CSV.

Pour plus d’informations sur cette fonctionnalité, voir [Démarrage rapide : Télécharger un rapport d’audit à l’aide du Portail Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report).

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Changement cassant : mises à jour de l’évaluation de la condition par Exchange ActiveSync (EAS)

**Type :** Modification planifiée  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Contrôle d’accès

Nous mettons à jour la manière dont Exchange ActiveSync (EAS) évalue les conditions suivantes :

- Emplacement de l’utilisateur, en fonction du pays, de la région ou de l’adresse IP

- Risque à la connexion

- Plateforme d’appareil.

Si vous avez déjà utilisé ces conditions dans vos stratégies d’accès conditionnel, sachez que le comportement de la condition peut changer. Par exemple, si vous avez précédemment utilisé la condition de l’emplacement de l’utilisateur dans une stratégie, la stratégie pourrait maintenant être ignorée en fonction de l’emplacement de votre utilisateur.

---
