---
title: Archives pour les nouveautés - Azure Active Directory | Microsoft Docs
description: Les notes de publication des nouveautés de la section Vue d’ensemble de ce jeu de contenus contient 6 mois d’activité. Au bout de 6 mois, les éléments sont supprimés de l’article principal et placés dans cet article d’archive.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb75c57b832dbd12dbd06fd42b636a16b40ff10e
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499350"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archive des nouveautés d’Azure Active Directory

L’article principal [Notes de publication des nouveautés](whats-new.md) contient les informations des 6 derniers mois, tandis que cet article comprend toutes les informations antérieures.

Les notes de publication des nouveautés vous donnent des informations sur les sujets suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues
- Fonctionnalités dépréciées
- Modifications planifiées

---

## <a name="july-2018"></a>Juillet 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Améliorations apportées aux notifications par e-mail Azure Active Directory

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Azure Active Directory (Azure AD) a une nouvelle conception visuelle, ainsi qu’un nouvel expéditeur, avec une nouvelle adresse e-mail et un nouveau nom d’affichage, lorsqu’ils proviendront des services suivants :
 
- Révisions d’accès Azure AD
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Notifications de l’expiration prochaine des certificats des applications d’entreprise
- Notifications du service de provisionnement des applications d’entreprise
 
Les notifications par e-mail proviendront de l’adresse e-mail et du nom d’affichage suivants :

- Adresse e-mail : azure-noreply@microsoft.com
- Nom complet : Microsoft Azure
 
Pour voir un exemple de nouvelle notification et obtenir plus d’informations, consultez [Notifications par e-mail dans Azure AD Privileged Identity Management](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Les journaux d’activité Azure AD sont désormais disponibles via Azure Monitor

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Les journaux d’activité Azure AD sont désormais disponibles en version préliminaire publique pour Azure Monitor (service de supervision à l’échelle de la plateforme d’Azure). Azure Monitor vous offre une rétention à long terme et une intégration transparente, en plus de ces améliorations :

- Rétention à long terme par le routage de vos fichiers journaux sur votre propre compte de stockage Azure.

- Intégration transparente de SIEM, sans avoir à écrire ou gérer des scripts personnalisés.

- Intégration transparente avec vos propres solutions personnalisées, outils d’analytique ou solutions de gestion des incidents.

Pour plus d’informations sur ces nouvelles fonctionnalités, consultez notre blog [Les journaux d’activité d’Azure AD dans Azure Monitor diagnostics est désormais disponible en version préliminaire publique](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) et notre documentation, [Journaux d’activité d’Azure Active Directory dans Azure Monitor (préversion)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informations d’accès conditionnel ajoutées au rapport de connexion Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Cette mise à jour vous permet de voir quelles stratégies sont évaluées lorsqu’un utilisateur se connecte, ainsi que le résultat de la stratégie. En outre, le rapport inclut désormais le type d’application client utilisé par l’utilisateur, pour que vous puissiez identifier le trafic de protocole hérité. Les entrées de rapport peuvent également désormais rechercher un ID de corrélation, qui se trouve dans le message d’erreur visible par l’utilisateur et peuvent être utilisées pour identifier et résoudre les problèmes de la requête de connexion correspondante.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Afficher les authentifications héritées via les journaux d’activité de connexions

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports
 
Avec le nouveau champ **Application cliente** des journaux d’activité de connexions, les clients peuvent désormais voir les utilisateurs qui utilisent des authentifications héritées. Les clients peuvent accéder à ces informations à l’aide de l’API Microsoft Graph Connexions ou via les journaux d’activité de connexions dans le portail Azure AD et ainsi utiliser la commande **Application cliente** pour filtrer les authentifications héritées. Pour plus de détails, consultez la documentation.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nouvelles applications fédérées disponibles dans la galerie Azure AD App - Juillet 2018

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En juillet 2018, nous avons ajouté à notre galerie d’applications les 16 applications suivantes qui prennent en charge la fédération :

[Hub de l’innovation](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Authentification unique pour certains administrateurs](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), mise en lots PSUC, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), Classe unifiée de PowerSchool, [intégration Eli](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [prise en charge à distance Bomgar](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [Connecteur SecureW2 JoinNow](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [Base de compétences](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nouvelles intégrations d’applications SaaS pour l’attribution d’utilisateurs - juillet 2018

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 
Azure AD vous permet d’automatiser la création, la maintenance et la suppression d’identités utilisateur dans des applications SaaS comme Dropbox, Salesforce, ServiceNow, etc. En juillet 2018, nous avons ajouté une prise en charge de l’attribution des utilisateurs pour les applications suivantes dans la galerie d’applications Azure AD :

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Pour obtenir la liste de toutes les applications qui prennent en charge l’attribution d’utilisateurs dans la galerie Azure AD, consultez [Intégration d’application SaaS avec Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health pour la synchronisation - un moyen plus simple pour corriger les erreurs de synchronisation d’attribut en double et orphelins

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** AD Connect  
**Fonctionnalité de produit :** Monitoring et création de rapports
 
Azure AD Connect Health introduit la mise à jour du libre-service pour vous aider à mettre en surbrillance et corriger les erreurs de synchronisation. Cette fonctionnalité résout les erreurs de synchronisation d’attribut en double et corrige les objets qui sont orphelins à partir d’Azure AD. Ce diagnostic offre les avantages suivants :

- Permet de réduire les erreurs de synchronisation d’attribut en double, en fournissant des corrections spécifiques

- Applique une correction pour les scénarios Azure AD dédiés, résolution des erreurs en une seule étape

- Aucune mise à niveau ou configuration n’est nécessaire pour démarrer et utiliser cette fonctionnalité

Pour plus d’informations, consultez [Diagnostiquer et corriger les erreurs de synchronisation d’attribut en double](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Mises à jour de Visual à Azure AD et expériences de connexion MSA

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Azure AD  
**Fonctionnalité de produit :** Authentification utilisateur

Nous mettons à jour l’interface utilisateur pour l’expérience de connexion des services en ligne de Microsoft, tels qu’Office 365 et Azure. Cette modification rend les écrans plus simples et moins encombrés. Pour plus d’informations sur cette modification, consultez le blog [Améliorations à venir à l’expérience de connexion Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/).

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nouvelle version d’Azure AD Connect - juillet 2018

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités

La dernière version d’Azure AD Connect comprend : 

- Correctifs de bogues et mises à jour de la prise en charge 

- Disponibilité générale de l’intégration Ping Federate

- Mises à jour de la dernière SQL 2012 du client 

Pour plus d’informations sur cette mise à jour, consultez [Azure AD Connect : Historique de publication des versions](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-tou-end-user-ui"></a>Mises à jour de l’interface utilisateur des conditions d’utilisation (conditions d’utilisation)

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Gouvernance

Nous mettons à jour la chaîne de l’acceptation de l’interface utilisateur des conditions d’utilisation.

**Texte actif**  Vous devez accepter les conditions d'utilisation pour accéder aux ressources [tenantName].<br>**Nouveau texte.** Vous devez lire les conditions d'utilisation pour accéder à la ressource [tenantName].

**Texte actuel :** Choosing to accept means that you agree to all of the above terms of use. (Accepter signifie que vous acceptez les conditions d’utilisation ci-dessus.)<br>**Nouveau texte :** Please click Accept to confirm that you have read and understood the terms of use. (Veuillez cliquer sur Accepter pour confirmer que vous avez lu et compris les conditions d’utilisation.)

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>L’authentification directe prend en charge les applications et les protocoles hérités

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
L’authentification directe prend en charge les applications et les protocoles hérités. Les limitations suivantes sont maintenant entièrement prises en charge :

- L’utilisateur se connecte aux applications clientes Office héritées, Office 2010 et Office 2013 sans exiger une authentification moderne.

- Accédez aux informations de disponibilité et de partage de calendrier dans des environnements hybrides Exchange sur Office 2010 uniquement.

- L’utilisateur se connecte à des applications clientes Skype Entreprise, sans exiger une authentification moderne.

- L'utilisateur se connecte à PowerShell version 1.0.

- Le programme d’inscription des appareils Apple (Apple DEP) à l’aide de l’Assistant de configuration iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Informations de sécurité convergées pour la réinitialisation de mot de passe en libre-service et l’authentification multifacteur

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** SSPR  
**Fonctionnalité de produit :** Authentification utilisateur

Cette nouvelle fonctionnalité permet aux utilisateurs de gérer leurs informations de sécurité (par exemple, numéro de téléphone, adresse de messagerie, application mobile, etc) pour la réinitialisation de mot de passe libre-service (SSPR) et l’authentification multifacteur (MFA) dans une seule expérience. Les utilisateurs ne devront plus inscrire les mêmes informations de sécurité pour SSPR et MFA dans deux expériences différentes. Cette nouvelle expérience s’applique également aux utilisateurs qui dispose de SSPR ou MFA.

Si une organisation ne respecte pas l’inscription MFA ou SSPR, les utilisateurs peuvent inscrire leurs informations de sécurité via le portail **Mes applications**. À partir de là, les utilisateurs peuvent inscrire des méthodes activées pour MFA ou SSPR. 

Il s’agit d’une inscription à une version préliminaire publique. Les administrateurs peuvent activer la nouvelle expérience (le cas échéant) pour un groupe sélectionné d’utilisateurs ou de tous les utilisateurs d’un client.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Utilisez l’application Microsoft Authenticator pour vérifier votre identité lorsque vous réinitialisez votre mot de passe

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** SSPR  
**Fonctionnalité de produit :** Authentification utilisateur

Cette fonctionnalité permet aux non administrateurs de vérifier leur identité lors de la réinitialisation d’un mot de passe à l’aide d’une notification ou d’un code à partir de Microsoft Authenticator (ou toute autre application authentificatrice). Une fois que les administrateurs ont activé cette méthode de réinitialisation de mot de passe en libre-service, les utilisateurs qui ont inscrit une application mobile par le biais d’aka.ms/mfasetup ou d’aka.ms/setupsecurityinfo peuvent utiliser leur application mobile comme méthode de vérification lorsqu’ils réinitialisent leur mot de passe.

Les notifications d’application mobile peuvent uniquement être activées dans le cadre d’une stratégie qui nécessite deux méthodes pour réinitialiser votre mot de passe.

---

## <a name="june-2018"></a>Juin 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Notification de modification : Correctif de sécurité du flux d’autorisation déléguée pour les applications qui utilisent les API de journaux d’activité Azure AD

**Type :** Modification planifiée  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

En raison du renforcement de la sécurité, nous avons dû modifier les autorisations pour les applications qui utilisent un flux d’autorisation déléguée pour accéder aux [API de journal d’activité Azure AD](https://aka.ms/aadreportsapi). Cette modification prendra effet d’ici **le 26 juin 2018**.

Si l’une de vos applications utilise des API de journal d’activité Azure AD, procédez comme suit pour vous assurer que l’application ne s’arrête pas après l’application de la modification.

**Pour mettre à jour les autorisations de votre application**

1. Connectez-vous au portail Azure, sélectionnez **Azure Active Directory**, puis **Inscriptions des applications**.
2. Sélectionnez l’application qui utilise l’API Journal d’activité Azure AD, sélectionnez **Paramètres**, **Autorisations requises**, puis l’API **Microsoft Azure Active Directory**.
3. Dans la zone **Autorisations déléguées** du panneau **Activer l’accès**, cochez la case à côté **Lire les données d'annuaire**, puis sélectionnez **Enregistrer**.
4. Sélectionnez **Accorder des autorisations**, puis **Oui**.
    
    >[!Note]
    >Vous devez être un administrateur général pour accorder des autorisations à l’application.

Pour plus d’informations, consultez la section [Accorder des autorisations](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) des prérequis pour accéder à l’article concernant API de création de rapports Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Configurer les paramètres TLS de connexion aux services Azure AD pour la conformité PCI DSS

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** S.O.  
**Fonctionnalité de produit :** Plateforme

Le protocole TLS (Transport Layer Security) garantit la confidentialité et l’intégrité des données entre deux applications qui communiquent. Il s’agit du protocole de sécurité le plus largement utilisé aujourd’hui.

Le [Conseil des normes de sécurité PCI](https://www.pcisecuritystandards.org/) a déterminé que les premières versions des protocoles TLS et SSL (Secure Sockets Layer) doivent être désactivées en faveur de nouveaux protocoles d’application plus sécurisés, dont l’utilisation est obligatoire depuis le **30 juin 2018**. Cela signifie que si vous vous connectez à des services Azure AD et que vous avez besoin de la conformité PCI DSS, vous devez désactiver TLS 1.0. Plusieurs versions de TLS sont disponibles, toutefois, TLS 1.2 est la version la plus récente disponible pour Azure Active Directory. Nous vous recommandons de passer directement à TLS 1.2, à la fois pour les combinaisons client/serveur et navigateur/serveur.

Les navigateurs obsolètes risquent de ne pas prendre en charge les versions les plus récentes de TLS, telles que TLS 1.2. Pour savoir quelles versions de TLS sont prises en charge par votre navigateur, accédez au site [Qualys SSL Labs](https://www.ssllabs.com/), puis cliquez sur **Test your browser** (Testez votre navigateur). Nous vous recommandons de mettre à jour votre navigateur web vers la dernière version disponible et d’activer uniquement le protocole TLS 1.2.

**Pour activer le protocole TLS 1.2 (pour chaque navigateur)**

- **Microsoft Edge et Internet Explorer (les deux sont configurés à l’aide d’Internet Explorer)**

    1. Ouvrez Internet Explorer, sélectionnez **Outils** > **Options Internet** > **Avancé**.
    2. Dans la zone **Sécurité**, sélectionnez **Utiliser TLS 1.2**, puis sélectionnez **OK**.
    3. Fermez toutes les fenêtres du navigateur, puis redémarrez Internet Explorer. 

- **Google Chrome**

    1. Ouvrez Google Chrome, tapez *chrome://settings/* dans la barre d’adresse, puis appuyez sur **Entrée**.
    2. Développez les options de la section **Avancé**, accédez à la zone **Système**, puis sélectionnez **Ouvrir les paramètres proxy**.
    3. Dans la boîte de dialogue **Propriétés Internet**, sélectionnez l’onglet **Avancé**, accédez à la zone **Sécurité**, sélectionnez **Utiliser TLS 1.2**, puis sélectionnez **OK**.
    4. Fermez toutes les fenêtres du navigateur, puis redémarrez Google Chrome.

- **Mozilla Firefox**

    1. Ouvrez Firefox, tapez *about:config* dans la barre d’adresses, puis appuyez sur **Entrée**.
    2. Lancez une recherche sur *TLS*, puis sélectionnez l’entrée **security.tls.version.max**.
    3. Définissez la valeur sur **3** pour forcer le navigateur à utiliser la version TLS 1.2, puis sélectionnez **OK**.

        >[!NOTE]
        >Firefox version 60.0 prend en charge TLS 1.3, vous pouvez donc également définir la valeur de security.tls.version.max sur **4**.

    4. Fermez toutes les fenêtres du navigateur, puis redémarrez Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nouvelles applications fédérées disponibles dans la galerie Azure AD App - Juin 2018

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En juin 2018, nous avons ajouté à notre galerie d’applications les 15 applications suivantes qui prennent en charge la fédération :

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Settling music](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1 Token enabled LOB App](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [SharePoint on-premises](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial). Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Protection des mots de passe Azure AD disponible en préversion publique

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Authentification utilisateur

Utilisez la protection des mots de passe Azure AD pour éliminer les mots de passe faciles à deviner de votre environnement. L’élimination de ces mots de passe permet de réduire le risque de compromission due à une attaque par pulvérisation de mots de passe.

Voici, plus précisément, ce que permet la protection des mots de passe Azure AD :

- Protège les comptes de votre organisation, à la fois dans Azure AD et dans Windows Server Active Directory (AD) 
- Empêche vos utilisateurs d’utiliser les 500 mots de passe les plus couramment utilisés, ainsi que plus d’un million de variations de ces mots de passe
- Permet de gérer la protection des mots de passe Azure AD à partir d’un même emplacement du portail Azure AD, à la fois pour Azure AD et pour les instances locales de Windows Server Active Directory.

Pour plus d’informations sur la protection des mots de passe Azure AD, consultez [Éliminer les mots de passe incorrects de votre organisation](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Nouveau modèle de stratégie d’accès conditionnel « tous les invités » créé lors de la création des conditions d’utilisation

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Gouvernance

Lors de la création de vos conditions d’utilisation, un nouveau modèle de stratégie d’accès conditionnel est créé pour « tous les invités » et « toutes les applications ». Ce nouveau modèle de stratégie applique les conditions d’utilisation qui viennent d’être créées, ce qui simplifie les processus de création et de mise en œuvre pour les invités.

Pour plus d’informations, consultez [Fonctionnalité Conditions d’utilisation d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Nouveau modèle de stratégie d’accès conditionnel «personnalisé » créé lors de la création des conditions d’utilisation

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Gouvernance

Lors de la création de vos conditions d’utilisation, un nouveau modèle de stratégie d’accès conditionnel «personnalisé » est également créé. Ce nouveau modèle de stratégie vous permet de créer les conditions d’utilisation, puis de passer immédiatement au panneau de création de la stratégie d’accès conditionnel, sans devoir accéder manuellement dans le portail.

Pour plus d’informations, consultez [Fonctionnalité Conditions d’utilisation d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nouvelles instructions plus complètes sur le déploiement d’Azure Multi-Factor Authentication

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Nous avons publié de nouvelles instructions étape par étape sur le déploiement d’Azure Multi-Factor Authentication (MFA) dans une organisation.

Pour consulter le guide de déploiement MFA, accédez au dépôt GitHub [Identity Deployment Guides](https://aka.ms/DeploymentPlans) (Guides de déploiement des identités). Pour envoyer vos commentaires sur les guides de déploiement, utilisez le [formulaire de commentaires Plan de déploiement](https://aka.ms/deploymentplanfeedback). Si vous avez des questions sur les guides de déploiement, contactez-nous ici : [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Rôles délégués de gestion des applications Azure AD en préversion publique

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Contrôle d’accès

Les administrateurs peuvent désormais déléguer des tâches de gestion des applications sans avoir à affecter le rôle Administrateur général. Les nouveaux rôles et nouvelles fonctionnalités sont les suivants :

- **Nouveaux rôles d’administrateur Azure AD standard :**

    - **Administrateur d’application.** Permet de gérer tous les aspects des applications, y compris l’inscription, les paramètres d’authentification unique, les affectations et les licences d’applications, les paramètres de proxy d’application et le consentement (sauf pour les ressources Azure AD).

    - **Administrateur d’applications cloud.** Accorde tous les privilèges de l’administrateur d’applications, sauf pour le proxy d’application, car il ne fournit pas d’accès local.

    - **Développeur d’applications.** Permet d’inscrire des applications, même si l’option **Les utilisateurs peuvent inscrire des applications** est désactivée.

- **Propriété (configurée pour chaque inscription d’application et chaque application d’entreprise, comme pour la propriété de groupe) :**
 
    - **Propriétaire d’inscription d’application.** Permet de gérer tous les aspects de l’inscription d’une application avec propriétaire, y compris le manifeste d’application et l’ajout de propriétaires supplémentaires.

    - **Propriétaire d’application d’entreprise.** Permet de gérer de nombreux aspects relatifs aux applications d’entreprise avec propriétaire, y compris les paramètres d’authentification unique, les affectations d’applications et le consentement (sauf pour les ressources Azure AD).

Pour plus d’informations sur la préversion publique, consultez le blog [Azure AD delegated application management roles are in public preview!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) . Pour plus d’informations sur les rôles et les autorisations, consultez [Attribution de rôles d’administrateur dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Mai 2018

### <a name="expressroute-support-changes"></a>Modifications de la prise en charge d’ExpressRoute

**Type :** Modification planifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Plateforme  

Les offres de SaaS, comme Azure Active Directory (Azure AD) sont conçues pour offrir de meilleures performances en accédant directement à Internet, sans passer par ExpressRoute ni par un autre tunnel VPN privé. Pour cette raison, à compter du **1er août 2018**, nous arrêtons la prise en charge d’ExpressRoute pour les services Azure AD qui utilisent l’appairage public Azure et les communautés Azure qui utilisent l’appairage Microsoft. Pour tous les services concernés par cette modification, vous remarquerez le passage progressif du trafic Azure AD d’ExpressRoute à Internet.

Même si nous modifions la prise en charge, nous savons qu’il existe toujours des situations où vous devrez peut-être utiliser un ensemble dédié de circuits pour le trafic d’authentification. Pour cette raison, Azure AD continue à prendre en charge les restrictions de plage d’adresses IP par abonné avec ExpressRoute et les services qui utilisent déjà l’appairage Microsoft avec la communauté « Autres services en ligne Office 365 ». Si vos services sont concernés, mais que vous avez besoin d’ExpressRoute, vous devez procéder comme suit :

- **Si vous utilisez l’appairage public Azure.** Passez à l’appairage Microsoft et inscrivez-vous à la communauté **Autres services en ligne Office 365 (12076:5100)**. Pour plus d’informations sur le passage de l’appairage public Azure à l’appairage Microsoft, consultez l’article [Déplacer un appairage public vers l’appairage Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) l’article.

- **Si vous utilisez l’appairage Microsoft.** Inscrivez-vous à la communauté **Autres services en ligne Office 365 (12076:5100)**. Pour plus d’informations sur la configuration de routage requise, consultez la section [Prise en charge des communautés BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) de l’article Configuration requise pour le routage ExpressRoute.

Si vous devez continuer à utiliser des circuits dédiés, discutez avec l’équipe des comptes Microsoft au sujet de la façon d’obtenir l’autorisation d’utiliser la communauté **Autres services en ligne Office 365 (12076:5100)**. Le comité de révision géré par MS Office vérifiera si vous avez vraiment besoin de ces circuits et s’assurera que vous comprenez bien les implications techniques découlant de leur conservation. Les abonnements non autorisés qui essaient de créer des filtres de routage pour Office 365 reçoivent un message d’erreur. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>API Microsoft Graph pour les scénarios d’administration des conditions d’utilisation

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Expérience de développement
 
Nous avons ajouté l’API Microsoft Graph pour vous permettre de gérer les conditions d’utilisation d’Azure AD. Libre à vous ainsi de créer, mettre à jour et supprimer l’objet des conditions d’utilisation.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Ajouter un point de terminaison mutualisé Azure AD en tant que fournisseur d’identité dans Azure AD B2C

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C
 
En vous aidant de stratégies personnalisées, vous pouvez maintenant ajouter le point de terminaison commun Azure AD en tant que fournisseur d’identité dans Azure AD B2C. Cela vous permet d’avoir un seul point d’entrée pour tous les utilisateurs Azure AD se connectant à vos applications. Pour plus d’informations, consultez [Azure Active Directory B2C : Autoriser la connexion d’utilisateurs à un fournisseur d’identité Azure AD mutualisé à l’aide de stratégies personnalisées](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Utiliser des URL internes pour accéder aux applications où que vous vous trouviez avec l’extension de connexion Mes applications et le proxy d’application Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Authentification unique
 
Les utilisateurs peuvent maintenant accéder aux applications via des URL internes, y compris lorsqu’ils se trouvent en dehors du réseau de l’entreprise. Il leur suffit pour cela d’utiliser l’extension de connexion sécurisée Mes applications pour Azure AD. Cela fonctionne avec toutes les d’application que vous avez publiées à l’aide du proxy d’application Azure AD, dans tous les navigateurs sur lesquels est installée une extension Panneau d’accès. Dès qu’un utilisateur se connecte à l’extension, la fonctionnalité de redirection d’URL est automatiquement activée. Vous pouvez télécharger cette extension pour [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367) et [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory : données en Europe pour les clients européens

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** GoLocal

Les clients européens veulent que leurs données restent en Europe. Ils ne veulent pas qu’elles soient répliquées en dehors des centres de données européens afin de respecter les lois européennes en vigueur et celles régissant leur vie privée. Cet [article](https://go.microsoft.com/fwlink/?linkid=872328) fournit des détails spécifiques sur les informations d’identité stockées en Europe ainsi que des informations complémentaires sur les données stockées en dehors des centres de données européens. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nouvelles intégrations d’applications SaaS pour l’attribution d’utilisateurs : mai 2018

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 
Azure AD vous permet d’automatiser la création, la maintenance et la suppression d’identités utilisateur dans des applications SaaS comme Dropbox, Salesforce, ServiceNow, etc. En mai 2018, nous avons ajouté une prise en charge de l’attribution des utilisateurs pour les applications suivantes dans la galerie d’applications Azure AD :

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Pour obtenir la liste de toutes les applications qui prennent en charge l’attribution d’utilisateurs dans la galerie Azure AD, consultez [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>La fonction de révisions d’accès Azure AD des groupes et applications fournit maintenant des révisions récurrentes.

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance
 
La fonctionnalité de révision des accès de groupes et d’applications fait désormais partie de l’offre Azure AD Premium P2.  Les administrateurs peuvent configurer les révisions d’accès des appartenances à un groupe et des attributions d’applications de façon à ce qu’elles se répètent automatiquement à intervalles réguliers, par exemple tous les mois ou tous les trimestres.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Des journaux d’activité Azure AD (connexions et audit) sont à présent disponibles via Microsoft Graph.

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports
 
Des journaux d’activité Azure AD (connexions et audit) sont à présent disponibles via Microsoft Graph. Nous avons exposé deux points de terminaison via Microsoft Graph pour accéder à ces journaux. Pour démarrer, consultez nos [documents](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) concernant l’accès par programmation aux API de création de rapports Azure AD. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Améliorations apportées à l’expérience d’échange B2B et à celle permettant de quitter une organisation

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C

**Échange Juste-à-temps :** À partir du moment où vous partagez une ressource avec un utilisateur invité au moyen d’une API B2B, vous n’avez pas besoin de lui envoyer d’invitation par e-mail. Dans la plupart des cas, l’utilisateur invité peut accéder à la ressource. Il lui suffit de se laisser guider dans cette procédure d’échange. Qu’importe qu’il reçoive ou non l’e-mail. Vous n’avez plus besoin de demander à vos utilisateurs invités s’ils ont bien cliqué sur le lien d’échange que vous leur avez envoyé. Aussi, à partir du moment où SPO utilise le gestionnaire d’invitation, les pièces jointes dans le cloud peuvent avoir la même URL réglementaire pour tous les utilisateurs (internes et externes), quel que soit l’état de l’échange.

**Expérience d’échange moderne :** Fini les pages d’accueil d’échange avec fractionnement de l’écran. Les utilisateurs peuvent bénéficier d’une expérience moderne et accepter la déclaration de confidentialité de l’organisation à l’origine de l’invitation, comme ils le font déjà pour les applications tierces.

**Les utilisateurs invités peuvent quitter l’organisation :** Lorsque la relation entre les utilisateurs et l’organisation est terminée, ils peuvent quitter celle-ci d’eux-mêmes. Il n’ont plus besoin d’appeler l’administrateur de l’organisation qui les a invités pour demander leur suppression et n’ont pas non plus besoin d’ouvrir un ticket de support.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nouvelles applications fédérées disponibles dans la galerie Azure AD App - Mai 2018

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En mai 2018, nous avons ajouté à notre galerie d’applications les 18 applications suivantes qui prennent en charge la fédération :

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty Govern, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), [まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [Arc Publishing - SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial).

Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nouveaux guides de déploiement étape par étape pour Azure Active Directory

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Répertoire
 
Nouveau guide étape par étape concernant le déploiement d’Azure Active Directory (Azure AD), notamment la réinitialisation de mot de passe libre-service, l’authentification unique (SSO), l’accès conditionnel, les proxys d’application, l’attribution d’utilisateurs, ainsi que les services de fédération Active Directory (AD FS) pour l’authentification directe et pour la synchronisation du hachage de mot de passe.

Pour consulter les guides de déploiement, accédez au dépôt GitHub [Identity Deployment Guides](https://aka.ms/DeploymentPlans) (Guides de déploiement des identités). Pour envoyer vos commentaires sur les guides de déploiement, utilisez le [formulaire de commentaires Plan de déploiement](https://aka.ms/deploymentplanfeedback). Si vous avez des questions sur les guides de déploiement, contactez-nous ici : [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Recherche d’applications d’entreprise : charger d’autres applications

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Authentification unique
 
Vous ne parvenez pas à localiser vos applications ou les principaux de service ? Nous vous permettons désormais de charger d’autres applications dans la liste de vos applications d’entreprise. Par défaut, nous affichons 20 applications. Vous pouvez désormais cliquer sur **Load more** (Charger plus d’applications) pour afficher d’autres applications. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>La version d’AADConnect du mois de mai contient une préversion publique de l’intégration à PingFederate, d’importantes mises à jour de sécurité, de nombreux correctifs de bogues et de nouveaux outils de résolution des problèmes. 

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** AD Connect  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
La version d’AADConnect du mois de mai contient une préversion publique de l’intégration à PingFederate, d’importantes mises à jour de sécurité, de nombreux correctifs de bogues et de nouveaux outils de résolution des problèmes. Vous trouverez les notes de publication [ici](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Révisions d’accès Azure AD : application automatique

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance

La fonctionnalité de révision d’accès des groupes et applications fait maintenant partie de l’offre Azure AD Premium P2. Un administrateur peut demander à appliquer automatiquement les modifications du réviseur à ce groupe ou à cette application dès que la révision d’accès est terminée. L’administrateur peut également spécifier ce qu’il advient de l’accès continu de l’utilisateur si les réviseurs n’ont pas répondu, ou encore s’ils ont supprimé l’accès, conservé l’accès ou suivi les suggestions du système. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-responsemode-for-new-apps"></a>Les jetons d’ID ne peuvent plus être retournés à l’aide de la requête response_mode pour les nouvelles applications. 

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Les applications créées le 25 avril 2018 et après ne pourront plus demander un **id_token** à l’aide de la **requête** response_mode.  Cette opération met en ligne Azure AD avec les spécifications OIDC et vous aide à limiter la surface d’attaque des applications.  Rien n’empêche les applications créées avant le 25 avril 2018 d’utiliser la **requête** response_mode avec un paramètre response_type **id_token**.  Si vous demandez un id_token depuis AAD, vous recevez un message d’erreur du type : **AADSTS70007: ‘query’ is not a supported value of ‘response_mode’ when requesting a token** (AADSTS70007 : « query » n’est pas une valeur « response_mode » prise en charge au moment de demander un jeton).

Les requêtes response_mode **fragment** et **form_post** continuent de fonctionner. Au moment de créer des objets d’application (par exemple, pour l’utilisation du proxy d’application), vérifiez que l’une de ces requêtes response_mode est utilisée avant de créer l’application.  

---
 
## <a name="april-2018"></a>Avril 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Les jetons d’accès Azure AD B2C sont en disponibilité générale

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C 

Vous pouvez maintenant accéder aux API web sécurisées par Azure AD B2C à l’aide de jetons d’accès. La fonctionnalité passe de la préversion publique à la disponibilité générale. L’interface utilisateur permettant de configurer les applications Azure AD B2C et les API web a été améliorée, et d’autres améliorations mineures ont également été apportées.
 
Pour plus d’informations, consultez [Azure AD B2C : Demande de jetons d'accès](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Test de la configuration de l’authentification unique pour les applications SAML

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Authentification unique

Lors de la configuration d’applications SSO SAML, vous pouvez tester l’intégration dans la page de configuration. Si vous rencontrez une erreur pendant la connexion, vous pouvez indiquer cette erreur dans l’expérience de test et recevoir d’Azure AD les étapes permettant de résoudre le problème.

Pour plus d'informations, consultez les pages suivantes :

- [Configuration de l'authentification unique pour les applications ne faisant pas partie de la galerie d'applications Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Débogage d’une authentification unique basée sur SAML aux applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>La fonctionnalité Conditions d’utilisation Azure AD propose à présent des rapports par utilisateur

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Conformité
 
Les administrateurs peuvent à présent sélectionner des conditions d’utilisation données et afficher tous les utilisateurs qui ont accepté les conditions d’utilisation, ainsi que la date et l’heure de leur consentement.

Pour plus d’informations, consultez [Fonctionnalité Conditions d’utilisation d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health : Adresse IP à risque pour la protection par verrouillage Extranet AD FS 

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Monitoring et création de rapports

Connect Health prend maintenant en charge la capacité de détecter des adresses IP qui dépassent un seuil d’échecs de connexion U/P (utilisateur/mot de passe) sur une base horaire ou quotidienne. Voici les fonctionnalités fournies par cette fonctionnalité :

- Rapport complet indiquant l’adresse IP et le nombre d’échecs de connexion généré sur une base horaire/quotidienne avec un seuil personnalisable.
- Alertes par e-mail indiquant quand une adresse IP spécifique a dépassé le seuil d’échecs de connexion U/P sur une base horaire/quotidienne.
- Option de téléchargement pour effectuer une analyse détaillée des données

Pour plus d’informations, consultez [Rapports sur les adresses IP à risque](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Configuration d’applications facilitée avec l’URL ou le fichier de métadonnées

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Authentification unique

Dans la page Applications d’entreprise, les administrateurs peuvent charger un fichier de métadonnées SAML pour l’authentification unique SAML pour les applications AAD, que celles-ci soient incluses ou non dans la galerie.

En outre, vous pouvez utiliser l’URL des métadonnées de fédération d’application Azure AD pour configurer l’authentification unique avec l’application ciblée.

Pour plus d’informations, consultez la page [Configuration de l’authentification unique pour les applications ne faisant pas partie de la galerie d’applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Fonctionnalité Conditions d’utilisation Azure AD maintenant à la disposition générale

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Conformité
 

La fonctionnalité Conditions d’utilisation Azure AD est passée de la préversion publique à la disponibilité générale.

Pour plus d’informations, consultez [Fonctionnalité Conditions d’utilisation d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Autoriser ou bloquer des invitations aux utilisateurs B2B à partir d’organisations spécifiques

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 

Vous pouvez maintenant spécifier les organisations partenaires avec lesquelles vous voulez partager et collaborer dans Azure AD B2B Collaboration. Pour ce faire, vous pouvez choisir de créer une liste de domaines autorisés ou refusés spécifiques. Lorsqu’un domaine est bloqué à l’aide de ces fonctionnalités, les employés ne peuvent plus envoyer d’invitations aux personnes de ce domaine.

Cela vous permet de contrôler l’accès à vos ressources, tout en permettant une expérience fluide pour les utilisateurs approuvés.

Cette fonctionnalité B2B Collaboration est disponible pour tous les clients Azure Active Directory. Elle peut servir conjointement avec les fonctionnalités Azure AD Premium, telles que la protection des identités et l’accès conditionnel pour un contrôle plus précis qui permet de savoir quand et de quelle façon les utilisateurs professionnels externes se connectent et obtiennent un accès.

Pour plus d’informations, consultez [Autoriser ou bloquer des invitations aux utilisateurs B2B à partir d’organisations spécifiques](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nouvelles applications fédérées disponibles dans la galerie Azure AD App

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En avril 2018, nous avons ajouté à notre galerie d’applications les 13 applications suivantes qui prennent en charge la fédération :

Criterion HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (On-Premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [Dynamic Signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), Shelf, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial).

Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Octroyer aux utilisateurs B2B dans Azure AD l’accès à vos applications locales (préversion publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C

Si votre organisation utilise des fonctionnalités d’Azure Active Directory (Azure AD) B2B Collaboration pour inviter des utilisateurs d’organisations partenaires à votre instance Azure AD, vous pouvez désormais fournir à ces utilisateurs B2B un accès aux applications locales. Ces applications locales peuvent utiliser l’authentification basée SAML ou l’Authentification Windows intégrée (IWA) avec la délégation contrainte Kerberos (KCD).

Pour plus d’informations, consultez [Accorder aux utilisateurs B2B dans Azure AD l’accès à vos applications locales](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Accéder à des tutoriels sur l’intégration de l’authentification unique dans la Place de marché Azure

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Intégration tierce

Si une application qui est répertoriée dans la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) prend en charge l’authentification unique SAML, en cliquant sur **Obtenir maintenant**, vous accédez au tutoriel sur l’intégration associé à cette application. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Amélioration des performances de l’attribution automatique d’utilisateurs Azure AD auprès des applications SaaS

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 
Auparavant, les clients qui utilisaient les connecteurs d’attribution d’utilisateurs Azure Active Directory pour les applications SaaS (Salesforce, ServiceNow et Box par exemple) pouvaient rencontrer une baisse de performances si leurs locataires Azure AD contenaient plus de 100 000 utilisateurs et groupes combinés, et s’ils utilisaient les attributions de groupes et d’utilisateurs pour déterminer les utilisateurs à attribuer.

Le 2 avril 2018, de très importantes améliorations des performances ont été déployées sur le service d’attribution Azure AD. Celles-ci réduisent considérablement le temps nécessaire pour effectuer les synchronisations initiales entre Azure Active Directory et les applications SaaS cibles.

Par conséquent, de nombreux clients pour lesquels les synchronisations initiales aux applications nécessitaient plusieurs jours ou ne se terminaient jamais effectuent maintenant cette opération en quelques minutes ou heures.

Pour plus d’informations, consultez [Que se passe-t-il pendant l’approvisionnement ?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning).

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Réinitialisation de mot de passe en libre-service à partir de l’écran de verrouillage Windows 10 pour les ordinateurs joints à une version hybride d’Azure AD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Réinitialisation de mot de passe en libre-service  
**Fonctionnalité de produit :** Authentification utilisateur
 
Nous avons mis à jour la fonctionnalité Windows 10 SSPR pour prendre en charge les ordinateurs qui sont joints à des environnements Azure AD hybrides. Cette fonctionnalité disponible dans Windows 10 RS4 permet aux utilisateurs de réinitialiser leur mot de passe à partir de l’écran de verrouillage d’un ordinateur Windows 10. Les utilisateurs qui sont activés et inscrits pour la réinitialisation de mot de passe en libre-service peuvent utiliser cette fonctionnalité.

Pour plus d’informations, consultez [Réinitialisation de mot de passe Azure AD depuis l’écran de connexion](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Mars 2018
 
### <a name="certificate-expire-notification"></a>Notification d’expiration de certificat

**Type :** Résolution  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Authentification unique
 
Azure AD envoie une notification quand le certificat d’une application de la galerie ou hors galerie est sur le point d’expirer. 

Certains utilisateurs n’ont pas reçu les notifications concernant les applications d’entreprise configurées pour l’authentification unique SAML. Ce problème a été résolu. Azure AD envoie une notification concernant les certificats expirant dans 7, 30 et 60 jours. Vous pouvez visualiser cet événement dans les journaux d’audit. 

Pour plus d'informations, consultez les pages suivantes :

- [Gérer des certificats pour l’authentification unique fédérée sur Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Rapports d’activité d’audit dans le portail Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Fournisseurs d’identité Twitter et GitHub dans Azure AD B2C

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C
 
Vous pouvez maintenant ajouter Twitter ou GitHub en tant que fournisseur d’identité dans Azure AD B2C. Twitter passe de la version préliminaire publique à la disponibilité générale. GitHub sort en version préliminaire publique.

Pour plus d’informations, consultez la page [Qu’est-ce qu’Azure AD B2B Collaboration ?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Restreindre l’accès du navigateur à l’aide d’Intune Managed Browser avec accès conditionnel par application Azure AD pour iOS et Android

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
**Désormais en préversion publique**

**Authentification unique sur Intune Managed Browser :** Vos employés peuvent utiliser l’authentification unique sur des clients natifs (tels que Microsoft Outlook) et Intune Managed Browser pour toutes les applications Azure AD connectés.

**Prise en charge de l’accès conditionnel dans Intune Managed Browser :** Vous pouvez désormais obliger vos employés à utiliser Intune Managed Browser grâce à des stratégies d’accès conditionnel basé sur les applications.

Pour en savoir plus sur ce sujet, consultez notre [billet de blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Pour plus d'informations, consultez les pages suivantes :

- [Configurer l’accès conditionnel basé sur les applications](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Gérer des stratégies Managed Browser](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Cmdlets de proxy d’application dans le module de disponibilité générale Powershell

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès
 
Les cmdlets de proxy d’application sont désormais pris en charge dans le module de disponibilité générale Powershell. Cela vous oblige à garder vos modules Powershell à jour : si vos dernières mises à jour remontent à plus d’un an, certaines applets de commande peuvent cesser de fonctionner. 

Pour plus d'informations, consultez la page [Azure AD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Les clients Office 365 natifs sont pris en charge par l’authentification unique transparente avec un protocole non interactif

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Les utilisateurs utilisant des clients Office 365 natifs (version 16.0.8730.xxxx et versions ultérieures) profitent d’une expérience de connexion silencieuse avec l’authentification unique transparente. Cette prise en charge est rendue possible par l’ajout un protocole non interactif (WS-Trust) à Azure AD.

Pour plus d’informations, consultez la page [Fonctionnement des connexions sur un client natif avec l’authentification unique transparente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work).
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Expérience de connexion silencieuse avec l’authentification unique fluide lorsqu’une application envoie des demandes de connexion aux points de terminaison des locataires d’Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Les utilisateurs obtiennent une expérience de connexion silencieuse avec l’authentification unique transparente si une application (par exemple, `https://contoso.sharepoint.com`) envoie des demandes de connexion aux points de terminaison de locataire d’Azure AD, c’est-à-dire `https://login.microsoftonline.com/contoso.com/<..>` ou `https://login.microsoftonline.com/<tenant_ID>/<..>` au lieu du point de terminaison habituel d’Azure AD (`https://login.microsoftonline.com/common/<...>`).

Pour plus d’informations, consultez la page [Authentification unique transparente Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Besoin d’ajouter seulement une URL Azure AD au lieu de deux aux paramètres de zone intranet des utilisateurs pour déployer l’authentification unique transparente

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Pour déployer l’authentification unique transparente chez vos utilisateurs, vous ne devez ajouter d’une URL Azure AD à leurs paramètres de zone intranet via une stratégie de groupe dans Active Directory : `https://autologon.microsoftazuread-sso.com`. Auparavant, les clients devaient ajouter deux URL.

Pour plus d’informations, consultez la page [Authentification unique transparente Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nouvelles applications fédérées disponibles dans la galerie Azure AD App

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En mars 2018, nous avons ajouté à notre galerie d’applications les 15 applications suivantes qui prennent en charge la fédération :

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant by FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [Amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial).

Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>La fonction PIM pour les ressources Azure est en disponibilité générale

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Privileged Identity Management  
**Fonctionnalité de produit :** Privileged Identity Management
 
Si vous utilisez Azure AD Privileged Identity Management pour les rôles d’annuaire, vous pouvez maintenant utiliser les fonctions d’accès et d’affectation temporaires PIM pour les rôles de ressources Azure comme les abonnements, les groupes de ressources, les machines virtuelles et les autres ressources prises en charge par Azure Resource Manager. Appliquez l’authentification multifacteur lors de l’activation juste-à-temps de rôles et planifiez les activations en accord avec les fenêtres de changement approuvées. En outre, cette version contient des améliorations non disponibles dans la version préliminaire publique, notamment une nouvelle interface utilisateur, des workflows d’approbation et la possibilité d’étendre les rôles arrivant à expiration et de renouveler les rôles expirés.

Pour plus d’informations, consultez [PIM pour les ressources Azure (préversion)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Ajout de revendications facultatives à vos jetons d’applications (version préliminaire publique)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Votre application Azure AD peut maintenant demander des revendications personnalisées ou facultatives dans les jetons JWT ou SAML.  Il s’agit de revendications relatives à l’utilisateur ou au locataire qui ne sont pas incluses par défaut dans le jeton en raison d’une contrainte de taille ou d’applicabilité.  Cette fonction est disponible en version préliminaire publique pour les applications Azure AD sur les points de terminaison v1.0 et v2.0.  Consultez la documentation pour plus d’informations sur les revendications pouvant être ajoutées et pour savoir comment modifier le manifeste de votre application pour les demander.  

Pour plus d’informations, consultez la page [Revendications facultatives dans Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD prend en charge PKCE pour des flux OAuth plus sécurisés

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Les documents Azure AD ont été mis à jour et indiquent désormais la prise en charge de PKCE, qui permet de sécuriser davantage la communication pendant le flux d’octroi de code d’autorisation OAuth 2.0.  Les méthodes S256 et plaintext code_challenge sont prises en charge sur les points de terminaison v1.0 et v2.0. 

Pour plus d’informations, consultez [Demander un code d’autorisation](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-getworkers-api"></a>Prise en charge de l’approvisionnement de toutes les valeurs d’attribut utilisateur disponibles dans l’API Workday Get_Workers

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 
La version préliminaire publique de l’approvisionnement entrant de Workday vers Active Directory et Azure AD prend désormais en charge la possibilité d’extraire et d’approvisionner toutes les valeurs d’attribut disponibles dans l’API Workday Get_Workers. Cela permet la prise en charge de centaines d’attributs standard et personnalisés supplémentaires, en plus de ceux fournis avec la version initiale du connecteur d’approvisionnement entrant Workday.

Pour plus d'informations, consultez les pages suivantes : [Personnaliser la liste des attributs d’utilisateurs Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Changement de l’appartenance dynamique à un groupe en appartenance statique et vice versa

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Collaboration
 
Il est possible de modifier la façon dont l’appartenance est gérée dans un groupe. Cela est utile lorsque vous souhaitez conserver le même nom et le même ID de groupe dans le système, afin que toutes les références au groupe existantes soient toujours valides ; la création d’un groupe nécessiterait la mise à jour de ces références.
Nous avons mis à jour le Centre d’administration Azure AD pour la prise en charge de cette fonctionnalité. Maintenant, les clients peuvent convertir l’appartenance dynamique à des groupes existants en appartenance affectée et vice versa. Les cmdlets PowerShell existants sont également toujours disponibles.

Pour plus d’informations, consultez [règles d’appartenance dynamique pour les groupes dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Comportement de déconnexion amélioré avec l’authentification unique transparente

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Auparavant, même si les utilisateurs se déconnectaient explicitement d’une application sécurisée par Azure AD, ils étaient automatiquement reconnectés avec l’authentification unique transparente s’ils tentaient à nouveau d’accéder à une application Azure AD au sein de leur réseau d’entreprise à partir d’un appareil joint à un domaine. Avec cette modification, la déconnexion est prise en charge.  Cela permet aux utilisateurs de conserver ou modifier le compte Azure AD auquel se connecter, au lieu d’être connecté automatiquement à l’aide de l’authentification unique transparente.

Pour plus d’informations, consultez [Authentification unique transparente Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Connecteur de proxy d’application version 1.5.402.0

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Cette version de connecteur est progressivement déployée tout au long du mois de novembre. Cette nouvelle version de connecteur inclut les modifications suivantes :

- Le connecteur définit désormais des cookies au niveau du domaine et non plus au niveau du sous-domaine. Cela garantit une meilleure expérience d’authentification unique et évite les invites d’authentification redondantes.
- Prise en charge des demandes de codage mémorisé en bloc
- Surveillance améliorée de l’intégrité du connecteur 
- Correction de plusieurs bogues et stabilité améliorée

Pour plus d’informations, consultez [Présentation des connecteurs de proxy d’application Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Février 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Navigation améliorée pour la gestion des utilisateurs et groupes

**Type :** Modification planifiée  
**Catégorie de service :** Gestion de répertoires  
**Fonctionnalité de produit :** Répertoire

L’expérience de navigation pour la gestion des utilisateurs et groupes a été simplifiée. Vous pouvez maintenant passer directement de la vue d’ensemble de l’annuaire à la liste de tous les utilisateurs, et accéder plus facilement à la liste des utilisateurs supprimés. Vous pouvez également accéder directement de la vue d’ensemble de l’annuaire à la liste de tous les groupes, et accéder plus facilement aux paramètres de gestion de groupe. Également depuis la vue d’ensemble de l’annuaire, vous pouvez rechercher un utilisateur, un groupe, une application d’entreprise ou une inscription d’application. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Disponibilité des rapports d’audit et des rapports sur les connexions dans Microsoft Azure gérée par 21Vianet (Azure China 21Vianet)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Azure Stack  
**Fonctionnalité de produit :** Monitoring et création de rapports

Les rapports du journal d’activité Azure AD sont maintenant disponibles dans Microsoft Azure géré par des instances 21Vianet (Azure China 21Vianet). Les journaux suivants sont inclus :

- **Journaux d’activité des connexions** : incluent tous les journaux des connexions associés à votre locataire.

- **Journaux d’audit de mot de passe libre-service** : comprennent tous les journaux d’audit SSPR.

- **Journaux d’audit de gestion des annuaires** : incluent tous les journaux d’audit liés à la gestion des annuaires, comme la gestion des utilisateurs, la gestion des applications, etc.

Ces journaux vous permettent d’obtenir des insights sur le fonctionnement de votre environnement. Les données fournies vous permettent de :

- Déterminer la façon dont les applications et les services sont utilisés par vos utilisateurs.

- Résoudre les problèmes empêchant les utilisateurs d’effectuer leur travail.

Pour plus d’informations sur l’utilisation de ces rapports, consultez [Création de rapports Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Utilisation du rôle « Lecteur de rapport » (rôle non-administrateur) pour afficher les rapports d’activité Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Monitoring et création de rapports

Suite aux commentaires clients sur la possibilité d’autoriser les rôles non administrateurs à accéder aux journaux d’activité Azure AD, nous permettons désormais aux utilisateurs dotés du rôle « Lecteur de rapport » d’accéder aux journaux d’activité des audits et des connexions sur le portail Azure, ainsi que d’utiliser nos API Graph. 

Pour plus d’informations sur l’utilisation de ces rapports, consultez [Création de rapports Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Revendication EmployeeID disponible en tant qu’attribut utilisateur et identificateur d’utilisateur

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Authentification unique
 
Vous pouvez configurer **EmployeeID** en tant qu’identificateur d’utilisateur et attribut utilisateur pour les utilisateurs membres et les invités B2B dans les applications d’authentification SAML à partir de l’interface utilisateur de l’application d’entreprise.

Pour plus d’informations, consultez [Personnalisation des revendications émises dans le jeton SAML pour les applications d’entreprise dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Gestion simplifiée des applications à l’aide de caractères génériques dans Proxy d’application Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Authentification utilisateur
 
Pour simplifier le déploiement d’application et réduire votre charge administrative, nous prenons désormais en charge la possibilité de publier des applications à l’aide de caractères génériques. Pour publier une application générique, vous pouvez suivre le flux de publication d’application standard, mais utiliser un caractère générique dans les URL internes et externes.

Pour plus d’informations, consultez [Applications génériques dans le proxy d’application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard).

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nouvelles applets de commande pour prendre en charge la configuration du proxy d’application

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Plateforme

La dernière version du module AzureAD PowerShell Preview contient de nouvelles applets de commande qui permettent aux clients de configurer des applications de proxy d’application à l’aide de PowerShell.

Ces nouvelles applets de commande sont : 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nouvelles applets de commande pour prendre en charge la configuration des groupes

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Plateforme

La dernière version du module AzureAD PowerShell contient des applets de commande qui permettent de gérer les groupes dans Azure AD. Ces applets de commande étaient auparavant disponibles dans le module AzureADPreview et sont maintenant ajoutées au module AzureAD.

Les applets de commande Group désormais mises à la disposition générale sont : 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Nouvelle version d’Azure AD Connect disponible

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** AD Sync  
**Fonctionnalité de produit :** Plateforme
 
Azure AD Connect est l’outil préféré pour synchroniser des données entre Azure AD et des sources de données locales, notamment Windows Server Active Directory et LDAP.

>[!Important]
>Cette version introduit des modifications de schéma et de règles de synchronisation. Le service de synchronisation Azure AD Connect déclenche des étapes d’importation et de synchronisation complètes après une mise à niveau. Pour plus d’informations sur la façon de modifier ce comportement, consultez [Comment différer la synchronisation complète après la mise à niveau](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Cette version comprend les mises à jour et modifications suivantes :

**Problèmes résolus**

- Résolution du problème de fenêtre de synchronisation sur les tâches en arrière-plan pour la page Filtrage de partitions lors du passage à la page suivante

- Correction d’un bogue qui entraînait une violation d’accès lors de l’action personnalisée ConfigDB.

- Correction d’un bogue de récupération suite à un délai de connexion SQL.

- Correction d’un bogue qui entraînait l’échec d’une vérification des prérequis pour les certificats avec caractères génériques SAN.

- Correction d’un bogue qui entraînait un incident d’exécution de miiserver.exe lors d’une exportation du connecteur AAD.

- Correction d’un bogue concernant la journalisation d’une tentative d’utilisation d’un mot de passe incorrect sur le contrôleur de domaine qui entraînait la modification de la configuration par l’Assistant AAD Connect.

**Améliorations et nouvelles fonctionnalités**
 
- Télémétrie applicative : l’administrateur peut activer/désactiver cette catégorie de données.

- Données d’intégrité Azure AD  : l’administrateur doit accéder au portail de contrôle d’intégrité pour contrôler ses paramètres d’intégrité. Une fois que la stratégie du service a été modifiée, les agents la lisent et la mettent en œuvre.

- Ajout d’actions de configuration d’écriture différée sur les appareils et d’une barre de progression pour l’initialisation de la page.

- Amélioration des diagnostics généraux avec un rapport HTML et une collecte de données complète dans un rapport au format ZIP/HTML.

- Amélioration de la fiabilité des mises à niveau automatiques et ajout d’une télémétrie supplémentaire pour garantir la possibilité de détermination de l’intégrité du serveur.

- Restriction des autorisations disponibles pour les comptes privilégiés sur le compte du Connecteur AD. Dans le cas des nouvelles installations, l’Assistant restreint les autorisations dont disposent les comptes privilégiés sur le compte MSOL après la création de ce dernier. Les modifications affectent les installations rapides et les installations personnalisées avec création automatique de compte.

- Modification du programme d’installation afin de ne pas exiger des privilèges d’administrateur système pour une nouvelle installation d’AADConnect.

- Nouvel utilitaire destiné à résoudre les problèmes de synchronisation pour un objet spécifique. Cet utilitaire vérifie les éléments suivants :

    - détection d’une incompatibilité de nom d’utilisateur principal (UPN) entre l’objet utilisateur synchronisé et le compte d’utilisateur dans le locataire Azure AD ;
  
    - vérification si l’objet a été exclu de la synchronisation en raison d’un filtrage de domaine ;
  
    - vérification si l’objet a été exclu de la synchronisation en raison d’un filtrage d’unité d’organisation.

- Nouvel utilitaire destiné à synchroniser le hachage de mot de passe actuel stocké dans le service Active Directory local pour un compte d’utilisateur spécifique. Cet utilitaire ne nécessite aucune modification de mot de passe. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Ajout d’applications prenant en charge les stratégies Intune App Protection à des fins d’utilisation avec l’accès conditionnel en fonction de l’application Azure AD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités

Nous avons ajouté d’autres applications qui prennent en charge l’accès conditionnel en fonction de l’application. Maintenant, vous pouvez accéder à Office 365 et à d’autres applications cloud liées à Azure AD à l’aide de ces applications clientes approuvées.

Les applications suivantes seront ajoutées d’ici la fin du mois de février :

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Pour plus d'informations, consultez les pages suivantes :

- [Spécification d’application cliente approuvée](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Accès conditionnel basé sur les applications Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Mise à jour des conditions d’utilisation pour l’expérience mobile 

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Conformité

Lorsque les conditions d’utilisation s’affichent, vous pouvez maintenant cliquer sur **Des problèmes d’affichage ? Cliquez ici**. Ce lien ouvre les conditions d’utilisation en mode natif sur votre appareil. Quelle que soit la taille de police dans le document ou la taille d’écran de l’appareil, vous pouvez effectuer un zoom avant et lire le document selon vos besoins. 

---
 
## <a name="january-2018"></a>Janvier 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nouvelles applications fédérées disponibles dans la galerie Azure AD App 

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce

En janvier 2018, les applications suivantes prenant en charge la fédération ont été ajoutées à la galerie d’applications :

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk Federated Directory et [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial).

Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Connexion avec un risque supplémentaire détecté

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

L’information que vous obtenez pour un événement à risque détecté est liée à votre abonnement Azure AD. Avec l’édition Azure AD Premium P2, vous obtenez des informations très détaillées sur toutes les détections sous-jacentes.

Avec l’édition Azure AD Premium P1, les détections qui ne sont pas couvertes par la licence s’affichent comme des événements à risque Connexion avec un risque supplémentaire détecté.

Pour plus d’informations, consultez [Événements à risque dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Masquer des applications Office 365 dans les panneaux d’accès de l’utilisateur

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Authentification unique

Désormais, vous pouvez mieux gérer comment les applications Office 365 apparaissent dans les panneaux d’accès de vos utilisateurs, grâce à un nouveau paramètre utilisateur. Cette option permet de réduire le nombre d’applications dans les panneaux d’accès d’un utilisateur, si vous préférez n’afficher que les applications Office dans le portail Office. Ce paramètre se trouve dans **Paramètres utilisateur** et s’intitule **Les utilisateurs peuvent voir uniquement les applications Office 365 dans le portail Office 365**.

Pour plus d’informations, consultez [Masquer une application de l’expérience utilisateur dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Se connecter de manière transparente à des applications autorisant l’authentification unique par mot de passe directement depuis l’URL de l’application 

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Authentification unique

L’extension de navigateur Mes applications est désormais disponible via un outil pratique qui vous propose une authentification unique à Mes applications sous la forme d’un raccourci dans votre navigateur. Après l’installation, l’utilisateur voit une icône en forme de gaufre dans son navigateur, qui lui assure un accès rapide aux applications. Les utilisateurs peuvent désormais profiter des fonctionnalités suivantes :

- Possibilité de se connecter directement aux applications à authentification unique par mot de passe dans la page de connexion de l’application
- Lancer une application à l’aide de la fonctionnalité de recherche rapide
- Raccourcis vers les applications récemment utilisées à partir de l’extension
- L’extension est disponible pour Microsoft Edge, Chrome et Firefox.
 
Pour plus d’informations, consultez [Extension de connexion sécurisée à Mes applications](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Mise hors service de l’expérience d’administration Azure AD dans le portail Azure Classic

**Type :** Déconseillé   
**Catégorie de service :** Azure AD  
**Fonctionnalité de produit :** Répertoire

À compter du 8 janvier 2018, l’expérience d’administration d’Azure AD dans le portail Azure Classic a été retirée. Un retrait qui a coïncidé avec celui du portail Azure Classic. À l’avenir, vous devrez utiliser le [Centre d’administration d’Azure AD](https://aad.portal.azure.com) pour toute l’administration d’Azure AD dans le portail.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Le portail web PhoneFactor a été mis hors service

**Type :** Déconseillé  
**Catégorie de service :** Azure AD  
**Fonctionnalité de produit :** Répertoire
 
À compter du 8 janvier 2018, le portail web PhoneFactor a été retiré. Ce portail permettait d’administrer le serveur MFA, mais ces fonctions ont été transférées au portail Azure à l’adresse portal.azure.com. 

La configuration de MFA se trouve dans : **Azure Active Directory \> serveur MFA**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Déprécier les rapports Azure AD

**Type :** Déconseillé  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités  


Avec la disponibilité générale de la nouvelle console d’administration d’Azure Active Directory et les nouvelles API désormais disponibles pour les rapports d’activité et de sécurité, les API de rapport sous le point de terminaison « /rapports » ont été retirées le 31 décembre 2017.

**Qu’est-ce qui est disponible ?**

Dans le cadre de la transition vers la nouvelle console d’administration, nous avons créé 2 API pour récupérer les journaux d’activité d’Azure AD. Le nouvel ensemble d’API fournit une fonctionnalité enrichie de filtrage et de tri en plus de fournir des activités d’audit et de connexion plus avancées. Les données précédemment disponibles via les rapports de sécurité sont désormais accessibles via l’API d’événements à risque Identity Protection dans Microsoft Graph.

Pour plus d'informations, consultez les pages suivantes :

- [Prise en main de l’API de création de rapports Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Prise en main d’Azure Active Directory Identity Protection et de Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Décembre 2017

### <a name="terms-of-use-in-the-access-panel"></a>Conditions d’utilisation dans le Panneau d’accès

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Conformité
 
Vous pouvez maintenant accéder au Panneau d’accès et afficher les conditions d’utilisation que vous avez précédemment acceptées.

Procédez comme suit :

1. Accédez au [portail MyApps](https://myapps.microsoft.com) et connectez-vous.

2. Dans l’angle supérieur droit, sélectionnez votre nom puis **Profil** dans la liste. 

3. Dans votre **profil**, cliquez sur **Vérifier les conditions d’utilisation**. 

4. Vérifiez les conditions d’utilisation que vous avez acceptées. 

Pour plus d’informations, consultez [Fonctionnalité Conditions d’utilisation d’Azure Active Directory (préversion)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nouvelle expérience de connexion Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Azure AD  
**Fonctionnalité de produit :** Authentification utilisateur
 
Les interfaces utilisateur d’Azure AD et du système d’identité de compte Microsoft ont été repensées pour gagner en cohérence. De plus, la page de connexion à Azure AD collecte d’abord le nom d’utilisateur, puis les informations d’identification dans un second écran.

Pour plus d’informations, consultez [The new Azure AD Signin Experience is now in Public Preview (La nouvelle expérience de connexion Azure AD est désormais en préversion publique)](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Invites de connexion moins nombreuses : La nouvelle expérience « Maintenir la connexion » pour Azure AD est en préversion

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Azure AD  
**Fonctionnalité de produit :** Authentification utilisateur
 
Nous avons remplacé la case à cocher **Maintenir la connexion** dans la page de connexion à Azure AD par une nouvelle invite qui s’affiche une fois l’utilisateur authentifié. 

Si vous répondez **Oui** à cette invite, le service vous donne un jeton d’actualisation permanent. Ce comportement est le même que quand vous activiez la case à cocher **Maintenir la connexion** dans l’ancienne expérience. Pour les locataires fédérés, cette invite s’affiche une fois l’utilisateur authentifié auprès du service fédéré.

Pour plus d’informations, consultez [Invites de connexion moins nombreuses : La nouvelle expérience « Maintenir la connexion » pour Azure AD est en préversion](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Ajouter la configuration pour exiger le développement des conditions d’utilisation avant leur acceptation

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Conformité
 
Une option destinée aux administrateurs oblige les utilisateurs à développer les conditions d’utilisation avant de les accepter.

Sélectionnez **Activé** ou **Désactivé** selon que vous souhaitiez obliger ou non les utilisateurs à développer les conditions d’utilisation. Le paramètre **Activé** oblige les utilisateurs à afficher les conditions d’utilisation avant de les accepter.

Pour plus d’informations, consultez [Fonctionnalité Conditions d’utilisation d’Azure Active Directory (préversion)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Activation délimitée des attributions de rôles éligibles

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Privileged Identity Management  
**Fonctionnalité de produit :** Privileged Identity Management
 
Vous pouvez utiliser l’activation délimitée pour activer les attributions de rôles de ressources Azure éligibles avec moins d’autonomie que les valeurs d’attribution par défaut d’origine. Par exemple, supposons que vous soyez affecté comme propriétaire d’un abonnement dans votre locataire. Avec l’activation délimitée, vous pouvez activer le groupe Propriétaire pour au maximum cinq ressources contenues dans l’abonnement (groupes de ressources et machines virtuelles). La délimitation de l’activation contribue à réduire le risque d’exécution de modifications indésirables sur les ressources Azure critiques.

Pour plus d’informations, consultez [Qu’est-ce qu’Azure AD Privileged Identity Management ?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nouvelles applications fédérées dans la galerie Applications Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications Entreprise  
**Fonctionnalité de produit :** Intégration tierce

En décembre 2017, nous avons ajouté à notre galerie d’applications les applications suivantes qui prennent en charge la fédération :

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

Pour plus d’informations sur les applications, consultez [Intégration des applications SaaS à Azure Active Directory](https://aka.ms/appstutorial).

Pour plus d’informations sur le référencement de votre application dans la galerie Azure AD App, consultez [Lister votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Flux de travail d’approbation pour les rôles d’annuaire Azure AD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Privileged Identity Management  
**Fonctionnalité de produit :** Privileged Identity Management
 
Les flux de travail d’approbation pour les rôles d’annuaire Azure AD sont généralement disponibles.

Avec les flux de travail d’approbation, les administrateurs de rôles privilégiés peuvent exiger que les membres des rôles éligibles demandent l’activation du rôle avant de pouvoir l’utiliser. Plusieurs utilisateurs et groupes peuvent se voir déléguer des responsabilités d’approbation. Les membres de rôle exigibles peuvent recevoir des notifications lorsque l’approbation est terminée et que leur rôle est actif.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Authentification directe : Prise en charge de Skype Entreprise

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur

L’authentification directe autorise désormais les connexions des utilisateurs aux applications clientes Skype Entreprise qui prennent en charge l’authentification moderne, notamment les topologies connectée et hybride. 

Pour plus d’informations, consultez [Topologies de Skype Entreprise prises en charge avec l’authentification moderne](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Mises à jour d’Azure Active AD Privileged Identity Management pour Azure RBAC (préversion)

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Privileged Identity Management  
**Fonctionnalité de produit :** Privileged Identity Management
 
Avec l’actualisation de la préversion publique d’Azure AD Privileged Identity Management (PIM) pour le Contrôle d’accès en fonction du rôle Azure, vous pouvez désormais :

* Utiliser JEA (Just Enough Administration).
* Exiger une approbation pour activer les rôles de ressource.
* Planifier l’activation future d’un rôle qui nécessite l’approbation des rôles Azure AD et RBAC Azure.
 
Pour plus d’informations, consultez [PIM pour les ressources Azure (préversion)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>Novembre 2017
 
### <a name="access-control-service-retirement"></a>Mise hors service d’Access Control Service

**Type :** Modification planifiée  
**Catégorie de service :** Access Control service  
**Fonctionnalité de produit :** Access Control service 

Microsoft Azure Active Directory Access Control (également appelé Access Control Service) sera mis hors service fin 2018. D’autres informations, notamment un planning détaillé et des instructions générales de migration, seront fournies dans les prochaines semaines. Sur cette page, vous pouvez laisser des commentaires ainsi que des questions sur Access Control Service. Un membre de l’équipe y répondra.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Restreindre l’accès du navigateur à Intune Managed Browser 

**Type :** Modification planifiée  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités

Vous pourrez restreindre l’accès du navigateur à Office 365 et à d’autres applications cloud connectées à Azure AD en utilisant Intune Managed Browser comme application approuvée. 

Vous pouvez désormais configurer la condition suivante pour conditionner l’accès en fonction de l’application :

**Applications clientes :** Browser

**Quel est l’impact de la modification ?**

Aujourd’hui, l’utilisation de cette condition bloque l’accès. Quand la préversion sera disponible, l’accès nécessitera l’utilisation de l’application Managed Browser. 

Vous trouverez des détails supplémentaires sur cette fonctionnalité et d’autres dans les notes de publication et les blogs à venir. 

Pour plus d’informations, consultez [Accès conditionnel dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nouvelles applications clientes approuvées pour l’accès conditionnel basé sur les applications Azure AD

**Type :** Modification planifiée  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités

Les applications suivantes se trouvent sur la liste des [applications clientes approuvées](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) :

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Pour plus d'informations, consultez les pages suivantes :

- [Spécification d’application cliente approuvée](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Accès conditionnel basé sur les applications Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Prise en charge des conditions d’utilisation en plusieurs langues

**Type :** Nouvelle fonctionnalité    
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Conformité

Les administrateurs peuvent maintenant créer des conditions d’utilisation qui contiennent plusieurs documents PDF. Vous pouvez baliser ces documents PDF avec une langue correspondante. Le fichier PDF qui s’affiche correspond à la langue que l’utilisateur a spécifiée dans ses préférences. S’il n’existe aucune correspondance, la langue par défaut est affichée.

---
 
### <a name="real-time-password-writeback-client-status"></a>État en temps réel du client de réécriture du mot de passe

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Réinitialisation de mot de passe libre-service  
**Fonctionnalité de produit :** Authentification utilisateur

Vous pouvez maintenant consulter l’état de votre client de réécriture du mot de passe local. Cette option est disponible dans la section **Intégration locale** de la page [Réinitialisation du mot de passe](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset). 

En cas de problème avec la connexion à votre client de réécriture local, un message d’erreur s’affiche avec :

- Des informations sur la raison pour laquelle vous ne pouvez pas vous connecter à votre client de réécriture local.
- Un lien vers la documentation qui vous aidera à résoudre le problème 

Pour plus d’informations, consultez [Intégration locale](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Accès conditionnel basé sur les applications Azure AD 
 
**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Azure AD  
**Fonctionnalité de produit :** Protection et sécurité des identités

Vous pouvez désormais restreindre l’accès à Office 365 et à d’autres applications cloud connectées à Azure AD, aux [applications clientes approuvées](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) qui prennent en charge les stratégies de protection des applications Intune par l’[accès conditionnel basé sur les applications Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Des stratégies de protection des applications Intune sont utilisées pour configurer et protéger les données d’entreprise sur ces applications clientes.

En combinant des stratégies d’accès conditionnel [basées sur les applications](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) et [basées sur les appareils](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications), vous bénéficiez de la flexibilité nécessaire pour protéger les données sur les appareils d’entreprise et personnels.

Les conditions et contrôles suivants sont désormais disponibles pour une utilisation avec l’accès conditionnel basé sur les applications :

**Condition de plateforme prise en charge**

- iOS
- Android

**Condition d’applications clientes**

- Applications mobiles et clients de bureau

**Contrôle d’accès**

- Demander une application cliente approuvée

Pour plus d’informations, consultez [Accès conditionnel basé sur les applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Gérer les appareils Azure AD dans le portail Azure

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion et inscription des appareils  
**Fonctionnalité de produit :** Protection et sécurité des identités

À partir de maintenant, tous vos appareils connectés à Azure AD et les activités liées à ces appareils sont regroupés dans un même emplacement. Il existe une nouvelle expérience d’administration pour gérer toutes les identités d’appareils et les paramètres dans le portail Azure. Dans cette version, vous pouvez :

- Afficher tous vos appareils qui sont disponibles pour l’accès conditionnel dans Azure AD.
- Afficher les propriétés, notamment celles de vos appareils hybrides joints à Azure AD.
- Rechercher des clés BitLocker pour vos appareils joints à Azure AD, gérer votre appareil avec Intune, et bien plus encore.
- Gérer les paramètres associés aux appareils Azure AD.

Pour plus d’informations, consultez [Gestion des appareils avec le portail Azure](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Prise en charge de macOS comme plateforme d’appareils pour l’accès conditionnel Azure AD 

**Type :** Nouvelle fonctionnalité    
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités 

Vous pouvez maintenant inclure (ou exclure) macOS comme condition de plateforme d’appareils dans votre stratégie d’accès conditionnel Azure AD. Avec l’ajout de macOS aux plateformes d’appareils prises en charge, vous pouvez :

- **Inscrire et gérer des appareils Mac OS avec Intune.** Comme pour d’autres plateformes telles qu’iOS et Android, une application de portail d’entreprise est disponible pour macOS afin d’unifier les inscriptions. Cette nouvelle application de portail d’entreprise pour macOS permet d’inscrire un appareil avec Intune auprès d’Azure AD.
- **Assurez-vous que les appareils macOS respectent les stratégies de conformité de votre entreprise, définies dans Intune.** Dans Intune sur le portail Azure, vous pouvez maintenant définir des stratégies de conformité pour les appareils macOS. 
- **Restreindre l’accès aux applications dans Azure AD aux seuls appareils macOS conformes.** La création d’une stratégie d’accès conditionnel propose macOS comme plateforme d’appareils. Désormais, vous pouvez créer des stratégies d’accès conditionnel propres à macOS pour l’ensemble des applications cibles dans Azure.

Pour plus d'informations, consultez les pages suivantes :

- [Créer une stratégie de conformité d’appareil pour les appareils macOS avec Intune](https://aka.ms/macoscompliancepolicy)
- [Accès conditionnel dans Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Extension Network Policy Server pour Microsoft Azure Multi-Factor Authentication 

**Type :** Nouvelle fonctionnalité    
**Catégorie de service :**  Authentification multifacteur  
**Fonctionnalité de produit :** Authentification utilisateur

L’extension NPS (Network Policy Server) pour Microsoft Azure Multi-Factor Authentication ajoute des fonctionnalités d’authentification multifacteur à votre infrastructure d’authentification en utilisant vos serveurs. Avec cette extension NPS, vous pouvez ajouter des vérifications par appel téléphonique, SMS ou application téléphonique à votre flux d’authentification. Et ce, sans avoir à installer, configurer et gérer de nouveaux serveurs. 

Cette extension a été créée pour les organisations qui souhaitent protéger des connexions VPN sans déployer le serveur Microsoft Azure Multi-Factor Authentication. L’extension NPS joue le rôle d’adaptateur entre RADIUS et Azure MFA sur le cloud pour fournir un second facteur d’authentification aux utilisateurs fédérés ou synchronisés.

Pour plus d’informations, consultez [Intégrer votre infrastructure NPS existante dans Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Restaurer ou supprimer définitivement les utilisateurs supprimés

**Type :** Nouvelle fonctionnalité    
**Catégorie de service :** User Management  
**Fonctionnalité de produit :** Répertoire 

Dans le centre d’administration Azure AD, vous pouvez désormais :

- Restaurer un utilisateur supprimé. 
- Supprimer définitivement un utilisateur.

**Pour essayer :**

1. Dans le centre d’administration Azure AD, sélectionnez [Tous les utilisateurs](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) dans la section **Gérer**. 

2. Dans la liste **Afficher**, sélectionnez **Utilisateurs supprimés récemment**. 

3. Sélectionnez un ou plusieurs utilisateurs supprimés récemment, puis restaurez-les ou supprimez-les définitivement.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nouvelles applications clientes approuvées pour l’accès conditionnel basé sur les applications Azure AD
 
**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités

Les applications suivantes ont été ajoutées à la liste des [applications clientes approuvées](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) :

- Planificateur Microsoft
- Azure Information Protection 

Pour plus d'informations, consultez les pages suivantes :

- [Spécification d’application cliente approuvée](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Accès conditionnel basé sur les applications Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Utiliser l’opérateur « OU » entre des contrôles dans une stratégie d’accès conditionnel 

**Type :** Fonctionnalité modifiée    
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Vous pouvez désormais utiliser l’opérateur « OU » (exiger un des contrôles sélectionnés) dans des stratégies de contrôle d’accès conditionnel. Cette fonctionnalité vous permet de créer des stratégies avec un opérateur « OU» entre des contrôles d’accès. Par exemple, vous pouvez utiliser cette fonctionnalité pour créer une stratégie qui oblige l’utilisateur à se connecter avec l’authentification multifacteur OU à utiliser un appareil conforme.

Pour plus d’informations, consultez [Contrôles dans l’accès conditionnel Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-events"></a>Agrégation d’événements à risque en temps réel

**Type :** Fonctionnalité modifiée    
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Dans Azure AD Identity Protection, tous les événements à risque en temps réel qui proviennent de la même adresse IP un jour donné sont maintenant agrégés par type d’événement à risque. Cette modification permet de limiter le nombre d’événements à risque affichés, sans modifier la sécurité utilisateur.

La détection en temps réel sous-jacente s’exécute à chaque connexion de l’utilisateur. Si vous avez configuré une stratégie de sécurité des risques de connexion pour l’authentification multifacteur ou pour bloquer l’accès, elle se déclenche à chaque connexion présentant un risque.
 
---
 
## <a name="october-2017"></a>Octobre 2017

### <a name="deprecate-azure-ad-reports"></a>Déprécier les rapports Azure AD

**Type :** Modification planifiée  
**Catégorie de service :** Reporting  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités  

Le portail Azure vous offre :

- Une nouvelle console d’administration d’Azure AD.
- De nouvelles API pour les rapports sur l’activité et la sécurité.
 
En raison de ces nouvelles fonctionnalités, les API de rapport sous le point de terminaison /reports seront supprimées le 10 décembre 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Détection automatique du champ de connexion

**Type :** Résolution   
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Authentification unique  

Azure Active Directory prend en charge la détection automatique des champs de connexion pour les applications qui affichent un champ de nom d’utilisateur et un champ de mot de passe HTML. Ces étapes sont documentées sous [Comment capturer automatiquement les champs de connexion d’une application](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application). Vous pouvez rechercher cette fonctionnalité en ajoutant une application *ne provenant pas de la galerie* sur la page **Applications d’entreprise** du [portail Azure](https://aad.portal.azure.com). En outre dans cette nouvelle application, vous pouvez configurer le mode **Authentification unique** sur **Authentification unique avec mot de passe**, entrer une URL web, puis enregistrer la page.
 
En raison d’un problème de service, cette fonctionnalité a été temporairement désactivée. Le problème a été résolu et la détection automatique des champs de connexion est à nouveau disponible.

---

### <a name="new-multi-factor-authentication-features"></a>Nouvelles fonctionnalités d’authentification multifacteur

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentification multifacteur  
**Fonctionnalité de produit :** Protection et sécurité des identités  

La fonctionnalité MFA (Multi-Factor Authentication) joue un rôle essentiel dans la protection de votre organisation. Pour rendre les informations d’identification plus adaptables et l’expérience plus transparente, les fonctionnalités suivantes ont été ajoutées : 

- Les résultats de la demande d’accès multifacteur sont intégrés directement dans le rapport de connexion à Azure AD, qui inclut l’accès par programme aux résultats de MFA.
- La configuration de MFA est intégrée plus étroitement dans l’expérience de configuration d’Azure AD dans le portail Azure.

Avec cette préversion publique, la gestion et la création de rapports MFA font partie intégrante de l’expérience de configuration de base d’Azure AD. Désormais, vous pouvez gérer la fonctionnalité de portail de gestion MFA dans l’expérience Azure AD.

Pour en savoir plus, consultez [Référence pour la génération de rapports d’authentification multifacteur dans le portail Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Conditions d’utilisation

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Conditions d’utilisation  
**Fonctionnalité de produit :** Conformité  

Vous pouvez utiliser des conditions d’utilisation d’Azure AD pour afficher des informations comme des exclusions pertinentes aux obligations juridiques ou de conformité pour les utilisateurs.

Vous pouvez utiliser la fonctionnalité Conditions d’utilisation d’Azure AD dans les scénarios suivants :

- Conditions d’utilisation générales pour tous les utilisateurs de votre organisation
- Conditions d’utilisation spécifiques en fonction des attributs d’un utilisateur (par exemple, médecins et infirmières, ou employés nationaux et internationaux) créées par des groupes dynamiques
- Conditions d’utilisation spécifiques pour l’accès aux applications à fort impact commercial, comme Salesforce

Pour plus d’informations, consultez [Fonctionnalité Conditions d’utilisation d’Azure Active Directory (préversion)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="enhancements-to-privileged-identity-management"></a>Améliorations apportées à Privileged Identity Management

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Privileged Identity Management  
**Fonctionnalité de produit :** Privileged Identity Management  

Avec Azure Active Directory Privileged Identity Management, vous pouvez gérer, contrôler et surveiller l’accès aux ressources Azure (préversion) au sein de votre organisation par les éléments suivants :

- Abonnements
- Groupes de ressources
- Machines virtuelles 

Toutes les ressources dans le portail Azure tirant parti de la fonctionnalité de contrôle d’accès en fonction du rôle (RBAC) d’Azure peuvent utiliser les capacités de gestion de la sécurité et du cycle de vie offertes par Azure AD Privileged Identity Management.

Pour plus d’informations, consultez [PIM pour les ressources Azure (préversion)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Révisions d’accès

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Conformité  

Les organisations peuvent utiliser les révisions d’accès (préversion) pour gérer efficacement les appartenances à des groupes et les accès aux applications d’entreprise : 

- Vous pouvez le recertifier l’accès utilisateur invité à l’aide des révisions d’accès de leur accès aux applications et appartenances à des groupes. Les réviseurs peuvent décider rapidement si des utilisateurs invités doivent bénéficier d’un accès ininterrompu en fonction des informations fournies par les révisions d’accès.
- Vous pouvez recertifier l’accès d’employé à des applications et l’appartenance à des groupes à l’aide de révisions d’accès.

Vous pouvez collecter les contrôles de révision d’accès dans des programmes importants pour votre organisation afin de suivre les révisions de conformité ou les applications sensibles aux risques.

Pour plus d’informations, consultez [Révisions d’accès Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Masquer des applications tierces dans Mes applications et le lanceur d’applications d’Office 365

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Authentification unique  

Désormais, vous pouvez mieux gérer les applications qui s’affichent sur les portails de vos utilisateurs grâce à une nouvelle propriété permettant de **masquer une application**. Vous pouvez masquer des applications pour plus de clarté en cas d’affichage de mosaïques d’applications pour les services principaux, de vignettes en double et de lanceurs d’applications des utilisateurs. La commande se trouve dans la section **Properties (Propriétés)** de l’application tierce et s’appelle **Visible to user? (Visible par l’utilisateur ?)**. Vous pouvez également masquer une application par programme l’aide de PowerShell. 

Pour plus d’informations, consultez [Masquer une application de l’expérience utilisateur dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Qu’est-ce qui est disponible ?**

 Dans le cadre de la transition vers la nouvelle console d’administration, deux API permettant de récupérer les journaux d’activité d’Azure AD sont disponibles. Le nouvel ensemble d’API fournit une fonctionnalité enrichie de filtrage et de tri en plus de fournir des activités d’audit et de connexion plus avancées. Les données auparavant disponibles dans les rapports de sécurité sont désormais accessibles via l’API Identity Protection Risk Events dans Microsoft Graph.


## <a name="september-2017"></a>Septembre 2017

### <a name="hotfix-for-identity-manager"></a>Correctif logiciel pour Identity Manager

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Identity Manager  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités  

Un package cumulatif (build 4.4.1642.0) est disponible depuis le 25 septembre 2017 pour Identity Manager 2016 Service Pack 1. Ce package cumulatif :

- Résout les problèmes et ajoute des améliorations.
- Est une mise à jour cumulative qui remplace toutes les mises à jour d’Identity Manager 2016 Service Pack 1 jusqu’à la build 4.4.1459.0 d’Identity Manager 2016. 
- Requiert que vous disposiez d’Identity Manager 2016 build 4.4.1302.0. 

Pour plus d’informations, consultez [Package de correctifs cumulatifs (build 4.4.1642.0) disponible pour le Service Pack 1 de Microsoft Identity Manager 2016](https://support.microsoft.com/help/4021562). 

---
