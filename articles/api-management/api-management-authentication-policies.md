---
title: Stratégies d’authentification dans Gestion des API Azure | Microsoft Docs
description: Découvrez les stratégies d’authentification disponibles dans Gestion des API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: c0f8da779ca656cf357c418b8766a53307643695
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764340"
---
# <a name="api-management-authentication-policies"></a>Stratégies d’authentification dans Gestion des API
Cette rubrique est une ressource de référence au sujet des stratégies Gestion des API suivantes. Pour plus d'informations sur l'ajout et la configuration des stratégies, consultez la page [Stratégies dans Gestion des API](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Stratégies d’authentification  
  
-   [Authenticate with Basic](api-management-authentication-policies.md#Basic) : authentification avec un service principal à l’aide de l’authentification de base.  
  
-   [Authenticate with client certificate](api-management-authentication-policies.md#ClientCertificate) : authentification avec un service principal à l’aide de certificats clients.  

-   [Authentifier avec identité gérée](api-management-authentication-policies.md#ManagedIdentity) -authentifier avec le [identité gérée](../active-directory/managed-identities-azure-resources/overview.md) pour le service de gestion des API.  
  
##  <a name="Basic"></a> Authenticate with Basic  
 La stratégie `authentication-basic` permet l’authentification auprès d’un service principal à l’aide de l’authentification de base. Cette stratégie définit en réalité l’en-tête d’autorisation HTTP sur la valeur correspondant aux informations d’identification fournies dans la stratégie.  
  
### <a name="policy-statement"></a>Instruction de la stratégie  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>Exemples  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>Éléments  
  
|Nom|Description|Obligatoire|  
|----------|-----------------|--------------|  
|authentification-basic|Élément racine.|Oui|  
  
### <a name="attributes"></a>Attributs  
  
|Nom|Description|Obligatoire|Default|  
|----------|-----------------|--------------|-------------|  
|username|Spécifie le nom d’utilisateur associé aux informations d’identification de base.|Oui|S.O.|  
|password|Spécifie le mot de passe associé aux informations d’identification de base.|Oui|S.O.|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound  
  
-   **Étendues de la stratégie :** API  
  
##  <a name="ClientCertificate"></a> Authenticate with client certificate  
 La stratégie `authentication-certificate` permet l’authentification auprès d’un service principal à l’aide d’un certificat client. Le certificat doit être [installé dans Gestion des API](https://go.microsoft.com/fwlink/?LinkID=511599) en premier et identifié par son empreinte.  
  
### <a name="policy-statement"></a>Instruction de la stratégie  
  
```xml  
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>  
```  
  
### <a name="examples"></a>Exemples  
  
Dans cet exemple de client certificat est identifié par son empreinte numérique.
```xml  
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />  
``` 
Dans cet exemple, certificat client est identifié par nom de la ressource.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Éléments  
  
|Nom|Description|Obligatoire|  
|----------|-----------------|--------------|  
|authentication-certificate|Élément racine.|Oui|  
  
### <a name="attributes"></a>Attributs  
  
|Nom|Description|Obligatoire|Default|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Empreinte du certificat client.|Soit `thumbprint` ou `certificate-id` doit être présent.|S.O.|  
|certificate-id|Le nom de ressource de certificat.|Soit `thumbprint` ou `certificate-id` doit être présent.|S.O.|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound  
  
-   **Étendues de la stratégie :** API  

##  <a name="ManagedIdentity"></a> Authentifier avec identité gérée  
 Utilisez le `authentication-managed-identity` stratégie auprès d’un service principal à l’aide de l’identité du service Gestion des API gérée. Cette stratégie utilise efficacement l’identité gérée pour obtenir un jeton d’accès d’Azure Active Directory pour accéder à la ressource spécifiée. 
  
### <a name="policy-statement"></a>Instruction de la stratégie  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Exemples  
  
```xml  
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" /> 
```  
  
### <a name="elements"></a>Éléments  
  
|Nom|Description|Obligatoire|  
|----------|-----------------|--------------|  
|authentication-managed-identity |Élément racine.|Oui|  
  
### <a name="attributes"></a>Attributs  
  
|Nom|Description|Obligatoire|Default|  
|----------|-----------------|--------------|-------------|  
|resource|Chaîne. L’URI ID d’application de la cible API web (ressource sécurisée) dans Azure Active Directory.|Oui|S.O.|  
|output-token-variable-name|Chaîne. Nom de la variable de contexte qui recevra la valeur du jeton en tant qu’un type d’objet `string`.|Non |S.O.|  
|ignore-error|Booléen. Si la valeur `true`, le pipeline de stratégie continuera à s’exécuter même si un jeton d’accès n’est pas obtenu.|Non |false|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound  
  
-   **Étendues de la stratégie :** global, product, API, operation  

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation de stratégies, consultez les pages :

+ [Stratégies dans Gestion des API](api-management-howto-policies.md)
+ [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
+ [Référence de stratégie](api-management-policy-reference.md) pour obtenir la liste complète des instructions et des paramètres de stratégie
+ [API Management policy samples](policy-samples.md) (Exemples de stratégie de gestion d’API)   
