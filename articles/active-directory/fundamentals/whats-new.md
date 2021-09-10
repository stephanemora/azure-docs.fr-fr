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
ms.date: 7/30/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c9b954776c33fedc3a770bc0aab0185ad595aff
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219745"
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
## <a name="july-2021"></a>Juillet 2021

### <a name="new-google-sign-in-integration-for-azure-ad-b2c-and-b2b-self-service-sign-up-and-invited-external-users-will-stop-working-starting-july-12-2021"></a>La nouvelle intégration de la connexion Google pour l’inscription en libre-service et les utilisateurs externes invités d’Azure AD B2C et B2B cessera de fonctionner à partir du 12 juillet 2021

**Type :** Modification planifiée  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 

Nous avons annoncé précédemment que [l’exception concernant les vues web incorporées de l’authentification Gmail expirerait au cours du second semestre 2021](https://www.yammer.com/cepartners/threads/1188371962232832).

Le 7 juillet 2021, nous avons appris de Google que certaines de ces restrictions s’appliqueront à partir du **12 juillet 2021**. Les clients Azure AD B2B et B2C qui configurent une nouvelle connexion Google ID dans leurs applications métier ou personnalisées pour inviter des utilisateurs externes ou activer l’inscription en libre-service verront les restrictions appliquées immédiatement. Par conséquent, les utilisateurs finaux verront apparaître un écran d’erreur qui bloquera leur connexion Gmail si l’authentification n’est pas déplacée vers une vue web du système. Pour plus d’informations, consultez la documentation dont le lien figure ci-dessous. 

La plupart des applications utilisent la vue web du système par défaut, et ne seront pas concernées par ce changement Cela ne concerne que les clients qui utilisent des vues web incorporées (le paramètre no défini par défaut). Nous conseillons aux clients de déplacer l’authentification de leur application vers les navigateurs système, avant de créer de nouvelles intégrations Google. Pour savoir comment passer aux navigateurs système pour les authentifications Gmail, veuillez lire la section Interface utilisateur web incorporée ou système dans la documentation [Utilisation de navigateurs web (MSAL.NET)](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui). Tous les kits SDK MSAL utilisent la vue web système par défaut. [Plus d’informations](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support)

---

### <a name="google-sign-in-on-embedded-web-views-expiring-september-30-2021"></a>Connexion Google sur les vues web incorporées expirant le 30 septembre 2021

**Type :** Modification planifiée  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 

Il y a environ deux mois, nous avons annoncé que l’exception concernant les vues web incorporées pour l’authentification Gmail expirerait au cours du second semestre 2021. 

Récemment, Google a précisé que la date serait le **30 septembre 2021**. 

Le lancement mondial de cette exception débutera le 30 septembre 2021. Les invités Azure AD B2B se connectant avec leurs comptes Gmail seront désormais invités à saisir un code dans une fenêtre de navigateur distincte pour terminer la connexion sur les clients mobiles et bureau de Microsoft Teams. Cela s’applique aux invités qui ont été invités ainsi qu’aux invités qui se sont connectés en utilisant l’inscription en libre-service. 

Les clients Azure AD B2C qui ont configuré des authentifications Gmail en vue web incorporée dans leurs applications métier/personnalisées ou qui ont déjà des intégrations Google ne pourront plus laisser leurs utilisateurs se connecter avec des comptes Gmail. Pour pallier ce problème, veillez à modifier vos applications afin d’utiliser le navigateur système pour la connexion. Pour plus d’informations, lisez la section Interface utilisateur web incorporée ou système dans la documentation [Utilisation de navigateurs web (MSAL.NET)](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui). Tous les kits SDK MSAL utilisent la vue web système par défaut. 

Étant donné que le flux de connexion à l’appareil commencera à être déployé le 30 septembre 2021, il est probable qu’il ne soit pas encore déployé dans votre région (auquel cas, vos utilisateurs finaux seront confrontés à l’écran d’erreur présenté dans la documentation jusqu’à ce que le déploiement soit terminé dans votre région). 

Pour plus d’informations sur les scénarios concernés connus ainsi que sur l’expérience à laquelle vos utilisateurs peuvent s’attendre, lisez [Ajouter Google comme fournisseur d’identité pour les utilisateurs invités B2B](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support).

---

### <a name="bug-fixes-in-my-apps"></a>Correctifs de bogues dans Mes applications

**Type :** Résolution  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 
- Auparavant, la présence de la bannière recommandant l’utilisation de collections faisait défiler le contenu derrière l’en-tête. Ce problème a été résolu. 
- Auparavant, il y avait un autre problème lors de l’ajout d’applications à une collection, où l’ordre des applications dans la collection Toutes les applications était réorganisé de manière aléatoire. Ce problème a également été résolu. 

Pour plus d’informations sur Mes applications, lisez [Se connecter et démarrer des applications à partir du portail Mes applications](../user-help/my-apps-portal-end-user-access.md).

---

### <a name="public-preview----application-authentication-method-policies"></a>Préversion publique : Stratégies de méthode d’authentification des applications

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** MS Graph  
**Fonctionnalité de produit :** Expérience de développement
 
Des stratégies de méthode d’authentification des applications dans MS Graph qui permettent aux administrateurs informatiques d’appliquer une durée de vie sur les informations d’identification, les secrets et les mots de passe des applications ou de bloquer complètement l’utilisation des secrets. Les stratégies peuvent être appliquées à l’ensemble d’un locataire en tant que configuration par défaut et elles peuvent être étendues à des applications ou des principaux de service spécifiques. [Plus d’informations](/graph/api/resources/policy-overview?view=graph-rest-1.0)
 
---

### <a name="public-preview----authentication-methods-nudge-to-download-microsoft-authenticator"></a>Préversion publique : Méthodes d’authentification : incitation à télécharger Microsoft Authenticator

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Application Microsoft Authenticator  
**Fonctionnalité de produit :** Authentification utilisateur
 
La stratégie d’incitation Authenticator aide les administrateurs à renforcer la sécurité de leur organisation en incitant les utilisateurs à adopter l’application Microsoft Authenticator. Avant cette fonctionnalité, un administrateur n’avait aucun moyen de pousser ses utilisateurs à configurer l’application Authenticator. 

L’incitation permet à l’administrateur d’inclure ou d’exclure des utilisateurs et des groupes de la démarche afin de garantir une adoption en douceur dans l’ensemble de l’organisation. [En savoir plus](../authentication/how-to-nudge-authenticator-app.md)
 
---

### <a name="public-preview----separation-of-duties-check"></a>Préversion publique : Vérification de la séparation des tâches 

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Dans la gestion des droits d’utilisation d’Azure AD, un administrateur peut définir qu’un package d’accès est incompatible avec un autre package d’accès ou avec un groupe.  Les utilisateurs dont les appartenances sont incompatibles seront alors incapables de demander un accès supplémentaire. [Plus d’informations](../governance/entitlement-management-access-package-request-policy.md#prevent-requests-from-users-with-incompatible-access-preview)
 
---

### <a name="public-preview----identity-protection-logs-in-log-analytics-storage-accounts-and-event-hubs"></a>Préversion publique : Journaux Identity Protection dans Log Analytics, Comptes de stockage et Event Hubs

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Vous pouvez désormais envoyer les journaux des utilisateurs à risque et des détections de risque à Azure Monitor, Comptes de stockage ou Log Analytics à l’aide des paramètres de diagnostic dans le panneau Azure AD. [Plus d’informations](../identity-protection/howto-export-risk-data.md)
 
---

### <a name="public-preview----application-proxy-api-addition-for-backend-ssl-certificate-validation"></a>Préversion publique : Ajout de l’API Proxy d’application pour la validation des certificats SSL en back-end

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès
 
Le type de ressource onPremisesPublishing comprend désormais la propriété « isBackendCertificateValidationEnabled » qui indique si la validation des certificat SSL en back-end est activée pour l’application. Pour toutes les nouvelles applications Proxy d’application, la propriété est définie sur true par défaut. Pour toutes les applications existantes, la propriété est définie sur false. Pour plus d’informations, lisez l’API du [type de ressource onPremisesPublishing](/graph/api/resources/onpremisespublishing?view=graph-rest-beta).
 
---

### <a name="general-availability---improved-authenticator-setup-experience-for-add-azure-ad-account-in-microsoft-authenticator-app-by-directly-signing-into-the-app"></a>Disponibilité générale : Amélioration de l’expérience de configuration d’Authenticator pour ajouter un compte Azure AD dans l’application Microsoft Authenticator en se connectant directement à l’application

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Application Microsoft Authenticator  
**Fonctionnalité de produit :** Authentification utilisateur
 
Les utilisateurs peuvent désormais utiliser leurs méthodes d’authentification existantes pour se connecter directement à l’application Microsoft Authenticator afin de configurer leurs informations d’identification. Les utilisateurs n’ont plus besoin de scanner un code QR et peuvent utiliser un passe d’accès temporaire ou un mot de passe + SMS (ou une autre méthode d’authentification) pour configurer leur compte dans l’application Authenticator.

Cela améliore le processus d’approvisionnement des informations d’identification de l’utilisateur pour l’application Microsoft Authenticator et offre à l’utilisateur final une méthode en libre-service pour approvisionner l’application. [Plus d’informations](../user-help/user-help-auth-app-add-work-school-account.md#sign-in-with-your-credentials)
 
---

### <a name="general-availability---set-manager-as-reviewer-in-azure-ad-entitlement-management-access-packages"></a>Disponibilité générale : Définir le gestionnaire comme réviseur dans les packages d’accès de gestion des droits d’utilisation d’Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Les packages d’accès dans la gestion des droits d’utilisation Azure AD prennent désormais en charge la définition du responsable de l’utilisateur comme réviseur pour les révisions d’accès régulières. [Plus d’informations](../governance/entitlement-management-access-reviews-create.md)

---

### <a name="general-availability---enable-external-users-to-self-service-sign-up-in-aad-using-msa-accounts"></a>Disponibilité générale : Permettre aux utilisateurs externes de s’inscrire en libre-service dans AAD à l’aide de comptes MSA

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Les utilisateurs peuvent désormais permettre aux utilisateurs externes de se connecter en libre-service dans Azure Active Directory à l’aide de comptes Microsoft. [Plus d’informations](../external-identities/microsoft-account.md)
 
---
 
### <a name="general-availability---external-identities-self-service-sign-up-with-email-one-time-passcode"></a>Disponibilité générale : Inscription en libre-service des identités externes avec un code secret à usage unique envoyé par e-mail

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 

Les utilisateurs peuvent désormais permettre aux utilisateurs externes de s’inscrire en libre-service dans Azure Active Directory à l’aide de leur adresse e-mail et de leur code secret à usage unique. [Plus d’informations](../external-identities/one-time-passcode.md)
 
---

### <a name="general-availability---anomalous-token"></a>Disponibilité générale : Jeton anormal

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
La détection de jetons anormaux est désormais disponible dans Identity Protection. Cette fonctionnalité peut détecter la présence de caractéristiques anormales dans le jeton, telles que la durée d’activité et l’authentification à partir d’une adresse IP inconnue. [Plus d’informations](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)
 
---

### <a name="general-availability---register-or-join-devices-in-conditional-access"></a>Disponibilité générale : Inscrire ou joindre des appareils en accès conditionnel

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
L’action utilisateur Inscrire ou joindre des appareils en accès conditionnel est désormais en disponibilité générale. Cette action utilisateur vous permet de contrôler les stratégies d’authentification multifacteur pour l’inscription d’appareils Azure AD. 

Actuellement, cette action utilisateur vous permet uniquement d’activer MFA en tant que contrôle lorsque les utilisateurs inscrivent ou joignent des appareils à Azure AD. Les autres contrôles qui ne s’appliquent pas à l’inscription d’appareils Azure AD, ou qui en dépendent, continuent d’être désactivés avec cette action utilisateur. [Plus d’informations](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) 

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2021"></a>Nouveaux connecteurs d’approvisionnement dans la galerie d’applications Azure AD – Juillet 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 
Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Clebex](../saas-apps/clebex-provisioning-tutorial.md)
- [Exium](../saas-apps/exium-provisioning-tutorial.md)
- [SoSafe](../saas-apps/sosafe-provisioning-tutorial.md)
- [Talentech](../saas-apps/talentech-provisioning-tutorial.md)
- [Thrive LXP](../saas-apps/thrive-lxp-provisioning-tutorial.md)
- [Vonage](../saas-apps/vonage-provisioning-tutorial.md)
- [Compresser](../saas-apps/zip-provisioning-tutorial.md)
- [TimeClock 365](../saas-apps/timeclock-365-provisioning-tutorial.md)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes d’utilisateur, lisez [Automatiser l’attribution d’utilisateurs à des applications SaaS avec Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="changes-to-security-and-microsoft-365-group-settings-in-azure-portal"></a>Modifications des paramètres de sécurité et de groupe Microsoft 365 dans Portail Azure

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Répertoire
 

Dans le passé, les utilisateurs pouvaient créer des groupes de sécurité et des groupes Microsoft 365 dans le portail Azure. Désormais, les utilisateurs auront la possibilité de créer des groupes sur le portail Azure, PowerShell et des API. Les clients sont tenus de vérifier et de mettre à jour les nouveaux paramètres qui ont été configurés pour leur organisation. [En savoir plus](../enterprise-users/groups-self-service-management.md#group-settings)
 
---

### <a name="all-apps-collection-has-been-renamed-to-apps"></a>La collection « Toutes les applications » a été renommée en « Applications »

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 
Dans le portail Mes applications, la collection qui s’appelait « Toutes les applications » a été renommée pour s’appeler « Applications ». Le produit évoluant, « Applications » est un nom plus approprié pour cette collection par défaut. [Plus d’informations](../manage-apps/my-apps-deployment-plan.md#plan-the-user-experience)
 
---
 
## <a name="june-2021"></a>Juin 2021

### <a name="context-panes-to-display-risk-details-in-identity-protection-reports"></a>Volets contextuels pour afficher les détails des risques dans les rapports Identity Protection

**Type :** Modification planifiée  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Pour les rapports sur les utilisateurs à risque, les connexions risquées et les détections de risque dans Identity Protection, les détails du risque d’une entrée sélectionnée seront affichés dans un volet contextuel apparaissant à droite de la page Juillet 2021. Ce changement n’a d’incidence que sur l’interface utilisateur et n’affecte aucune des fonctionnalités existantes. Pour en savoir plus sur le fonctionnement de ces fonctionnalités, reportez-vous à [Guide pratique : Examiner les risques](../identity-protection/howto-identity-protection-investigate-risk.md).
 
---

### <a name="public-preview----create-azure-ad-access-reviews-of-service-principals-that-are-assigned-to-privileged-roles"></a>Préversion publique : Créer des révisions d’accès Azure AD des principaux de service affectés à des rôles privilégiés

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités
 
 Vous pouvez utiliser les révisions d’accès Azure AD pour passer en revue l’accès du principal de service aux rôles privilégiés Azure AD et aux rôles de ressource Azure. [Plus d’informations](../privileged-identity-management/pim-how-to-start-security-review.md#open-access-reviews)
 
---

### <a name="public-preview----group-owners-in-azure-ad-can-create-and-manage-azure-ad-access-reviews-for-their-groups"></a>Préversion publique : Les propriétaires de groupes dans Azure AD peuvent créer et gérer des révisions d’accès Azure AD pour leurs groupes

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités
 
Les propriétaires de groupes dans Azure AD peuvent désormais créer et gérer des révisions d’accès Azure AD sur leurs groupes. Cette fonctionnalité peut être activée par les administrateurs clients via les paramètres de révision d’accès Azure AD et est désactivée par défaut. [Plus d’informations](../governance/create-access-review.md#allow--group-owners-to-create-and-manage-access-reviews-preview)
 
---

### <a name="public-preview----customers-can-scope-access-reviews-of-privileged-roles-to-just-users-with-eligible-or-active-access"></a>Préversion publique : Les clients peuvent limiter les révisions d’accès de rôles privilégiés aux seuls utilisateurs ayant un accès actif ou admissible

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités
 
Lorsque les administrateurs créent des révisions d’accès des attributions de rôles privilégiés, ils peuvent limiter les révisions uniquement aux utilisateurs attribués admissibles ou uniquement aux utilisateurs attribués actifs. [Plus d’informations](../privileged-identity-management/pim-how-to-start-security-review.md)
 
---

### <a name="public-preview----microsoft-graph-apis-for-mobility-mdmmam-management-policies"></a>Préversion publique : Stratégies de gestion des API Microsoft Graph pour la mobilité (MDM/MAM)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Gestion du cycle de vie des appareils
 
La prise en charge de Microsoft Graph pour la configuration de la mobilité (MDM/MAM) dans Azure AD est en préversion publique. Les administrateurs peuvent configurer l’étendue de l’utilisateur et les URL des applications MDM comme Intune à l’aide de Microsoft Graph v1.0. Pour plus d’informations, consultez [Type de ressource mobilityManagementPolicy](/graph/api/resources/mobilitymanagementpolicy?view=graph-rest-beta).

---

### <a name="general-availability---custom-questions-in-access-package-request-flow-in-azure-active-directory-entitlement-management"></a>Disponibilité générale : Questions personnalisées dans le flux de demandes de package d’accès dans la gestion des droits d’utilisation Azure Active Directory

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
La gestion des droits d’utilisation Azure AD prend désormais en charge la création de questions personnalisées dans le flux de demandes de package d’accès. Cette fonctionnalité vous permet de configurer des questions personnalisées dans la stratégie de package d’accès. Ces questions sont affichées aux demandeurs qui peuvent saisir leurs réponses dans le cadre du processus de demande d’accès. Ces réponses seront affichées aux approbateurs, leur donnant des informations utiles qui leur permettront de prendre de meilleures décisions concernant la demande d’accès. [Plus d’informations](../governance/entitlement-management-access-package-create.md#add-requestor-information-to-an-access-package)

---

### <a name="general-availability---multi-geo-sharepoint-sites-as-resources-in-entitlement-management-access-packages"></a>Disponibilité générale : Sites SharePoint avec multigéographie comme ressources dans les packages d’accès de la gestion des droits d’utilisation

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Les packages d’accès dans la gestion des droits d’utilisation prennent désormais en charge les sites SharePoint avec multigéographie pour les clients qui utilisent les capacités de multigéographie de SharePoint Online. [Plus d’informations](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site)
 
---

### <a name="general-availability---knowledge-admin-and-knowledge-manager-built-in-roles"></a>Disponibilité générale : Rôles intégrés Administrateur des connaissances et Manager des connaissances

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 
Deux nouveaux rôles, Administrateur des connaissances et Manager des connaissances, sont désormais en disponibilité générale.

- Les utilisateurs dotés du rôle Administrateur des connaissances ont un accès complet à tous les paramètres de connaissances organisationnelles dans le centre d’administration de Microsoft 365. Ils peuvent créer et gérer du contenu, comme des rubriques et des acronymes. De plus, ces utilisateurs peuvent créer des centres de contenu, superviser l’intégrité du service et créer des demandes de service. [En savoir plus](../roles/permissions-reference.md#knowledge-administrator)
- Les utilisateurs dotés du rôle Manager des connaissances peuvent créer et gérer du contenu et sont principalement responsables de la qualité et de la structure des connaissances. Ils disposent de tous les droits sur les actions de gestion des rubriques pour confirmer une rubrique, approuver des modifications ou supprimer une rubrique. Ce rôle peut également gérer les taxonomies dans le cadre de l'outil de gestion du magasin de termes et créer des centres de contenu. [Plus d’informations](../roles/permissions-reference.md#knowledge-manager)

---

### <a name="general-availability---cloud-app-security-administrator-built-in-role"></a>Disponibilité générale : Rôle intégré Administrateur Cloud App Security

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 
 Les utilisateurs dotés de ce rôle disposent d’autorisations complètes dans Cloud App Security. Ils peuvent ajouter des administrateurs, ajouter des stratégies et des paramètres Microsoft Cloud App Security (MCAS), charger des journaux et effectuer des actions de gouvernance. [Plus d’informations](../roles/permissions-reference.md#cloud-app-security-administrator)
 
---

### <a name="general-availability---windows-update-deployment-administrator"></a>Disponibilité générale : Administrateur de déploiements Windows Update

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 

 Les utilisateurs dotés de ce rôle peuvent créer et gérer tous les aspects des déploiements Windows Update par le biais du service de déploiement Windows Update pour Entreprise. Le service de déploiement permet aux utilisateurs de définir des paramètres pour savoir quand et comment les mises à jour sont déployées. De plus, les utilisateurs peuvent spécifier quelles mises à jour sont proposées aux groupes d’appareils dans leur locataire. Il permet également aux utilisateurs de surveiller la progression des mises à jour. [Plus d’informations](../roles/permissions-reference.md#windows-update-deployment-administrator)
 
---

### <a name="general-availability---multi-camera-support-for-windows-hello"></a>Disponibilité générale : Prise en charge de plusieurs caméras pour Windows Hello

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Désormais, avec la mise à jour Windows 10 21H1, Windows Hello prend en charge plusieurs caméras. La mise à jour comprend les valeurs par défaut permettant d’utiliser la caméra externe lorsque des caméras intégrées et externes sont présentes. [Plus d’informations](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/it-tools-to-support-windows-10-version-21h1/ba-p/2365103)

---
 
### <a name="general-availability---access-reviews-ms-graph-apis-now-in-v10"></a>Disponibilité générale : Les API MS Graph de révision d’accès maintenant en v1.0

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités
 
Les API MS Graph de révision d’accès Azure Active Directory sont désormais en v1.0 et prennent en charge des fonctionnalités de révision d’accès entièrement configurables. [Plus d’informations](/graph/api/resources/accessreviewsv2-root?view=graph-rest-1.0)
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---june-2021"></a>Nouveaux connecteurs d’approvisionnement dans la galerie d’applications Azure AD – Juin 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 
Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [askSpoke](../saas-apps/askspoke-provisioning-tutorial.md)
- [Cloud Academy - SSO](../saas-apps/cloud-academy-sso-provisioning-tutorial.md)
- [CheckProof](../saas-apps/checkproof-provisioning-tutorial.md)
- [GoLinks](../saas-apps/golinks-provisioning-tutorial.md)
- [Holmes Cloud](../saas-apps/holmes-cloud-provisioning-tutorial.md)
- [H5mag](../saas-apps/h5mag-provisioning-tutorial.md)
- [LimbleCMMS](../saas-apps/limblecmms-provisioning-tutorial.md)
- [LogMeIn](../saas-apps/logmein-provisioning-tutorial.md)
- [SECURE DELIVER](../saas-apps/secure-deliver-provisioning-tutorial.md)
- [Sigma Computing](../saas-apps/sigma-computing-provisioning-tutorial.md)
- [Smallstep SSH](../saas-apps/smallstep-ssh-provisioning-tutorial.md)
- [Tribeloo](../saas-apps/tribeloo-provisioning-tutorial.md)
- [Twingate](../saas-apps/twingate-provisioning-tutorial.md)

Pour plus d’informations, consultez [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2021"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD – Juin 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En juin 2021, nous avons ajouté les 42 applications suivantes à notre galerie d’applications avec prise en charge de la fédération :

[Taksel](https://app.taksel.it/admin/integrations), [IDrive360](../saas-apps/idrive360-tutorial.md), [VIDA](../saas-apps/vida-tutorial.md), [ProProfs Classroom](../saas-apps/proprofs-classroom-tutorial.md), [WAN-Sign](../saas-apps/wan-sign-tutorial.md), [Citrix Cloud SAML SSO](../saas-apps/citrix-cloud-saml-sso-tutorial.md), [Fabric](../saas-apps/fabric-tutorial.md), [DssAD](https://cloudlicensing.deepseedsolutions.com/), [RICOH Creative Collaboration RICC](https://www.ricoh-europe.com/products/software-apps/collaboration-board-software/ricc/), [Styleflow](../saas-apps/styleflow-tutorial.md), [Chaos](https://accounts.chaosgroup.com/corporate_login), [Traced Connector](https://control.traced.app/signup), [Squarespace](https://account.squarespace.com/org/azure), [MX3 Diagnostics Connector](https://mx3www.playground.dynuddns.com/signin-oidc), [Ten Spot](https://tenspot.co/api/v1/sso/azure/login/), [Finvari](../saas-apps/finvari-tutorial.md), [Mobile4ERP](https://play.google.com/store/apps/details?id=com.negevsoft.mobile4erp), [WalkMe US OpenID Connect](https://www.walkme.com/), [Neustar UltraDNS](../saas-apps/neustar-ultradns-tutorial.md), [cloudtamer.io](../saas-apps/cloudtamer-io-tutorial.md), [A Cloud Guru](../saas-apps/a-cloud-guru-tutorial.md), [PetroVue](../saas-apps/petrovue-tutorial.md), [Postman](../saas-apps/postman-tutorial.md), [ReadCube Papers](../saas-apps/readcube-papers-tutorial.md), [Peklostroj](https://app.peklostroj.cz/), [SynCloud](https://onboard.syncloud.io/), [Polymerhq.io](https://www.polymerhq.io/), [Bonos](../saas-apps/bonos-tutorial.md), [Astra Schedule](../saas-apps/astra-schedule-tutorial.md), [Draup](../saas-apps/draup-inc-tutorial.md), [Inc](../saas-apps/draup-inc-tutorial.md), [Applied Mental Health](../saas-apps/applied-mental-health-tutorial.md), [iHASCO Training](../saas-apps/ihasco-training-tutorial.md), [Nexsure](../saas-apps/nexsure-tutorial.md), [XEOX](https://login.xeox.com/), [Plandisc](https://create.plandisc.com/account/logon), [foundU](../saas-apps/foundu-tutorial.md), [Standard for Success Accreditation](../saas-apps/standard-for-success-accreditation-tutorial.md), [Penji Teams](https://web.penjiapp.com/), [CheckPoint Infinity Portal](../saas-apps/checkpoint-infinity-portal-tutorial.md), [Teamgo](../saas-apps/teamgo-tutorial.md), [Hopsworks.ai](../saas-apps/hopsworks-ai-tutorial.md), [HoloMeeting 2](https://backend2.holomeeting.io/)

Pour accéder à la documentation de ces applications, cliquez https://aka.ms/AppsTutorial

Pour référencer votre application dans la Galerie d’applications Azure AD, lisez les informations détaillées ici : https://aka.ms/AzureADAppRequest
 
---

### <a name="device-code-flow-now-includes-an-app-verification-prompt"></a>Le flux de code d’appareil comprend désormais une invite de vérification d’application

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Le [flux de code d’appareil](../develop/v2-oauth2-device-code.md) a été mis à jour pour inclure une invite supplémentaire pour l’utilisateur. Lors de la connexion, l’utilisateur verra une invite lui demandant de valider l’application à laquelle il se connecte.  Cette invite permet de s’assurer qu’il n’est pas victime d’une attaque de hameçonnage. [Plus d’informations](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt)
 
---

### <a name="user-last-sign-in-date-and-time-is-now-available-on-azure-portal"></a>La date et l’heure de la dernière connexion de l’utilisateur sont désormais disponibles sur Portail Azure

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** User Management  
**Fonctionnalité de produit :** User Management
 
Vous pouvez désormais afficher la date et l’heure de la dernière connexion de vos utilisateurs sur le portail Azure. Ces informations sont disponibles pour chaque utilisateur sur la page de son profil. Ces informations vous aident à identifier les utilisateurs inactifs et à gérer efficacement les événements à risque. [Plus d’informations](./active-directory-users-profile-azure-portal.md?context=%2fazure%2factive-directory%2fenterprise-users%2fcontext%2fugr-context)
 
---

### <a name="mim-bhold-suite-impact-of-end-of-support-for-microsoft-silverlight"></a>Impact de la fin du support de Microsoft Silverlight sur la suite MIM BHOLD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Microsoft Identity Manager  
**Fonctionnalité de produit :** Gouvernance des identités
 
Microsoft Silverlight atteindra la fin de son support le 12 octobre 2021. Ce changement n’a d’impact que sur les clients qui utilisent la suite Microsoft BHOLD et n’en a aucun sur les autres scénarios de Microsoft Identity Manager. Pour plus d’informations, consultez [Fin du support de Silverlight](https://support.microsoft.com/windows/silverlight-end-of-support-0a3be3c7-bead-e203-2dfd-74f0a64f1788).  

Les utilisateurs qui n’ont pas installé Microsoft Silverlight dans leur navigateur ne peuvent pas utiliser les modules de la suite BHOLD qui nécessitent Silverlight. Cela inclut le générateur de modèles BHOLD, l’intégration en libre-service FIM BHOLD et l’analyse BHOLD.  Les clients ayant un déploiement BHOLD existant d’un ou plusieurs de ces modules doivent planifier la désinstallation de ces modules de leurs ordinateurs serveurs BHOLD d’ici octobre 2021. Ils doivent également planifier la désinstallation de Silverlight de tous les ordinateurs des utilisateurs qui interagissaient auparavant avec ce déploiement BHOLD.
 
---

### <a name="my-experiences-end-of-support-for-internet-explorer-11"></a>Expériences My* : Fin du support d’Internet Explorer 11

**Type :** Déprécié  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 

Microsoft 365 et d’autres applications cesseront de prendre en charge Internet Explorer 11 le 21 août 2021, y compris les expériences My*. Les My* accessibles via Internet Explorer ne recevront pas de correctifs de bogues ni de mises à jour, ce qui peut entraîner des problèmes. Ces dates sont déterminées par l’équipe Edge et sont sujettes à modification. [Plus d’informations](https://blogs.windows.com/windowsexperience/2021/05/19/the-future-of-internet-explorer-on-windows-10-is-in-microsoft-edge/)
 
---

### <a name="planned-deprecation---malware-linked-ip-address-detection-in-identity-protection"></a>Dépréciation planifiée : Détection Adresse IP liée à un programme malveillant dans Identity Protection

**Type :** Déprécié  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
À partir du 1er octobre 2021, Azure AD Identity Protection ne générera plus la détection « Adresse IP liée à un programme malveillant ». Aucune action n’est requise et les clients resteront protégés par les autres détections fournies par Identity Protection. Pour en savoir plus sur les stratégies de protection, consultez [Stratégies d’Identity Protection](../identity-protection/concept-identity-protection-policies.md).
 
---
 
## <a name="may-2021"></a>Mai 2021

### <a name="public-preview----azure-ad-verifiable-credentials"></a>Préversion publique : Justificatifs vérifiables Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Authentification utilisateur
 
Les clients d’Azure AD peuvent désormais concevoir et émettre facilement des justificatifs vérifiables. Les justificatifs vérifiables peuvent être utilisés comme preuve d’emploi, d’éducation ou de toute autre affirmation tout en respectant la confidentialité. Validez numériquement tout élément d’information relatif aux personnes et activités. [Plus d’informations](../verifiable-credentials/index.yml)

---

### <a name="public-preview----device-code-flow-now-includes-an-app-verification-prompt"></a>Préversion publique : Le flux de code d’appareil comprend désormais une invite de vérification d’application

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** authentification utilisateur  
**Fonctionnalité produit :** authentifications (connexions)
 
Pour améliorer la sécurité, le [flux de code d’appareil](../develop/v2-oauth2-device-code.md) a été mis à jour afin d’ajouter une autre invite destinée à confirmer que l’utilisateur se connecte bien à l’application prévue. Le lancement doit commencer en juin et se terminer pour le 30 juin.

Pour empêcher les attaques par hameçonnage lorsqu’un attaquant tente de se connecter à une application malveillante, l’invite suivante est ajoutée : « Essayez-vous de vous connecter à [nom d’affichage de l’application] ? ». Ce message s’affiche pour tous les utilisateurs qui se connectent à l’aide du flux de code d’appareil. Pour plus de sécurité, il ne peut être ni supprimé ni contourné. [Plus d’informations](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt)

---

### <a name="public-preview----build-and-test-expressions-for-user-provisioning"></a>Préversion publique : Générer et tester des expressions pour l’attribution d’utilisateurs

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Le générateur d’expressions vous permet de créer et de tester des expressions, sans avoir à attendre le cycle de synchronisation complet. [Plus d’informations](../app-provisioning/functions-for-customizing-application-data.md) 

---

### <a name="public-preview----enhanced-audit-logs-for-conditional-access-policy-changes"></a>Préversion publique : Journaux d’audit améliorés pour les modifications de stratégie d’accès conditionnel

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Comprendre les modifications apportées à vos stratégies au fil du temps constitue un aspect important de la gestion de l’accès conditionnel. Les modifications de stratégie peuvent entraîner des interruptions pour vos utilisateurs finaux et dans un tel contexte, il apparaît essentiel de conserver un journal des modifications et de permettre aux administrateurs de restaurer les versions de stratégie précédentes. 

En plus d’indiquer qui a modifié la stratégie et à quel moment, les journaux d’audit contiendront désormais la valeur des propriétés modifiées. Ce changement offre aux administrateurs une meilleure visibilité sur les affectations, les conditions ou les contrôles modifiés. Pour revenir à une version antérieure d’une stratégie, vous pouvez copier la représentation JSON de l’ancienne version et utiliser les API d’accès conditionnel pour rétablir la stratégie à son état précédent. [Plus d’informations](../conditional-access/concept-conditional-access-policies.md)

---

### <a name="public-preview----sign-in-logs-include-authentication-methods-used-during-sign-in"></a>Préversion publique : Les journaux de connexion incluent les méthodes d’authentification utilisées lors de la connexion

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Monitoring et création de rapports
 

Les administrateurs peuvent désormais voir les étapes séquentielles effectuées par les utilisateurs pour se connecter, y compris les méthodes d’authentification utilisées lors de la connexion. 

Pour accéder à ces informations, accédez aux journaux de connexion Azure AD, sélectionnez une connexion, puis accédez à l’onglet Détails de la méthode d’authentification. Ici, nous avons inclus des informations telles que la méthode utilisée, les détails qui s’y rapportent (numéro de téléphone, nom du téléphone, par exemple), la condition d’authentification satisfaite et les détails des résultats. [Plus d’informations](../reports-monitoring/concept-sign-ins.md)

---

### <a name="public-preview----pim-adds-support-for-abac-conditions-in-azure-storage-roles"></a>Préversion publique : PIM ajoute la prise en charge des conditions ABAC aux rôles Stockage Azure

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Privileged Identity Management  
**Fonctionnalité de produit :** Privileged Identity Management
 
En plus de la préversion publique du contrôle d’accès en fonction des attributs pour un rôle RBAC Azure spécifique, vous pouvez également ajouter des conditions ABAC à Privileged Identity Management pour vos affectations éligibles. [Plus d’informations](../../role-based-access-control/conditions-overview.md#conditions-and-privileged-identity-management-pim)

---

### <a name="general-availability---conditional-access-and-identity-protection-reports-in-b2c"></a>Disponibilité générale : rapports d’accès conditionnel et de protection des identités dans B2C

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

B2C prend désormais en charge l’accès conditionnel et la protection des identités pour les utilisateurs et les applications B2C (Entreprise-client). Cela permet aux clients de protéger leurs utilisateurs avec des contrôles d’accès granulaires basés sur les risques et les emplacements. Grâce à ces fonctionnalités, les clients peuvent maintenant examiner les signaux et créer une stratégie visant à offrir davantage de sécurité et d’accès à vos clients. [Plus d’informations](../../active-directory-b2c/conditional-access-identity-protection-overview.md)

---

### <a name="general-availability---kmsi-and-password-reset-now-in-next-generation-of-user-flows"></a>Disponibilité générale : KMSI et réinitialisation du mot de passe dans la nouvelle génération de flux d’utilisateurs

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

La nouvelle génération de flux d’utilisateurs B2C prend désormais en charge la fonctionnalité [KMSI (Maintenir la connexion)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) et la réinitialisation de mot de passe. La fonctionnalité KMSI permet aux clients d’étendre la durée de vie de la session pour les utilisateurs de leurs applications web et natives à l’aide d’un cookie persistant. Cette fonctionnalité maintient la session active même lorsque l’utilisateur ferme et ouvre à nouveau le navigateur. La session est révoquée lorsque l’utilisateur se déconnecte. La réinitialisation de mot de passe permet aux utilisateurs de réinitialiser leur mot de passe à partir du lien « Mot de passe oublié ? ». Cela permet aussi à l’administrateur de forcer la réinitialisation du mot de passe expiré de l’utilisateur dans l’annuaire Azure AD B2C. [Plus d’informations](../../active-directory-b2c/add-password-reset-policy.md?pivots=b2c-user-flow)
 
---

### <a name="general-availability---new-log-analytics-workbook-application-role-assignment-activity"></a>Disponibilité générale : nouveau classeur Log Analytics pour l’activité d’attribution de rôle d’application

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Un nouveau classeur a été ajouté pour exposer les événements d’audit relatifs aux modifications d’attribution de rôle d’application. [Plus d’informations](../governance/entitlement-management-logs-and-reporting.md)

---

### <a name="general-availability---next-generation-azure-ad-b2c-user-flows"></a>Disponibilité générale : nouvelle génération de flux d’utilisateurs Azure AD B2C 

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C
 
La nouvelle expérience de flux d'utilisateurs simplifiée offre la parité des fonctionnalités avec les fonctionnalités d’évaluation et est le foyer de toutes les nouvelles fonctionnalités. Les utilisateurs peuvent activer de nouvelles fonctionnalités au sein du même flux d’utilisateur, ce qui réduira la nécessité de créer plusieurs versions à chaque nouvelle mise en production de fonctionnalité. La nouvelle expérience utilisateur conviviale simplifie également la sélection et la création de flux d’utilisateurs. Pour plus d’informations sur l’utilisation de cette fonctionnalité, consultez [, Créer des flux d’utilisateurs dans Azure AD B2C](../../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-user-flow). [Plus d’informations](../../active-directory-b2c/user-flow-versions.md)

---

### <a name="general-availability---azure-active-directory-threat-intelligence-for-sign-in-risk"></a>Disponibilité générale : renseignement sur les menaces Azure Active Directory pour le risque de connexion

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Cette nouvelle détection sert de méthode ad hoc pour permettre à nos équipes de sécurité de vous avertir et de protéger vos utilisateurs en augmentant leur risque de session à un niveau élevé lorsque nous observons une attaque. La détection marquera également les connexions associées comme étant risquées. Cette détection suit le renseignement sur les menaces Azure Active Directory en matière de détection de risque d’utilisateur pour fournir une couverture complète des différentes attaques observées par les équipes de sécurité Microsoft. [Plus d’informations](../identity-protection/concept-identity-protection-risks.md#user-linked-detections)
 
---

### <a name="general-availability---conditional-access-named-locations-improvements"></a>Disponibilité générale : améliorations des emplacements nommés de l’accès conditionnel

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
La prise en charge d’IPv6 dans les emplacements nommés est désormais mise à la disposition générale. Les mises à jour comprennent :

- Ajout de la fonctionnalité permettant de définir des plages d’adresses IPv6
- Augmentation de la limite du nombre d’emplacements nommés de 90 à 195
- Augmentation de la limite des plages d’adresses IP par emplacement nommé de 1200 à 2000
- Ajout de fonctionnalités pour rechercher et trier des emplacements nommés et filtrer par type d’emplacement et type d’approbation
- Ajout d’emplacements nommés auxquels une connexion appartenait dans les journaux de connexion
 
En outre, pour empêcher les administrateurs de définir des emplacements nommés de manière problématique, des vérifications supplémentaires ont été ajoutées afin de réduire les risques de configuration incorrecte. [Plus d’informations](../conditional-access/location-condition.md)

---

### <a name="general-availability---restricted-guest-access-permissions-in-azure-ad"></a>Disponibilité générale : Autorisations d’accès invité restreintes dans Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** User Management  
**Fonctionnalité de produit :** Répertoire
 
Les autorisations au niveau répertoire pour les utilisateurs invités ont été mises à jour. Ces autorisations permettent aux administrateurs d’imposer des restrictions et contrôles supplémentaires à l’accès utilisateur invité externe.

Les administrateurs peuvent désormais ajouter davantage de restrictions à l’accès d’invités externes aux informations d’appartenance et de profil des utilisateurs et des groupes. En outre, les clients peuvent gérer l’accès des utilisateurs externes à grande échelle en masquant les appartenances de groupe, notamment en empêchant les utilisateurs invités de voir les appartenances des groupes dans lesquels ils se trouvent. Pour plus d’informations, consultez [Restreindre les autorisations d’accès invité dans Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2021"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Mai 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [AuditBoard](../saas-apps/auditboard-provisioning-tutorial.md)
- [Cisco Umbrella User Management](../saas-apps/cisco-umbrella-user-management-provisioning-tutorial.md)
- [Insite LMS](../saas-apps/insite-lms-provisioning-tutorial.md)
- [kpifire](../saas-apps/kpifire-provisioning-tutorial.md)
- [UNIFI](../saas-apps/unifi-provisioning-tutorial.md)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, consultez [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2021"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Mai 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En mai 2021, nous avons ajouté les 29 applications suivantes à notre galerie d’applications avec prise en charge de la fédération :

[InviteDesk](https://app.invitedesk.com/login), [Webrecruit ATS](https://id-test.webrecruit.co.uk/), [Workshop](../saas-apps/workshop-tutorial.md), [Gravity Sketch](https://landingpad.me/), [JustLogin](../saas-apps/justlogin-tutorial.md), [Custellence](https://custellence.com/sso/), [WEVO](https://hello.wevoconversion.com/login), [AppTec360 MDM](https://www.apptec360.com/ms/autopilot.html), [Filemail](https://www.filemail.com/login),[Ardoq](../saas-apps/ardoq-tutorial.md), [Leadfamly](../saas-apps/leadfamly-tutorial.md), [Documo](../saas-apps/documo-tutorial.md), [Autodesk SSO](../saas-apps/autodesk-sso-tutorial.md), [Check Point Harmony Connect](../saas-apps/check-point-harmony-connect-tutorial.md), [BrightHire](https://app.brighthire.ai/), [Rescana](../saas-apps/rescana-tutorial.md), [Bluewhale](https://cloud.bluewhale.dk/), [AlacrityLaw](../saas-apps/alacritylaw-tutorial.md), [Equisolve](../saas-apps/equisolve-tutorial.md), [Zip](../saas-apps/zip-tutorial.md), [Cognician](../saas-apps/cognician-tutorial.md), [Acra](https://www.acrasuite.com/), [VaultMe](https://app.vaultme.com/#/signIn), [TAP App Security](../saas-apps/tap-app-security-tutorial.md), [Cavelo Office365 Cloud Connector](https://dashboard.prod.cavelodata.com/), [Clebex](../saas-apps/clebex-tutorial.md), [Banyan Command Center](../saas-apps/banyan-command-center-tutorial.md), [Check Point Remote Access VPN](../saas-apps/check-point-remote-access-vpn-tutorial.md), [LogMeIn](../saas-apps/logmein-tutorial.md)

La documentation de toutes ces applications est disponible ici https://aka.ms/AppsTutorial

Pour référencer votre application dans la galerie d’applications Azure AD, lisez les informations détaillées ici : https://aka.ms/AzureADAppRequest

---

### <a name="improved-conditional-access-messaging-for-android-and-ios"></a>Amélioration de la messagerie d’accès conditionnel pour Android et iOS

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion et inscription des appareils  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 

Nous avons mis à jour la formulation sur l’écran d’accès conditionnel présenté aux utilisateurs qui ne peuvent pas accéder aux ressources de l’entreprise. Ils seront bloqués jusqu’à ce qu’ils inscrivent leur appareil auprès de la gestion des périphériques mobiles. Ces améliorations s’appliquent aux plateformes Android et iOS/iPadOS. Les modifications sont les suivantes :

- « Aidez-nous à protéger votre appareil » a été remplacé par « Configurez votre appareil pour obtenir l’accès »
- « Votre connexion a réussi mais votre administrateur que votre appareil soit géré par Microsoft pour accéder à cette ressource. » a été remplacé par « [Nom de l’organisation] requiert que vous sécurisiez cet appareil pour accéder à la messagerie, aux fichiers et aux données de [Nom de l’organisation]. » 
- « Inscrivez-vous maintenant » a été remplacé par « Continuer »

Les informations présentes dans [Inscrivez votre appareil d’entreprise Android](https://support.microsoft.com/topic/enroll-your-android-enterprise-device-d661c82d-fa28-5dfd-b711-6dff41ae83bb) sont obsolètes.

---

### <a name="azure-information-protection-service-will-begin-asking-for-consent"></a>Le service Azure Information Protection commence par demander le consentement.

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Le service Azure Information Protection connecte les utilisateurs au locataire qui a chiffré le document afin de permettre l’accès au document.  À compter de juin, Azure AD invitera l’utilisateur à donner son consentement lorsque cet accès est accordé à plusieurs organisations.  Cela permet à l’utilisateur de comprendre que l’organisation propriétaire du document collectera des informations le concernant dans le cadre de l’accès au document. [Plus d’informations](/azure/information-protection/known-issues#sharing-external-doc-types-across-tenants)
 
---

### <a name="provisioning-logs-schema-change-impacting-graph-api-and-azure-monitor-integration"></a>Modification du schéma des journaux d’approvisionnement impactant l’intégration de l’API Graph et d’Azure Monitor

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Monitoring et création de rapports
 

Les attributs « Action » et « statusInfo » seront remplacés par « provisioningAction » et « provisoiningStatusInfo ». Mettez à jour tous les scripts que vous avez créés à l’aide de l’[API Graph de journaux d’approvisionnement](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true) ou des [intégrations Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md). 
 

---

### <a name="new-arm-api-to-manage-pim-for-azure-resources-and-azure-ad-roles"></a>Nouvelle API ARM pour gérer PIM pour les ressources Azure et les rôles Azure AD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Privileged Identity Management  
**Fonctionnalité de produit :** Privileged Identity Management
 
Une version mise à jour de l’API PIM pour le rôle Ressource Azure et le rôle Azure AD a été publiée. L’API PIM pour le rôle Ressource Azure est maintenant publiée dans le cadre de l’API ARM, qui s’aligne sur l’API de gestion des rôles pour l’attribution de rôle Azure standard. L’API PIM pour les rôles Azure AD est également publiée sous l’API Graph, alignée avec les API unifiedRoleManagement. Voici quelques-uns des avantages de ce changement :

- Alignement de l’API PIM avec des objets dans ARM et Graph pour la gestion des rôles, réduisant ainsi la nécessité d’appeler PIM pour intégrer de nouvelles ressources Azure. 
- Toutes les ressources Azure fonctionnent automatiquement avec la nouvelle API PIM.
- Réduction de la nécessité d’appeler PIM pour définir un rôle ou conserver un ID de ressource PIM
- Prise en charge des autorisations d’API d’application uniquement dans PIM pour les rôles Azure AD et Ressource Azure

La version précédente de l’API PIM sous /privilegedaccess continuera de fonctionner, mais nous vous recommandons d’opter dès à présent pour cette nouvelle API. [Plus d’informations](../privileged-identity-management/pim-apis.md)
 
---

### <a name="revision-of-roles-in-azure-ad-entitlement-management"></a>Révision des rôles dans la gestion des droits d’utilisation Azure AD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Rôles  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Un nouveau rôle, Administrateur de gouvernance des identités, a récemment été introduit. Ce rôle remplacement le rôle Administrateur d’utilisateurs pour la gestion des catalogues et des packages d’accès dans la gestion des droits d’utilisation Azure AD. Si vous avez attribué aux administrateurs le rôle Administrateur d’utilisateurs ou si vous leur demandez d’activer ce rôle pour gérer les packages d’accès dans la gestion des droits d’utilisation Azure AD, privilégiez plutôt le rôle Administrateur de gouvernance des identités. Le rôle Administrateur d’utilisateurs ne fournira plus de droits d’administration relatifs aux catalogues ou packages d’accès. [Plus d’informations](../governance/identity-governance-overview.md#appendix---least-privileged-roles-for-managing-in-identity-governance-features)

---

## <a name="april-2021"></a>Avril 2021

### <a name="bug-fixed---azure-ad-will-no-longer-double-encode-the-state-parameter-in-responses"></a>Résolution de bogue : Azure AD n’encodera plus en double le paramètre d’état dans les réponses.

**Type :** Résolution  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Azure AD a identifié, testé et publié un correctif pour un bogue dans la réponse `/authorize` à une application cliente.  Azure AD n’a pas pu correctement encoder l’URL du paramètre `state` lors du renvoi de réponses au client.  Ainsi, une application cliente peut rejeter la requête en raison d’une incompatibilité des paramètres d’état. [Plus d’informations](../develop/reference-breaking-changes.md#bug-fix-azure-ad-will-no-longer-url-encode-the-state-parameter-twice) 

---

### <a name="users-can-only-create-security-and-microsoft-365-groups-in-azure-portal-being-deprecated"></a>Les utilisateurs peuvent uniquement créer des groupes de sécurité et Microsoft 365 dans le portail Azure (déconseillé)

**Type :** Modification planifiée  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Répertoire
 
Les utilisateurs ne sont plus tenus de créer des groupes de sécurité et Microsoft 365 uniquement dans le portail Azure. Le nouveau paramètre permettra aux utilisateurs de créer des groupes de sécurité dans le portail Azure, PowerShell et l’API. Les utilisateurs doivent vérifier et mettre à jour le nouveau paramètre. [Plus d’informations](../enterprise-users/groups-self-service-management.md)

---

### <a name="public-preview----external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>Préversion publique : Inscription en libre service des identités externes dans AAD à l’aide des comptes Code secret à usage unique envoyé par e-mail

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Les utilisateurs externes peuvent désormais utiliser des comptes e-mail à code secret à usage unique pour s’inscrire ou se connecter aux applications internes Azure AD et métier. [Plus d’informations](../external-identities/one-time-passcode.md)

---

### <a name="general-availability---external-identities-self-service-sign-up"></a>Disponibilité générale : Inscription en libre service des identités externes

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
L’inscription en libre-service des utilisateurs externes est désormais en disponibilité générale. Cette nouvelle fonctionnalité permet aux utilisateurs externes de s’inscrire en libre-service à une application. 

Vous pouvez créer des expériences personnalisées pour ces utilisateurs externes, notamment la collecte d’informations les concernant lors du processus d’inscription et l’autorisation de fournisseurs d’identité externes tels que Facebook et Google. Vous pouvez également intégrer des fournisseurs de cloud tiers pour diverses fonctionnalités telles que la vérification des identités ou l’approbation des utilisateurs. [Plus d’informations](../external-identities/self-service-sign-up-overview.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Disponibilité générale : inscription et connexion par téléphone à Azure AD B2C à l’aide d’une stratégie intégrée

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C
 
L’inscription et la connexion par téléphone B2C à l’aide d’une stratégie intégrée permettent aux administrateurs informatiques et aux développeurs de l’organisation d’autoriser leurs utilisateurs finaux à s’inscrire et à se connecter à l’aide d’un numéro de téléphone dans les flux d’utilisateurs. Avec cette fonctionnalité, des liens d’exclusion de responsabilité tels que la politique de confidentialité et les conditions d’utilisation peuvent être personnalisés et affichés sur la page avant que l’utilisateur final ne reçoive le code secret à usage unique par SMS. [Plus d’informations](../../active-directory-b2c/phone-authentication-user-flows.md)
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---april-2021"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Avril 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En avril 2021, nous avons ajouté les 31 applications suivantes à notre galerie d’applications avec prise en charge de la fédération :

[Zii Travel Azure AD Connect](http://ziitravel.com/), [Cerby](../saas-apps/cerby-tutorial.md), [Selflessly](https://app.selflessly.io/sign-in), [Apollo CX](https://apollo.cxlabs.de/sso/aad), [Pedagoo](https://account.pedagoo.com/), [Measureup](https://account.measureup.com/), [Wistec Education](https://wisteceducation.fi/login/index.php), [ProcessUnity](../saas-apps/processunity-tutorial.md), [Cisco Intersight](../saas-apps/cisco-intersight-tutorial.md), [Codility](../saas-apps/codility-tutorial.md), [H5mag](https://account.h5mag.com/auth/request-access/ms365), [Check Point Identity Awareness](../saas-apps/check-point-identity-awareness-tutorial.md), [Jarvis](https://jarvis.live/login), [desknet's NEO](../saas-apps/desknets-neo-tutorial.md), [SDS & Chemical Information Management](../saas-apps/sds-chemical-information-management-tutorial.md), [Wúru App](../saas-apps/wuru-app-tutorial.md), [Holmes](../saas-apps/holmes-tutorial.md), [Tide Multi Tenant](https://gallery.tideapp.co.uk/), [Telenor](https://admin.smartansatt.telenor.no/), [Yooz US](https://us1.getyooz.com/?kc_idp_hint=microsoft), [Mooncamp](https://app.mooncamp.com/#/login), [inwise SSO](https://app.inwise.com/defaultsso.aspx), [Ecolab Digital Solutions](https://ecolabb2c.b2clogin.com/account.ecolab.com/oauth2/v2.0/authorize?p=B2C_1A_Connect_OIDC_SignIn&client_id=01281626-dbed-4405-a430-66457825d361&nonce=defaultNonce&redirect_uri=https://jwt.ms&scope=openid&response_type=id_token&prompt=login), [Taguchi Digital Marketing System](https://login.taguchi.com.au/), [XpressDox EU Cloud](https://test.xpressdox.com/Authentication/Login.aspx), [EZSSH](https://docs.keytos.io/getting-started/registering-a-new-tenant/registering_app_in_tenant/), [EZSSH Client](https://portal.ezssh.io/signup), [Verto 365](https://www.vertocloud.com/Login/), [KPN Grip](https://www.grip-on-it.com/), [AddressLook](https://portal.bbsonlineservices.net/Manage/AddressLook), [Cornerstone Single Sign-On](../saas-apps/cornerstone-ondemand-tutorial.md)

Pour accéder à la documentation de ces applications, cliquez https://aka.ms/AppsTutorial

Pour référencer votre application dans la Galerie d’applications Azure AD, lisez les informations détaillées ici : https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---april-2021"></a>Nouveaux connecteurs de provisionnement dans la galerie d’applications Azure AD - Avril 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 
Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Bentley - Automatic User Provisioning](../saas-apps/bentley-automatic-user-provisioning-tutorial.md)
- [Boxcryptor](../saas-apps/boxcryptor-provisioning-tutorial.md)
- [BrowserStack Single Sign-on](../saas-apps/browserstack-single-sign-on-provisioning-tutorial.md)
- [Eletive](../saas-apps/eletive-provisioning-tutorial.md)
- [Jostle](../saas-apps/jostle-provisioning-tutorial.md)
- [Olfeo SAAS](../saas-apps/olfeo-saas-provisioning-tutorial.md)
- [Proware](../saas-apps/proware-provisioning-tutorial.md)
- [Segment](../saas-apps/segment-provisioning-tutorial.md)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, consultez [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="introducing-new-versions-of-page-layouts-for-b2c"></a>Présentation des nouvelles versions des mises en page pour B2C

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C
 
Les [mises en page](../../active-directory-b2c/page-layout.md) pour les scénarios B2C sur Azure AD B2C ont été mises à jour pour réduire les risques de sécurité en introduisant les nouvelles versions de jQuery and Handlebars JS.
 
---

### <a name="updates-to-sign-in-diagnostic"></a>Mises à jour du diagnostic de connexion

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Signalement  
**Fonctionnalité de produit :** Monitoring et création de rapports
 
L’outil Diagnostic de connexion couvre un plus grand nombre de scénarios. 

Avec cette mise à jour, les scénarios liés aux événements suivants sont désormais inclus dans les résultats du diagnostic de connexion : 
- Événements liés aux problèmes de configuration des applications d’entreprise.
- Événements du fournisseur de services d’applications d’entreprise (côté application).
- Événements d’informations d’identification incorrectes. 

Ces résultats affichent des informations contextuelles et pertinentes sur l’événement et les mesures à prendre pour remédier aux problèmes. En outre, en l’absence de diagnostics contextuels détaillés, le diagnostic de connexion propose un contenu plus descriptif sur l’événement d’erreur.

Pour plus d’informations, consultez [Qu’est-ce que le diagnostic de connexion dans Azure Active Directory ?](../reports-monitoring/overview-sign-in-diagnostics.md)

---
### <a name="azure-ad-connect-cloud-sync-general-availability-refresh"></a>Actualisation de la disponibilité générale de la synchronisation cloud Azure AD Connect 
**Type :** Fonctionnalité modifiée  
**Catégorie de service :** synchronisation cloud Azure AD Connect **Fonctionnalité produit :** Directory

La synchronisation cloud Azure AD Connect dispose désormais d’un agent mis à jour (version# - 1.1.359). Pour plus d’informations sur les mises à jour de l’agent, y compris les correctifs de bogues, consultez l'[historique des versions](../cloud-sync/reference-version-history.md). Avec l’agent mis à jour, les clients de la synchronisation cloud peuvent utiliser des cmdlets GMSA pour définir et réinitialiser leur autorisation gMSA à un niveau granulaire. En outre, nous avons modifié la limite de synchronisation des membres à l’aide du filtrage d’étendue de groupe de 1 499 à 50 000 membres. 

Découvrez le nouveau [générateur d’expressions](../cloud-sync/how-to-expression-builder.md#deploy-the-expression) pour la synchronisation cloud, qui vous aide à créer des expressions simples et complexes lorsque vous effectuez des transformations de valeurs d’attribut d’Active directory vers Azure AD à l’aide du mappage d’attributs.

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

Pour plus d’informations, consultez [Activer la prise en charge de TLS 1.2 dans votre environnement en vue de la dépréciation de TLS 1.1 et 1.0 dans Azure AD](/troubleshoot/azure/active-directory/enable-support-tls-environment).

---

### <a name="public-preview----azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>Préversion publique : La gestion des droits d’utilisation Azure AD prend désormais en charge la multigéographie SharePoint Online

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Les organisations qui utilisent la multigéographie SharePoint Online peuvent désormais inclure des sites d’environnements multigéographie spécifiques dans leurs packages d’accès de gestion des droits d’utilisation. [Plus d’informations](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site)

---

### <a name="public-preview----restore-deleted-apps-from-app-registrations"></a>Préversion publique : Restaurer des applications supprimées à partir d’Inscriptions d’applications

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Expérience de développement
 
Les clients peuvent désormais afficher, restaurer et supprimer définitivement des inscriptions d’applications supprimées à partir du portail Azure. Cela s’applique uniquement aux applications associées à un annuaire, et non aux applications d’un compte Microsoft personnel. [Plus d’informations](../develop/howto-restore-app.md)
 
---

### <a name="public-preview----new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>Préversion publique : Nouvelle « action utilisateur » dans l’accès conditionnel pour l’inscription ou la jonction d’appareils

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
 Une nouvelle action utilisateur nommée « Inscrire ou joindre des appareils » est disponible dans Accès conditionnel. Cette action utilisateur vous permet de contrôler les stratégies d’authentification multifacteur pour l’inscription d’appareils Azure AD. 

Actuellement, cette action utilisateur vous permet uniquement d’activer MFA en tant que contrôle lorsque les utilisateurs inscrivent ou joignent des appareils à Azure AD. Les autres contrôles qui ne s’appliquent pas à l’inscription d’appareil Azure AD, ou qui en dépendent, sont désactivés avec cette action utilisateur. [Plus d’informations](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) 
 
---

### <a name="public-preview----optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>Préversion publique : Optimiser les groupes de connecteurs afin d’utiliser le service cloud Proxy d’application le plus proche

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès
 
Avec cette nouvelle fonctionnalité, les groupes de connecteurs peuvent être affectés au service de proxy d’application régional le plus proche dans lequel une application est hébergée. Cela peut améliorer les performances des applications dans les scénarios où elles sont hébergées dans des régions autres que la région du locataire d’accueil. [Plus d’informations](../app-proxy/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview) 
 
---

### <a name="public-preview----external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>Préversion publique : Inscription en libre service des identités externes dans AAD à l’aide des comptes Code secret à usage unique envoyé par e-mail

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C

Les utilisateurs externes pourront désormais utiliser des comptes e-mail à code secret à usage unique pour s’inscrire auprès d’applications métier et internes Azure AD. [Plus d’informations](../external-identities/one-time-passcode.md)

---

### <a name="public-preview----availability-of-ad-fs-sign-ins-in-azure-ad"></a>Préversion publique : Disponibilité des connexions AD FS dans Azure AD

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

### <a name="general-availability---user-type-attribute-can-now-be-updated-in-the-azure-admin-portal"></a>Disponibilité générale : L’attribut Type d’utilisateur peut désormais être mis à jour dans le portail d’administration Azure

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** expérience utilisateur et gestion  
**Fonctionnalité de produit :** User Management
 
Les clients peuvent désormais mettre à jour le type des utilisateurs Azure AD lorsqu’ils mettent à jour leurs informations de profil utilisateur à partir du portail d’administration Azure. Le type d’utilisateur peut également être mis à jour à partir de Microsoft Graph. Pour en savoir plus, consultez [Ajouter ou mettre à jour les informations du profil utilisateur](active-directory-users-profile-azure-portal.md).
 
---

### <a name="general-availability---replica-sets-for-azure-active-directory-domain-services"></a>Disponibilité générale : Jeux de réplicas pour Azure Active Directory Domain Services

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

[Bambuser Live Video Shopping](https://lcx.bambuser.com/), [DeepDyve Inc](https://www.deepdyve.com/azure-sso), [Moqups](../saas-apps/moqups-tutorial.md), [RICOH Spaces Mobile](https://ricohspaces.app/welcome), [Flipgrid](https://auth.flipgrid.com/), [hCaptcha Enterprise](../saas-apps/hcaptcha-enterprise-tutorial.md), [SchoolStream ASA](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx), [TransPerfect GlobalLink Dashboard](../saas-apps/transperfect-globallink-dashboard-tutorial.md), [SimplificaCI](https://app.simplificaci.com.br/), [Thrive LXP](../saas-apps/thrive-lxp-tutorial.md), [Lexonis TalentScape](../saas-apps/lexonis-talentscape-tutorial.md), [Exium](../saas-apps/exium-tutorial.md), [Sapient](../saas-apps/sapient-tutorial.md), [TrueChoice](../saas-apps/truechoice-tutorial.md), [RICOH Spaces](https://ricohspaces.app/welcome), [Saba Cloud](../saas-apps/learning-at-work-tutorial.md), [Acunetix 360](../saas-apps/acunetix-360-tutorial.md), [Exceed.ai](../saas-apps/exceed-ai-tutorial.md), [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-tutorial.md), [Enterprise Vault.cloud for Outlook](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb), [Smartlook](../saas-apps/smartlook-tutorial.md), [Accenture Academy](../saas-apps/accenture-academy-tutorial.md), [Onshape](../saas-apps/onshape-tutorial.md), [Tradeshift](../saas-apps/tradeshift-tutorial.md), [JuriBlox](../saas-apps/juriblox-tutorial.md), [SecurityStudio](../saas-apps/securitystudio-tutorial.md), [ClicData](https://app.clicdata.com/), [Evergreen](../saas-apps/evergreen-tutorial.md), [Patchdeck](https://patchdeck.com/ad_auth/authenticate/), [FAX.PLUS](../saas-apps/fax-plus-tutorial.md), [ValidSign](../saas-apps/validsign-tutorial.md), [AWS Single Sign-on](../saas-apps/aws-single-sign-on-tutorial.md), [Nura Space](https://dashboard.nuraspace.com/login), [Broadcom DX SaaS](../saas-apps/broadcom-dx-saas-tutorial.md), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [SendPro Enterprise](../saas-apps/sendpro-enterprise-tutorial.md), [FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

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
 
La prise en charge native par le proxy d’application Azure AD de l’authentification basée sur l’en-tête est désormais en disponibilité générale. Avec cette fonctionnalité, vous pouvez configurer les attributs utilisateur requis en tant qu’en-têtes HTTP pour l’application sans avoir besoin de composants supplémentaires pour le déploiement. [Plus d’informations](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md)

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
 
### <a name="public-preview----use-a-temporary-access-pass-to-register-passwordless-credentials"></a>Préversion publique : Utiliser un passe d’accès temporaire pour inscrire les informations d’identification sans mot de passe

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités

La Passe d’accès temporaire est un code secret à durée limitée qui sert d’informations d’identification fortes et permet l’intégration d’informations d’identification et de récupération sans mot de passe lorsqu’un utilisateur a perdu ou oublié son application de facteur d’authentification fort (par exemple, la clé de sécurité FIDO2 ou Microsoft Authenticator) et doit se connecter pour inscrire de nouvelles méthodes d’authentification fortes. [Plus d’informations](../authentication/howto-authentication-temporary-access-pass.md)

---

### <a name="public-preview----keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>Préversion publique : Maintenir la connexion dans la prochaine génération de flux d’utilisateur

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

La nouvelle génération de flux d’utilisateurs B2C prend désormais en charge la fonctionnalité [Maintenir la connexion](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) qui permet aux clients d’étendre la durée de vie de la session pour les utilisateurs de leurs applications web et natives à l’aide d’un cookie persistant.  La fonctionnalité maintient la session active même lorsque l’utilisateur ferme et ouvre à nouveau le navigateur. Elle est révoquée lorsque l’utilisateur se déconnecte.

---

### <a name="public-preview----reset-redemption-status-for-a-guest-user"></a>Préversion publique : Réinitialiser l’état d’acceptation d’un utilisateur invité

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Les clients peuvent à présent réinviter des utilisateurs invités externes existants à réinitialiser leur état d’acceptation, ce qui permet au compte d’utilisateur invité de rester sans qu’aucun accès ne soit perdu. [Plus d’informations](../external-identities/reset-redemption-status.md)
 
---

### <a name="public-preview----synchronization-provisioning-apis-now-support-application-permissions"></a>Préversion publique : Les API de synchronisation (approvisionnement) prennent désormais en charge les permissions d’application

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Les clients peuvent désormais utiliser application.readwrite.ownedby comme permission d’application pour appeler les API de synchronisation. Attention ! Cette fonction est prise en charge uniquement pour l’approvisionnement par Azure AD des applications tiers (par exemle, AWS, Data Bricks, etc.). Elle n’est actuellement pas prise en charge pour l’approvisionnement RH (Workday/Successfactors) ou la synchronisation Cloud (AD vers Azure AD). [Plus d’informations](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true)
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>Disponibilité générale : Rôle intégré Administrateur de la stratégie d’authentification

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
 
Microsoft Authenticator offre des capacités d’authentification multifacteur et de gestion des comptes et, à présent, il remplit également automatiquement les mots de passe sur les sites et les applications consultés par les utilisateurs sur leur appareil mobile (iOS et Android). 

Pour utiliser la fonction de remplissage automatique sur Authenticator, les utilisateurs doivent ajouter leur compte Microsoft personnel à Authenticator et l’utiliser pour synchroniser leurs mots de passe. Pour l’instant, les comptes professionnels ou scolaires ne peuvent pas être utilisés pour synchroniser les mots de passe. [Plus d’informations](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins)

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>Disponibilité générale : inviter des utilisateurs internes à une collaboration B2B

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Les clients peuvent désormais inviter des invités internes à utiliser la collaboration B2B au lieu d’envoyer une invitation à un compte interne existant. Cela permet aux clients de conserver l’ID d’objet, l’UPN, les appartenances aux groupes et les affectations d’applications de cet utilisateur. [Plus d’informations](../external-identities/invite-internal-users.md)

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>Disponibilité générale : Rôle intégré Administrateur de nom de domaine

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

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>Disponibilité générale : 10 rôles Azure Active Directory maintenant renommés

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

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>Disponibilité générale : Le manager de second niveau peut être défini en tant qu’approbateur de remplacement

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