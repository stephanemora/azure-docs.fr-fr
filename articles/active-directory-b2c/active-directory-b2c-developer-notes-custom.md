---
title: Remarques sur le développement de stratégies personnalisées - Azure Active Directory B2C | Microsoft Docs
description: Notes à destination des développeurs pour configurer et maintenir Azure AD B2C avec des stratégies personnalisées.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: cf9c6f6a54c38f00e477e2a9d62e72ab5faccdef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317970"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Remarques sur le développement de stratégies personnalisées dans Azure Active Directory B2C

Configuration de stratégie personnalisée dans Azure Active Directory B2C est désormais disponible. Cette méthode de configuration est destinée aux développeurs avancée des identités création de solutions d’identité complexe. Stratégies personnalisées à disposition la puissance de l’infrastructure d’expérience d’identité dans les locataires Azure AD B2C. Advanced identité, les développeurs qui utilisent des stratégies personnalisées doivent prévoir du temps de suivre des procédures et lire des documents de référence.

Si la plupart des options de stratégie personnalisée disponibles sont maintenant à la disposition générale, il existe des fonctionnalités sous-jacentes, telles que les types de profil technique et de la définition du contenu des API qui sont à différents stades du cycle de vie du logiciel. Beaucoup d’autres est disponibles. Le tableau ci-dessous indique le niveau de disponibilité à un niveau plus granulaire.  

## <a name="features-that-are-generally-available"></a>Fonctionnalités qui sont généralement disponibles

- Créer et charger des parcours utilisateur d’authentification personnalisés à l’aide de stratégies personnalisées.  
    - Décrire des parcours utilisateur étape par étape comme des échanges entre des fournisseurs de revendications.  
    - Définir le branchement conditionnel dans des parcours utilisateur.  
- Interagir avec des services compatibles avec l’API REST dans votre parcours utilisateur d’authentification personnalisée.  
- Fédérer avec les fournisseurs d’identité qui sont compatibles avec le protocole OpenIDConnect.  
- Fédérer avec les fournisseurs d’identité qui adhèrent au protocole SAML 2.0.   

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilités des développeurs de l’ensemble de fonctionnalités de stratégie personnalisée

Configuration de stratégie manuelle accorde l’accès de niveau inférieur à la plateforme sous-jacente d’Azure AD B2C et entraîne la création d’une infrastructure approuvée unique. Les nombreuses permutations possibles de fournisseurs d’identité personnalisés, relations d’approbation, les intégrations aux services externes et des flux de travail étape par étape nécessitent une approche méthodique de conception et la configuration. 

Les développeurs utilisent l’ensemble de fonctionnalités de stratégie personnalisée doivent respecter les consignes suivantes :

- Vous familiariser avec le langage de configuration de la gestion des stratégies et de clé/secrets personnalisé. Pour plus d’informations, consultez [TrustFrameworkPolicy](trustframeworkpolicy.md). 
- S’approprier les scénarios et les intégrations personnalisées. Documenter votre travail et informer votre organisation de site actif.  
- Effectuer un test de scénario méthodique. 
- Suivre les meilleures pratiques de test et de développement de logiciels avec au minimum un environnement de développement et de test et un environnement de production. 
- Suivre les nouveaux développements des services et fournisseurs d’identité que vous intégrez. Par exemple, suivre les modifications apportées aux secrets, ainsi que les modifications planifiées et non planifiées du service. 
- Configurer la surveillance active et surveiller la réactivité des environnements de production. Pour plus d’informations sur l’intégration avec Application Insights, consultez [Azure Active Directory B2C : Collecter les journaux](active-directory-b2c-custom-guide-eventlogger-appins.md). 
- Garder les adresses e-mail de contact à jour dans l’abonnement Azure et rester attentif aux e-mails de l’équipe de site en ligne Microsoft. 
- Prendre les mesures adéquates lorsque cela est recommandé par l’équipe de site en ligne Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Termes du contrat de fonctionnalités en version préliminaire publique

- Nous vous encourageons à utiliser les fonctionnalités de la version préliminaire publique uniquement à des fins d’évaluation. 
- Contrats de niveau de service (SLA) ne s’appliquent pas aux fonctionnalités de préversion publique.
- Demandes de support pour les fonctionnalités en version préliminaire publique peuvent être déposées via les canaux de support habituels. 

## <a name="features-by-stage-and-known-issues"></a>Fonctionnalités par étape et problèmes connus

Fonctionnalités d’infrastructure d’expérience de stratégie/identité personnalisée sont en cours de développement rapid et constant. Le tableau suivant est un index de fonctionnalités et la disponibilité des composants.

### <a name="identity-providers-tokens-protocols"></a>Fournisseurs d’identité, jetons et protocoles

| Fonctionnalité | Développement | VERSION PRÉLIMINAIRE | GA | Notes |
|-------- | ----------- | ------- | -- | ----- |
| IDP-OpenIDConnect |  |  | X | Par exemple, Google +.  |
| IDP-OAUTH2 |  |  | X | Par exemple, Facebook.  |
| Fournisseur d’identité-OAUTH1 (twitter) |  | X |  | Par exemple, Twitter. |
| Fournisseur d’identité-OAUTH1 (ex-twitter) |  |  |  | Non pris en charge |
| IDP-SAML |  |   | X | Par exemple, Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| Partie de confiance OAUTH1 tiers |  |  |  | Non pris en charge. |
| Partie de confiance OAUTH2 tiers |  |  | X |  |
| OIDC avec partie de confiance |  |  | X |  |
| SAML avec partie de confiance | X |  |  |  |
| WSFED avec partie de confiance | X |  |  |  |
| API REST avec basic et authentification de certificat |  |  | X | Par exemple, Azure Logic Apps. |

### <a name="component-support"></a>Prise en charge des composants

| Fonctionnalité | Développement | VERSION PRÉLIMINAIRE | GA | Notes |
| ------- | ----------- | ------- | -- | ----- |
| Azure Multi-Factor Authentication |  |  | X |  |
| Azure Active Directory comme annuaire local |  |  | X |  |
| Sous-système d’E-mail Azure pour la vérification par e-mail |  |  | X |  |
| Prise en charge multi-langage|  |  | X |  |
| Validations de prédicats |  |  | X | Par exemple, la complexité de mot de passe. |
| À l’aide de fournisseurs de services de messagerie tiers | X |  |  |  |

### <a name="content-definition"></a>Définition du contenu

| Fonctionnalité | Développement | VERSION PRÉLIMINAIRE | GA | Notes |
| ------- | ----------- | ------- | -- | ----- |
| Page d’erreur, api.error |  |  | X |  |
| Page de sélection de fournisseurs d’identité, api.idpselections |  |  | X |  |
| Sélection de fournisseurs d’identité pour l’inscription, api.idpselections.signup |  |  | X |  |
| Mot de passe oublié, api.localaccountpasswordreset |  |  | X |  |
| Connexion au compte local, api.localaccountsignin |  |  | X |  |
| Inscription à un compte local, api.localaccountsignup |  |  | X |  |
| Page MFA, api.phonefactor |  |  | X |  |
| Autodéclaration api.selfasserted d’abonnement, compte social |  |  | X |  |
| Mise à jour de profil autodéclaré, api.selfasserted.profileupdate |  |  | X |  |
| Inscription unifiée ou page de connexion, api.signuporsignin, avec le paramètre « disableSignup » |  |  | X |  |
| JavaScript / Page de contrat |  | X |  |  |

### <a name="app-ief-integration"></a>Intégration App-IEF

| Fonctionnalité | Développement | VERSION PRÉLIMINAIRE | GA | Notes |
| ------- | ----------- | ------- | -- | ----- |
| Paramètre de chaîne de requête domain_hint |  |  | X | Disponible en tant que revendication, peut être passé au fournisseur d’identité. |
| Paramètre de chaîne de requête login_hint |  |  | X | Disponible en tant que revendication, peut être passé au fournisseur d’identité. |
| Insérer du code JSON dans le parcours utilisateur via client_assertion | X |  |  | Sera déconseillée. |
| Insérer du code JSON dans le parcours utilisateur en tant que id_token_hint |  | X |  | Go-approche à adopter pour passer JSON. |
| Passer un jeton de fournisseur d’identité à l’application |  | X |  | Par exemple, à partir de Facebook à l’application. |

### <a name="session-management"></a>Gestion des sessions

| Fonctionnalité | Développement | VERSION PRÉLIMINAIRE | GA | Notes |
| ------- | ----------- | ------- | -- | ----- |
| Fournisseur de session par authentification unique |  |  | X |  |
| Fournisseur de session de connexion externe |  |  | X |  |
| Fournisseur de session par authentification unique SAML |  |  | X |  |
| Fournisseur de Session par défaut l’authentification unique |  |  | X |  |

### <a name="security"></a>Sécurité

| Fonctionnalité | Développement | VERSION PRÉLIMINAIRE | GA | Notes |
|-------- | ----------- | ------- | -- | ----- |
| Clés de stratégie : générer, manuel, charger |  |  | X |  |
| Clés de stratégie : RSA/certificat, secrets |  |  | X |  |
| Chargement de la stratégie |  |  | X |  |

### <a name="developer-interface"></a>Interface de développeur

| Fonctionnalité | Développement | VERSION PRÉLIMINAIRE | GA | Notes |
| ------- | ----------- | ------- | -- | ----- |
| Expérience utilisateur de l’infrastructure d’expérience d’identité dans le Portail Azure |  |  | X |  |
| Journaux d’activité du parcours utilisateur Application Insights |  | X |  | Utilisé pour la résolution des problèmes pendant le développement.  |
| Journaux des événements application Insights (via les étapes d’orchestration) |  | X |  | Utilisé pour contrôler le flux d’utilisateurs en production. |

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur les stratégies personnalisées et les différences avec les flux d’utilisateurs](active-directory-b2c-overview-custom.md).
