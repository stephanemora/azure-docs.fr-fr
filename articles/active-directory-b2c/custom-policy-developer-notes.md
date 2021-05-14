---
title: Notes à destination des développeurs en matière de flux d’utilisateurs et de stratégies personnalisées
titleSuffix: Azure AD B2C
description: Notes à destination des développeurs pour configurer et maintenir Azure AD B2C avec des flux d’utilisateurs et stratégies personnalisées
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/30/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e6261699166e0157750fc691bc0c1726d8cefd50
ms.sourcegitcommit: f6b76df4c22f1c605682418f3f2385131512508d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108324056"
---
# <a name="developer-notes-for-azure-active-directory-b2c"></a>Notes à destination des développeurs pour Azure Active Directory B2C

Les [flux d’utilisateurs et stratégies personnalisées](user-flow-overview.md) Azure Active Directory B2C sont en disponibilité générale. Les fonctionnalités Azure AD B2C sont continuellement cours de développement. Dès lors, bien que la plupart des fonctionnalités soient en disponibilité générale, certaines fonctionnalités se trouvent à différents stades du cycle de publication logicielle. Cet article traite des améliorations cumulatives apportées à Azure AD B2C et spécifie la disponibilité des fonctionnalités.

## <a name="terms-for-features-in-public-preview"></a>Conditions d’utilisation des fonctionnalités de la préversion publique

- Nous vous encourageons à utiliser les fonctionnalités de la préversion publique à des fins d’évaluation uniquement.
- Les contrats de niveau de service (SLA) ne s’appliquent pas aux fonctionnalités de la préversion publique.
- Les demandes de support pour les fonctionnalités de la préversion publique peuvent être soumises par le biais des canaux de support habituels.

## <a name="user-flows"></a>Flux d’utilisateurs

|Fonctionnalité  |Flux utilisateur  |Stratégie personnalisée  |Notes  |
|---------|:---------:|:---------:|---------|
| [Inscription et connexion](add-sign-up-and-sign-in-policy.md) avec un e-mail et un mot de passe. | GA | GA| |
| [Inscription et connexion](add-sign-up-and-sign-in-policy.md) avec un nom d’utilisateur et un mot de passe.| GA | GA | |
| [Flux de modification du profil](add-profile-editing-policy.md) | GA | GA | |
| [Réinitialisation de mots de passe en libre service](add-password-reset-policy.md). | GA| GA| |
| [Forcer la réinitialisation du mot de passe](force-password-reset.md) | PRÉVERSION | N/D | |
| [Inscription et connexion par téléphone](phone-authentication-user-flows.md) | GA | GA | |

## <a name="user-experience-customization"></a>Personnalisation de l’expérience utilisateur

|Fonctionnalité  |Flux utilisateur  |Stratégie personnalisée  |Notes  |
|---------|:---------:|:---------:|---------|
| [Prise en charge multilingue](localization.md)| GA | GA | |
| [Vérification d’e-mail personnalisée](custom-email-mailjet.md) | N/D | GA| |
| [Personnaliser l’interface utilisateur avec des modèles intégrés](customize-ui.md) | GA| GA| |
| [Personnaliser l’interface utilisateur avec des modèles personnalisés](customize-ui-with-html.md) | GA| GA| À l’aide de modèles HTML. |
| [JavaScript](javascript-and-page-layout.md) | GA | GA | |
| [Expérience de connexion incorporée](embedded-login.md) | N/D |  PRÉVERSION| À l’aide de l’élément de cadre en ligne `<iframe>`. |
| [Complexité du mot de passe](password-complexity.md) | GA | GA | |
| [Désactiver la vérification e-mail](disable-email-verification.md) | GA|  GA| Non recommandé dans les environnements de production. La désactivation de la vérification par e-mail dans le processus d’inscription peut entraîner la réception de courrier indésirable. |


## <a name="protocols-and-authorization-flows"></a>Protocoles et flux d’autorisation

|Fonctionnalité  |Flux utilisateur  |Stratégie personnalisée  |Notes  |
|---------|:---------:|:---------:|---------|
|[Code d’autorisation OAuth2](authorization-code-flow.md) | GA | GA |
|[Code d’autorisation OAuth2 avec PKCE](authorization-code-flow.md)| GA | GA | Clients publics et applications monopages. |
|[Flux implicite OAuth2](implicit-flow-single-page-application.md) | GA | GA | |
|[Informations d’identification de mot de passe du propriétaire de ressource OAuth2](add-ropc-policy.md) | PRÉVERSION | PRÉVERSION | |
|OAuth1 | N/D | N/D | Non pris en charge. |
|[OpenID Connect](openid-connect.md) | GA | GA | |
|[SAML2](saml-service-provider.md) | N/D | GA | Liaisons POST et de redirection. |
| WSFED | N/D | N/D | Non pris en charge. |

## <a name="identity-providers"></a>Fournisseurs d’identité

|Fonctionnalité  |Flux utilisateur  |Stratégie personnalisée  |Notes  |
|---------|:---------:|:---------:|---------|
|[AD FS](identity-provider-adfs.md) | N/D | GA | |
|[Amazon](identity-provider-amazon.md) | GA | GA | |
|[Apple](identity-provider-apple-id.md) | PRÉVERSION | PRÉVERSION | |
|[Azure AD (un seul locataire)](identity-provider-azure-ad-single-tenant.md) | GA | GA | |
|[Azure AD (plusieurs locataires)](identity-provider-azure-ad-multi-tenant.md) | N/D  | GA | |
|[Azure AD B2C](identity-provider-azure-ad-b2c.md) | GA | GA | |
|[Ebay](identity-provider-ebay.md) | N/D | PRÉVERSION | |
|[Facebook](identity-provider-facebook.md) | GA | GA | |
|[GitHub](identity-provider-github.md) | GA | GA | |
|[Google](identity-provider-google.md) | GA | GA | |
|[ID.me](identity-provider-id-me.md) | GA | GA | |
|[LinkedIn](identity-provider-linkedin.md) | GA | GA | |
|[Compte Microsoft](identity-provider-microsoft-account.md) | GA | GA | |
|[QQ](identity-provider-qq.md) | PRÉVERSION | GA | |
|[Salesforce](identity-provider-salesforce.md) | GA | GA | |
|[Salesforce (protocole SAML)](identity-provider-salesforce-saml.md) | N/D | GA | |
|[Twitter](identity-provider-twitter.md) | GA | GA | |
|[WeChat](identity-provider-wechat.md) | PRÉVERSION | GA | |
|[Weibo](identity-provider-weibo.md) | PRÉVERSION | GA | |

## <a name="generic-identity-providers"></a>Fournisseurs d’identité générique

|Fonctionnalité  |Flux utilisateur  |Stratégie personnalisée  |Notes  |
|---------|:---------:|:---------:|---------|
|[OAuth2](oauth2-technical-profile.md) | N/D | GA | Par exemple, [Google](identity-provider-google.md), [GitHub](identity-provider-github.md) et [Facebook](identity-provider-facebook.md).|
|[OAuth1](oauth1-technical-profile.md) | N/D | GA | Par exemple, [Twitter](identity-provider-twitter.md). |
|[OpenID Connect](openid-connect-technical-profile.md) | GA | GA | Par exemple, [Azure AD](identity-provider-azure-ad-single-tenant.md).  |
|[SAML2](identity-provider-generic-saml.md) | N/D | GA | Par exemple, [Salesforce](identity-provider-salesforce-saml.md) et [AD-FS]. (identity-provider-adfs.md) |
| WSFED | N/D | N/D | |

### <a name="api-connectors"></a>Connecteurs d’API

|Fonctionnalité  |Flux utilisateur  |Stratégie personnalisée  |Notes  |
|---------|:---------:|:---------:|---------|
|[Connecteurs d’API](api-connectors-overview.md) | PRÉVERSION | GA | |
|[Sécuriser avec l'authentification de base](secure-rest-api.md#http-basic-authentication) | PRÉVERSION | GA | |
|[Sécuriser avec l’authentification par certificat client](secure-rest-api.md#https-client-certificate-authentication) | PRÉVERSION | GA | |
|[Sécuriser avec l’authentification du porteur OAuth2](secure-rest-api.md#oauth2-bearer-authentication) | N/D | GA | |
|[Sécuriser avec l’authentification par clé API](secure-rest-api.md#api-key-authentication) | N/D | GA | |

### <a name="application-and-azure-ad-b2c-integration"></a>Intégration des applications et Azure AD B2C

|Fonctionnalité  |Flux utilisateur  |Stratégie personnalisée  |Notes  |
|---------|:---------:|:---------:|---------|
| [Rediriger la connexion vers un fournisseur social](direct-signin.md#redirect-sign-in-to-a-social-provider) | GA | GA | Paramètre de chaîne de requête `domain_hint`. |
| [Préremplir le nom de connexion](direct-signin.md#prepopulate-the-sign-in-name) | GA | GA | Paramètre de chaîne de requête `login_hint`. |
| Insérer du code JSON dans le parcours utilisateur via `client_assertion`| N/D| Déprécié |  |
| Insérer du code JSON dans le parcours utilisateur en tant que [id_token_hint](id-token-hint.md) | N/D | GA | |
| [Transmettre le jeton du fournisseur d’identité à l’application](idp-pass-through-user-flow.md)| PRÉVERSION| PRÉVERSION| Par exemple, de Facebook à l’application. |


## <a name="custom-policy-features"></a>Caractéristiques de la stratégie de personnalisée

### <a name="session-management"></a>Gestion des sessions

| Fonctionnalité |  Stratégie personnalisée | Notes |
| ------- | :--: | ----- |
| [Fournisseur de session SSO par défaut](custom-policy-reference-sso.md#defaultssosessionprovider) | GA |  |
| [Fournisseur de session de connexion externe](custom-policy-reference-sso.md#externalloginssosessionprovider) | GA |  |
| [Fournisseur de session par authentification unique SAML](custom-policy-reference-sso.md#samlssosessionprovider) | GA |  |
| [Fournisseur de session par authentification unique OAuth](custom-policy-reference-sso.md#oauthssosessionprovider)  | GA|  |
| [Authentification unique](session-behavior.md#sign-out)  |  PRÉVERSION |  |

### <a name="components"></a>Components

| Fonctionnalité | Stratégie personnalisée | Notes |
| ------- | :--: | ----- |
| [Authentification par facteur téléphonique](phone-factor-technical-profile.md) | GA |  |
| [Authentification Azure AD MFA](multi-factor-auth-technical-profile.md) | PRÉVERSION |  |
| [Mot de passe à usage unique](one-time-password-technical-profile.md) | GA |  |
| [Azure Active Directory](active-directory-technical-profile.md) comme répertoire local | GA |  |
| [Validations de prédicats](predicates.md) | GA | Par exemple, la complexité des mots de passe. |
| [Contrôles d’affichage](display-controls.md) | GA |  |

### <a name="developer-interface"></a>Interface de développeur

| Fonctionnalité | Stratégie personnalisée | Notes |
| ------- | :--: | ----- |
| Dans le portail Azure | GA |   |
| [Journaux de parcours utilisateur Application Insights](troubleshoot-with-application-insights.md) | PRÉVERSION | Utilisés pour la résolution des problèmes pendant le développement.  |
| [Journaux d’événements Application Insights](analytics-with-application-insights.md) | PRÉVERSION | Utilisés pour superviser les flux utilisateur en production. |

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilités des développeurs de l’ensemble de fonctionnalités de stratégie personnalisée

La configuration manuelle de stratégie accorde le niveau d’accès minimal à la plateforme sous-jacente d’Azure AD B2C et se traduit par la création d’un framework de confiance unique. Les nombreuses permutations possibles de fournisseurs d’identité personnalisés, les relations de confiance, les intégrations avec des services externes et les workflows étape par étape exigent une approche de conception et de configuration méthodique.

Les développeurs qui utilisent les fonctionnalités de stratégie personnalisée doivent respecter les recommandations suivantes :

- Se familiariser avec le langage de configuration des stratégies personnalisées et la gestion des clés/secrets. Pour plus d’informations, consultez [TrustFrameworkPolicy](trustframeworkpolicy.md).
- S’approprier les scénarios et les intégrations personnalisées. Documenter votre travail et informer l’organisation de votre site actif.
- Effectuer un test de scénario méthodique.
- Suivre les meilleures pratiques en matière de développement de logiciels et de mise en lots. Au moins un environnement de développement et de test est recommandé.
- Suivre les nouveaux développements des services et fournisseurs d’identité que vous intégrez. Par exemple, suivre les modifications apportées aux secrets, ainsi que les modifications planifiées et non planifiées du service.
- Configurer la surveillance active et surveiller la réactivité des environnements de production. Pour plus d’informations sur l’intégration avec Application Insights, consultez [Azure Active Directory B2C : collecte des journaux](analytics-with-application-insights.md).
- Garder les adresses e-mail de contact à jour dans l’abonnement Azure et rester attentif aux e-mails de l’équipe de site en ligne Microsoft.
- Prendre les mesures adéquates lorsque cela est recommandé par l’équipe de site en ligne Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Opérations Microsoft Graph disponibles pour Azure AD B2C](microsoft-graph-operations.md).
- [Approfondissez vos connaissances sur les stratégies personnalisées et découvrez ce qui les distingue des flux utilisateur](custom-policy-overview.md).
