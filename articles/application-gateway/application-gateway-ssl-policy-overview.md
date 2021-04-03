---
title: Présentation de la stratégie TLS pour Azure Application Gateway
description: Découvrez comment configurer la stratégie TLS pour Azure Application Gateway et réduire la surcharge de chiffrement et de déchiffrement à partir d’une batterie de serveurs back-end.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: amsriva
ms.openlocfilehash: 77239cd8586b8fb07abf6862be436979541bdb99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97631688"
---
# <a name="application-gateway-tls-policy-overview"></a>Présentation de la stratégie TLS Application Gateway

Vous pouvez utiliser Azure Application Gateway pour centraliser la gestion des certificats TLS/SSL et réduire la surcharge de chiffrement et de déchiffrement à partir d’une batterie de serveurs back-end. Cette gestion TLS centralisée permet également de spécifier une stratégie TLS centrale adaptée aux besoins de sécurité de votre organisation. Celle-ci vous aide à respecter les exigences de conformité ainsi que les directives en matière de sécurité et pratiques recommandées.

La stratégie TLS inclut le contrôle de la version du protocole TLS ainsi que des suites de chiffrement et de l’ordre dans lequel les chiffrements sont utilisés lors d’une négociation TLS. Application Gateway offre deux mécanismes pour contrôler la stratégie TLS. Vous pouvez utiliser une stratégie prédéfinie ou une stratégie personnalisée.

## <a name="predefined-tls-policy"></a>Stratégie TLS prédéfinie

Application Gateway comporte trois stratégies de sécurité prédéfinies. Vous pouvez configurer votre passerelle avec n’importe laquelle de ces stratégies pour obtenir le niveau de sécurité approprié. Les noms de stratégie sont annotés en fonction de leur année et leur mois de configuration. Chaque stratégie offre différentes versions de protocole TLS et de suites de chiffrement. Nous vous recommandons d’utiliser les dernières stratégies TLS pour garantir la meilleure sécurité TLS.

## <a name="known-issue"></a>Problème connu
Application Gateway v2 ne prend pas en charge les chiffrements DHE suivants et ceux-ci ne sont pas utilisés pour les connexions TLS avec les clients, même s’ils sont mentionnés dans les stratégies prédéfinies. Au lieu de chiffrements DHE, les chiffrements ECDHE sécurisés et plus rapides sont recommandés.

- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Propriété  |Valeur  |
|---|---|
|Nom     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Default| True (si aucune stratégie prédéfinie n’est spécifiée) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Propriété  |Valeur  |
|   ---      |  ---       |
|Nom     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Default| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Propriété  |Valeur  |
|---|---|
|Nom     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Default| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Stratégie TLS personnalisée

Si une stratégie TLS prédéfinie doit être configurée pour vos besoins, vous devez définir votre propre stratégie TLS personnalisée. Avec une stratégie TLS personnalisée, vous contrôlez totalement la version minimale du protocole TLS à prendre en charge, ainsi que les suites de chiffrement prises en charge et leur ordre de priorité.

> [!IMPORTANT]
> Si vous utilisez une stratégie SSL personnalisée dans le SKU Application Gateway v1 (standard ou WAF), assurez-vous d’ajouter le chiffrement obligatoire « TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 » à la liste. Ce chiffrement est requis pour activer les métriques et la journalisation dans la référence (SKU) Application Gateway v1.
> Cela n’est pas obligatoire pour la référence SKU Application Gateway v2 (Standard_v2 ou WAF_v2).
 
### <a name="tlsssl-protocol-versions"></a>Versions du protocole TLS/SSL

* Les protocoles SSL 2.0 et 3.0 sont désactivés par défaut pour toutes les passerelles d’application. Ces versions de protocole ne sont pas configurables.
* Une stratégie TLS personnalisée vous permet de sélectionner l’un des trois protocoles suivants en tant que version minimale du protocole TLS pour votre passerelle : TLSv1_0, TLSv1_1 et TLSv1_2.
* Si aucune stratégie TLS n’est définie, les trois protocoles (TLSv1_0, TLSv1_1 et TLSv1_2) sont activés.

### <a name="cipher-suites"></a>Suites de chiffrement

Application Gateway prend en charge les suites de chiffrement suivantes à partir desquelles vous pouvez choisir votre stratégie personnalisée. L’ordre des suites de chiffrement détermine l’ordre de priorité pendant la négociation TLS.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> Les suites de chiffrement TLS utilisées pour la connexion sont également basées sur le type de certificat utilisé. Dans les connexions client-passerelle Application Gateway, les suites de chiffrement utilisées sont basées sur le type de certificats de serveur sur l’écouteur Application Gateway. Dans Application Gateway aux connexions du pool principal, les suites de chiffrement utilisées sont basées sur le type de certificats de serveur sur les serveurs du pool principal.

## <a name="next-steps"></a>Étapes suivantes

Pour apprendre à personnaliser une stratégie TLS, consultez [Configurer les versions de stratégie TLS et les suites de chiffrement sur Application Gateway](application-gateway-configure-ssl-policy-powershell.md).
