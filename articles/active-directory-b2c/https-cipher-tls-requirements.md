---
title: Configuration requise pour TLS et les suites de chiffrement - Azure AD B2C
titleSuffix: Azure AD B2C
description: Notes pour les développeurs au sujet de la configuration requise de TLS et des suites de chiffrement HTTPS pour les interactions avec les points de terminaison d’API web.
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
ms.openlocfilehash: 3fd14d08c2cb19bcd3f06e60b90620d4cdca6b56
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776381"
---
# <a name="azure-active-directory-b2c-tls-and-cipher-suite-requirements"></a>Configuration requise pour TLS et les suites de chiffrement Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) se connecte à vos points de terminaison par le biais de [connecteurs d’API](api-connectors-overview.md) et de [fournisseurs d’identité](oauth2-technical-profile.md) dans les [flux d’utilisateurs](user-flow-overview.md). Cet article décrit la configuration requise de TLS et des chiffrements HTTPS pour vos points de terminaison.

Les points de terminaison configurés avec des connecteurs d’API et des fournisseurs d’identité doivent être publiés sur un URI HTTPS accessible publiquement. Avant l’établissement d’une connexion sécurisée avec le point de terminaison, le protocole et le chiffrement sont négociés entre Azure AD B2C et le point de terminaison sur la base des fonctionnalités des deux côtés de la connexion.

Azure AD B2C doit être en mesure de se connecter à vos points de terminaison au moyen de TLS (Transport Layer Security) et des suites de chiffrement comme cela est décrit dans cet article.

## <a name="tls-versions"></a>Versions TLS

TLS version 1.2 est un protocole de chiffrement qui fournit l’authentification et le chiffrement des données entre les serveurs et les clients. Votre point de terminaison doit prendre en charge la communication sécurisée sur **TLS version 1.2**. Les versions 1.0 et 1.1 antérieures de TLS sont dépréciées. 

## <a name="cipher-suites"></a>Suites de chiffrement

Les suites de chiffrement sont des ensembles d’algorithmes de chiffrement. Elles fournissent des informations essentielles sur la façon de communiquer des données de manière sécurisée en utilisant le protocole HTTPS via TLS.

Votre point de terminaison doit prendre en charge au moins l’un des chiffrements suivants :

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

## <a name="endpoints-in-scope"></a>Points de terminaison dans l’étendue

Les points de terminaison suivants utilisés dans votre environnement Azure AD B2C doivent remplir les exigences décrites dans cet article :

- [Connecteurs d’API](api-connectors-overview.md) 
- OAuth1
    - Point de terminaison de jeton 
    - Point de terminaison des informations utilisateur
- Fournisseurs d’identité OAuth2 et OpenID Connect
    - Point de terminaison de détection OpenID Connect
    - Point de terminaison JWKS OpenID Connect
    - Point de terminaison de jeton 
    - Point de terminaison des informations utilisateur
- [Indicateur de jeton d’ID](id-token-hint.md)
    - Point de terminaison de détection OpenID Connect
    - Point de terminaison JWKS OpenID Connect
- Point de terminaison des métadonnées du [fournisseur d’identité SAML](saml-service-provider.md)
- Point de terminaison des métadonnées du [fournisseur de services SAML](identity-provider-generic-saml.md)

## <a name="check-your-endpoint-compatibility"></a>Vérifier la compatibilité de vos points de terminaison

Pour vérifier que vos points de terminaison remplissent les exigences décrites dans cet article, effectuez un test avec un chiffrement TLS et un outil d’analyse. Testez votre point de terminaison à l’aide de [SSLLABS](https://www.ssllabs.com/ssltest/analyze.html).


## <a name="next-steps"></a>Étapes suivantes

Consultez également les articles suivants :

- [Résoudre les problèmes liés aux applications qui ne prennent pas en charge TLS 1.2](../cloud-services/applications-dont-support-tls-1-2.md)
- [Suites de chiffrement dans TLS/SSL (SSP Schannel)](https://docs.microsoft.com/windows/win32/secauthn/cipher-suites-in-schannel)
- [Comment activer TLS 1.2](https://docs.microsoft.com/mem/configmgr/core/plan-design/security/enable-tls-1-2)
- [Résolution du problème lié à TLS 1.0](https://docs.microsoft.com/security/engineering/solving-tls1-problem)




