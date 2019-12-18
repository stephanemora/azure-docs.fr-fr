---
title: Notes pour les développeurs relatives aux stratégies personnalisées
titleSuffix: Azure AD B2C
description: Notes à destination des développeurs pour configurer et maintenir Azure AD B2C avec des stratégies personnalisées.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: edbd31434715c380badf15118b0779885aed700f
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949753"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Notes développeur pour les stratégies personnalisées dans Azure Active Directory B2C

La configuration de stratégie personnalisée dans Azure Active Directory B2C est désormais mise à la disposition générale. Cette méthode de configuration s’adresse aux développeurs d’identité d’un niveau avancé en matière de création de solutions d’identité complexes. Les stratégies personnalisées mettent la puissance d’Identity Experience Framework au service des locataires Azure AD B2C.
Les développeurs d’identité avancés qui utilisent des stratégies personnalisées doivent prévoir du temps pour suivre des procédures pas à pas et lire des documents de référence.

Si la plupart des options de stratégie personnalisée disponibles sont désormais à la disposition générale, certaines fonctionnalités sous-jacentes comme les types de profil technique et les API de définition de contenu se trouvent à différentes phases du cycle de vie logiciel. Beaucoup d’autres seront bientôt disponibles. Le tableau ci-dessous fournit des informations plus précises sur leur niveau de disponibilité.

## <a name="features-that-are-generally-available"></a>Fonctionnalités mises à la disposition générale

- Créer et charger des parcours utilisateur d’authentification personnalisés à l’aide de stratégies personnalisées.
    - Décrire des parcours utilisateur étape par étape comme des échanges entre des fournisseurs de revendications.
    - Définir le branchement conditionnel dans des parcours utilisateur.
- Interagir avec des services compatibles avec l’API REST dans vos parcours utilisateur d’authentification personnalisés.
- Se fédérer avec des fournisseurs d’identité conformes au protocole OpenIDConnect.
- Se fédérer avec des fournisseurs d’identité qui adhèrent au protocole SAML 2.0.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilités des développeurs de l’ensemble de fonctionnalités de stratégie personnalisée

La configuration manuelle de stratégie accorde le niveau d’accès minimal à la plateforme sous-jacente d’Azure AD B2C et se traduit par la création d’un framework de confiance unique. Les nombreuses permutations possibles de fournisseurs d’identité personnalisés, les relations de confiance, les intégrations avec des services externes et les workflows étape par étape exigent une approche de conception et de configuration méthodique.

Les développeurs qui utilisent les fonctionnalités de stratégie personnalisée doivent respecter les recommandations suivantes :

- Se familiariser avec le langage de configuration des stratégies personnalisées et la gestion des clés/secrets. Pour plus d’informations, consultez [TrustFrameworkPolicy](trustframeworkpolicy.md).
- S’approprier les scénarios et les intégrations personnalisées. Documenter votre travail et informer l’organisation de votre site actif.
- Effectuer un test de scénario méthodique.
- Suivre les meilleures pratiques de test et de développement de logiciels avec au minimum un environnement de développement et de test et un environnement de production.
- Suivre les nouveaux développements des services et fournisseurs d’identité que vous intégrez. Par exemple, suivre les modifications apportées aux secrets, ainsi que les modifications planifiées et non planifiées du service.
- Configurer la surveillance active et surveiller la réactivité des environnements de production. Pour plus d’informations sur l’intégration avec Application Insights, consultez [Azure Active Directory B2C : collecte des journaux](active-directory-b2c-custom-guide-eventlogger-appins.md).
- Garder les adresses e-mail de contact à jour dans l’abonnement Azure et rester attentif aux e-mails de l’équipe de site en ligne Microsoft.
- Prendre les mesures adéquates lorsque cela est recommandé par l’équipe de site en ligne Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Conditions d’utilisation des fonctionnalités de la préversion publique

- Nous vous encourageons à utiliser les fonctionnalités de la préversion publique à des fins d’évaluation uniquement.
- Les contrats de niveau de service (SLA) ne s’appliquent pas aux fonctionnalités de la préversion publique.
- Les demandes de support pour les fonctionnalités de la préversion publique peuvent être soumises par le biais des canaux de support habituels.

## <a name="features-by-stage-and-known-issues"></a>Fonctionnalités par étape et problèmes connus

Les fonctionnalités de stratégie personnalisée/Identity Experience Framework font l’objet d’un développement rapide et constant. Le tableau suivant indique la disponibilité des fonctionnalités et des composants.

### <a name="identity-providers-tokens-protocols"></a>Fournisseurs d’identité, jetons et protocoles

| Fonctionnalité | Développement | PRÉVERSION | GA | Notes |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | Par exemple, Google+.  |
| IDP-OAUTH2 |  |  | X | Par exemple, Facebook.  |
| IDP-OAUTH1 (twitter) |  | X |  | Par exemple, Twitter. |
| IDP-OAUTH1 (ex-twitter) |  |  |  | Non pris en charge |
| IDP-SAML |  |   | X | Par exemple, Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| OAUTH1 avec partie de confiance |  |  |  | Non pris en charge. |
| OAUTH2 avec partie de confiance |  |  | X |  |
| OIDC avec partie de confiance |  |  | X |  |
| SAML avec partie de confiance | X |  |  |  |
| WSFED avec partie de confiance | X |  |  |  |
| API REST avec l’authentification de base et par certificat |  |  | X | Par exemple, Azure Logic Apps. |

### <a name="component-support"></a>Prise en charge des composants

| Fonctionnalité | Développement | PRÉVERSION | GA | Notes |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Multi-Factor Authentication |  |  | X |  |
| Azure Active Directory comme annuaire local |  |  | X |  |
| Sous-système Azure Email pour la vérification d’e-mail |  |  | X |  |
| Prise en charge multi-langage|  |  | X |  |
| Validations de prédicats |  |  | X | Par exemple, la complexité des mots de passe. |
| Utilisation de fournisseurs de services d’e-mail tiers | X |  |  |  |

### <a name="content-definition"></a>Définition du contenu

| Fonctionnalité | Développement | PRÉVERSION | GA | Notes |
| ------- | :-----------: | :-------: | :--: | ----- |
| Page d’erreur, api.error |  |  | X |  |
| Page de sélection de fournisseurs d’identité, api.idpselections |  |  | X |  |
| Sélection de fournisseurs d’identité pour l’inscription, api.idpselections.signup |  |  | X |  |
| Mot de passe oublié, api.localaccountpasswordreset |  |  | X |  |
| Connexion au compte local, api.localaccountsignin |  |  | X |  |
| Inscription à un compte local, api.localaccountsignup |  |  | X |  |
| Page MFA, api.phonefactor |  |  | X |  |
| Inscription autodéclarée à un compte social, api.selfasserted |  |  | X |  |
| Mise à jour de profil autodéclaré, api.selfasserted.profileupdate |  |  | X |  |
| Page d’inscription ou de connexion unifiée, api.signuporsignin, avec le paramètre « disableSignup » |  |  | X |  |
| JavaScript / Mise en page |  | X |  |  |

### <a name="app-ief-integration"></a>Intégration App-IEF

| Fonctionnalité | Développement | PRÉVERSION | GA | Notes |
| ------- | :-----------: | :-------: | :--: | ----- |
| Paramètre de chaîne de requête domain_hint |  |  | X | Disponible sous forme de revendication, peut être transmis au fournisseur d’identité. |
| Paramètre de chaîne de requête login_hint |  |  | X | Disponible sous forme de revendication, peut être transmis au fournisseur d’identité. |
| Insérer du code JSON dans le parcours utilisateur via client_assertion | X |  |  | Sera déconseillé. |
| Insérer du code JSON dans le parcours utilisateur en tant que id_token_hint |  | X |  | Approche à adopter pour transmettre JSON. |
| Transmettre le jeton IDP TOKEN à l’application |  | X |  | Par exemple, de Facebook à l’application. |

### <a name="session-management"></a>Gestion des sessions

| Fonctionnalité | Développement | PRÉVERSION | GA | Notes |
| ------- | :-----------: | :-------: | :--: | ----- |
| Fournisseur de session par authentification unique |  |  | X |  |
| Fournisseur de session de connexion externe |  |  | X |  |
| Fournisseur de session par authentification unique SAML |  |  | X |  |
| Fournisseur de session SSO par défaut |  |  | X |  |

### <a name="security"></a>Sécurité

| Fonctionnalité | Développement | PRÉVERSION | GA | Notes |
|-------- | :-----------: | :-------: | :--: | ----- |
| Clés de stratégie : générer, manuel, charger |  |  | X |  |
| Clés de stratégie : RSA/certificat, secrets |  |  | X |  |
| Chargement de stratégie |  |  | X |  |

### <a name="developer-interface"></a>Interface de développeur

| Fonctionnalité | Développement | PRÉVERSION | GA | Notes |
| ------- | :-----------: | :-------: | :--: | ----- |
| Expérience utilisateur de l’infrastructure d’expérience d’identité dans le Portail Azure |  |  | X |  |
| Journaux d’activité du parcours utilisateur Application Insights |  | X |  | Utilisés pour la résolution des problèmes pendant le développement.  |
| Journaux d’événements Application Insights (via les étapes d’orchestration) |  | X |  | Utilisés pour superviser les flux utilisateur en production. |

## <a name="next-steps"></a>Étapes suivantes

[Approfondissez vos connaissances sur les stratégies personnalisées et découvrez ce qui les distingue des flux utilisateur](active-directory-b2c-overview-custom.md).
