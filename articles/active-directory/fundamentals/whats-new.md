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
ms.date: 5/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a822ca89128b85c24c6972c2b4fecd94a607f138
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112006254"
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

## <a name="may-2021"></a>Mai 2021

### <a name="public-preview---azure-ad-verifiable-credentials"></a>Préversion publique - Justificatifs vérifiables Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Authentification utilisateur
 
Les clients Azure AD peuvent désormais facilement créer et émettre des justificatifs vérifiables pour représenter la preuve d’emploi, d’études ou toute autre revendication dans le respect de la vie privée. Validez numériquement tout élément d’information relatif aux personnes et activités. [Plus d’informations](../verifiable-credentials/index.yml)

---

### <a name="public-preview---device-code-flow-now-includes-an-app-verification-prompt"></a>Préversion publique : le flux de code d’appareil comprend désormais une invite de vérification d’application

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** authentification utilisateur  
**Fonctionnalité produit :** authentifications (connexions)
 
Pour améliorer la sécurité, le [flux de code d’appareil](../develop/v2-oauth2-device-code.md) a été mis à jour afin d’ajouter une invite destinée à confirmer que l’utilisateur se connecte bien à l’application prévue. Le déploiement doit commencer en juin et se terminer pour le 30 juin.

Pour empêcher les attaques par hameçonnage lorsqu’un attaquant tente de se connecter à une application malveillante, l’invite suivante est ajoutée : « Essayez-vous de vous connecter à [nom d’affichage de l’application] ? ». Ce message s’affiche pour tous les utilisateurs qui se connectent à l’aide du flux de code d’appareil. Pour plus de sécurité, il ne peut être ni supprimé ni contourné. [Plus d’informations](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt)

---

### <a name="public-preview---build-and-test-expressions-for-user-provisioning"></a>Préversion publique : générer et tester des expressions pour l’attribution d’utilisateurs

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Le générateur d’expressions vous permet de créer et de tester des expressions, sans avoir à attendre le cycle de synchronisation complet. [Plus d’informations](../app-provisioning/functions-for-customizing-application-data.md) 

---

### <a name="public-preview---enhanced-audit-logs-for-conditional-access-policy-changes"></a>Préversion publique : journaux d’audit améliorés pour les modifications de stratégie d’accès conditionnel

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Comprendre les modifications apportées à vos stratégies au fil du temps constitue un aspect important de la gestion de l’accès conditionnel. Les modifications de stratégie peuvent entraîner des interruptions pour vos utilisateurs finaux et dans un tel contexte, il apparaît essentiel de conserver un journal des modifications et de permettre aux administrateurs de restaurer les versions de stratégie précédentes. 

En plus de montrer qui a apporté une modification de stratégie et à quel moment, les journaux d’audit contiennent désormais une valeur des propriétés modifiées pour offrir aux administrateurs une plus grande visibilité en matière d’attributions, de conditions ou de contrôles modifiés. Pour revenir à une version antérieure d’une stratégie, vous pouvez copier la représentation JSON de l’ancienne version et utiliser les API d’accès conditionnel pour rétablir rapidement la stratégie à son état précédent. [Plus d’informations](../conditional-access/concept-conditional-access-policies.md)

---

### <a name="public-preview---sign-in-logs-include-authentication-methods-used-during-sign-in"></a>Préversion publique : les journaux de connexion incluent les méthodes d’authentification utilisées lors de la connexion

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Monitoring et création de rapports
 

Les administrateurs peuvent désormais voir les étapes séquentielles effectuées par les utilisateurs pour se connecter, y compris les méthodes d’authentification utilisées lors de la connexion. 

Pour accéder à ces informations, accédez aux journaux de connexion Azure AD, sélectionnez une connexion, puis accédez à l’onglet Détails de la méthode d’authentification. Ici, nous avons inclus des informations telles que la méthode utilisée, les détails qui s’y rapportent (numéro de téléphone, nom du téléphone, par exemple), la condition d’authentification satisfaite et les détails des résultats. [Plus d’informations](../reports-monitoring/concept-sign-ins.md)

---

### <a name="public-preview---pim-adds-support-for-abac-conditions-in-azure-storage-roles"></a>Préversion publique : PIM ajoute la prise en charge des conditions ABAC aux rôles Stockage Azure

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

La nouvelle génération de flux d’utilisateurs B2C prend désormais en charge la fonctionnalité [KMSI (Maintenir la connexion)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) et la réinitialisation de mot de passe. La fonctionnalité KMSI permet aux clients d’étendre la durée de vie de la session pour les utilisateurs de leurs applications web et natives à l’aide d’un cookie persistant. Cette fonctionnalité maintient la session active même lorsque l’utilisateur ferme et rouvre le navigateur, et est révoquée lorsque l’utilisateur se déconnecte. La réinitialisation du mot de passe permet aux utilisateurs de réinitialiser leur mot de passe à partir du lien « Mot de passe oublié ? ». Cela permet aussi à l’administrateur de forcer la réinitialisation du mot de passe expiré de l’utilisateur dans l’annuaire Azure AD B2C. [Plus d’informations](../../active-directory-b2c/add-password-reset-policy.md?pivots=b2c-user-flow)
 
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
 
La nouvelle expérience de flux d'utilisateurs simplifiée offre la parité des fonctionnalités avec les fonctionnalités d’évaluation et est le foyer de toutes les nouvelles fonctionnalités. Les utilisateurs pourront activer de nouvelles fonctionnalités au sein du même flux d'utilisateurs, ce qui réduira la nécessité de créer plusieurs versions à chaque nouvelle sortie de fonctionnalité. La nouvelle expérience utilisateur conviviale simplifie également la sélection et la création de flux d’utilisateurs. Pour plus d’informations sur l’utilisation de cette fonctionnalité, consultez [, Créer des flux d’utilisateurs dans Azure AD B2C](../../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-user-flow). [Plus d’informations](../../active-directory-b2c/user-flow-versions.md)

---

### <a name="general-availability---azure-active-directory-threat-intelligence-for-sign-in-risk"></a>Disponibilité générale : renseignement sur les menaces Azure Active Directory pour le risque de connexion

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Cette nouvelle détection sert de méthode ad hoc pour permettre à nos équipes de sécurité de vous avertir et de protéger vos utilisateurs en augmentant leur risque de session à un niveau élevé lorsque nous observons une attaque, et en marquant les connexions associées comme étant à risque. Cette détection suit le renseignement sur les menaces Azure Active Directory en matière de détection de risque d’utilisateur pour fournir une couverture complète des différentes attaques observées par les équipes de sécurité Microsoft. [Plus d’informations](../identity-protection/concept-identity-protection-risks.md#user-risk)
 
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
 
En outre, pour empêcher les administrateurs de définir des emplacements nommés problématiques, des vérifications supplémentaires ont été ajoutées afin de réduire les risques de configuration incorrecte. [Plus d’informations](../conditional-access/location-condition.md)

---

### <a name="general-availability---restricted-guest-access-permissions-in-azure-ad"></a>Disponibilité générale : autorisations d’accès invité restreintes dans Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** User Management  
**Fonctionnalité de produit :** Répertoire
 
Les autorisations au niveau répertoire pour les utilisateurs invités ont été mises à jour. Ces autorisations permettent aux administrateurs d’imposer des restrictions et contrôles supplémentaires à l’accès utilisateur invité externe.

Les administrateurs peuvent désormais ajouter des restrictions à l’accès d’invités externes aux informations d’appartenance et de profil des utilisateurs et des groupes. En outre, les clients peuvent gérer l’accès des utilisateurs externes à grande échelle en masquant les appartenances de groupe, notamment en empêchant les utilisateurs invités de voir les appartenances des groupes dans lesquels ils se trouvent. Pour plus d’informations, consultez [Restreindre les autorisations d’accès invité dans Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).
 
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
 
En mai 2021, nous avons ajouté les 29 applications suivantes à notre galerie d’applications avec prise en charge de la fédération

[InviteDesk](https://app.invitedesk.com/login), [Webrecruit ATS](https://id-test.webrecruit.co.uk/), [Workshop](../saas-apps/workshop-tutorial.md), [Gravity Sketch](https://landingpad.me/), [JustLogin](../saas-apps/justlogin-tutorial.md), [Custellence](https://custellence.com/sso/), [WEVO](https://hello.wevoconversion.com/login), [AppTec360 MDM](https://www.apptec360.com/ms/autopilot.html), [Filemail](https://www.filemail.com/login),[Ardoq](../saas-apps/ardoq-tutorial.md), [Leadfamly](../saas-apps/leadfamly-tutorial.md), [Documo](../saas-apps/documo-tutorial.md), [Autodesk SSO](../saas-apps/autodesk-sso-tutorial.md), [Check Point Harmony Connect](../saas-apps/check-point-harmony-connect-tutorial.md), [BrightHire](https://app.brighthire.ai/), [Rescana](../saas-apps/rescana-tutorial.md), [Bluewhale](https://cloud.bluewhale.dk/), [AlacrityLaw](../saas-apps/alacritylaw-tutorial.md), [Equisolve](../saas-apps/equisolve-tutorial.md), [Zip](../saas-apps/zip-tutorial.md), [Cognician](../saas-apps/cognician-tutorial.md), [Acra](https://www.acrasuite.com/), [VaultMe](https://app.vaultme.com/#/signIn), [TAP App Security](../saas-apps/tap-app-security-tutorial.md), [Cavelo Office365 Cloud Connector](https://dashboard.prod.cavelodata.com/), [Clebex](../saas-apps/clebex-tutorial.md), [Banyan Command Center](../saas-apps/banyan-command-center-tutorial.md), [Check Point Remote Access VPN](../saas-apps/check-point-remote-access-vpn-tutorial.md), [LogMeIn](../saas-apps/logmein-tutorial.md)

La documentation de toutes ces applications est disponible ici https://aka.ms/AppsTutorial

Pour référencer votre application dans la galerie d’applications Azure AD, lisez les informations détaillées ici : https://aka.ms/AzureADAppRequest

---

### <a name="improved-conditional-access-messaging-for-android-and-ios"></a>Amélioration de la messagerie d’accès conditionnel pour Android et iOS

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion et inscription des appareils  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 

Nous avons mis à jour la formulation sur l’écran d’accès conditionnel présenté aux utilisateurs qui ne peuvent pas accéder aux ressources de l’entreprise tant qu’ils n’ont pas inscrit leur appareil dans la gestion des périphériques mobiles. Ces améliorations s’appliquent aux plateformes Android et iOS/iPadOS. Les modifications sont les suivantes :

- « Aidez-nous à protéger votre appareil » a été remplacé par « Configurez votre appareil pour obtenir l’accès »
- « Votre connexion a réussi mais votre administrateur que votre appareil soit géré par Microsoft pour accéder à cette ressource. » a été remplacé par « [Nom de l’organisation] requiert que vous sécurisiez cet appareil pour accéder à la messagerie, aux fichiers et aux données de [Nom de l’organisation]. » 
- « Inscrivez-vous maintenant » a été remplacé par « Continuer »

Notez que les informations présentes dans [Inscrivez votre appareil d’entreprise Android](https://support.microsoft.com/topic/enroll-your-android-enterprise-device-d661c82d-fa28-5dfd-b711-6dff41ae83bb) sont obsolètes.

---

### <a name="azure-information-protection-service-will-begin-asking-for-consent"></a>Le service Azure Information Protection commence par demander le consentement.

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Le service Azure Information Protection connecte les utilisateurs au locataire qui a chiffré le document afin de permettre l’accès au document.  À compter de juin, Azure AD invitera l’utilisateur à donner son consentement lorsque cet accès interviendra entre organisations.  Cela permet à l’utilisateur de comprendre que l’organisation propriétaire du document collectera des informations le concernant dans le cadre de l’accès au document. [Plus d’informations](/azure/information-protection/known-issues#sharing-external-doc-types-across-tenants)
 
---

### <a name="provisioning-logs-schema-change-impacting-graph-api-and-azure-monitor-integration"></a>Modification du schéma des journaux d’approvisionnement impactant l’intégration de l’API Graph et d’Azure Monitor

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Monitoring et création de rapports
 

Les attributs « Action » et « statusInfo » seront remplacés par « provisioningAction » et « provisoiningStatusInfo ». Mettez à jour tous les scripts que vous avez créés à l’aide de l’[approvisionnement de journaux de l’API Graph](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true) ou des [intégrations Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md). 
 

---

### <a name="new-arm-api-to-manage-pim-for-azure-resources-and-azure-ad-roles"></a>Nouvelle API ARM pour gérer PIM pour les ressources Azure et les rôles Azure AD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Privileged Identity Management  
**Fonctionnalité de produit :** Privileged Identity Management
 
Une version mise à jour de l’API PIM pour le rôle Ressource Azure et le rôle Azure AD a été publiée. L’API PIM pour le rôle Ressource Azure est maintenant publiée dans le cadre de l’API ARM, qui s’aligne sur l’API de gestion des rôles pour l’attribution de rôle Azure standard. L’API PIM pour les rôles Azure AD est également publiée sous l’API Graph, alignée avec les API unifiedRoleManagement. Voici quelques-uns des avantages de cette modification :

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
 
Un nouvel rôle Administrateur Identity Governance a récemment été introduit. Ce rôle remplacement le rôle Administrateur d’utilisateurs pour la gestion des catalogues et des packages d’accès en matière de gestions des droits d’utilisation Azure AD. Si vous avez attribué aux administrateurs le rôle Administrateur d’utilisateurs ou si vous leur demandez d’activer ce rôle pour gérer les packages d’accès dans la gestion des droits d’utilisation Azure AD, privilégiez le rôle Administrateur Identity Governance. Le rôle Administrateur d’utilisateurs ne fournira plus de droits d’administration relatifs aux catalogues ou packages d’accès. [Plus d’informations](../governance/identity-governance-overview.md#appendix---least-privileged-roles-for-managing-in-identity-governance-features)

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

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>Préversion publique - Inscription en libre service External Identities dans AAD à l’aide des comptes e-mail à code secret à usage unique

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Les utilisateurs externes peuvent désormais utiliser des comptes e-mail à code secret à usage unique pour s’inscrire ou se connecter aux applications internes Azure AD et métier. [Plus d’informations](../external-identities/one-time-passcode.md)

---

### <a name="general-availability---external-identities-self-service-sign-up"></a>Disponibilité générale - Inscription en libre-service des identités externes

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
 
Avec cette nouvelle fonctionnalité, les groupes de connecteurs peuvent être affectés au service de proxy d’application régional le plus proche dans lequel une application est hébergée. Cela peut améliorer les performances des applications dans les scénarios où elles sont hébergées dans des régions autres que la région du locataire d’accueil. [Plus d’informations](../app-proxy/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview) 
 
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
